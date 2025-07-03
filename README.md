Guía Definitiva para Instalar ComfyUI con Aceleración ROCm en Ubuntu 22.04 LTS (AMD RX 7000 Series)

Este README te guiará paso a paso para configurar ComfyUI con soporte de aceleración por GPU (ROCm) en Ubuntu 22.04 LTS, optimizado para tarjetas gráficas AMD Radeon de la serie RX 7000 (como la RX 7900 XTX). Esta guía aborda los problemas comunes de dependencias y asegura una instalación funcional.

🚀 Requisitos Mínimos del Sistema

Antes de empezar, asegúrate de cumplir con estos requisitos:

    Sistema Operativo: Ubuntu 22.04 LTS (Jammy Jellyfish).

    Kernel Linux: Versión 6.x.x o superior. Puedes verificarlo con uname -r. (Ej: 6.8.0-60-generic es excelente).

    GPU AMD: Una tarjeta AMD Radeon RX serie 7000 (RDNA3), como la RX 7900 XTX (con al menos 20 GB de VRAM).

    RAM: 32 GB o más (64 GB o más es ideal para modelos grandes y video).

    Espacio en Disco: Mínimo 200 GB libres en la partición donde instalarás ComfyUI y tus modelos (idealmente un SSD NVMe). Los modelos de IA son muy grandes.

🛠️ Instalación Paso a Paso

Sigue estos pasos con precisión. Un error en un paso puede causar problemas más adelante.

Paso 1: Preparación Inicial del Sistema

    Abre una Terminal (Ctrl + Alt + T).

    Actualiza tu sistema y reinicia (crucial):
    Bash

    sudo apt update && sudo apt upgrade -y
    sudo reboot

    Después del reinicio, abre otra terminal.

Paso 2: Purga Completa de Instalaciones ROCm Previas (¡Importante!)

Este paso es fundamental para evitar conflictos de versiones y dependencias rotas. No omitas este paso, incluso si crees que no tenías ROCm instalado.
Bash

sudo apt remove --purge $(dpkg -l | grep -E "rocm|hip|hsa|amd" | awk '{print $2}') -y
sudo apt autoremove -y
sudo rm -f /etc/apt/sources.list.d/rocm.list
sudo rm -f /etc/apt/sources.d/amdgpu.list
sudo apt update

Paso 3: Instalación de ROCm 5.7 (Recomendado para RX 7000 en Ubuntu 22.04)

La versión 5.7 de ROCm ha demostrado ser muy estable y compatible con las RX 7000 series y las versiones de PyTorch precompiladas.

    Agrega las claves GPG y el repositorio de ROCm 5.7:
    Bash

wget -qO - http://repo.radeon.com/rocm/rocm.gpg.key | sudo apt-key add -
echo 'deb [arch=amd64] http://repo.radeon.com/rocm/apt/5.7/ ubuntu main' | sudo tee /etc/apt/sources.list.d/rocm.list
sudo apt update

Instala los componentes principales de ROCm 5.7:
Bash

sudo apt install rocm-dkms=5.7.* rocm-dev=5.7.* rocm-utils=5.7.* rocminfo -y

Este comando instala los drivers del kernel (dkms), las herramientas de desarrollo, utilidades y la herramienta rocminfo.

Configura las variables de entorno para ROCm:
Esto asegura que el sistema encuentre las librerías y ejecutables de ROCm.
Bash

echo 'export PATH=/opt/rocm/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/opt/rocm/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

Agrega tu usuario a los grupos de permisos de la GPU:
Esto es crucial para que ComfyUI y PyTorch puedan acceder a tu GPU.
Bash

sudo usermod -a -G render $LOGNAME
sudo usermod -a -G video $LOGNAME

¡Reinicia tu computadora! (¡CRÍTICO!)
Bash

    sudo reboot

    Este reinicio es absolutamente necesario para que los drivers de ROCm y los cambios de permisos surtan efecto.

Paso 4: Verificar la Instalación de ROCm

Después de reiniciar, abre una terminal y verifica que ROCm detecte tu GPU.
Bash

/opt/rocm/bin/rocminfo | head -20

Deberías ver información sobre tu "AMD Radeon RX 7900 XTX" y no errores como "Agent creation failed".

Paso 5: Preparar ComfyUI (Descarga y Organización de Archivos)

Dado que ya tenías una carpeta con modelos, vamos a descargar el software principal de ComfyUI y luego moveremos tus modelos existentes.

    Mueve tu carpeta ComfyUI actual (si solo tiene modelos) a una carpeta temporal para no perder tus modelos:
    Si ya tienes una carpeta ComfyUI en /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ que solo contiene tus modelos (sin main.py), renómbrala temporalmente para no confundirla con la instalación principal:
    Bash

cd /media/diplomacia/01DBEB84FE5586F0/IA_Projects/
mv ComfyUI ComfyUI_modelos_antiguos

Si no tienes una carpeta ComfyUI aquí o no contiene modelos, omite este paso.

Clona la versión principal de ComfyUI en tu directorio de proyectos:
Esto descargará el software de ComfyUI (incluido main.py) en una carpeta llamada ComfyUI.
Bash

git clone https://github.com/comfyanonymous/ComfyUI.git

Mueve tus modelos y nodos a la nueva instalación de ComfyUI (si tenías una carpeta con modelos):
Si renombraste ComfyUI a ComfyUI_modelos_antiguos en el paso 1, ahora mueve tus modelos de allí a la nueva instalación:
Bash

# Mueve los contenidos de la carpeta 'models'
mv /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI_modelos_antiguos/models/* /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI/models/

# Mueve los contenidos de la carpeta 'custom_nodes' (si tenías nodos personalizados)
mv /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI_modelos_antiguos/custom_nodes/* /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI/custom_nodes/

Verifica en tu explorador de archivos que tus modelos y nodos estén ahora en la nueva carpeta ComfyUI (/media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI/models/ y .../custom_nodes/).

Elimina la carpeta de modelos antigua (opcional, una vez verificado):
Bash

    rm -rf /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI_modelos_antiguos

Paso 6: Configurar Entorno Virtual y Dependencias de Python

Este paso instala PyTorch y otras librerías dentro de un entorno virtual dedicado para ComfyUI.

    Navega a tu carpeta de ComfyUI:
    Bash

cd /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI/

Instala python3.10-venv (si no lo tienes) y crea/activa el entorno virtual:
Bash

sudo apt install python3.10-venv -y # Asegúrate de tenerlo
rm -rf venv                          # Elimina cualquier 'venv' anterior o corrupto
python3 -m venv venv                 # Crea el nuevo entorno virtual
source venv/bin/activate             # Activa el entorno (verás (venv) en el prompt)

Instala PyTorch con soporte ROCm 5.7 y otras dependencias:
Bash

    pip install --no-cache-dir --force-reinstall torch==2.3.1+rocm5.7 torchvision==0.14.1+rocm5.7 torchaudio==0.13.1 --index-url https://download.pytorch.org/whl/rocm5.7
    pip install accelerate einops transformers safetensors aiohttp

    --no-cache-dir --force-reinstall asegura una instalación limpia.
    aiohttp es necesario para el ComfyUI Manager.

Paso 7: Verificar la Detección de la GPU por PyTorch

Con el entorno virtual (venv) activo en tu terminal:
Bash

python -c "import torch; print(f'PyTorch version: {torch.__version__}'); print(f'ROCm backend: {torch.backends.hip.is_available()}'); print(f'Número de GPUs detectadas: {torch.cuda.device_count()}'); if torch.cuda.device_count() > 0: print(f'Nombre de la GPU: {torch.cuda.get_device_name(0)}')"

La salida esperada es:

    PyTorch version: 2.3.1+rocm5.7

    ROCm backend: True

    Número de GPUs detectadas: 1

    Nombre de la GPU: Radeon RX 7900 XTX (o similar)

Paso 8: Lanzar ComfyUI

Finalmente, con el entorno virtual (venv) activo y en la carpeta /media/diplomacia/01DBEB84FE5586F0/IA_Projects/ComfyUI/:
Bash

python3 main.py

ComfyUI se iniciará y te proporcionará una URL (generalmente http://127.0.0.1:8188). Pégala en tu navegador web.

## Versiones del sistema y software utilizado

Para asegurar compatibilidad y facilitar la instalación, estas son las versiones específicas usadas y probadas en esta guía:

| Componente           | Versión                     | Comentarios                          |
|---------------------|-----------------------------|------------------------------------|
| **Ubuntu**           | 22.04.5 LTS                 | Base estable y con soporte largo   |
| **Kernel Linux**     | 5.19+                       | Compatible con ROCm y GPU AMD      |
| **ROCm**             | 5.7                         | Versión oficial AMD                |
| **Python**           | 3.10.12                     | Instalado en entorno virtual (venv)|
| **PyTorch (ROCm)**   | 2.3.1+rocm5.7               | Soporte nativo AMD ROCm            |
| **TorchVision**      | 0.14.1+rocm5.7              | Compatible con PyTorch + ROCm      |
| **GCC**              | 11.4.0                      | Compilador para librerías y Python |
| **pip**              | Última versión recomendada  | Para gestión de paquetes Python    |
| **ComfyUI**          | Última versión estable       | Interfaz para generación IA        |

---

### Cómo verificar las versiones instaladas

Puedes verificar las versiones con los siguientes comandos:

```bash
python3 --version
python3 -c "import torch; print(torch.__version__); print(torch.version.hip)"
python3 -c "import torchvision; print(torchvision.__version__)"
/opt/rocm/bin/rocminfo | head -20
uname -r
gcc --version | head -1
pip --version

✅ Notas y Recomendaciones Finales

    Modelos y Nodos: Tus modelos (.safetensors, etc.) deben ir en la carpeta ComfyUI/models/checkpoints/ y sus subcarpetas (ej: loras, vae). Los nodos personalizados van en ComfyUI/custom_nodes/.

    Actualizaciones: Para actualizar ComfyUI en el futuro, ve a la carpeta ComfyUI y ejecuta git pull. Si hay nuevos requerimientos, pip install -r requirements.txt (dentro del venv activo).

    Monitoreo: Usa radeontop (instalar con sudo apt install radeontop) en otra terminal para ver el uso de tu GPU.

    Troubleshooting: Si encuentras errores, asegúrate de que el entorno virtual (venv) esté activo y de estar en la carpeta correcta antes de ejecutar los comandos de pip o python3 main.py.
