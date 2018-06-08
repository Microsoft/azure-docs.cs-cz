---
title: Klasifikace leteckou Image | Microsoft Docs
description: Poskytuje pokyny pro scénář skutečných na klasifikaci leteckou bitové kopie
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
ms.component: desktop-workbench
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: d34f25fd75816f0ae840b3cbb2e0e88cbc2bfd91
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832403"
---
# <a name="aerial-image-classification"></a>Klasifikace leteckou bitové kopie

Tento příklad ukazuje, jak používat Azure Machine Learning Workbench ke koordinaci distribuované školení a operationalization klasifikační modely bitové kopie. Můžeme použít dva přístupy k trénování: (i) upřesnění, hluboké neuronové sítě pomocí [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU clusteru a (ii) pomocí [Microsoft Machine Learning pro Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) balíček do obrázky featurize pomocí pretrained CNTK modely a ke cvičení třídění pomocí odvozené funkcí. Potom jsme nastaví velký obrázek v cloudu pomocí použít trénované modely paralelní způsobem [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) clusteru, abychom mohli škálovat rychlosti školení a operationalization přidáním nebo odebráním uzlů pracovního procesu.

Tento příklad označuje dva přístupy k přenosu learning, která využívá pretrained modely, aby se zabránilo nákladů na školení hluboké neuronové sítě od začátku. Retraining hluboké neuronové sítě obvykle vyžaduje výpočetní GPU, ale může vést k větší přesnost po dostatečně velké trénovací sady. Cvičení jednoduché třídění featurized Image nevyžaduje GPU výpočetní, je ze své podstaty rychlý a nahodile škálovatelné a vyhovuje méně parametry. Tato metoda je proto ideální volbou, když málo ukázek školení jsou k dispozici –, jako je často případ případy použití vlastní. 

Cluster Spark použité v tomto příkladu má 40 uzlů pracovního procesu a stojí ~$40/hr provoz; prostředky clusteru Batch AI zahrnují osm grafickými procesory a náklady ~$10/hr pracovat. Dokončení tohoto návodu trvá přibližně tři hodiny. Jakmile budete hotovi, postupujte podle pokynů čištění a odebrat prostředky, které jste vytvořili a zastavit nabíhání poplatků za.

## <a name="link-to-the-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie

Veřejné úložiště GitHub pro tento scénář skutečných obsahuje všechny materiály, včetně ukázky kódu, potřebné pro tento příklad:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Popis případu použití

V tomto scénáři jsme cvičení machine learning modely klasifikovat typ krajině ukazuje leteckou bitové kopie pozemků 224 měření 224 měření x. Krajině použití klasifikační modely lze použít ke sledování urbanization, odlesňování, ztrátě mokřady a jiných hlavní prostředí trendů pomocí pravidelně shromažďují leteckých snímků. Jsme připravili jste školení a ověření bitovou kopii sady založené na obrazů z USA National zemědělství obrazů Program a krajině použít popisky, které zveřejnil USA National krajině titulní databáze. Zde se zobrazují příklad obrázků v každé třídě krajině použití:

![Příklad oblasti pro každý krajině použít popisek](media/scenario-aerial-image-classification/example-labels.PNG)

Po školení a ověření modelu třídění budou použity ho leteckou Image pokrývání uzlů Middlesex okres, MA – domovské služby společnosti Microsoft Research nový Anglii & Center vývoj (NERD)--ukazují, jak lze pomocí těchto modelů prostudovali trendy v urbanistické vývoj.

K vytvoření klasifikátoru bitovou kopii pomocí přenosu učení, datových vědců často vytvořit více modelů s rozsahem metod a vyberte nejvíce původce model. Azure Machine Learning Workbench můžete pomoct data vědců koordinaci školení napříč různými výpočetními prostředí, sledovat a porovnejte výkon více modelů a použít zvolený model rozsáhlých datových sad v cloudu.

## <a name="scenario-structure"></a>Struktura scénář

V tomto příkladu jsou data obrázku a pretrained modely umístěny v účtu úložiště Azure. Clusteru Azure HDInsight Spark přečte tyto soubory a vytvoří model klasifikace bitovou kopii pomocí MMLSpark. Trénovaného modelu a jeho předpovědi se pak zapisují na účet úložiště, kde mohou být analyzován a vizualizovaný poznámkového bloku Jupyter spuštěn místně. Azure Machine Learning Workbench koordinuje vzdálené spuštění skriptů na clusteru Spark. Také sleduje přesnost metriky pro více modelů Trénink pomocí různých metod, které uživateli umožňují vyberte nejvíce původce model.

![Schéma pro scénář skutečných klasifikace leteckou bitové kopie](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Tyto podrobné pokyny, začněte tím, že provede vytváření a Příprava účtu úložiště Azure a cluster Spark, včetně instalace dat přenosu a závislostí. Potom popisují způsob spuštění úlohy školení a porovnejte výkon výsledné modelů. Nakonec se ukazují, jak použít zvolený model pro sadu velký obrázek na clusteru Spark a analyzujte výsledky předpovědi místně.


## <a name="set-up-the-execution-environment"></a>Nastavení prostředí pro spuštění

Následující pokyny vás provede procesem nastavení prostředí pro spuštění v tomto příkladu.

### <a name="prerequisites"></a>Požadavky
- [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici)
    - Vytvoříte clusteru služby HDInsight Spark s 40 uzlů pracovního procesu (celkový počet 168 jádra). Zkontrolujte, zda má váš účet dostatek dostupné jader kontrolou "využití + kvóty" karta pro vaše předplatné na portálu Azure.
       - Pokud máte menší počet jader, které jsou k dispozici, můžete upravovat snížit počet pracovních procesů, které jsou zřízené pro šablonu clusteru HDInsight. Pokyny k tomuto vyskytovat v části "Vytvoření clusteru HDInsight Spark".
    - Tato ukázka vytvoří cluster školení AI dávky s dva NC6 (GPU 1, 6 virtuální procesory) virtuálních počítačů. Zajistěte, aby váš účet dostatek dostupné jader v oblasti Východ USA kontrolou "využití + kvóty" karta pro vaše předplatné na portálu Azure.
- [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)
    - Postupujte podle [instalace a vytvoření rychlý Start](../service/quickstart-installation.md) instalace nástroje Azure Machine Learning Workbench a vytvoření experimentování a modelu správy účtů.
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK a Azure CLI 2.0
    - Dokončení v následujících částech [Batch AI recepty README](https://github.com/Azure/BatchAI/tree/master/recipes):
        - "Požadavky"
        - "Vytvoření a získat aplikaci Azure Active Directory (AAD)"
        - "Registrovat poskytovatele prostředků BatchAI" (v části "spuštění recepty pomocí rozhraní příkazového řádku Azure 2.0")
        - "Nainstalovat klienta pro správu Azure Batch AI"
        - "Instalace Azure Python SDK"
    - Záznam ID klienta, tajný klíč a ID klienta aplikace Azure Active Directory, který jste dostali pokyn k vytvoření. Tyto přihlašovací údaje použijete později v tomto kurzu.
    - Době psaní tohoto textu pomocí Azure Machine Learning Workbench a Azure Batch AI samostatné větve 2.0 rozhraní příkazového řádku Azure. Pro přehlednost označujeme nástroje Workbench verzi rozhraní příkazového řádku jako "rozhraní příkazového řádku spustit z Azure Machine Learning Workbench" a obecné verzi, (která zahrnuje Batch AI) jako "Azure CLI 2.0."
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), uvolněte nástroj pro spolupráci přenos souborů mezi účty úložiště Azure
    - Zajistěte, aby byl ve složce obsahující AzCopy spustitelný soubor na proměnné prostředí PATH vašeho systému. (Informace o úpravě proměnné prostředí jsou k dispozici [zde](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Klientem SSH; Doporučujeme, abyste [PuTTY](http://www.putty.org/).

V tomto příkladu byla testována v počítačích s Windows 10; byste měli být schopni spustit z libovolného počítače systému Windows, včetně virtuálních počítačů Azure dat vědecké účely. Azure CLI 2.0 nainstalovala ze souboru MSI podle [tyto pokyny](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Méně závažné změny mohou být vyžadovány (například změny filepaths) v systému macOS při spuštění v tomto příkladu.

### <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Tento příklad vyžaduje clusteru HDInsight Spark a účet úložiště Azure na hostitele příslušné soubory. Postupujte podle těchto pokynů můžete vytvořit tyto prostředky do nové skupiny prostředků Azure:

#### <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Leteckou Image klasifikaci" a vyberte šablonu
5.  Klikněte na **Vytvořit**
 
#### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

1. Po načtení projektu v Azure Machine Learning Workbench, otevřete kliknutím na soubor rozhraní příkazového řádku (CLI) -> Otevřete příkazový řádek.
    Použijte tuto verzi rozhraní příkazového řádku pro většinu kurzu. (Pokud vyznačeno, zobrazí se výzva otevřete jinou verzi rozhraní příkazového řádku k přípravě prostředků Batch AI.)

1. Z rozhraní příkazového řádku přihlaste se k účtu Azure tak, že spustíte následující příkaz:

    ```
    az login
    ```

    Jste vyzváni k navštívení adresy URL a zadejte poskytnutý kód dočasné; Web požadavků přihlašovací údaje účtu Azure.
    
1. Po dokončení přihlášení vrátit do rozhraní příkazového řádku a spusťte následující příkaz, abyste zjistili, která předplatná Azure, která jsou k dispozici ke svému účtu Azure:

    ```
    az account list
    ```

    Tento příkaz vypíše všechna předplatná spojená s vaším účtem Azure. Najít ID odběru, který chcete použít. Zápis ID předplatného uvedeno v následujícím příkazu nastavte aktivní předplatné spuštěním příkazu:

    ```
    az account set --subscription [subscription ID]
    ```

1. Prostředky Azure vytvořené v tomto příkladu jsou uloženy ve skupině prostředků Azure. Vyberte název skupiny prostředků jedinečný a zapisují na označené pozici, potom spusťte obou příkazů pro vytvoření skupiny prostředků Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Vytvoření účtu úložiště

Nyní vytvoříme účet úložiště, že hostitelé projektu souborů, které musí být přístupné pomocí HDInsight Spark.

1. Vyberte název účtu úložiště jedinečný a zápisu je uvedeno v následujícím `set` příkaz a potom spuštěním příkazů, jak vytvořit účet úložiště Azure:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Vypsat klíče účtu úložiště tak, že spustíte následující příkaz:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Zaznamenejte hodnotu `key1` jako úložiště klíč v následujícím příkazu, spusťte příkaz pro uložení hodnota.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Vytvoření sdílené složky s názvem `baitshare` ve vašem účtu úložiště pomocí následujícího příkazu:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. Ve svém oblíbeném textovém editoru, načíst `settings.cfg` z projektu Azure Machine Learning Workbench "Kódu" podadresáři a vložte název účtu úložiště a klíč, které je uvedené. Uložte a zavřete `settings.cfg` souboru.
1. Pokud jste tak již neučinili, stáhněte a nainstalujte [AzCopy](http://aka.ms/downloadazcopy) nástroj. Zkontrolujte, zda spustitelný soubor AzCopy v systémové cestě zadáním "AzCopy" a stisknutím klávesy Enter zobrazíte jeho dokumentace.
1. Vydejte následující příkazy pro kopírování všechny ukázková data, pretrained modely a skripty školení modelu do vhodného umístění ve vašem účtu úložiště:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Očekávejte přenosu souborů na trvat přibližně jednu hodinu. Na popředí, můžete přejít k části: pravděpodobně muset otevřít jiné rozhraní příkazového řádku pomocí Workbench a znovu definovat dočasné proměnné.

#### <a name="create-the-hdinsight-spark-cluster"></a>Vytvoření clusteru HDInsight Spark

Naše doporučenou metodu pro vytvoření clusteru HDInsight používá šablonu správce prostředků clusteru HDInsight Spark zahrnuté v podsložce "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" Tento projekt.

1. Šablona clusteru HDInsight Spark je soubor "template.json" v části "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" podsložkou tento projekt. Ve výchozím nastavení vytvoří šablona dílčí clusteru Spark s 40 uzlů pracovního procesu. Pokud toto číslo je nutné upravit, otevřete šablonu ve svém oblíbeném textovém editoru a nahradit všechny instance "40" číslo pracovní uzel podle svého výběru.
    - Z důvodu nedostatku paměti chyby mohou nastat později, pokud je menší počet uzlů pracovního procesu, který zvolíte. Boje proti chybami paměti, může spustit skripty školení a operationalization na podmnožinu dostupných dat, jak je popsáno dále v tomto dokumentu.
2. Vyberte jedinečný název a heslo pro HDInsight clusteru a zapíše je uvedeno v následujícím příkazu: potom vytvořte cluster vydáním příkazů:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Nasazení vašeho clusteru může trvat až 30 minut (včetně zřizování a skript akce spuštění).

### <a name="set-up-batch-ai-resources"></a>Nastavení prostředků Batch AI

Čekají na dokončení přenosu souborů účtu úložiště a nasazení clusteru Spark můžete připravit clusteru Batch AI serveru NFS (Network File) a GPU. Otevřete z příkazového řádku Azure CLI 2.0 a spusťte následující příkaz:

```
az --version 
```

Potvrďte, že `batchai` je v seznamu nainstalovaných modulů. Pokud ne, můžete pomocí různých rozhraní příkazového řádku (například jeden otevřít prostřednictvím Workbench).

Dále zkontrolujte, jestli registrace poskytovatele byla úspěšně dokončena. (Registrace zprostředkovatele trvá až 15 minut a stále může být probíhající, pokud jste nedávno dokončili [pokyny k instalaci Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes).) Potvrďte, že "Microsoft.Batch" a "Microsoft.BatchAI" se objeví se stavem "Registrovaná" ve výstupu příkazu:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

V opačném případě spusťte příkazy registraci tohoto zprostředkovatele a počkejte ~ 15 minut pro registraci dokončit.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Upravte následující příkazy a výrazy v závorkách nahraďte hodnoty, které jste použili předtím během vytváření účtu prostředků skupiny a úložiště. Potom uložení hodnot jako proměnné spuštěním příkazů:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Určit složku obsahující projekt Azure Machine Learning (například `C:\Users\<your username>\AzureML\aerialimageclassification`). Nahraďte hodnotu v závorkách filepath složky (s žádné koncové zpětné lomítko) a spusťte příkaz:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Nyní jste připraveni vytvořit prostředky Batch AI pro tento kurz potřeba.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Příprava Batch AI síťového souborového serveru

Cluster Batch AI přistupuje k datům školení na souborovém serveru sítě. Přístup k datům může být several-fold rychlejší při přístupu k souborům ze systému souborů NFS oproti sdílenou složku Azure nebo Azure Blob Storage.

1. Vydejte následující příkaz k vytvoření síťového serveru:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Zkontrolujte stav zřizování sítě souborového serveru pomocí následujícího příkazu:

    ```
    az batchai file-server list
    ```

    Pokud stav "zřizování" sítě souborového serveru s názvem "landuseclassifier" je "bylo úspěšné", je připravený k použití. Očekávají, že zřizování trvat přibližně pět minut.
1. Najít IP adresu vašeho systému souborů NFS ve výstupu příkazu předchozí (vlastnost "fileServerPublicIp" v části "mountSettings"). Zápis IP adres uvedenou v následující příkaz a potom uložení hodnoty spuštěním příkazu:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Vaše oblíbené nástroje SSH pomocí (následující ukázkový příkaz používá [PuTTY](http://www.putty.org/)), provedení tohoto projektu `prep_nfs.sh` skriptu na systém souborů NFS přenosu bitové kopie školení a ověření nastaví existuje.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Nemusíte dělat starosti Pokud aktualizace dat stažení a extrakci průběh posuňte se v okně prostředí tak rychle, že jsou nečitelná.

V případě potřeby si můžete ověřit, zda přenos dat proběhl podle plánu přihlášení k souborovému serveru s vaše oblíbené nástroje SSH a kontrola `/mnt/data` obsah adresáře. Byste měli najít dvě složky, training_images a validation_images, každý obsahující s podsložky pojmenována v souladu s pevnou pomocí kategorií.  Nastaví školení a ověřování by měl obsahovat ~ 44 tisíc a bitové kopie k ~ 11, v uvedeném pořadí.

#### <a name="create-a-batch-ai-cluster"></a>Vytvoření clusteru s podporou Batch AI

1. Vytvoření clusteru po vydání následujícího příkazu:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Použijte následující příkaz a zkontrolovat, zda že je stav zřizování clusteru:

    ```
    az batchai cluster list
    ```

    Při přidělování stavu pro cluster s názvem "landuseclassifier" změny z Změna velikosti konstantní, je možné k odesílání úloh. Úlohy však nespouštět spuštění, dokud se všechny virtuální počítače v clusteru opustili "přípravu" stavu. Pokud vlastnost "chyby" clusteru není null, při vytváření clusteru došlo k chybě a neměl by být použit.

#### <a name="record-batch-ai-training-credentials"></a>Záznamů přihlašovací údaje Batch AI školení

Na popředí pro přidělení clusteru k dokončení, otevřete `settings.cfg` soubor z podadresáři "Kód" tohoto projektu v textovém editoru podle svého výběru. Aktualizujte následující proměnné svoje vlastní přihlašovací údaje:
- `bait_subscription_id` (ID vašeho předplatného Azure 36 znaků)
- `bait_aad_client_id` (ID aplikace nebo klienta Azure Active Directory uvedených v části "Požadavky")
- `bait_aad_secret` (Azure Active Directory tajný klíč aplikace uvedený v části "Požadavky")
- `bait_aad_tenant` (ID klienta Azure Active Directory uvedených v části "Požadavky")
- `bait_region` (době psaní tohoto textu je jedinou možností: eastus)
- `bait_resource_group_name` (skupina prostředků, které jste zvolili dříve)

Pokud jste přiřadili tyto hodnoty, by měl vypadat upravené řádky souboru settings.cfg následující text:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Uložte a zavřete `settings.cfg`.

Nyní můžete zavřít okno rozhraní příkazového řádku, kde spustit příkazy vytváření prostředků Batch AI. Všechny další kroky v tomto kurzu použijte rozhraní příkazového řádku spustit z Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Příprava prostředí pro spuštění Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Zaregistrujte se jako cíl výpočetní služby Azure Machine Learning Workbench clusteru HDInsight

Po dokončení vytváření clusteru HDInsight se zaregistrujte takto jako cíl výpočetní pro svůj projekt clusteru:

1.  Vydejte následující příkaz z Azure Machine Learning rozhraní příkazového řádku:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Tento příkaz přidá dva soubory, `myhdi.runconfig` a `myhdi.compute`, do projektu `aml_config` složky.

1. Otevřete `myhdi.compute` souboru ve svém oblíbeném textovém editoru. Změnit `yarnDeployMode: cluster` řádku číst `yarnDeployMode: client`, uložte a zavřete soubor.
1. Spusťte následující příkaz k přípravě svého prostředí HDInsight pro použití:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Instalovat místní závislosti

Otevření rozhraní příkazového řádku z Azure Machine Learning Workbench a nainstalujte závislosti nutné pro místní provádění po vydání následujícího příkazu:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Získávání a pochopení dat

Tento scénář používá veřejně dostupné leteckých snímků data z [National Program obrazů zemědělství](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) v řešení 1 měření. Vygenerování sady 224 pixelů x 224 pixelů soubory PNG oříznout z původní data NAIP a seřazené podle jmenovek použití krajině z [National krajině zahrnují databáze](https://www.mrlc.gov/nlcd2011.php). Ukázkový obrázek s popiskem "Developed" je zobrazena v plné velikosti:

![Dlaždici ukázka vyvinuté krajině](media/scenario-aerial-image-classification/sample-tile-developed.png)

Třída vyrovnáváním sady ~ 44 tisíc a 11 tisíc bitové kopie se používají pro trénování modelu a ověření, v uvedeném pořadí. Jsme ukazují, že nasazení modelu na bitovou kopii k ~ 67 nastaven dlaždicové vyplnění Middlesex okres, MA – nové v Anglii výzkum a vývoj (NERD) center domovské služby společnosti Microsoft. Další informace o tom, jak tyto bitové kopie sady sestavený najdete v tématu [Trapně paralelní Image klasifikace úložiště git](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Umístění Middlesex okres, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Během instalace leteckou bitovou kopii sady použité v tomto příkladu byly předávány účet úložiště, který jste vytvořili. Školení, ověřování a operationalization Image jsou všechny soubory PNG s rozlišením jednoho pixelu za odmocnina metr 224 pixelů x 224 pixelů. Image školení a ověření mít byla uspořádány do podsložek podle jejich krajině použití popisku. (Použití popisků krajině operationalization bitových kopií neznámé a v mnoha případech nejednoznačný; některé z těchto bitových kopií obsahují více typů krajině.) Další informace o tom, jak tyto bitové kopie sady sestavený najdete v tématu [Trapně paralelní Image klasifikace úložiště git](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Chcete-li zobrazit příklad, že účet bitové kopie ve službě Azure storage (volitelné):
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Vyhledávání pro název účtu úložiště na panelu Hledat v horní části obrazovky. Klikněte na účet úložiště ve výsledcích hledání.
2. Klikněte na odkaz "Objekty BLOB" v hlavním podokně účet úložiště.
3. Klikněte na kontejner s názvem "train." Zobrazí seznam adresářů pojmenována v souladu s pevnou použít.
4. Klikněte na žádném z těchto adresářů k načtení seznamu bitových kopií, které obsahuje.
5. Klikněte na žádný obrázek a stažení zobrazíte bitovou kopii.
6. V případě potřeby klikněte na kontejnery s názvem "test" a "middlesexma2016", chcete-li zobrazit i jejich obsah.

## <a name="modeling"></a>Modelování

### <a name="training-models-with-azure-batch-ai"></a>Školení modely Azure Batch AI

`run_batch_ai.py` Skript v podsložce "Code\02_Modeling" projektu Workbench slouží k vydávání úlohy Batch AI školení. Tato úloha retrains klasifikátor bitové kopie: DNN volená uživatelem (AlexNet nebo ResNet 18 pretrained na ImageNet). Hloubka retraining můžete také zadat: retraining pouze poslední vrstva sítě může snížit overfitting při při optimalizaci celé síti (nebo pro AlexNet, plně připojené vrstvy) jsou k dispozici málo ukázek školení může vést k větší modelu výkon, pokud je sada školení je dostatečně velké.

Po dokončení úlohy školení, tento skript uloží do úložiště objektů blob v modelu (společně se soubor s popisem mapování mezi výstup celé číslo modelu a popisky řetězec) a jsou předpovědi. Soubor protokolu NÁVNADA úlohy je analyzována extrahovat timecourse chyba míra zlepšení přes epoch školení. Timecourse chyba míra zlepšování se zaznamená AML Workbench historie spouštění funkce pro pozdější zobrazení.

Vyberte název vaší trained model, typu pretrained modelu a retraining hloubka. Zápis váš výběr uvedeno v následujícím příkazu zahajte retraining spuštěním příkazu ze služby Azure ML rozhraní příkazového řádku:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Očekávají, že Azure Machine Learning, spusťte na trvat asi půl hodiny. Doporučujeme spustit několik podobné příkazy (různých názvu modelu výstup, typ pretrained modelu a retraining hloubku), tak, aby můžete porovnat výkon modely trénink na různé metody.

### <a name="training-models-with-mmlspark"></a>Školení modely MMLSpark

`run_mmlspark.py` Skript v podsložce "Code\02_Modeling" Workbench projektu se používá ke cvičení [MMLSpark](https://github.com/Azure/mmlspark) modelu pro klasifikaci bitové kopie. První featurizes skriptu školení nastavit obrázky pomocí třídění bitové kopie, které DNN pretrained na datovou sadu ImageNet (AlexNet nebo ResNet 18-layer). Tento skript potom využívá featurized Image ke cvičení model MMLSpark (náhodné doménové struktury nebo logistic regresní model) ke klasifikaci bitové kopie. Testovací sada bitové kopie je pak featurized a skóre s naučeného modelu. Přesnost předpovědi modelu na testovací sada je vypočtena a přihlášení k funkci Azure Machine Learning Workbench je historie spouštění. Nakonec pro cvičný model MMLSpark a jeho předpovědi na testovací sada se uloží do úložiště objektů blob.

Vyberte název modelu jedinečné výstup trained model, typu pretrained modelu a typ modelu MMLSpark. Zápis váš výběr uvedenou v šabloně následující příkaz, zahajte retraining spuštěním příkazu ze služby Azure ML rozhraní příkazového řádku:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Další `--sample_frac` parametr můžete a natrénuje a otestuje model s podmnožinu dostupných dat použít. Pomocí malé ukázkové zlomek snižuje požadavky modulu runtime a paměti za cenu přesnost trained model. (Například spouštění s `--sample_frac 0.1` je předpokládaná zhruba dvacet minut.) Další informace o tomto a dalších parametrů spuštění `python Code\02_Modeling\run_mmlspark.py -h`.

Uživatelé se doporučuje spusťte tento skript několikrát s různými vstupní parametry. Výkon výsledné modely můžete potom porovná v Azure Machine Learning Workbench na historii běhů funkce.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Porovnání modelu výkonu pomocí funkce Workbench spustit historie

Když jste provedli dva nebo více školení běží buď typu, přejděte na funkci historii běhů v Workbench kliknutím na ikonu hodiny společně levé nabídce. Vyberte `run_mmlspark.py` ze seznamu skriptů na levé straně. Podokno načte porovnávání přesnost sadu testů pro všechny spustí. Pokud chcete zobrazit další podrobnosti, přejděte dolů a klikněte na název jednotlivých spustit.

## <a name="deployment"></a>Nasazení

Pokud chcete použít jeden z trénované modely leteckou Image dlaždice Middlesex okres, MA pomocí vzdálené spuštění v HDInsight, Vložit název požadované modelu do následující příkaz a spusťte ho:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Další `--sample_frac` parametr slouží k zprovoznit model s podmnožinu dat k dispozici. Pomocí malé ukázkové zlomek snižuje požadavky modulu runtime a paměti za cenu úplnost předpovědi. Další informace o tomto a dalších parametrů spuštění `python Code\03_Deployment\batch_score_spark -h`.

Tento skript modelu předpovědi zapíše do svého účtu úložiště. Jsou předpovědi můžete prověřit, jak je popsáno v následující části.

## <a name="visualization"></a>Vizualizace

Poznámkového bloku Jupyter "Modelu předpovědi analysis" v podsložce "Code\04_Result_Analysis" projektu Workbench vizualizuje předpovědi modelu. Načtení a spusťte poznámkového bloku následujícím způsobem:
1. Otevřete projekt v Workbench a klikněte na složku ("soubory") ikonu podél nabídky na levé straně načíst výpis adresáře.
2. Přejděte do podsložky "Code\04_Result_Analysis" a klikněte na tlačítko v poznámkovém bloku s názvem "Model analysis předpovědi." Má být zobrazena preview vykreslování poznámkového bloku.
3. Klikněte na tlačítko "Spustit poznámkového bloku Server" načíst poznámkového bloku.
4. V první buňky zadejte název pro model, jejichž výsledky se má analyzovat na označené pozici.
5. Klikněte na "buňky -> spustit všechny" provést všechny buňky v poznámkovém bloku.
6. Přečtěte si společně s další informace o analýzy a vizualizací, které představuje poznámkového bloku.

## <a name="cleanup"></a>Vyčištění
Po dokončení v příkladu doporučujeme odstranit všechny prostředky, které jste vytvořili spuštěním následujícího příkazu z Azure rozhraní příkazového řádku:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Odkazy

- [Trapně paralelní Image klasifikace úložiště](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Popisuje vytváření datové sady z volně dostupných snímků a popisky
- [MMLSpark](https://github.com/Azure/mmlspark) úložiště GitHub
   - Obsahuje další příklady modelu trénování a hodnocení s MMLSpark

## <a name="conclusions"></a>Závěrů

Azure Machine Learning Workbench pomáhá datových vědců snadno nasadit svůj kód na vzdálené výpočetní cíle. V tomto příkladu místní znakové školení MMLSpark nasazená pro vzdálené spuštění v clusteru HDInsight a místní skript spustí úlohu školení na clusteru služby Azure Batch AI GPU. Funkce historie spouštění Azure Machine Learning Workbench je sledovat výkon více modelů a pomohl nám určit nejpřesnější modelu. Funkce poznámkové bloky Jupyter na Workbench pomohl vizualizovat předpovědi naše modely v interaktivní, grafické prostředí.

## <a name="next-steps"></a>Další postup
Se můžete ponořit hlouběji do tento příklad:
- V Azure Machine Learning Workbench je funkce historie spustit klikněte na ozubené kolečko symboly, které mají vybrat, které grafy a metriky se.
- Prozkoumat ukázkové skripty pro příkazy volání `run_logger`. Zkontrolujte, že chápete, jak se nahrává jednotlivé metriky.
- Prozkoumat ukázkové skripty pro příkazy volání `blob_service`. Zkontrolujte, zda je pochopit, jak trénované modely a předpovědi jsou uložené a načítají z cloudu.
- Prozkoumejte obsah kontejnery vytvoří ve vašem účtu úložiště objektů blob. Ujistěte se, že rozumíte které skriptu nebo příkaz zodpovídá za vytvoření každou skupinu souborů.
- Upravte skript školení pro učení jiný typ modelu MMLSpark nebo změnit model hyperparameters. K určení, zda změny zvětšit nebo zmenšit jeho přesnost pomocí funkce historie spouštění.
