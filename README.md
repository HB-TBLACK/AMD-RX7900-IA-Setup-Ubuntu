Configuración de PyTorch ROCm para GPUs AMD Serie 7000 en Ubuntu 22.04.5

Este documento explica cómo instalar y configurar correctamente PyTorch con soporte ROCm en Ubuntu 22.04.5 para usar GPUs AMD RX 7900 (y otras RX 7000 series) en proyectos de inteligencia artificial.
Requerimientos mínimos

    Ubuntu 22.04.5 LTS

    Kernel Linux >= 5.19 (ideal para soporte ROCm actualizado)

    GPU AMD RX 7000 series (Ejemplo: RX 7900 XTX)

    ROCm 5.7 instalado y funcionando

    Python 3.10.x

Instalación paso a paso
1. Actualizar sistema y preparar Ubuntu

sudo apt update && sudo apt upgrade -y
sudo reboot

2. Instalar ROCm 5.7

Sigue la guía oficial AMD para agregar el repositorio ROCm para Ubuntu 22.04:
https://rocmdocs.amd.com/en/latest/Installation_Guide/Installation-Guide.html#ubuntu

Resumen comandos básicos:

wget -qO - http://repo.radeon.com/rocm/rocm.gpg.key | sudo apt-key add -
echo 'deb [arch=amd64] http://repo.radeon.com/rocm/apt/5.7/ ubuntu main' | sudo tee /etc/apt/sources.list.d/rocm.list
sudo apt update
sudo apt install rocm-dkms=5.7.* rocm-dev=5.7.* rocm-utils=5.7.* -y

Agregar variables entorno:

echo 'export PATH=/opt/rocm/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/opt/rocm/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

Reinicia para aplicar cambios.
3. Instalar Python 3.10 y crear entorno virtual

sudo apt install python3.10 python3.10-venv python3.10-dev -y
python3.10 -m venv ~/venv_rocm
source ~/venv_rocm/bin/activate

4. Instalar PyTorch, torchvision y torchaudio con soporte ROCm 5.7

pip install --upgrade pip
pip install torch==2.3.1+rocm5.7 torchvision==0.14.1+rocm5.7 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/rocm5.7

5. Verificar instalación y detección GPU

Abre Python en el entorno virtual:

import torch
print(torch.__version__)  # Debe mostrar 2.3.1+rocm5.7
print(torch.cuda.is_available())  # Debe ser True
print(torch.cuda.device_count())  # Debe mostrar 1 o más
print(torch.cuda.get_device_name(0))  # Debe mostrar Radeon RX 7900 XTX

Notas y recomendaciones

    Siempre usar ROCm 5.7 para RX 7000 series, versiones anteriores no soportan bien la arquitectura RDNA 3.

    PyTorch debe coincidir exactamente con la versión ROCm para evitar errores (ej. torch==2.3.1+rocm5.7).

    Mantener kernel Linux actualizado para mejor estabilidad con ROCm.

    Si vas a usar frameworks IA (ComfyUI, Stable Diffusion, etc), asegúrate que estén configurados para ROCm (no CUDA).

    No mezclar versiones PyTorch sin soporte ROCm; causa fallos de memoria y carga de modelos.

Con esto tienes el entorno base para usar tu GPU AMD 7900 en IA sin pelear con drivers ni versiones.
