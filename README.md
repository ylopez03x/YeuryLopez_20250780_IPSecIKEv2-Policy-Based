# VPN Site-to-Site IPSec IKEv2 — Policy-Based
**Autor:** Yeury Lopez | **Matrícula:** 20250780 | **Repositorio:** YeuryLopez_20250780_IPSecIKEv2-Policy-Based

---

## Video de demostración

[![Ver en YouTube](https://img.shields.io/badge/YouTube-Ver%20Video-red?logo=youtube)](https://youtu.be/si4uS9ZNgV8)

**Enlace directo:** https://youtu.be/si4uS9ZNgV8

---

## 1. Objetivo
Configurar una VPN Site-to-Site basada en políticas utilizando IPSec con IKEv2. IKEv2 es más eficiente y seguro que IKEv1, con menos intercambios de mensajes para establecer el túnel. El tráfico interesante se define mediante ACLs.

---

## 2. Topología

> <img width="940" height="589" alt="image" src="https://github.com/user-attachments/assets/33e4a6d6-8c70-4070-903e-f996efdcc2ee" />

---

## 3. Direccionamiento IP

| Dispositivo | Interfaz | Dirección IP | Rol |
|---|---|---|---|
| R1 | Fa0/0 | 10.7.80.1/30 | WAN → ISP |
| R1 | Fa1/0 | 192.168.7.1/24 | LAN |
| ISP | Fa0/1 | 10.7.80.2/30 | WAN → R1 |
| ISP | Fa0/0 | 10.7.81.1/30 | WAN → R2 |
| R2 | Fa0/0 | 10.7.81.2/30 | WAN → ISP |
| R2 | Fa1/0 | 192.168.80.1/24 | LAN |
| Linux1 | ens3 | 192.168.7.2/24 | Host LAN R1 |
| Linux2 | ens3 | 192.168.80.2/24 | Host LAN R2 |

---

## 4. Parámetros VPN

| Parámetro | Valor |
|---|---|
| Tipo de VPN | IPSec IKEv2 Policy-Based |
| IKEv2 Proposal — Cifrado | AES-CBC-128 |
| IKEv2 Proposal — Integridad | SHA-1 |
| IKEv2 Proposal — Grupo DH | Group 2 (1024-bit) |
| IKEv2 — Autenticación | Pre-Shared Key (PSK) |
| Fase 2 — Modo | Tunnel |
| Pre-Shared Key | Yeury0780 |
| IOS requerido | 15.x+ (adventerprisek9) |

---

## 5. Configuración

### 5.1 Configuración R1


```
crypto ikev2 proposal PROP-IKEv2
 encryption aes-cbc-128
 integrity sha1
 group 2
crypto ikev2 policy POL-IKEv2
 proposal PROP-IKEv2
crypto ikev2 keyring KR-IKEv2
 peer R2
  address 10.7.81.2
  pre-shared-key Yeury0780
crypto ikev2 profile PROF-IKEv2
 match identity remote address 10.7.81.2 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local KR-IKEv2
crypto map CMAP 10 ipsec-isakmp
 set peer 10.7.81.2
 set transform-set TS
 set ikev2-profile PROF-IKEv2
 match address VPN-TRAFFIC
```

### 5.2 Configuración R2

---

## 6. Verificación y Funcionamiento

### 6.1 Estado IKEv2 SA

Ejecutar en R1:
```
show crypto ikev2 sa
```
> <img width="1016" height="218" alt="image" src="https://github.com/user-attachments/assets/f17a1a62-245b-4e80-8800-d579b8a97ac8" />


### 6.2 Estado IPSec SA

Ejecutar en R1:
```
show crypto ipsec sa
```
> <img width="814" height="349" alt="image" src="https://github.com/user-attachments/assets/6fcd6153-2c38-4221-9ac0-df6b50ccbffa" />


### 6.3 Demostración de conectividad

Ejecutar en Linux1:
```
ping -c 4 192.168.80.2
```
> <img width="776" height="189" alt="image" src="https://github.com/user-attachments/assets/348cfb1d-ad0a-48fd-8a80-387c3fce1f4a" />
