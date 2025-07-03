# Instalación ComfyUI + ROCm + GPU AMD RX 7900 en Ubuntu 22.04

Este proyecto contiene un script para instalar **ComfyUI** con soporte para **GPU AMD RX 7900 XTX** en **Ubuntu 22.04.5 LTS** usando **ROCm 5.7** y **PyTorch 2.3.1 con HIP**.

## ✅ Objetivo

Evitar que otros usuarios pasen por todos los errores que se presentan al instalar ROCm, PyTorch y entornos compatibles para IA con GPU AMD en Linux. Esta guía ya está probada y funcional.

---

## 📋 Requisitos

- Ubuntu **22.04.5 LTS**
- GPU AMD RX 7900 XTX (también puede servir con otras RX 7000)
- Python 3.10 instalado
- Conexión a internet
- Ganas de evitar dolores de cabeza

---

## ⚙️ ¿Qué instala este script?

- Drivers ROCm 5.7 oficiales
- PyTorch 2.3.1 con soporte ROCm (hip)
- ComfyUI con entorno virtual
- Dependencias necesarias para correr IA con aceleración por GPU AMD
- Comprobaciones post-instalación

---

## 🚀 ¿Qué modelos funcionan?

Ya se ha probado con:
- **Wanon 2.1** (funciona perfectamente)
- SDXL
- Otros modelos `.ckpt` y `.safetensors`

---

## 🧰 Instrucciones

Clona este repositorio y ejecuta el script:

```bash
git clone https://github.com/HB-TBLACK/AMD-RX7900-IA-Setup-Ubuntu.git
cd AMD-RX7900-IA-Setup-Ubuntu
chmod +x setup.sh
./setup.sh
