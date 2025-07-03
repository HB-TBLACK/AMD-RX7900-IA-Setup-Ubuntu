# Instalador automatizado de ComfyUI con soporte AMD GPU (ROCm) en Ubuntu 22.04

Este proyecto facilita la instalación completa de un entorno IA en Linux para GPUs AMD, usando ROCm + PyTorch compatible, junto a ComfyUI.

🔧 **Requisitos:**
- Ubuntu 22.04.5 LTS
- GPU AMD RX 7000+ (se probó con RX 7900 XTX)
- Conexión a Internet
- Python 3.10

📦 **Qué instala:**
- ROCm 5.7 oficial
- PyTorch 2.3.1 con soporte HIP (ROCm)
- ComfyUI y entorno virtual Python
- Dependencias necesarias para ejecutar modelos de IA de forma acelerada por GPU AMD

📁 **Archivos incluidos:**
- `setup.sh`: script de instalación paso a paso
- `README.md`: este documento

🚀 **Ejecutar instalación:**

```bash
chmod +x setup.sh
./setup.sh
