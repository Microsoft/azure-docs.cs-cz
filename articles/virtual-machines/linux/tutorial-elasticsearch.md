---
title: Nasazení Elasticsearch na vývojový virtuální počítač v Azure
description: Kurz – Instalace řešení Elastic Stack na vývojový virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: eeb1b8b9105e055339cb31fa4b9d4b411cb06c54
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Instalace řešení Elastic Stack na virtuální počítač Azure

Tento článek vás provede nasazením [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) a [Kibana](https://www.elastic.co/products/kibana) na virtuální počítač s Ubuntu v Azure. Pokud chcete vidět Elastic Stack v akci, můžete se volitelně připojit ke Kibana a pracovat s ukázkovými daty protokolování. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu ve skupině prostředků Azure
> * Instalace Elasticsearch, Logstash a Kibana na virtuální počítač
> * Odeslání ukázkových dat do Elasticsearch pomocí Logstash 
> * Otevření portů a práce s daty v konzole Kibana


 Toto nasazení je vhodné pro základní vývoj s řešením Elastic Stack. Další informace o řešení Elastic Stack, včetně doporučení pro produkční prostředí, najdete v [dokumentaci Elastic](https://www.elastic.co/guide/index.html) a v [Azure Architecture Center](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). 

Následující příklad vytvoří virtuální počítač *myVM*, a pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, vytvoří je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači

Pokud ještě neznáte veřejnou IP adresu svého virtuálního počítače, spusťte příkaz [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list):

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Použijte správnou veřejnou IP adresu vašeho virtuálního počítače. V tomto příkladu je IP adresa *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Instalace řešení Elastic Stack

Importujte podpisový klíč Elasticsearch a aktualizujte svůj seznam zdrojů APT tak, aby zahrnoval úložiště balíčků Elastic:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Nainstalujte na virtuální počítač prostředí Java Virtual Machine a nakonfigurujte proměnnou JAVA_HOME, která je nezbytná pro spuštění komponent řešení Elastic Stack.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Spuštěním následujících příkazů aktualizujte zdroje balíčků Ubuntu a nainstalujte Elasticsearch, Kibana a Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Podrobné pokyny k instalaci, včetně rozložení adresářů a počáteční konfigurace, se udržují v [dokumentaci Elastic](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html).

## <a name="start-elasticsearch"></a>Spuštění Elasticsearch 

Spusťte na svém virtuálním počítači Elasticsearch pomocí následujícího příkazu:

```bash
sudo systemctl start elasticsearch.service
```

Tento příkaz negeneruje žádný výstup, proto ověřte spuštění Elasticsearch na virtuálním počítači pomocí tohoto příkazu `curl`:

```bash
sudo curl -XGET 'localhost:9200/'
```

Pokud je Elasticsearch spuštěný, zobrazí se výstup podobný tomuto:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Spuštění Logstash a přidání dat do Elasticsearch

Spusťte Logstash pomocí následujícího příkazu:

```bash 
sudo systemctl start logstash.service
```

Otestujte Logstash v interaktivním režimu, abyste se ujistili, že funguje správně:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Toto je základní [kanál](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) Logstash, který vypisuje standardní vstup na standardním výstupu. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Nastavte Logstash tak, aby směroval zprávy jádra z tohoto virtuálního počítače do Elasticsearch. V prázdném adresáři vytvořte nový soubor `vm-syslog-logstash.conf` a vložte do něj následující konfiguraci Logstash:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Otestujte konfiguraci a odešlete do Elasticsearch data Syslogu:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

V terminálu se budou vypisovat položky Syslogu odesílané do Elasticsearch. Jakmile odešlete nějaká data, pomocí `CTRL+C` ukončete Logstash.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Spuštění Kibana a vizualizace dat v Elasticsearch

Upravte soubor `/etc/kibana/kibana.yml` a změňte IP adresu, na které Kibana naslouchá, abyste k ní měli přístup z webového prohlížeče.

```bash
server.host:"0.0.0.0"
```

Spusťte Kibana pomocí následujícího příkazu:

```bash
sudo systemctl start kibana.service
```

Otevřením portu 5601 z Azure CLI povolte vzdálený přístup ke konzole Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Otevřete konzolu Kibana a vyberte **Create** (Vytvořit). Vygeneruje se výchozí index založený na datech Syslogu, která jste dříve odeslali do Elasticsearch. 

![Proházení událostí Syslogu v Kibana](media/elasticsearch-install/kibana-index.png)

V konzole Kibana vyberte **Discover** (Vyhledat), abyste mohli prohledávat, procházet a filtrovat události Syslogu.

![Proházení událostí Syslogu v Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste na vývojový virtuální počítač v Azure nasadili řešení Elastic Stack. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu ve skupině prostředků Azure
> * Instalace Elasticsearch, Logstash a Kibana na virtuální počítač
> * Odeslání ukázkových dat do Elasticsearch z Logstash 
> * Otevření portů a práce s daty v konzole Kibana
