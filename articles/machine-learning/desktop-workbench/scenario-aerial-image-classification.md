---
title: Klasifikace leteckých snímků | Dokumentace Microsoftu
description: Obsahuje pokyny pro scénáře reálného světa, klasifikace leteckých snímků
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
ms.component: core
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ROBOTS: NOINDEX
ms.openlocfilehash: e71de2d543c8f29b7195e74d574855bcbe739162
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260546"
---
# <a name="aerial-image-classification"></a>Klasifikace leteckých snímků

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Tento příklad ukazuje, jak používat Azure Machine Learning Workbench ke koordinaci distribuované trénování a operacionalizaci modelů klasifikace imagí. Můžeme použít dva přístupy k trénování: (i) upřesnění, hluboké neuronové sítě pomocí [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) clusteru GPU a (ii) pomocí [Microsoft Machine Learning pro Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) balíček do vytrénovaných Image, pomocí které je předem vytrénované CNTK modely a tak moct trénovat třídění díky funkcím, které odvozené. Můžeme následně trénované modely paralelní způsobem použít nastaví velký obrázek v cloudu pomocí [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) clusteru, abychom mohli rychlosti školení a operacionalizaci škálovat přidáváním nebo odebíráním pracovních uzlů.

Tento příklad ukazuje dva přístupy k přenosu learning, která využívá, které je předem vytrénované modely, abyste se vyhnuli nákladům hluboké neuronové sítě školení od začátku. Přeškolení hluboké neuronové sítě obvykle vyžaduje výpočetní GPU, ale může vést k větší přesnost při cvičnou sadou je dostatečně velký. Školení jednoduché třídění imagemi natrénuje nevyžaduje GPU výpočetní prostředky, je ze své podstaty rychlé a škálovatelné libovolně a vyhovuje méně parametrů. Tato metoda je proto skvělou volbou, když několik školení ukázky jsou k dispozici – jak se často stává třeba u případy použití vlastní. 

Cluster Spark, který je použitý v tomto příkladu má 40 pracovních uzlů a náklady na ~$40/hr provozovat; prostředky clusteru Batch AI zahrnují osm GPU a nákladů ~$10/hr provoz. V tomto návodu trvá přibližně tři hodiny. Až skončíte, postupujte podle pokynů vyčistit a odebrat prostředky, které jste vytvořili a zastavit nabíhání poplatků za.

## <a name="link-to-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie

Veřejného úložiště GitHub pro tento scénář reálného světa obsahuje veškeré materiály, včetně ukázek kódu, třeba v tomto příkladu:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Popis případu použití

V tomto scénáři jsme trénování modelů strojového učení klasifikovat typ pozemního, jak je znázorněno letecké obrázcích 224 měřiče x 224 měřiče vykreslení. Modelů klasifikace použití pozemního slouží ke sledování urbanization, odlesňování, ztráta mokřady a další hlavní prostředí trendy používání pravidelně shromažďovány leteckých snímků. Jsme připravili jste bitové kopie sady školení a ověřování založené na obrázcích v USA Národní zemědělství Trénováním programu a pozemního použití popisků publikoval(a) USA National pozemního titulní databáze. Příklad imagí v každé třídě použijte land se tady zobrazí:

![Příklad oblasti pro každý pozemního použít popisek](media/scenario-aerial-image-classification/example-labels.PNG)

Po školení a ověření modelu třídění jsme aplikujete na letecké imagí pokrývání uzlů Middlesex County, MA – Domovská stránka nástroje od Microsoftu Anglie nový výzkumu a vývoje (NERD) Centrum – předvést, jak lze pomocí těchto modelů studovat trendy v měst vývoj.

K vytvoření třídění image pomocí přenosu v učení, odborníci přes data často vytvořit více modely pomocí široké škály metod a vyberte nejvíce nejvýkonnějšího modelu. Azure Machine Learning Workbench můžete dat odborníky trénování koordinovat v různých výpočetních prostředích, sledovat a porovnat výkon jednotlivých více modelů a použít zvolený model u velkých datových sad v cloudu.

## <a name="scenario-structure"></a>Struktura scénář

V tomto příkladu obrazová data a které je předem vytrénované modely jsou umístěny v účtu služby Azure storage. Cluster Azure HDInsight Spark čte tyto soubory a sestaví model klasifikace obrázků pomocí MMLSpark. Trénovaného modelu a jeho předpovědi jsou pak zapsány do účtu úložiště, kde mohou být analyzovat a vizualizovat poznámkového bloku Jupyter, který je spuštěn místně. Azure Machine Learning Workbench koordinuje vzdálené spouštění skriptů v clusteru Spark. Sleduje také metriky přesnosti pro více modelů trénovaných pomocí různých metod, které uživateli umožňují vybrat nejvíce nejvýkonnějšího modelu.

![Schéma pro scénář reálného světa klasifikace leteckých snímků](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Provede vás vytváření a Příprava účtu služby Azure storage a cluster Spark, včetně instalace závislostí a přenosu dat začněte těchto podrobných pokynů. Potom se popisují, jak spouštět úlohy trénování a porovnat výsledný modelů. A konečně, ukazují, jak použít zvolený model sadu velký obrázek v clusteru Spark a analyzovat výsledky předpovědí místně.


## <a name="set-up-the-execution-environment"></a>Nastavení prostředí pro spuštění

Postupujte podle následujících pokynů vás provede procesem nastavení prostředí pro spuštění v tomto příkladu.

### <a name="prerequisites"></a>Požadavky
- [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici)
    - Vytvoříte cluster HDInsight Spark s 40 pracovních uzlů (celkový počet 168 jader). Ujistěte se, že váš účet má dostatek dostupných jader kontrolou "využití a kvóty" karty pro vaše předplatné na webu Azure portal.
       - Pokud máte k dispozici menší počet jader, můžete upravit šablony clusteru HDInsight, která má snížit počet pracovních procesů, které jsou zřízené. Pokyny k tomuto se zobrazí v části "Vytvoření clusteru HDInsight Spark".
    - Tato ukázka vytvoří cluster Batch AI Training s dvěma NC6 (1 GPU, virtuálním procesorem 6) virtuálních počítačů. Ujistěte se, že váš účet má dostatek dostupných jader v oblasti USA – východ kontrolou "využití a kvóty" karty pro vaše předplatné na webu Azure portal.
- [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)
    - Postupujte podle [instalace a vytvoření rychlý Start](../desktop-workbench/quickstart-installation.md) nainstalujte aplikaci Azure Machine Learning Workbench a vytvářet experimenty a účty správy modelů.
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK a Azure CLI
    - Dokončení v následujících částech [služby Batch AI recepty README](https://github.com/Azure/BatchAI/tree/master/recipes):
        - "Požadavky"
        - "Vytvořit a aplikace Azure Active Directory (AAD)"
        - "Registraci poskytovatelů prostředků BatchAI" (v části "spuštění recepty pomocí rozhraní příkazového řádku Azure")
        - "Nainstalovat klienta služby Azure Batch AI Správa"
        - "Instalace sady Azure Python SDK"
    - Záznam ID klienta, tajného kódu a ID tenanta, které jsou směrované na vytvoření aplikace Azure Active Directory. Tyto přihlašovací údaje použijete později v tomto kurzu.
    - V době psaní tohoto návodu, Azure Machine Learning Workbench a Azure Batch AI pomocí samostatných forky rozhraní příkazového řádku Azure. Pro přehlednost označujeme v aplikaci Workbench verze rozhraní příkazového řádku jako "spuštění aplikace Azure Machine Learning Workbench CLI" a v obecné verzi (která zahrnuje služby Batch AI) jako "Rozhraní příkazového řádku Azure."
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), bezplatný nástroj pro koordinaci přenos souborů mezi účty Azure storage
    - Ujistěte se, že složka obsahující spustitelný soubor AzCopy je na váš systém proměnné prostředí PATH. (Pokyny, jak upravit proměnné prostředí jsou k dispozici [tady](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Klient SSH; Doporučujeme [PuTTY](http://www.putty.org/).

V tomto příkladu byl testován na počítači s Windows 10; je třeba spustit z libovolného počítače Windows, včetně Azure Data Science Virtual Machines. Rozhraní příkazového řádku Azure byla nainstalovaná z MSI podle [tyto pokyny](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Menší změny mohou být vyžadovány (například změny filepaths) při spuštění v tomto příkladu v systému macOS.

### <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Tento příklad vyžaduje cluster HDInsight Spark a účet úložiště Azure na hostitele příslušné soubory. Postupujte podle těchto pokynů k vytvoření těchto prostředků do nové skupiny prostředků Azure:

#### <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvořte nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Letecké klasifikace obrázků" a vyberte šablonu
5.  Klikněte na **Vytvořit**
 
#### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

1. Po načtení projektu v aplikaci Azure Machine Learning Workbench otevřete kliknutím na soubor rozhraní příkazového řádku (CLI) -> otevřít příkazový řádek.
    Pomocí této verze rozhraní příkazového řádku pro většinu tohoto kurzu. (Jednociferným, zobrazí se výzva k otevření jinou verzi rozhraní příkazového řádku a příprava prostředků služby Batch AI.)

1. Z rozhraní příkazového řádku přihlaste se ke svému účtu spuštěním následujícího příkazu:

    ```
    az login
    ```

    Zobrazí se výzva k navštivte adresu URL a zadejte zadaný dočasný kód; na webu požádá o přihlašovací údaje účtu Azure.
    
1. Po dokončení přihlášení se vraťte na rozhraní příkazového řádku a spusťte následující příkaz k určení, jaké předplatné Azure propojeno se ke svému účtu Azure k dispozici:

    ```
    az account list
    ```

    Tento příkaz vypíše všechna předplatná přidružená k účtu Azure. Najdete ID odběru, který chcete použít. Zápis ID předplatného uvedeno v následujícím příkazu nastavte aktivní předplatné spuštěním příkazu:

    ```
    az account set --subscription [subscription ID]
    ```

1. Prostředky Azure vytvořené v tomto příkladu jsou uloženy ve skupině prostředků Azure. Zvolte název skupiny prostředků jedinečný a zapíše je uvedeno, následné provádění oba příkazy k vytvoření skupiny prostředků Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Vytvoření účtu úložiště

Teď vytvoříme účet úložiště, že hostitelé soubory projektu, které musí být přistupováno HDInsight Spark.

1. Zvolte jedinečným názvem účtu úložiště a zapíše je uvedeno v následujícím `set` příkaz a pak vytvořte účet úložiště Azure pomocí provádí oba příkazy:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Vypsat klíče účtu úložiště pomocí následujícího příkazu:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Zaznamenaná hodnota `key1` jako klíč úložiště v následujícím příkazu, spusťte příkaz pro uložení hodnoty.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Vytvoření sdílené složky s názvem `baitshare` ve vašem účtu úložiště pomocí následujícího příkazu:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. V oblíbeném textovém editoru, načtěte `settings.cfg` soubor z projektu aplikace Azure Machine Learning Workbench "Code" podadresáře a vložte název účtu úložiště a klíč, jak je uvedeno. Uložte a zavřete `settings.cfg` souboru.
1. Pokud jste tak již neučinili, stáhněte a nainstalujte [AzCopy](https://aka.ms/downloadazcopy) nástroj. Zajistěte, aby byl spustitelný soubor AzCopy v systémové cestě psát "AzCopy" a stisknutím klávesy Enter zobrazíte jeho dokumentaci.
1. Tyto příkazy ke zkopírování všech ukázkových dat, které je předem vytrénované modely a skripty trénování modelu do vhodného umístění ve vašem účtu úložiště:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Můžete očekávejte přenos souborů do trvat přibližně jednu hodinu. Zatímco čekáte, můžete přejít k části: budete muset otevřít jiné rozhraní příkazového řádku pomocí aplikace Workbench a dočasné proměnné znovu definovat.

#### <a name="create-the-hdinsight-spark-cluster"></a>Vytvoření clusteru HDInsight Spark

Naše doporučovaná metoda pro vytvoření clusteru HDInsight používá šablonu správce prostředků clusteru HDInsight Spark zahrnuté v podsložce "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" Tento projekt.

1. Šablonu clusteru HDInsight Spark je "template.json" soubor v podsložce "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" Tento projekt. Ve výchozím nastavení šablona vytvoří Spark cluster s 40 pracovních uzlů. Pokud toto číslo je nutné upravit, otevřete šablonu ve svém oblíbeném textovém editoru a nahraďte všechny výskyty "40" číslo pracovního uzlu podle vašeho výběru.
    - Limit paměti chyby mohou nastat později, pokud je menší počet pracovních uzlů, které zvolíte. K boji s chybami paměti, může spouštět skripty školení a operacionalizaci na podmnožinu dostupných dat, jak je popsáno dále v tomto dokumentu.
2. Zvolte jedinečný název a heslo pro HDInsight cluster a zapíše je uvedeno v následujícím příkazu: vytvořte cluster pomocí příkazů:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Nasazení vašeho clusteru může trvat až 30 minut (včetně spuštění akce zřizování a skriptu).

### <a name="set-up-batch-ai-resources"></a>Nastavení prostředků služby Batch AI

Zatímco čekáte přenos souborů účtu úložiště a nasazení clusteru Spark dokončení, můžete připravit cluster Batch AI serveru NFS (Network File) a GPU. Otevřete příkazový řádek Azure CLI a spusťte následující příkaz:

```
az --version 
```

Ujistěte se, že `batchai` je v seznamu nainstalovaných modulů. Pokud ne, můžete pomocí různých rozhraní příkazového řádku (například jeden otevřít pomocí aplikace Workbench).

Dále zkontrolujte, že registrace poskytovatele byla úspěšně dokončena. (Registrace poskytovatele trvá až 15 minut a může být stále probíhající, pokud jste nedávno dokončili [pokyny k instalaci služby Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes).) Potvrďte, že "Microsoft.Batch" a "Microsoft.BatchAI", zobrazí se stavem "Registrováno" ve výstupu následujícího příkazu:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Pokud ne, spuštěním následujícího poskytovatele registrace příkazů a Počkejte přibližně 15 minut, než se registrace dokončí.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Upravte následující příkazy a výrazy v závorkách nahraďte hodnotami, které jste použili dříve při vytváření prostředku skupiny a úložiště účtu. Uložte hodnoty jako proměnné spuštěním příkazů:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Určit složku obsahující projekt Azure Machine Learning (například `C:\Users\<your username>\AzureML\aerialimageclassification`). Hodnotu v závorkách nahraďte filepath složky (s žádné koncové lomítko) a spusťte příkaz:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Nyní jste připraveni vytvořit prostředky služby Batch AI pro tento kurz potřeba.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Příprava souborový Server Batch AI sítě

Cluster Batch AI přistupuje k cvičných dat na souborovém serveru sítě. Přístup k datům může být several-fold rychlejší při přistupování k souborům ze systému souborů NFS a sdílené složky Azure nebo Azure Blob Storage.

1. Vydejte následující příkaz k vytvoření souborového serveru sítě:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Zkontrolujte stav zřizování sítě souborového serveru pomocí následujícího příkazu:

    ```
    az batchai file-server list
    ```

    Když "provisioningState" sítě souborový Server s názvem "landuseclassifier" je "bylo dokončeno", bude připravený k použití. Můžete očekávejte, zřízení až po trvat déle než pět minut.
1. Zjistit IP adresu vašeho systému souborů NFS ve výstupu předchozího příkazu (vlastnost "fileServerPublicIp" v části "mountSettings"). Zápis IP adresu uvedenou v následující příkaz a potom uložení hodnoty spuštěním příkazu:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Pomocí vašeho oblíbeného nástroje SSH (následující ukázkový příkaz používá [PuTTY](http://www.putty.org/)), spusťte tento projekt `prep_nfs.sh` nastaví skript v systému souborů NFS přenosu školení a ověření bitové kopie existuje.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Není třeba pokud se aktualizace průběhu stahování a extrakce dat posuňte se v okně prostředí tak rychle, že jsou nečitelnou.

V případě potřeby můžete potvrdit, že se přenos dat proběhl podle plánu tak, že přihlášení k souborovému serveru pomocí vašeho oblíbeného nástroje SSH a kontrola `/mnt/data` obsah adresáře. Byste měli najít dvě složky training_images a validation_images, každá obsahuje s podsložek pojmenovaných podle pozemního pomocí kategorií.  Školení a ověření sady by měl obsahovat ~ 44 tisíc a ~ 11 tisíc obrázků v uvedeném pořadí.

#### <a name="create-a-batch-ai-cluster"></a>Vytvoření clusteru Batch AI

1. Vytvoření clusteru pomocí následujícího příkazu:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Chcete-li zkontrolovat, že je stav zřizování clusteru použijte následující příkaz:

    ```
    az batchai cluster list
    ```

    Když se zobrazí stav přidělení pro cluster s názvem "landuseclassifier" změny během změny velikosti pro konstantní, je možné odesílat úlohy. Úlohy, ale nespustí, spuštění, dokud se všechny virtuální počítače v clusteru ještě zbývá stavu "přípravy". Pokud je vlastnost "chyby" clusteru není null, došlo k chybě při vytváření clusteru a neměl by se používat.

#### <a name="record-batch-ai-training-credentials"></a>Záznam přihlašovací údaje služby Batch AI Training

Zatímco čekáte přidělení clusteru dokončit, otevřete `settings.cfg` souboru z podadresáře "Kód" Tento projekt v textovém editoru podle vašeho výběru. Vlastní přihlašovací údaje aktualizujte následující proměnné:
- `bait_subscription_id` (ID svého předplatného Azure ze 36 znaků)
- `bait_aad_client_id` (ID aplikace/klienta Azure Active Directory uvedené v oddílu "Požadavky")
- `bait_aad_secret` (Azure Active Directory tajný klíč aplikace uvedené v oddílu "Požadavky")
- `bait_aad_tenant` (ID tenanta Azure Active Directory uvedené v oddílu "Požadavky")
- `bait_region` (v době psaní tohoto textu je jedinou možností: eastus)
- `bait_resource_group_name` (skupina prostředků, kterou jste zvolili dříve)

Jakmile přiřadíte tyto hodnoty, upravené řádky settings.cfg souboru by se mělo podobat následujícím textem:

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

Teď můžete zavřít okno rozhraní příkazového řádku, kde jste spustili příkazy vytváření prostředků služby Batch AI. Všechny další kroky v tomto kurzu pomocí rozhraní příkazového řádku spustit z aplikace Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Příprava prostředí pro spuštění aplikace Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Zaregistrujte se ke clusteru HDInsight jako cílové výpočetní prostředí Azure Machine Learning Workbench

Po vytvoření clusteru HDInsight se zaregistrujte se cluster jako cílové výpočetní prostředí pro váš projekt následujícím způsobem:

1.  Vydejte následující příkaz z Azure Machine Learning rozhraní příkazového řádku:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Tento příkaz přidá dva soubory `myhdi.runconfig` a `myhdi.compute`, do svého projektu `aml_config` složky.

1. Otevřít `myhdi.compute` souboru ve svém oblíbeném textovém editoru. Upravit `yarnDeployMode: cluster` řádku číst `yarnDeployMode: client`, uložte a zavřete soubor.
1. Spusťte následující příkaz, který připraví vaše prostředí HDInsight pro použití:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Nainstalujte místní závislosti

Aplikace Azure Machine Learning Workbench otevřete rozhraní příkazového řádku a nainstalujte závislosti vyžadované pro místní spuštění vydání následujícího příkazu:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Získávání a pochopení dat

Tento scénář využívá veřejně dostupnou leteckých snímků dat z [národní Program Trénováním zemědělství](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) rozlišení měřiče 1. Vygenerování sady 224 pixel x 224 pixel soubory PNG výtisky oříznout z původní data NAIP a seřazených podle pozemního použít popisky z [databáze zahrnují národní pozemního](https://www.mrlc.gov/nlcd2011.php). Ukázkový obrázek s popiskem "Developed" se zobrazí v plné velikosti:

![Ukázka dlaždice vyvinuté pozemek](media/scenario-aerial-image-classification/sample-tile-developed.png)

Třída vyrovnáváním ~ 44 tisíc a 11 tisíc obrázků se používají pro trénování modelu a ověření, v uvedeném pořadí. Jsme ukazují, jak nastavit nasazení modelu na přibližně 67 k imagi dlaždicové vyplnění Middlesex County, MA – nový Anglie výzkum a vývoj (NERD) center Domovská stránka nástroje od Microsoftu. Další informace o jak byly vytvořeny tyto sady image, najdete v článku [úložiště git zpracovatelné paralelní klasifikace obrázků](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Umístění Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Během instalace leteckých sad použitých v tomto příkladu byly převedeny do účtu úložiště, který jste vytvořili. Školení, ověření a operacionalizaci obrázky jsou všechny soubory PNG s rozlišením jeden pixel na čtvereček měřiče 224 pixel x 224 pixelů. Školení a ověřování imagí mají byla uspořádány do podsložek podle jejich pozemního použít popisek. (Použít popisky pozemního imagí operacionalizace nejsou známé a v mnoha případech nejednoznačný; některé z těchto imagí obsahují více typů pozemního.) Další informace o jak byly vytvořeny tyto sady image, najdete v článku [úložiště git zpracovatelné paralelní klasifikace obrázků](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Chcete-li zobrazit příklad, že Image ve službě Azure storage account (volitelné):
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledávání pro název účtu úložiště na panelu hledání v horní části obrazovky. Klikněte na tlačítko na vašem účtu úložiště ve výsledcích hledání.
2. Kliknutím na odkaz "BLOB" v hlavním podokně účtu úložiště.
3. Klikněte na kontejner s názvem "train". Zobrazí se seznam adresářů s názvem podle pozemního použít.
4. Klikněte na některý z těchto adresářů se načíst seznam imagí, které obsahuje.
5. Klikněte na libovolné obrázku a stáhněte ho chcete-li zobrazit obrázek.
6. V případě potřeby klikněte na kontejnery s názvem "test" a "middlesexma2016", chcete-li zobrazit i jejich obsah.

## <a name="modeling"></a>Modelování

### <a name="training-models-with-azure-batch-ai"></a>Trénování modelů s Azure Batch AI

`run_batch_ai.py` Skript v podsložce "Code\02_Modeling" projekt aplikace Workbench slouží k vydávání úlohu služby Batch AI Training. Tato úloha retrains klasifikátor image: DNN uživatelem (AlexNet nebo modelem ResNet 18, které je předem vytrénované na ImageNet). Hloubka přetrénování je taky možné specifikovat: přetrénování jenom poslední vrstva sítě může snížit overfitting při několik školení ukázky jsou k dispozici, při dolaďování celé síti (nebo pro AlexNet plně propojené vrstvy) může vést k větší modelu výkon při cvičnou sadou je dostatečně velký.

Po dokončení úlohy trénování tento skript uloží a předpovědi modelu (spolu s soubor s popisem mapování mezi výstup celé číslo modelu a popisky řetězec) do úložiště objektů blob. Soubor protokolu BAIT úlohy je analyzován extrahovat timecourse zlepšování míra chyb přes epoch školení. Funkce historie spuštění AML Workbench pro možnost pozdější kontroly se protokoluje při timecourse zlepšování míra chyb.

Vyberte název trénovaného modelu, které je předem vytrénované model typu a retraining hloubku. Zápis nastavení uvedeno v následujícím příkazu zahajte přetrénování spuštěním příkazu ze Azure ML rozhraní příkazového řádku:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Očekávat, že Azure Machine Learning, spusťte na dokončení trvat přibližně půl hodiny. Doporučujeme spustit několik podobných příkazech (změnou výstupní název modelu, které je předem vytrénované model typu a retraining hloubku) tak, aby můžete porovnat výkon jednotlivých modelů trénovaných s různými způsoby.

### <a name="training-models-with-mmlspark"></a>Trénování modelů s MMLSpark

`run_mmlspark.py` Skript v podsložce "Code\02_Modeling" projekt aplikace Workbench se používá k natrénování [MMLSpark](https://github.com/Azure/mmlspark) model pro klasifikace obrázků. První featurizes skript školení nastavit obrázků s využitím image třídění, který DNN které je předem vytrénované na datové sadě ImageNet (AlexNet nebo modelem ResNet 18 layer). Skript potom použije natrénuje obrázků k trénování modelu MMLSpark (náhodný doménová struktura nebo model logistické regrese) ke klasifikaci obrázků. Testovací sada obrázků je pak natrénuje a skóre s trénovaného modelu. Přesnost předpovědi modelu v sadě testů se počítá a přihlášení k funkci Azure Machine Learning Workbench na historii spuštění. Nakonec trénovaného modelu MMLSpark a jeho předpovědi v sadě testů se uloží do úložiště objektů blob.

Zvolte název modelu jedinečná výstupní trénovaného modelu, které je předem vytrénované model typu a typem MMLSpark modelu. Zápis požadovaná nastavení na označené v šabloně následující příkaz, začněte přetrénování spuštěním příkazu ze Azure ML rozhraní příkazového řádku:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Zobrazí se další `--sample_frac` parametr lze použít k trénování a testování modelu s použitím podmnožiny dat k dispozici. Pomocí malé ukázkové zlomek snižuje požadavky na modul runtime a paměti ovšem na úkor přesnosti trénovaného modelu. (Například běh s `--sample_frac 0.1` má trvat přibližně 20 minut.) Další informace o tomto a dalších parametrů spuštění `python Code\02_Modeling\run_mmlspark.py -h`.

Uživatelé můžou ke spuštění tohoto skriptu několikrát s různými vstupní parametry. Výkon výsledné modely pak jde porovnat v Azure Machine Learning Workbench na historii spuštění funkce.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Porovnání modelu výkonu pomocí funkce historie spuštění aplikace Workbench

Poté, co jste uzavřeli dohodu o dva nebo více školení spuštění jednoho z těchto typů, přejděte na funkci historie spuštění v aplikaci Workbench kliknutím na ikonu hodin na panelu nabídky na levé straně. Vyberte `run_mmlspark.py` ze seznamu skripty na levé straně. Podokno načte přesnost sadu testů pro všechna spuštění porovnání. Pokud chcete zobrazit více podrobností, přejděte dolů a klikněte na název jednotlivých spuštění.

## <a name="deployment"></a>Nasazení

Pokud chcete použít jeden z trénované modely letecké imagí bloků Middlesex County, MA pomocí vzdálené spuštění na HDInsight, vložte název požadovaného modelu do následující příkaz a spusťte ho:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Zobrazí se další `--sample_frac` parametr lze použít na zprovoznění modelu s použitím podmnožiny dat k dispozici. Pomocí malé ukázkové zlomek snižuje požadavky na úkor předpovědi úplnost modulu runtime a paměti. Další informace o tomto a dalších parametrů spuštění `python Code\03_Deployment\batch_score_spark -h`.

Tento skript zapíše předpovědi modelu do účtu úložiště. Předpovědi můžete prozkoumat, jak je popsáno v další části.

## <a name="visualization"></a>Vizualizace

Poznámkový blok Jupyter "Model předpověď analýzy" v podsložce "Code\04_Result_Analysis" projekt aplikace Workbench vizualizuje předpovědi modelu. Načtení a spuštění poznámkového bloku následujícím způsobem:
1. Otevřete projekt v aplikaci Workbench a klikněte na složku ("souborů") ikona podél nabídky na levé straně pro načtení seznamu adresářů.
2. Přejděte do podsložky "Code\04_Result_Analysis" a klikněte na tlačítko v poznámkovém bloku s názvem "Model předpověď analýzy." Má být zobrazena ve verzi preview vykreslování poznámkového bloku.
3. Klikněte na možnost "Spustit Server poznámkového bloku" načíst poznámkového bloku.
4. V první buňky zadejte název modelu, jejichž výsledky chcete analyzovat na označené.
5. Klikněte na "buňky -> spustit všechny" provádět všechny buňky v poznámkovém bloku.
6. Přečtěte si společně s další informace o analýzy a vizualizace, které představuje poznámkového bloku.

## <a name="cleanup"></a>Vyčištění
Po dokončení příkladu doporučujeme odstranit všechny prostředky, které vytvoříte spuštěním následujícího příkazu z příkazového řádku rozhraní:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Odkazy

- [Úložiště zpracovatelné paralelní klasifikace obrázků](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Popisuje vytváření datové sady z volně dostupných trénováním a popisky
- [MMLSpark](https://github.com/Azure/mmlspark) úložiště GitHub
   - Obsahuje další příklady trénování modelu a vyhodnocení s MMLSpark

## <a name="conclusions"></a>Závěry

Azure Machine Learning Workbench pomáhá odborníkům přes data jednoduše nasadit svůj kód na cílových výpočetních prostředí vzdálené. V tomto příkladu místní MMLSpark školení kódu byla nasazena pro vzdálené spuštění v clusteru HDInsight a místního skript spustili trénovací úlohu v clusteru služby Azure Batch AI GPU. Funkce Azure Machine Learning Workbench na historii spuštění sledovat výkon více modelů a pomohl nám identifikovat co nejvíce zpřesnili modelu. Funkce poznámkové bloky Jupyter aplikace Workbench pomáhá vizualizovat naše modelů prognózy v interaktivní, grafické prostředí.

## <a name="next-steps"></a>Další postup
Chcete-li se podrobněji věnovat v tomto příkladu:
- Ve funkci Azure Machine Learning Workbench na historie spuštění klikněte na tlačítko ozubeného kola symboly, které chcete vybrat, které grafy a metriky se zobrazí.
- Prozkoumejte ukázky skriptů pro příkazy volání `run_logger`. Zkontrolujte, že chápete, jak se jednotlivé metriky zaznamenán.
- Prozkoumejte ukázky skriptů pro příkazy volání `blob_service`. Zkontrolujte, že chápete, jak trénované modely a předpovědi jsou uložené a načítají z cloudu.
- Seznamte se s obsahem kontejnery vytvořené v účtu blob storage. Ujistěte se, že víte, které skript nebo příkaz zodpovídá za vytvoření každou skupinu souborů.
- Upravte skript školení k trénování jiný typ modelu MMLSpark nebo chcete-li změnit model hyperparameters. K určení, zda změny zvýší nebo sníží jeho přesnost pomocí funkce historie spuštění.
