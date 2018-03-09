---
title: "Postup vytvoření DSVM a HDI jako výpočetní cíle pro Azure ML"
description: "Vytvoření clusteru DSVM a HDI Spark jako výpočetní cíle pro Azure ML experimenty."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 15cdee0fb3994874c88b16bebec35f5eae9f8de2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Vytvoření clusteru DSVM a HDI Spark jako výpočetní cíle

Můžete snadno škálovat nahoru i horizontální navýšení kapacity experimentu machine learning přidáním další výpočetní cílů například na základě Ubuntu DSVM (datové vědy virtuálního počítače) a Apache Spark pro cluster Azure HDInsight. Tento článek nevystavíte slabé stránky zabezpečení vás provede kroky k vytvoření těchto výpočetní cílů v Azure. Další informace o Azure ML výpočetní cíle, najdete v části [Přehled služby Azure Machine Learning experimentování](experimentation-service-configuration.md).

>[!NOTE]
>Je potřeba zajistit, máte příslušná oprávnění k vytvoření prostředkům, například virtuálních počítačů a HDI clustery v Azure, než budete pokračovat. Obě tyto prostředky také spotřebovat mnoho výpočetních jader v závislosti na konfiguraci. Ujistěte se, že vaše předplatné má dostatečnou kapacitu pro virtuální jader procesoru. Ve spojení se podpory Azure může vždy získat zvýšit maximální počet jader, které jsou povoleny v rámci vašeho předplatného.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Vytvoření DSVM Ubuntu na portálu Azure

Můžete vytvořit DSVM z portálu Azure. 

1. Přihlaste se k portálu Azure z https://portal.azure.com
2. Klikněte na **+ nový** odkaz a vyhledejte "datové vědy virtuálního počítače pro Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Zvolte **datové vědy virtuálního počítače pro Linux (Ubuntu)** v seznamu a postupujte podle kroků na obrazovce pokyny pro vytvoření DSVM.

>[!IMPORTANT]
>Zajistěte, aby si zvolíte **heslo** jako _typ ověřování_.

![použít pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Vytvoření DSVM Ubuntu pomocí rozhraní příkazového řádku azure

Také můžete šablonu správu prostředků Azure k nasazení DSVM.

>[!NOTE]
>Všechny následující příkazy se předpokládá, že být vydaný v kořenové složce projektu Azure ML.

Nejprve vytvořte `mydsvm.json` pomocí svém oblíbeném textovém editoru v souboru `docs` složky. (Pokud nemáte `docs` vytvořit složky v kořenové složce projektu.) Tento soubor použít ke konfiguraci některých základních parametrů šablony správu prostředků Azure. 

Zkopírujte a vložte následující fragment kódu JSON do `mydsvm.json` souboru a zadejte příslušné hodnoty:

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

Pro _vmSize_ pole, můžete použít libovolnou velikost virtuálního počítače suppported uvedené v [šablony správu prostředků Ubuntu DSVM Azure](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Doporučujeme použít jednu z níže velikosti jako výpočetní cíle pro Azure ML. 


>[!TIP]
> Pro [hloubkové učení úlohy](how-to-use-gpu.md) můžete nasadit do GPU používá technologii virtuálních počítačů.

- [Virtuální počítače pro obecné účely](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [Grafický procesor používá technologii virtuálních počítačů](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

Další informace o těchto [velikostí pro virtuální počítače s Linuxem v Azure](../../virtual-machines/linux/sizes.md) a jejich [informace o cenách](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Spusťte okno příkazového řádku z aplikace Azure ML Workbench kliknutím na **soubor** --> **spusťte příkazový řádek**, nebo **otevřete PowerShell** položku nabídky. 

>[!NOTE]
>Můžete to provést taky v jakémkoli příkazového řádku prostředí, kde máte az rozhraní příkazového řádku nainstalována.

V okně příkazového řádku, zadejte následující příkazy:

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
## <a name="attach-a-dsvm-compute-target"></a>Připojte cíl výpočetní DSVM
Po vytvoření DSVM nyní můžete připojit se k projektu Azure ML.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Nyní byste měli být připraven ke spuštění na tento DSVM experimenty.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Navrácení DSVM a později ji znovu spustit
Po dokončení úlohy výpočetní z Azure ML, můžete zrušit přidělení DSVM. Tato akce vypne virtuální počítač, uvolnit výpočetní prostředky, ale ponechá virtuální disky. Výpočet nákladů na vám není účtován, když je virtuální počítač navrácený.

Chcete-li zrušit přidělení virtuálního počítače:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Chcete-li Oživte virtuální počítač zpět, použijte `az ml start` příkaz:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Rozbalte disk operačního systému DSVM
Virtuální počítač s Linuxem v Azure obvykle dodává s diskem operačního systému 30 GB. Pokud se použije jako výpočetní cíl pro Azure ML, ho můžete konzumaci rychle Docker modul stahování dolů imagí Dockeru a sestavování conda vrstvy nad ho. Je vhodné k tomu chyba "disk plný", jsou právě spuštění rozšíření disk operačního systému na větší velikost (jako je například 200 GB). Referenční dokumentace [způsob, jak rozbalit virtuální pevné disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](../../virtual-machines/linux/expand-disks.md) se dozvíte, jak k tomu snadno z příkazového řádku azure. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Vytvoření serveru Apache Spark pro cluster Azure HDInsight na portálu Azure

Ke spuštění úloh Spark Škálováním na více systémů, musíte vytvořit Apache Spark pro cluster Azure HDInsight na portálu Azure.

1. Přihlaste se k portálu Azure z https://portal.azure.com
2. Klikněte na **+ nový** odkaz a vyhledejte "HDInsight".

    ![Najít hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Zvolte **HDInsight** v seznamu a klikněte na **vytvořit** tlačítko.
4. V **Základy** konfigurační obrazovce **clusteru typ** nastavení, ujistěte se, zvolíte **Spark** jako _clusteru typu_, **Linux** jako _operačního systému_, a **Spark 2.1.0 (HDI 3.6)** jako _Version.

    ![Konfigurace hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Oznámení na obrazovce výše má cluster _uživatelské jméno přihlášení clusteru_ pole a _uživatelské jméno Secure Shell (SSH)_ pole. Toto jsou dvě identity jiného uživatele, i když pro usnadnění práce můžete zadat stejné heslo pro obě přihlášení. _Uživatelské jméno přihlášení clusteru_ se používá k přihlášení do správy webového uživatelského rozhraní clusteru HDI. _Uživatelské jméno přihlášení SSH_ se používá k přihlášení k hlavnímu uzlu clusteru, a to je, co je potřeba pro Azure ML odesláním úloh Spark.

5. Zvolte velikost clusteru a velikost uzlu potřebovat a dokončete Průvodce vytvořením. To může trvat až 30 minut na dokončení zřizování clusteru. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Připojení cílové výpočetní cluster HDI Spark

Po vytvoření clusteru Spark HDI nyní můžete připojit se k projektu Azure ML.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Nyní byste měli být připravení spustit experimenty na tomto clusteru Spark.

## <a name="next-steps"></a>Další postup

Další informace o:
- [Přehled služby Azure Machine Learning experimentování](experimentation-service-configuration.md)
- [Azure Machine Learning Workbench experimentování služby konfigurační soubory](experimentation-service-configuration-reference.md)
- [Apache Spark pro cluster Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Datové vědy virtuálního počítače](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
