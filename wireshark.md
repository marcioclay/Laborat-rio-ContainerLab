## 🐳 Roteiro Didático: Wireshark + Containerlab + VS Code

1. Instalação do Wireshark
No Host Linux/WSL
```
bash
sudo apt-get update
sudo apt-get install -y wireshark
```
Durante a instalação, escolha Yes quando perguntar se usuários comuns podem capturar pacotes.

Verifique:
```
bash
wireshark --version
```
👉 Assim você terá o Wireshark disponível no host, podendo capturar tráfego da bridge br1.

2. Integração com VS Code
Abra o terminal integrado do VS Code.

Rode:
```
bash
wireshark &
```
Se estiver em WSL2, o Wireshark abre na interface gráfica do Windows.

3. Preparar a Topologia no Containerlab
Arquivo lab.clab.yml:
```
yaml
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

    node-c:
      kind: linux
      image: nicolaka/netshoot:latest
      exec:
        - ip addr add 10.0.0.3/24 dev eth1

    br1:
      kind: bridge

  links:
    - endpoints: ["node-a:eth1", "br1:eth1"]
    - endpoints: ["node-b:eth1", "br1:eth2"]
    - endpoints: ["node-c:eth1", "br1:eth3"]
```
Deploy:
```
bash
sudo containerlab deploy -t lab.clab.yml
```

4. Testar Conectividade
Entre no node-a:
```
bash
docker exec -it clab-ebpf-lab-node-a bash
ping 10.0.0.2   # ping para node-b
ping 10.0.0.3   # ping para node-c
```
👉 Isso gera tráfego ICMP que você vai capturar.

5. Capturar Pacotes com Wireshark
Abra o Wireshark no host.

Na tela inicial, selecione a interface br1 (se não aparecer, verifique se a bridge foi criada com ip link add name br1 type bridge).

Inicie a captura.

👉 Cabeçalho IP e ICMP, origem, destino, TTL, checksum.

6. Alternativa com Tcpdump
Se não quiser abrir GUI:
```
bash
docker exec -it clab-ebpf-lab-node-a tcpdump -i eth1
```
👉 Isso mostra pacotes diretamente no terminal.

🎯 Demonstração Didática
- Ping entre os nós → confirmar conectividade.
- Wireshark na bridge → visualizar pacotes ICMP.
- Aplicar programa eBPF no node-b (xdp_drop.o) → mostrar que os pacotes deixam de aparecer.
- Discussão em sala → diferença entre switch (bridge) e roteador, e como o eBPF pode filtrar tráfego.
