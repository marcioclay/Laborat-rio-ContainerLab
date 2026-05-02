# Estrutura do laboratório

📘 Infraestrutura do Containerlab
## 1. Camada de Host (Linux)
O sistema operacional hospedeiro (Linux, WSL2 no seu caso) fornece os recursos de rede e kernel.

É nele que você cria bridges (br1), interfaces virtuais e onde o Docker está instalado.

O Containerlab usa diretamente o kernel Linux para manipular namespaces de rede, veth pairs e bridges.

## 2. Camada de Docker
O Docker é o motor que cria e gerencia os containers.

Cada nó definido no YAML (node-a, node-b, etc.) vira um container Docker.

O Containerlab não substitui o Docker, ele orquestra o Docker para montar topologias de rede complexas.

Exemplo: node-a → container Docker com imagem netshoot ou ubuntu-wireshark.

## 3. Camada do Containerlab
O Containerlab é a “cola” que junta tudo:

Lê o arquivo YAML (lab.clab.yml).

Cria os containers no Docker.

Configura as interfaces de rede (veth pairs).

Conecta os containers às bridges (como br1).

Gera inventários (Ansible, Nornir) e metadados para automação.

👉 Ele transforma uma descrição declarativa (YAML) em uma topologia de rede funcional.

## 4. Camada dos Containers (nós)
Cada container é um Linux isolado.

Você pode instalar programas (Wireshark, MQTT, compiladores) se usar imagens customizadas.

Eles se comunicam entre si pelas interfaces configuradas pelo Containerlab.

Exemplo: node-a com Wireshark para capturar pacotes, node-b com MQTT broker, node-c com programas eBPF.

🎯 Metáfora 
Pense assim:

Host Linux → o “chão” da sala de aula.

Docker → as carteiras (containers) onde os alunos sentam.

Containerlab → o professor que organiza a sala, decide quem senta onde e quem conversa com quem.

Containers (nós) → os alunos, cada um com seu papel (um fala MQTT, outro captura pacotes, outro roda eBPF).

Bridge → o quadro branco central, onde todos podem interagir.
