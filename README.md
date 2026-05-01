# eBPF-LAB: Guia de Instalação e Configuração
Este documento descreve como instalar e configurar o ambiente de laboratório com Docker, Containerlab e Ubuntu, além de corrigir erros comuns encontrados durante a instalação.

1. Ambiente
- Sistema operacional: Ubuntu (WSL2 ou nativo)
- Pré-requisitos:
  - Git
  - Curl
  - VS Code (opcional, para edição e visualização)

2. Instalação do Docker
Remover pacotes conflitantes
```
sudo apt-get remove docker docker.io containerd runc

```

Adicionar repositório oficial do Docker

```
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

Instalar Docker Engine 

```
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

Testar instalação

```
docker --version
docker ps
```

3. Instalação do Containerlab

```
bash -c "$(curl -sL https://get.containerlab.dev)"
```

Verificar versão:

```
containerlab version
```

4. Configuração do Laboratório

Arquivo lab.clab.yml
Exemplo de topologia simples com dois nós:

```
name: ebpf-lab

topology:
  nodes:
    node-a:
      kind: linux
      image: nicolaka/netshoot:latest
      exec:
        - ip addr add 10.0.0.1/24 dev eth1

    node-b:
      kind: linux
      image: nicolaka/netshoot:latest
      binds:
        - xdp_drop.o:/xdp_drop.o
      exec:
        - ip addr add 10.0.0.2/24 dev eth1

  links:
    - endpoints: ["node-a:eth1", "node-b:eth1"]
```

Deploy da topologia

```
sudo containerlab deploy -t lab.clab.yml
```

Visualização gráfica

```
containerlab graph -t lab.clab.yml
```

Abra o endereço http://localhost:50080 no navegador ou use o Simple Browser no VS Code para visualizar em uma aba.

5. Erros comuns e correções

- Conflito entre containerd.io e containerd  
→ Solução: remover pacotes antigos e usar repositório oficial do Docker.

- Containers não aparecem no VS Code  
→ Solução: habilitar integração do Docker Desktop com WSL em Settings → Resources → WSL Integration.

- Wireshark no WSL sem interface gráfica  
→ Solução: usar Wireshark no Windows para capturar tráfego das interfaces Docker, ou rodar tcpdump nos containers e abrir os .pcap no Wireshark.

6. Expansão da topologia
Para adicionar mais nós, basta incluir novos blocos em nodes: e definir os links em links:.
Exemplo com um switch central (bridge):

```

nodes:
  node-a:
    kind: linux
    image: nicolaka/netshoot:latest
    exec:
      - ip addr add 10.0.0.1/24 dev eth1
  node-b:
    kind: linux
    image: nicolaka/netshoot:latest
    exec:
      - ip addr add 10.0.0.2/24 dev eth1
  node-c:
    kind: linux
    image: nicolaka/netshoot:latest
    exec:
      - ip addr add 10.0.0.3/24 dev eth1
  switch:
    kind: bridge

links:
  - endpoints: ["node-a:eth1", "switch:eth1"]
  - endpoints: ["node-b:eth1", "switch:eth2"]
  - endpoints: ["node-c:eth1", "switch:eth3"]
```
   


