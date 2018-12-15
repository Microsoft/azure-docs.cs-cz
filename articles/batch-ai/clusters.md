---
title: Fungují s clustery Azure Batch AI | Dokumentace Microsoftu
description: Jak vybrat konfiguraci clusteru Batch AI a vytvářet a spravovat cluster AI
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 9af8ce84805e48dd3c91dd7fb4fcf0b136fbfc60
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410087"
---
# <a name="work-with-batch-ai-clusters"></a>Fungují s clustery Batch AI 

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Tento článek vysvětluje, jak fungují s clustery v Azure Batch AI. Zavádí koncepci clustery, typy konfigurace, které jsou možné a příklady. Většina příkladů můžete vytvářet a spravovat cluster v tomto článku pomocí Azure CLI. Můžete však použít jiné nástroje, včetně webu Azure portal a se sadami SDK služby Azure Batch AI pro práci s clustery.

Cluster Batch AI je jedním z několika zdrojů ve službě. Zobrazit [Přehled prostředků služby Batch AI](resource-concepts.md) k pochopit tak rozsah clusterů ve službě.

## <a name="introduction-to-clusters"></a>Úvod do clusterů

Cluster v Batch AI obsahuje výpočetních prostředků pro spouštění machine learning a úlohy trénování umělé Inteligence. Všechny uzly v clusteru mají stejnou velikost virtuálního počítače a image operačního systému. Batch AI nabízí celou řadu možností pro vytváření clusterů, které jsou přizpůsobené pro různé potřeby. Obvykle je nastavit jiný cluster pro každou kategorii výpočetní výkon potřebný k dokončení projektu. Můžete škálovat počet uzlů v clusteru nahoru a dolů podle potřeby a rozpočet. Clustery se dají zřizovat a sdílen týmem nebo každého jednotlivce můžete zřídit vlastní cluster. 

Každý cluster existuje v rámci služby Batch AI prostředek s názvem *pracovní prostor*. Před zřizováním jakéhokoli jiného clusteru, musí mít nastavení pracovního prostoru služby Batch AI. Například pokud používáte Azure CLI, použijte [vytvořit pracovní prostor az batchai](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) příkaz pro nastavení pracovního prostoru. 

Po vytvoření clusteru mohou odesílat úlohy jeden po druhém do fronty. Batch AI zpracovává pak proces přidělování prostředků pro spouštění úloh na clusteru. 

## <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Při plánování cluster, nejprve určete vaše požadavky na výpočetní výkon. Batch AI nabízí možnosti konfigurace na flexibilní, takže clusteru můžete přizpůsobit vašim potřebám. Následují možnosti majors vzít v úvahu při zřizování clusteru:

* **Velikost virtuálního počítače** -vybrat kteroukoli [velikost virtuálního počítače](../virtual-machines/linux/sizes.md) , který je k dispozici v [podporované oblasti](https://azure.microsoft.com/global-infrastructure/services/) pro uzly clusteru. Každý cluster může obsahovat pouze jednu velikost virtuálního počítače, takže pokud vaše úlohy vyžadují více typů virtuálních počítačů, budete muset zřizovat samostatný cluster pro každý typ požadavku výpočetní. Trénování machine learning nebo modely AI se využily výsledky mnohaletých architektur, které využívají grafické procesory, najdete v článku [GPU optimalizované velikosti virtuálních počítačů](../virtual-machines/linux/sizes-gpu.md) v Azure.
  
* **Virtuální počítač prioritu** – Batch AI nabízí vyhrazený nebo s nízkou prioritou virtuálních počítačů pro cluster. Vyhrazených virtuálních počítačů jsou vyhrazené pro použití v clusteru. Možnost s nízkou prioritou přiděluje nevyužitou kapacitu virtuálních počítačů Azure na výrazné úspory nákladů výměnou za možnost úloh, přičemž dojde ke zrušení Pokud uvolňuje virtuální počítače Azure. Úlohy, které běží po dobu delší než 24 hodin na virtuálním počítači s nízkou prioritou jsou také automaticky dojde ke zrušení. Pokud rozpočtu je důležitý, zvažte použití virtuálních počítačů s nízkou prioritou úloh krátkého experimentování. Pak přepnout na vyhrazených virtuálních počítačích až nastane čas ke spuštění úloh delší dobu.

* **Počet uzlů** – Batch AI nabízí možnosti pro ruční a automatické škálování pro počet uzlů v clusteru. S ruční variantu můžete řídit při škálování clusteru nahoru a dolů tak můžete spravovat své vlastní výpočetní náklady. Možnost automatického škálování zajišťuje clusteru vždy downscales když ji nepoužíváte, abyste minimalizovali náklady na výpočetní prostředky. 

  Pokud zvolíte ruční škálování clusteru, pak nadefinujte počáteční cílem při vytváření clusteru. Cílem je počet uzlů, které původně přiděluje služby Batch AI. Později můžete ručně změnit velikost z počtu uzlů.  

  Pokud zvolíte možnost automatického škálování, můžete definovat maximální a minimální počet cílových uzlů během vytváření clusteru. Cíl může být v rozsahu od 0 do maximální počet uzlů, které podporuje vaše [kvóta pro jádra služby Batch AI](quota-limits.md). Cíl 0 umožňuje spravovat vaši konfiguraci clusteru bez za všechny náklady na výpočetní výkon. Pokud jste požádali o cíl vyšší než vaše podporuje limit kvóty, se zřizování nezdaří. 

* **Image virtuálního počítače** – Batch AI ve výchozím nastavení zřizuje cluster virtuálních počítačů s výchozí image Ubuntu Server, který podporuje úlohy kontejneru. Můžete použít jiné předkonfigurovanou image Linuxu na Azure Marketplace, jako [virtuální počítač pro datové vědy](../machine-learning/data-science-virtual-machine/overview.md). 

* **Úložiště** – Batch AI poskytuje možnost automatického úložiště, kterou můžete nastavit při vytváření clusteru pomocí rozhraní příkazového řádku Azure. Tato možnost automaticky vytvoří kontejner sdílených složek a objektů Blob v Azure file v rámci nového účtu úložiště. Tyto prostředky úložiště jsou připojené k jednotlivým uzlům v clusteru během doby spuštění, což přístup z místní cesty k souborům. Názvy účtů úložiště, název sdílené složky, název kontejneru objektů Blob a přípojný cest všechny mají výchozí hodnoty, které se taky dají upravit pomocí dalších parametrů při vytváření clusteru. 

  Pokud jsou definovány žádné možnosti úložiště, budete muset samostatně vytvoření prostředků úložiště a jejich definování při odesílání úlohy. Tato možnost je užitečná, pokud váš cluster se spravuje na úrovni organizace, ale úložiště se spravuje na úrovni uživatele. Další informace o tom, jak nahrávat soubory do služby Azure Storage a přistupovat k nim při spuštění najdete v tématu [úlohy služby Batch AI Store vstupní a výstupní s Azure Storage](use-azure-storage.md).

* **Přístup uživatelů** – Batch AI vám umožní generovat veřejné a privátní SSH soubory klíče při vytváření clusteru, nebo zadat vlastní klíče SSH, abyste mohli SSH do jednotlivých uzlů. Můžete také definovat (ve výchozím nastavení jako aktuální uživatel) uživatelské jméno a heslo. Tyto přihlašovací údaje slouží pro přístup k uzlům během provádění, aby bylo možné zobrazit různé metriky nebo získáte tak další poznatky pro vaše úlohy.

* **Úloha instalace** – Batch AI můžete zadat argumenty příkazového řádku, který se spustí na každém uzlu po přidělení. Můžete také definovat cesta kde se mají protokolovat výstupní soubor pro. Tuto možnost použijte, pokud máte další kroky zřizování nad základní image.

* **Další konfigurace** – existuje několik méně běžné scénáře, které by mohly vyžadovat více specializované konfigurace. V tomto případě [clusteru konfigurační soubor](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) lze připojit pomocí příkazu Azure CLI vytvořit cluster. 

## <a name="create-the-cluster"></a>Vytvoření clusteru

Poté, co jste se rozhodli možnosti konfigurace clusteru, použijte rozhraní příkazového řádku Azure, webu Azure portal nebo rozhraní API služby Batch AI k vytvoření clusteru. Například k vytvoření clusteru pomocí rozhraní příkazového řádku Azure, můžete postupovat podle [az batchai cluster vytvořit](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) dokumentaci k vytvoření přesnou příkaz, který obsahuje konfigurace, které potřebujete. 

Na nejzákladnější konfigurace, následující příkaz ustanovení Standard_NC6 clusteru s jedním uzlem a přístup přes SSH. Ve výchozím nastavení obsahuje tento cluster vyhrazené virtuální počítače s nejnovější výchozí image Ubuntu Server.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

Následující příklad ustanovení Standard_NC6 clusteru, který automaticky škáluje od 0 do 4 uzly a uzly s nízkou prioritou a účet úložiště automaticky používá. Toto nastavení je to dobrá konfigurace Pokud budete potřebovat cluster, který je s nízkými náklady a snadnou správou. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

Následující příklad zřídí Standard_NC6 clusteru, který obsahuje bitovou kopii virtuálního počítače pro datové vědy, vlastního úložiště a připojování možnosti, vlastní přihlašovací údaje SSH, instalační program úlohu, která nainstaluje *rozbalte* balíčku a clusteru konfigurační soubor pro další nastavení. Tato konfigurace je příklad clusteru, který je více přizpůsobené pro vaše konkrétní potřeby.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>Monitorování clusteru

[Az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show), a [az batchai cluster uzel seznamu](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) příkazy je možné monitorovat různé informace o každém clusteru.

### <a name="list-all-clusters"></a>Seznam všech clusterů

Následující alls příkaz seznam clusterů v pracovním prostoru.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>Zobrazit informace o clusteru

Následující příkaz zobrazí úplné informace o konkrétní cluster ve formátu tabulky.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Pokud váš cluster bylo zřízeno, pomocí možnosti automatického úložiště, budete chtít načíst název účtu úložiště pro použití při nahrávání skripty a úlohy trénování. Použijte následující příkaz:

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

Výstup by měl vypadat přibližně takto.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Seznam uzlů clusteru

Pokud potřebujete připojení k uzlům clusteru, následující příkaz načte seznam uzlů a informace o připojení.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

Výstup pro každý uzel bude podobný následujícímu:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
Tyto informace můžete použít k vytvoření připojení SSH k uzlu pomocí příkazu, který je podobný následujícímu.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Odeslání úloh do clusteru

Po zřízení clusteru, pak můžete odeslat úlohy pro spuštění na uzlech. Najdete v článku [az batchai úlohy](/cli/azure/batchai/job?view=azure-cli-latest) příkaz různé způsoby, jak připravit, odesílání a monitorování úloh pomocí Azure CLI. 

## <a name="downscale-cluster-for-later-use"></a>Downscale clusteru pro pozdější použití.

Jakmile budete hotovi, spouštění úloh, budete chtít snížit vašeho clusteru. Se doporučuje vždy downscale clusterů, když nejsou používány k uložení výpočetní náklady. Downscaling clusteru na 0 uzlů umožňuje ukončit účtování poplatků při nepotřebujete na bázi clustery v budoucnu, když budete chtít upscale znovu. Pokud automatické škálování jste vybrali při vytváření clusteru, by měla minimální cílovou automaticky snížit clusteru. Pokud jste vybrali ruční škálování, snížit cluster pomocí následujícího příkazu.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Odstranění clusteru

Jakmile budete hotovi, použití clusteru, použijte [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) příkaz k jeho odstranění.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

Také automaticky odstranění skupiny prostředků odstraní všechny clustery, které byly zřízeny v příslušné skupině prostředků.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>Další postup

Další příklady vytvoření cluster Batch AI, najdete v článku [portál](quickstart-create-cluster-portal.md) nebo [rozhraní příkazového řádku Azure](quickstart-create-cluster-cli.md) rychlý start, nebo [služby Batch AI recepty](https://github.com/Azure/BatchAI/tree/master/recipes) na Githubu.
