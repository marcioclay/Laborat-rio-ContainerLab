## 🐳 Comandos e Correções no Containerlab + Docker + Wireshark

🔧 Comandos Básicos de Docker
```
bash
docker ps              # Lista containers ativos
docker ps -a           # Lista todos os containers (ativos e parados)
docker exec -it <nome> bash   # Acessa um container em modo interativo
exemplo: docker exec -it clab-ebpf-lab-node-a
docker stop <nome>     # Para um container
docker rm <nome>       # Remove um container
docker logs <nome>     # Mostra logs de um container
```

🧩 Comandos do Containerlab
```
bash
sudo containerlab deploy -t lab.clab.yml   # Sobe a topologia definida no arquivo
sudo containerlab destroy -t lab.clab.yml  # Remove a topologia
```

🌐 Configuração de Interfaces
Dentro do container:
```
bash
ip addr show           # Lista interfaces e IPs
ip addr add 10.0.0.1/24 dev eth1   # Adiciona IP a uma interface
```
⚠️ Erro comum: Device "eth1" does not exist  
👉 Correção: usar a interface correta (eth0 ou eth1, dependendo da imagem e configuração).

🔀 Criação de Bridge no Host
```
bash
sudo ip link add name br1 type bridge   # Cria uma bridge chamada br1
sudo ip link set br1 up                 # Ativa a bridge
ip link show br1                        # Verifica se está UP
```
⚠️ Erro comum: bridge 'br1' referenced in topology but does not exist  
👉 Correção: criar a bridge manualmente antes do deploy.

🚫 Erro com eth0 no YAML
⚠️ Mensagem: Eth0 interface name is not allowed for node-a node when network mode is not set to none.  
👉 Correção: usar eth1 no YAML, pois o Containerlab cria interfaces extras (eth1, eth2, etc.) para conectar os nós às bridges.

📡 Teste de Conectividade
```
bash
ping 10.0.0.2   # Testa comunicação entre node-a e node-b
ping 10.0.0.3   # Testa comunicação entre node-a e node-c
```

📊 Captura de Pacotes
- Wireshark

Abrir Wireshark no host:
Selecionar a interface da bridge (br1 ou br-<id>).
Filtrar pacotes ICMP:

Código
icmp
Tcpdump (alternativa no terminal)
```
bash
docker exec -it clab-ebpf-lab-node-a tcpdump -i eth1
```

🧠 Didática
Erro 1: Interface inexistente (eth1) → corrigir para interface válida.
Erro 2: Bridge ausente (br1) → criar manualmente com ip link add.
Erro 3: Uso de eth0 no YAML → não permitido, usar eth1.
Demonstração: ping entre nós → captura no Wireshark → análise de ICMP (Request/Reply).
