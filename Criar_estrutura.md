### 🧩 Exemplo de Criação de Nós no Containerlab

O Containerlab permite criar topologias de rede complexas usando containers como se fossem equipamentos reais — switches, roteadores e hosts.
A seguir, um exemplo completo de configuração em YAML e explicação de cada parte.

📘 Arquivo lab.clab.yml
```
name: rede-lab

topology:
  nodes:
    # 🖥️ Nó Host
    host1:
      kind: linux
      image: ubuntu:22.04
      exec:
        - ip addr add 10.0.0.10/24 dev eth1
        - ip route add default via 10.0.0.1

    # 🌐 Roteador
    router1:
      kind: linux
      image: ubuntu:22.04
      exec:
        - sysctl -w net.ipv4.ip_forward=1
        - ip addr add 10.0.0.1/24 dev eth1
        - ip addr add 192.168.0.1/24 dev eth2

    # 🔀 Switch (Bridge)
    switch1:
      kind: bridge

    # 🖥️ Segundo Host
    host2:
      kind: linux
      image: ubuntu:22.04
      exec:
        - ip addr add 192.168.0.10/24 dev eth1
        - ip route add default via 192.168.0.1

  links:
    - endpoints: ["host1:eth1", "switch1:eth1"]
    - endpoints: ["router1:eth1", "switch1:eth2"]
    - endpoints: ["router1:eth2", "host2:eth1"]
```

## 🔍 Explicação 
| Tipo de Nó | Função | Comandos Importantes |
| --- | --- | --- |
| **Host** | Simula um computador na rede. | ``ip ``addr ``add``, ``ip ``route ``add`` |
| **Roteador** | Encaminha pacotes entre redes diferentes. | ``sysctl ``-w ``net.ipv4.ip_forward=1`` |
| **Switch (Bridge)** | Conecta vários nós na mesma rede local. | ``kind: ``bridge`` | 

## 🚀 Passos para Execução

1. Criar a bridge no host (se necessário):
```
bash
sudo ip link add name switch1 type bridge
sudo ip link set switch1 up
```
2. Deploy da topologia:
```
sudo containerlab deploy -t lab.clab.yml
```   
3. Verificar containers ativos:
```
docker ps
```
4. Testar conectividade:

```
docker exec -it clab-rede-lab-host1 ping 10.0.0.1
docker exec -it clab-rede-lab-host2 ping 192.168.0.1
```   

🧠 Dica 
- Cada nó é um Linux isolado, com suas próprias interfaces e rotas.
- O switch é uma bridge virtual criada no host, e o roteador é apenas um container com o encaminhamento de pacotes ativado.
- Usar tcpdump ou Wireshark para observar o tráfego entre os nós.
