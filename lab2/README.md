### **Sub-rede 1 (Vendas)**
- **Endereço de Rede:** `192.168.10.0/24`
- **Dispositivos:** `pc0`, `pc1`
- **IPs dos Dispositivos:** `192.168.10.15`, `192.168.10.16`
- **Portas do Switch:** `FastEthernet 0/1`, `0/2`

### **Sub-rede 2 (DevOps)**
- **Endereço de Rede:** `192.168.11.0/24`
- **Dispositivos:** `pc2`, `lpt1`, `DNSS`
- **IPs dos Dispositivos:** `192.168.11.15`, `192.168.11.14`, `192.168.11.13`
- **Portas do Switch:** `FastEthernet 0/11`, `0/12`, `0/13`

---

![Cenario](cenario.png)i

## Configuração do Switch

### 1. Criação das VLANs

Use os seguintes comandos na **CLI (Command Line Interface)** do switch:

```bash
> enable
> configure terminal
> hostname Switch_vlan
> vlan 10
> name vendas
> vlan 20
> name devops
> end
> write memory

> enable
> configure terminal
> interface range fa0/1-10
> switchport mode access
> switchport access vlan 10
> exit
> interface range fa0/11-20
> switchport mode access
> switchport access vlan 20
> end
> write memory

## Configuração do Roteamento Inter-VLANs  
Neste ponto, os dispositivos estão separados em dois domínios de broadcast. Pings entre dispositivos da mesma VLAN funcionarão, mas entre VLANs diferentes, não. Para resolver isso, configuraremos o roteamento.  

### 1. Configuração da Porta Trunk no Switch
- Conecte o roteador ao switch usando a porta GigabitEthernet 0/1 do switch.
> enable
> configure terminal
> interface GigabitEthernet0/1
> switchport mode trunk
> switchport trunk allowed vlan 10,20
> exit

### 2. Configuração do Roteador (Router-on-a-stick)
> enable
> show ip interface brief
> configure t
> interface GigabitEthernet0/0/1
> no shutdown
> exit
> interface GigabitEthernet0/0/1.10
> encapsulation dot1Q 10
> ip address 192.168.10.1 255.255.255.0
> exit
> interface GigabitEthernet0/0/1.20
> encapsulation dot1Q 20
> ip address 192.168.11.1 255.255.255.0
> end
> write memory

Após isso, definimos em cada host o default gateway
- Para a rede vendas, é 192.168.10.1
- Para a rede devops, é 192.168.11.1
