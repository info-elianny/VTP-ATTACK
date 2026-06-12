# 🛡️ Ataque VTP (VLAN Trunking Protocol)

📹 [Video demostración](https://youtu.be/zYQJRqLTaCk)

---

## 📌 Objetivo del Laboratorio

Demostrar cómo un atacante puede manipular el protocolo VTP para agregar o eliminar VLANs en todos los switches de un dominio VTP, con el fin de observar el impacto que esta manipulación tiene sobre la segmentación y disponibilidad de la red.

### Topología de Red
![Topología de Red](https://i.postimg.cc/7LfFrPRC/vtp-1.png)

---

## 🎯 Objetivo del Script

Enviar mensajes VTP maliciosos hacia un switch objetivo conectado mediante un enlace trunk, utilizando Yersinia en modo gráfico, logrando agregar VLANs falsas o eliminar VLANs existentes en todos los switches del dominio VTP, afectando la topología completa de la red.

---

## ⚙️ Requisitos

### Hardware y Software

- Un equipo atacante con *Kali Linux*
- Un *switch Cisco* con VTP habilitado y el puerto en modo trunk
- Al menos un *switch adicional* en el mismo dominio VTP
- *Yersinia* instalado en Kali Linux
- Permisos de *administrador (root)* para ejecutar Yersinia
- El puerto del switch conectado a Kali debe estar en modo *trunk*

### Instalación de dependencias

```bash
apt-get install yersinia -y
```

---

## 🔧 Parámetros Utilizados

| Parámetro | Descripción |
|-----------|-------------|
| Interfaz de red | Adaptador desde el cual se realizará el ataque, debe estar conectado al switch en modo trunk |
| ID de la VLAN | Identificador numérico de la VLAN a agregar o eliminar del dominio VTP |
| Nombre de la VLAN | Nombre asignado a la VLAN al momento de agregarla |
| Dominio VTP | Nombre del dominio VTP configurado en los switches de la red |
| Modo de ataque | Selección entre agregar VLAN (Add VLAN) o eliminar VLAN (Del VLAN) |
| Sending packets | Opción de Yersinia para iniciar el envío continuo de paquetes VTP maliciosos |

---

## 🚀 Cómo Ejecutar la Herramienta

```bash
sudo yersinia -G
```

> ⚠️ *Debe ejecutarse con permisos root (sudo)*

---

## 📋 Funcionamiento del Ataque

**Paso 1:** Se abre Yersinia en modo gráfico con permisos de root. Se selecciona la interfaz de red conectada al switch en modo trunk.

```bash
sudo yersinia -G
```

**Paso 2:** En el menú principal se hace clic en "Launch attack" y se selecciona la pestaña VTP.

**Paso 3:** Para eliminar una VLAN, en la ventana "Choose protocol attack" se selecciona **deleting one vlan**, se ingresa el ID de la VLAN a eliminar y se hace clic en OK.

**Paso 4:** Para agregar una VLAN, se selecciona **adding one vlan**, se ingresa el ID y el nombre de la VLAN y se hace clic en OK.

**Paso 5:** Yersinia envía los paquetes VTP hacia el switch, el cual los propaga automáticamente a todos los demás switches del dominio VTP, aplicando los cambios en toda la red.

**Paso 6:** Se verifica el impacto en el switch con los comandos `show vlan brief` y `show vtp status` para confirmar que las VLANs fueron agregadas o eliminadas exitosamente.

---

## 📸 Capturas de Pantalla

### Tabla de VLANs antes del ataque
![Tabla de VLANs antes del ataque](https://i.postimg.cc/rsJnt7b2/vtp-cap-1.png)

---

### Ataque corriendo en Yersinia para eliminar una VLAN
![Ataque eliminando VLAN](https://i.postimg.cc/L8hCnC3c/vtp-cap-2.png)
![Ataque eliminando VLAN](https://i.postimg.cc/TYNQMjvk/vtp-cap-3.png)
![Ataque eliminando VLAN](https://i.postimg.cc/DZ5LQF2c/vtp-cap-4.png)

---

### Resultado del ataque — eliminación de la VLAN
![Resultado eliminación](https://i.postimg.cc/nh7mRqFT/vtp-cap-5.png)

---

### Ataque corriendo en Yersinia para agregar una VLAN
![Ataque agregando VLAN](https://i.postimg.cc/qRm6hLLb/vtp-cap-6.png)
![Ataque agregando VLAN](https://i.postimg.cc/fbDV8Fgs/vtp-cap-7.png)
![Ataque agregando VLAN](https://i.postimg.cc/qRZgM7Bg/vtp-cap-8.png)

---

### Resultado del ataque — VLAN agregada
![Resultado agregando VLAN](https://i.postimg.cc/gcQrCrBV/vtp-cap-9.png)

---

## 🌐 Documentación de la Red

| Dispositivo | Interfaz | Dirección IP | Máscara de Red |
|-------------|----------|-------------|----------------|
| R-1 | e0/0 | 6.6.1.1 | 255.255.255.0 |
| SW-1 | — | — | — |
| SW-2 | — | — | — |
| Kali Linux (Atacante) | e0/2 | 6.6.1.14 | 255.255.255.0 |
| Windows 7 (Víctima) | e0/1 | 6.6.1.12 | 255.255.255.0 |
| Cloud | net | 192.168.206.140 | 255.255.255.0 |

### VLANs Configuradas

| VLAN | Nombre |
|------|--------|
| 10 | ADMINISTRACIÓN |
| 20 | USUARIOS |
| 50 | FINANZAS |
| 60 | VENTAS |

---

## 🛡️ Contramedidas para Mitigar el Ataque

### 1. VTP Mode Transparent
Cambia todos los switches a modo transparent. En este modo el switch no procesa ni aplica cambios VTP, solo los reenvía. Ningún atacante puede modificar la base de datos de VLANs.

```
SW1(config)# vtp mode transparent
SW2(config)# vtp mode transparent
```

![VTP Mode Transparent resultado](https://i.postimg.cc/63T5SNrS/vtp-m-1.png)
![VTP Mode Transparent resultado](https://i.postimg.cc/1RnDnkkw/vtp-m-2.png)

### 2. VTP Password
Configura una contraseña en el dominio VTP. Sin la contraseña correcta el switch rechaza cualquier mensaje VTP externo.

```
SW1(config)# vtp password Elianny2026
SW2(config)# vtp password Elianny2026
```

![VTP Password resultado](https://i.postimg.cc/5tr6f02j/vtp-m-3.png)


> ⚠️ *Este laboratorio fue realizado en un entorno controlado con fines educativos y de investigación.*  
> ⚠️ *BY: Elianny*
