# Guia de Estudos: Gerenciamento de Máquinas Virtuais no Azure (AZ-104)

Repositório criado como projeto final para o Bootcamp **Microsoft Azure AI Fundamentals** da DIO. O objetivo deste guia é documentar os principais conceitos, processos e boas práticas relacionados ao gerenciamento de Máquinas Virtuais (VMs) no Microsoft Azure, servindo como um material de consulta para profissionais e estudantes.

---

## 📖 Índice

1.  [Conceitos Fundamentais de VMs](#1-conceitos-fundamentais-de-vms)
2.  [Criação e Implantação de VMs](#2-criação-e-implantação-de-vms)
3.  [Rede e Conectividade](#3-rede-e-conectividade)
4.  [Armazenamento para VMs](#4-armazenamento-para-vms)
5.  [Gerenciamento e Manutenção](#5-gerenciamento-e-manutenção)
6.  [Disponibilidade e Resiliência](#6-disponibilidade-e-resiliência)
7.  [Backup e Recuperação](#7-backup-e-recuperação)
8.  [Monitoramento e Alertas](#8-monitoramento-e-alertas)
9.  [Comandos Úteis (CLI & PowerShell)](#9-comandos-úteis)

---

### **1. Conceitos Fundamentais de VMs**

-   **O que é uma Máquina Virtual?**
    -   Uma Máquina Virtual (VM) no Azure é um recurso de computação escalável que emula um computador físico. Ela pertence à categoria de **IaaS (Infrastructure as a Service)**, o que significa que o usuário é responsável por gerenciar o sistema operacional, as aplicações e os dados, enquanto a Microsoft gerencia a infraestrutura física subjacente (hardware, rede, etc.).

-   **Imagens (Images):**
    -   **Marketplace:** Imagens pré-configuradas e prontas para uso, fornecidas pela Microsoft e por parceiros. Incluem diversas versões de Windows Server e distribuições Linux (Ubuntu, CentOS, etc.), muitas vezes com softwares já instalados.
    -   **Personalizadas (Custom Images):** Imagens que você mesmo cria a partir de uma VM já configurada. Ideal para padronizar o ambiente e acelerar a criação de novas VMs com as mesmas configurações e softwares.

-   **Tamanhos de VM (VM Sizes):**
    -   O Azure oferece uma vasta gama de tamanhos de VM, otimizados para diferentes cargas de trabalho. As séries mais comuns são:
        -   **Série B (Burstable):** Econômica, ideal para cargas de trabalho que não precisam de desempenho de CPU constante, como servidores de desenvolvimento ou pequenas aplicações web.
        -   **Série D (General Purpose):** Equilíbrio entre CPU, memória e armazenamento. Ótima para a maioria das cargas de trabalho de produção.
        -   **Série E (Memory Optimized):** Alta proporção de memória para CPU. Ideal para bancos de dados em memória, caches e aplicações de BI.
        -   **Série F (Compute Optimized):** Alta proporção de CPU para memória. Ideal para processamento em lote, servidores de jogos e aplicações que exigem alto poder de processamento.

-   **Recursos Associados:**
    -   Ao criar uma VM, outros recursos são criados em conjunto e são essenciais para seu funcionamento:
        -   **Disco de SO (OS Disk):** Disco que armazena o sistema operacional.
        -   **Disco Temporário (Temporary Disk):** Armazenamento local no host físico, não persistente.
        -   **Interface de Rede (NIC):** Permite a comunicação da VM com a VNet.
        -   **Endereço IP Público:** Para acesso via internet.

### **2. Criação e Implantação de VMs**

-   **Via Portal do Azure:**
    -   Método mais visual e intuitivo, ideal para iniciantes. O portal guia o usuário através de um assistente passo a passo, desde a configuração básica até rede, discos e gerenciamento.
    -   ![Criando uma VM através do Portal do Azure](imagens/portal-criacao-vm.png)

-   **Via Azure CLI (Command-Line Interface):**
    -   Ferramenta de linha de comando para gerenciar recursos do Azure. É multiplataforma e ideal para automação.
    -   Exemplo de comando para criar uma VM Linux:
        ```bash
        # Comando para criar uma VM Ubuntu LTS
        az vm create \
          --resource-group "MeuResourceGroup" \
          --name "minhaVM-CLI" \
          --image "UbuntuLTS" \
          --admin-username "azureuser" \
          --generate-ssh-keys \
          --location "EastUS"
        ```

-   **Via PowerShell:**
    -   Linguagem de script da Microsoft, profundamente integrada ao ecossistema Windows e Azure.

### **3. Rede e Conectividade**

-   **Virtual Network (VNet) e Sub-redes:** Uma VNet é uma rede privada e isolada no Azure. As sub-redes permitem segmentar a VNet, organizando e protegendo os recursos. É uma boa prática ter sub-redes separadas para diferentes camadas de uma aplicação (ex: frontend, backend, dados).

-   **Network Security Groups (NSGs):** Funcionam como um firewall no nível da placa de rede (NIC) ou da sub-rede. Eles contêm regras de entrada (Inbound) e saída (Outbound) que permitem ou negam o tráfego com base em IP, porta e protocolo. As regras são processadas por prioridade (números menores têm maior prioridade).

-   **Endereços IP (Público e Privado):**
    -   **Privado:** Usado para comunicação dentro da VNet. É atribuído a partir do intervalo de endereços da sub-rede.
    -   **Público:** Usado para comunicação com a internet. É um recurso separado que pode ser associado à NIC da VM.

-   **Conexão Segura:**
    -   **SSH (Linux) e RDP (Windows):** Protocolos padrão para acesso remoto. Expor as portas 22 (SSH) e 3389 (RDP) diretamente à internet é um grande risco de segurança.
    -   **Azure Bastion:** É um serviço de PaaS que permite conectar-se às VMs de forma segura através do portal do Azure, sem a necessidade de um IP Público na VM. Ele atua como um "jump server" gerenciado pela Microsoft, aumentando significativamente a segurança.

### **4. Armazenamento para VMs**

-   **Tipos de Disco:**
    -   **Standard HDD:** Mais barato, ideal para desenvolvimento, testes e cargas de trabalho não sensíveis à latência.
    -   **Standard SSD:** Bom equilíbrio entre custo e desempenho. Adequado para servidores web e aplicações de baixo a médio uso.
    -   **Premium SSD:** Alto desempenho e baixa latência, com IOPS e throughput garantidos. Ideal para bancos de dados e cargas de trabalho de produção.
    -   **Ultra Disk:** Desempenho máximo, com a possibilidade de ajustar IOPS e throughput dinamicamente. Para cargas de trabalho extremamente exigentes, como SAP HANA.

-   **Discos Gerenciados (Managed Disks):**
    -   Com os Discos Gerenciados, o Azure gerencia automaticamente a conta de armazenamento, a resiliência e a escalabilidade. Eles são o padrão e a prática recomendada. Simplificam o gerenciamento e oferecem melhores SLAs.

-   **Adicionar e Gerenciar Discos de Dados:**
    -   Para adicionar mais espaço de armazenamento a uma VM, você pode criar e anexar discos de dados. O processo é:
        1.  Criar o recurso de Disco Gerenciado no Azure.
        2.  Anexar o disco à VM através das configurações da VM.
        3.  Conectar-se à VM e, dentro do sistema operacional, inicializar, formatar e montar o novo disco.

### **5. Gerenciamento e Manutenção**

-   **Redimensionar uma VM:** É possível alterar o tamanho de uma VM (ex: de D2s_v3 para D4s_v3) para aumentar ou diminuir os recursos de CPU e memória. Esta operação exige uma reinicialização da VM.

-   **Extensões de VM (VM Extensions):** Pequenos aplicativos que fornecem configuração e automação pós-implantação. Permitem instalar softwares, configurar antivírus, executar scripts, etc. A `CustomScriptExtension` é extremamente útil para automatizar a configuração inicial de uma VM.

-   **Capturar uma imagem de VM:**
    -   **Snapshot:** Uma cópia pontual de um disco. Útil para backup rápido antes de uma alteração.
    -   **Imagem:** Um "template" para criar novas VMs. Antes de criar uma imagem, a VM original deve ser "generalizada" para remover informações específicas da máquina (processo conhecido como `sysprep` no Windows).

### **6. Disponibilidade e Resiliência**

-   **SLA (Service Level Agreement):** O SLA para VMs do Azure varia de 99.9% a 99.99% dependendo de como a VM é implantada.
    -   **VM Única com Disco Premium SSD:** 99.9%
    -   **Availability Set:** 99.95%
    -   **Availability Zones:** 99.99%

-   **Availability Sets (Conjuntos de Disponibilidade):**
    -   Protegem contra falhas de hardware **dentro de um mesmo datacenter**. As VMs são distribuídas em diferentes **Fault Domains** (racks com energia e rede independentes) e **Update Domains** (grupos que são reiniciados juntos durante manutenções planejadas).

-   **Availability Zones (Zonas de Disponibilidade):**
    -   Protegem contra a falha de um **datacenter inteiro**. São datacenters fisicamente separados dentro de uma mesma região do Azure, com energia, resfriamento e rede independentes. Implantar VMs em diferentes zonas oferece o mais alto nível de resiliência dentro de uma região.

### **7. Backup e Recuperação**

-   **Azure Backup:** É o serviço nativo do Azure para backup de dados. Para VMs, ele é "agentless" (não exige a instalação de agentes dentro da VM).
-   **Recovery Services Vault (Cofre dos Serviços de Recuperação):** É o recurso central que armazena os backups e gerencia as políticas. Deve ser criado na mesma região das VMs a serem protegidas.
-   **Processo de Backup e Restauração:**
    1.  Crie um Recovery Services Vault.
    2.  Defina uma política de backup (frequência e retenção).
    3.  Associe as VMs a essa política para habilitar o backup.
    4.  Para restaurar, você pode:
        -   Criar uma nova VM a partir do ponto de recuperação.
        -   Restaurar apenas os discos da VM.
        -   Recuperar arquivos individuais diretamente do ponto de recuperação (File-Level Recovery).

### **8. Monitoramento e Alertas**

-   **Métricas (Metrics):** Dados numéricos coletados em intervalos regulares. São ideais para monitoramento de desempenho em tempo real e para criar gráficos. Principais métricas: `Percentage CPU`, `Network In/Out`, `Disk Read/Write`.

-   **Log Analytics:** Ferramenta para armazenar e consultar grandes volumes de dados de log. Utiliza a linguagem de consulta **KQL (Kusto Query Language)** para diagnósticos profundos e análises complexas.

-   **Alertas (Alerts):** Permitem criar regras para ser notificado quando algo acontecer. Uma regra de alerta consiste em:
    -   **Recurso:** O que será monitorado (ex: a VM).
    -   **Condição:** O gatilho (ex: CPU > 90% por 5 minutos).
    -   **Action Group:** O que fazer quando o alerta disparar (ex: enviar um e-mail, SMS, ou chamar uma automação).

### **9. Comandos Úteis**

#### **Azure CLI**
```bash
# Listar todas as VMs em um grupo de recursos
az vm list -g MeuResourceGroup -o table

# Iniciar uma VM
az vm start -g MeuResourceGroup -n minhaVM

# Parar (e desalocar, para economizar custos) uma VM
az vm deallocate -g MeuResourceGroup -n minhaVM

# Obter o endereço IP de uma VM
az vm show -d -g MeuResourceGroup -n minhaVM --query publicIps -o tsv
```

#### **PowerShell**
```powershell
# Obter informações de uma VM específica
Get-AzVM -ResourceGroupName MeuResourceGroup -Name "minhaVM"

# Iniciar uma VM
Start-AzVM -ResourceGroupName MeuResourceGroup -Name "minhaVM"

# Parar (e desalocar) uma VM
Stop-AzVM -ResourceGroupName MeuResourceGroup -Name "minhaVM" -Force

# Listar todas as VMs em uma subscrição
Get-AzVM
```

---

## 👨‍💻 Autor

**Gabriel R.C.**
