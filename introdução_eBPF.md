# Aula: Introdução ao eBPF e XDP
📌 Objetivos da Aula
Entender o que é eBPF e como funciona.

Conhecer o papel do XDP no processamento de pacotes.

Explorar casos de uso práticos em redes.

Visualizar a arquitetura e fluxo de pacotes.

🔎 O que é eBPF?
Extended Berkeley Packet Filter (eBPF) é uma tecnologia introduzida no Linux kernel em 2014.

Permite executar programas dentro do kernel de forma segura e eficiente.

Usos principais:

Monitoramento de rede

Manipulação de tráfego

Balanceamento de carga

Segurança

##🚀 O que é XDP?

- XDP (eXpress Data Path) é um tipo especial de programa eBPF.

- Atua logo na entrada da placa de rede, antes mesmo do pacote entrar na pilha de rede do kernel.

- Modos de operação:

1. Genérico: roda dentro da pilha de rede.

2. Nativo (driver): roda diretamente no driver da placa de rede.

3. Offload em hardware: roda na própria NIC (SmartNICs).

  ## 📊 Comparação: XDP vs Tecnologias Tradicionais

| Tecnologia | Local de Execução | Desempenho | Complexidade |
| --- | --- | --- | --- |
| **[iptables](ca://s?q=iptables_vs_XDP)** | Kernel (camada de rede) | Médio | Baixa |
| **[DPDK](ca://s?q=DPDK_vs_XDP)** | Espaço do usuário | Muito alto | Alta |
| **XDP** | Driver/NIC | Altíssimo | Moderada |

🛠️ Exemplos de Uso
Firewall ultrarrápido: bloquear pacotes ICMP antes de chegarem ao kernel.

Monitoramento em tempo real: contar pacotes descartados.

Balanceamento de carga: distribuir tráfego entre servidores.

## 📷 Diagramas e Fluxos

Arquitetura Simplificada
```
[Placa de Rede] --> [XDP/eBPF] --> [Kernel Linux] --> [Aplicações]
```

##Fluxo de Pacotes

- Pacote chega à NIC.

- Programa XDP decide:

1. Aceitar

2. Descartar

3. Redirecionar

- Pacote segue para o kernel ou é tratado diretamente.

  <img width="474" height="285" alt="image" src="https://github.com/user-attachments/assets/06a022a6-f5f5-4142-995a-d9f350fbf504" />

    
<img width="827" height="538" alt="image" src="https://github.com/user-attachments/assets/9fe15c04-79ab-462b-94c6-2c427b4d220f" />






  
