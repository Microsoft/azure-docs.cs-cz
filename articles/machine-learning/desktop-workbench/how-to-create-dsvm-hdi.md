---
title: Jak vytvořit DSVM a HDI podle cílových výpočetních prostředí pro Azure ML
description: Vytvoření clusteru DSVM a HDI Spark jako cílových výpočetních prostředí pro experimentování ve službě Azure ML.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 211f60b9c25b4bd20769f6a4840afaecf8373b9f
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782341"
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Vytvoření clusteru DSVM a HDI Spark jako cílových výpočetních prostředí

Můžete snadno vertikálně navýšit kapacitu nebo horizontální navýšení kapacity experimentu s Machine learningem tak, že přidáte další výpočetní cíle jako je založený na Ubuntu DSVM (virtuální počítač datové vědy) a Apache Spark pro cluster Azure HDInsight. Tento článek procházení vás provede kroky k vytvoření těchto cílů v Azure compute. Další informace o Azure ML cílových výpočetních prostředí najdete [Přehled služby experimentování ve službě Azure Machine Learning](experimentation-service-configuration.md).

>[!NOTE]
>Je nutné se ujistit, máte příslušná oprávnění k vytváření prostředků, jako je například virtuální počítač a HDI clustery v Azure, než budete pokračovat. Také oba tyto prostředky můžou využívat mnoho výpočetních jader, v závislosti na vaší konfiguraci. Ujistěte se, že vaše předplatné má dostatečnou kapacitu pro virtuálních jader procesoru. Kontaktovat podporu Azure můžete získat vždy zvýšit maximální počet jader, které jsou povoleny v rámci vašeho předplatného.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Vytvoření DSVM Ubuntu na webu Azure portal

Můžete vytvořit DSVM z webu Azure portal. 

1. Přihlaste se k webu Azure portal https://portal.azure.com
2. Klikněte na **+ nová** odkaz a vyhledejte "virtuální počítač pro datové vědy pro Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Zvolte **virtuální počítač pro datové vědy pro Linux (Ubuntu)** v seznamu a použijte na obrazovce pokyny k vytvoření datové VĚDY.

>[!IMPORTANT]
>Ujistěte se, že zvolíte **heslo** jako _typ ověřování_.

![použít pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Vytvoření DSVM Ubuntu pomocí azure cli

Šablony Azure resource management můžete použít také k nasazení DSVM.

>[!NOTE]
>Všechny tyto příkazy se předpokládá, že z kořenové složky projektu aplikace Azure ML něhož budou vydány.

Nejprve vytvořte `mydsvm.json` soubor pomocí ve svém oblíbeném textovém editoru `docs` složky. (Pokud nemáte k dispozici `docs` vytvoření složky v kořenové složce projektu.) Tento soubor použít ke konfiguraci některých základních parametrů pro šablony Azure resource management. 

Zkopírujte a vložte následující fragment kódu JSON do `mydsvm.json` souboru a vyplňte příslušné hodnoty:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Pro _vmSize_ pole, můžete použít jakoukoli velikost virtuálního počítače nepodporuje uvedené v [šablony správy prostředků Ubuntu DSVM Azure](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Doporučujeme, abyste použili některou z níže velikosti podle cílových výpočetních prostředí pro Azure ML. 


>[!TIP]
> Pro [hloubkové strojové učení](how-to-use-gpu.md) můžete nasadit do GPU s využitím virtuálních počítačů.

- [Virtuální počítače pro obecné účely](../../virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [GPU s využitím virtuálních počítačů](../../virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

Další informace o těchto [velikostí pro virtuální počítače s Linuxem v Azure](../../virtual-machines/linux/sizes.md) a jejich [informace o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Otevřete okno příkazového řádku z aplikace Azure ML Workbench kliknutím na **souboru** --> **otevřít příkazový řádek**, nebo **otevřete PowerShell** položky nabídky. 

>[!NOTE]
>Můžete to provést také v jakémkoli příkazového řádku prostředí, kde máte nainstalován az-cli.

V okně příkazového řádku, zadejte následujících příkazů:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Připojit cílové výpočetní prostředí DSVM
Po vytvoření datové VĚDY je nyní můžete připojit ho do projektu Azure ML.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Teď byste měli být připravení spouštět experimenty v tomto DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Zrušit přidělení DSVM a počítač restartovat později
Po dokončení výpočetní úlohy v Azure ML, můžete navrátit datové VĚDY. Tato akce vypne virtuální počítač uvolnit výpočetní prostředky, ale zachová virtuálních disků. Se vám neúčtovaly výpočetní náklady při zrušení přidělení virtuálního počítače.

K uvolnění virtuálního počítače:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Chcete-li Vdechněte život virtuálního počítače, použijte `az ml start` příkaz:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Rozšíření disku operačního systému DSVM
Datové VĚDY se systémem Ubuntu obsahuje disk s operačním systémem 50GB a 100GB datový disk. Docker ukládá obrázky na datový disk jako více místa je k dispozici. Když se použije jako cílové výpočetní prostředí pro Azure ML, tento disk je možné modul Docker potažením dolů imagí Dockeru a vytváření conda vrstvy dojde k jeho zvýraznění. Můžete potřebovat vyhnout chybě "disk je plný", když jste uprostřed spuštění rozšíření disku na větší velikost (například 200 GB). Referenční dokumentace [způsob, jak rozbalit virtuální pevné disky na virtuální počítač s Linuxem pomocí Azure CLI](../../virtual-machines/linux/expand-disks.md) se dozvíte, jak to snadno provést z příkazového řádku azure. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Na webu Azure portal vytvořit Apache Spark pro cluster Azure HDInsight

Ke spuštění úlohy Spark horizontální navýšení kapacity, je potřeba vytvořit Apache Spark pro cluster Azure HDInsight na webu Azure portal.

1. Přihlaste se k webu Azure portal https://portal.azure.com
2. Klikněte na **+ nová** odkaz a vyhledejte "HDInsight".

    ![Najít hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Zvolte **HDInsight** v seznamu a potom kliknout na **vytvořit** tlačítko.
4. V **Základy** obrazovce konfigurace **typ clusteru** nastavení, ujistěte se, že zvolíte **Spark** jako _typ clusteru_, **Linux** jako _operační systém_, a **Spark 2.1.0 (HDI 3.6)** jako _verze.

    ![Konfigurace hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Všimněte si, že na obrazovce výše má cluster _uživatelské jméno přihlášení clusteru_ pole a _uživatelské jméno Secure Shell (SSH)_ pole. Toto jsou dvě různé uživatelské identity, i když pro usnadnění práce můžete zadat stejné heslo pro obě přihlášení. _Uživatelské jméno přihlášení clusteru_ se používá k přihlášení k management webové uživatelské rozhraní clusteru HDI. _Přihlašovací uživatelské jméno SSH_ se používá k přihlášení k hlavnímu uzlu clusteru, a je to, co je potřeba pro Azure ML k odeslání Sparkových úloh.

5. Zvolte velikost clusteru a velikost uzlu, které potřebujete a dokončete Průvodce vytvořením. Může trvat až 30 minut na dokončení zřízení clusteru. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Připojení cílové výpočetní prostředí clusteru Hdinsight Spark

Po vytvoření clusteru Hdinsight Spark, můžete ji teď připojit do projektu Azure ML.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Teď byste měli být připravení spouštět experimenty v tomto clusteru Spark.

## <a name="next-steps"></a>Další postup

Další informace:
- [Přehled služby experimentování ve službě Azure Machine Learning](experimentation-service-configuration.md)
- [Soubory služby experimentování ve službě konfigurace služby Azure Machine Learning Workbench](experimentation-service-configuration-reference.md)
- [Apache Spark pro cluster Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Virtuální počítač pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
