---
title: Vývoj akcí skriptů v prostředí HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak přizpůsobit clustery Hadoop pomocí akce skriptu. Akce skriptu lze nainstalovat další software spuštěných v clusteru s Hadoop nebo změnit konfiguraci aplikace nainstalované v clusteru.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 921da2db8e235e17611788cae7e976597bd76703
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Vývoj skriptů akce skriptu pro clustery se systémem HDInsight Windows
Zjistěte, jak k psaní skriptů akce skriptu pro HDInsight. Informace o použití akce skriptu skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md). Stejný článek napsán pro clustery HDInsight se systémem Linux, najdete v části [vyvíjet akce skriptu skripty pro HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat pouze pro clustery HDInsight se systémem Windows. HDInsight je k dispozici pouze v systému Windows verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o použití akce skriptu s clustery se systémem Linux najdete v tématu [vývoj akcí skriptů v prostředí HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



Akce skriptu lze nainstalovat další software spuštěných v clusteru s Hadoop nebo změnit konfiguraci aplikace nainstalované v clusteru. Akce skriptů jsou skripty, které jsou spuštěny na uzlech clusteru při nasazených clusterů HDInsight, a jsou prováděna po dokončení konfigurace HDInsight uzly v clusteru. Akce skriptu se spustí v části oprávnění k účtu správce systému a poskytuje úplná přístupová práva pro uzly clusteru. Každý cluster lze zadat seznam akcí skriptů spouštění v pořadí, ve kterém jsou uvedené.

> [!NOTE]
> Pokud se setkáte se následující chybová zpráva:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: Termín 'Uložit-HDIFile' nebyl rozpoznán jako název rutiny, funkce, soubor skriptu nebo spustitelného programu. Zkontrolujte, zda název, nebo pokud byl zahrnut cestu, ověřte, zda je cesta správná a zkuste to znovu.
> Je to proto, že jste nezahrnuli pomocné metody.  V tématu [pomocné metody pro vlastní skripty](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Ukázkové skripty
Akce skriptu pro vytváření clusterů HDInsight v operačním systému Windows, je skript prostředí Azure PowerShell. Následující skript je ukázkou pro konfiguraci lokality konfigurační soubory:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Skript používá čtyři parametry, název konfiguračního souboru, vlastnosti, kterou chcete upravit, hodnotu, kterou chcete nastavit a popis. Příklad:

    hive-site.xml hive.metastore.client.socket.timeout 90

Tyto parametry nastavit hodnotu hive.metastore.client.socket.timeout a 90 v souboru hive-site.xml.  Výchozí hodnota je 60 sekund.

Tento vzorový skript naleznete také v [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight nabízí několik skriptů k instalaci dalších součástí v clusterech HDInsight:

| Název | Skript |
| --- | --- |
| **Nainstalujte Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. V tématu [instalací a použitím clustery Spark v HDInsight][hdinsight-install-spark]. |
| **Nainstalujte jazyk R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. V tématu [instalací a použitím R v clusterech HDInsight] [hdinsight skripty r]. |
| **Nainstalujte Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. V tématu [instalace a použití clusterů v HDInsight Solr](hdinsight-hadoop-solr-install.md). |
| - **Nainstalujte Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. V tématu [instalace a použití clusterů v HDInsight Giraph](hdinsight-hadoop-giraph-install.md). |

Akce skriptu se dá nasadit na portálu Azure, Azure PowerShell nebo pomocí sady .NET SDK HDInsight.  Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu][hdinsight-cluster-customize].

> [!NOTE]
> Ukázkové skripty pracovat pouze s verze clusteru HDInsight verze 3.1 nebo vyšší. Další informace o verzích clusterů HDInsight, naleznete v části [verze clusteru HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Pomocné metody pro vlastní skripty
Pomocné metody akcí skriptů jsou nástroje, které můžete použít při zápisu vlastních skriptů. Tyto metody jsou definovány v [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)a můžou být součástí skripty pomocí následující ukázka:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Zde jsou pomocné metody, které jsou poskytovány tento skript:

| Pomocná metoda | Popis |
| --- | --- |
| **Save-HDIFile** |Stažení souboru z zadaný identifikátor URI (Uniform Resource) do umístění na místní disk, který je přidružený k uzlu virtuálního počítače Azure přiřadili ke clusteru. |
| **Expand-HDIZippedFile** |Rozbalte soubor ZIP. |
| **Invoke-HDICmdScript** |Spusťte skript z cmd.exe. |
| **Write-HDILog** |Zapište výstup z vlastní skript používané pro akci skriptu. |
| **Get-Services** |Získáte seznam služeb, které jsou spuštěny na počítači, kde se skript spustí. |
| **Get-Service** |S názvem konkrétní služby jako vstup, získat podrobné informace pro konkrétní službu (název služby, zpracovat ID, stavu, atd.) v počítači, kde se skript spustí. |
| **Get-HDIServices** |Získejte seznam HDInsight služby spuštěné v počítači, kde se skript spustí. |
| **Get-HDIService** |S konkrétním názvem služby HDInsight jako vstup, získat podrobné informace pro konkrétní službu (název služby, zpracovat ID, stavu, atd.) v počítači, kde se skript spustí. |
| **Get-ServicesRunning** |Získání seznamu služeb, které jsou spuštěné v počítači, kde se skript spustí. |
| **Get-ServiceRunning** |Zkontrolujte, zda specifické služby (podle názvu) je spuštěn v počítači, kde se skript spustí. |
| **Get-HDIServicesRunning** |Získejte seznam HDInsight služby spuštěné v počítači, kde se skript spustí. |
| **Get-HDIServiceRunning** |Zkontrolujte, zda specifické služby HDInsight (podle názvu) je spuštěn v počítači, kde se skript spustí. |
| **Get-HDIHadoopVersion** |Získá verzi Hadoop nainstalovaná na počítači, kde se skript spustí. |
| **Test IsHDIHeadNode** |Zkontrolujte, jestli je počítač, kde se skript spustí hlavního uzlu. |
| **Test IsActiveHDIHeadNode** |Zkontrolujte, jestli je počítač, kde se skript spustí active hlavního uzlu. |
| **Test IsHDIDataNode** |Zkontrolujte, jestli je počítač, kde se skript spustí datový uzel. |
| **Edit-HDIConfigFile** |Upravte konfigurační soubory hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml nebo yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Osvědčené postupy pro vývoj skriptů
Při vývoji vlastních skriptů pro cluster služby HDInsight, existuje několik doporučených postupech pro mějte na paměti:

* Kontrola verze Hadoop

    Pouze HDInsight verze 3.1 (Hadoop 2.4) a vyšší podporu pomocí akce skriptu k instalaci vlastní součásti v clusteru. Ve vašem vlastního skriptu, je nutné použít **Get-HDIHadoopVersion** pomocnou metodu, zkontrolujte verzi Hadoop, než budete pokračovat v provádění další úloh ve skriptu.
* Zadejte stabilní odkazy na zdroje skriptu

    Uživatelé měli ujistit, všechny skripty a další artefaktů použít do vlastního nastavení clusteru s podporou zůstaly dostupné v celém dobu životnosti clusteru a že verze těchto souborů se po dobu trvání nemění. Pokud obnovování uzly v clusteru se vyžaduje, je nutné tyto prostředky. Osvědčeným postupem je ke stažení a archivaci vše v účtu úložiště, který uživatelské ovládací prvky. Tento účet může být výchozí účet úložiště nebo některé z dalších účtů úložiště, zadaný v době nasazení pro vlastní cluster.
    V Spark a R přizpůsobit clusteru ukázky předpokladu, dokumentace, například, že je místní kopie prostředků v rámci tohoto účtu úložiště: https://hdiconfigactions.blob.core.windows.net/.
* Zajistěte, aby byl skript přizpůsobení clusteru idempotent

    Měli očekávat, že jsou uzly clusteru služby HDInsight obnovit z Image po dobu životnosti clusteru. Spuštění skriptu přizpůsobení clusteru vždy, když je obnovit z Image clusteru. Tento skript musí být navržena pro uzpůsobeny idempotent v tom smyslu, že při obnovování, skript by měl zajištění, že clusteru je vráceny do stejného stavu, který byl právě po skript spustili poprvé původně vytvoření clusteru. Například pokud vlastní skript nainstalován při prvním spuštění aplikace v D:\AppLocation, pak na každé následné spuštění při obnovování, skript by měl zkontrolujte, zda aplikace existuje v umístění D:\AppLocation předtím, než budete pokračovat v dalších krocích skript.
* Vlastní součásti nainstalovat na optimální umístění

    Pokud uzly clusteru se obnoví z Image, jednotku C:\ prostředků a systémové jednotce D:\ můžete naformátována, což vede ke ztrátě dat a aplikací nainstalovaných na těchto jednotkách. Tato ztráta může také dojít, pokud do virtuálních počítačů (VM) Azure uzlu, který je součástí clusteru přestane fungovat a bude nahrazen nový uzel. Součásti můžete nainstalovat na jednotku D:\, nebo v umístění C:\apps v clusteru. Všech jiných umístění na jednotce C:\ jsou vyhrazené. Zadejte umístění, kde aplikace nebo knihovny se nainstalují ve skriptu přizpůsobení clusteru.
* Zajištění vysoké dostupnosti architektury clusteru

    HDInsight má aktivní – pasivní architekturu pro vysokou dostupnost, ve kterém jednou z hlavního uzlu je v aktivním režimu (které jsou spuštěny služby HDInsight) a z hlavního uzlu je v pohotovostním režimu (v HDInsight, které nejsou spuštěny služby). Uzly přepínače aktivní a pasivní režim, pokud jsou přerušení služby HDInsight. Pokud akce skriptu se používá k instalaci služby na obou head uzlů pro vysokou dostupnost, Všimněte si, že není možné automaticky převzít tyto služby uživatel nainstaloval mechanismus převzetí služeb při selhání HDInsight. Proto uživatel nainstaloval služeb v HDInsight hlavních uzlech, které jsou očekávané k zajištění vysoké dostupnosti musí mít vlastní mechanismus převzetí služeb při selhání, pokud v režimu aktivní pasivní nebo v režimu aktivní aktivní.

    Příkaz akce skriptu HDInsight spustí na obou hlavních uzlech při roli Hlavní uzel je zadán jako hodnota v *ClusterRoleCollection* parametr. Proto při návrhu vlastní skript, ujistěte se, že váš skript známa tento instalační program. Nespouštějte k potížím, kde jsou stejné služby instalaci a spuštění na obou hlavních uzlech a jejich skončili neslučitelných mezi sebou. Navíc mějte na paměti, že dojde ke ztrátě dat během obnovování, takže softwaru nainstalované prostřednictvím akce skriptu musí být odolné vůči tyto události. Aplikace by měl být pro práci s vysokou dostupností data, která se distribuuje do mnoha uzly. Současně lze obnovit z Image až 1/5 uzlů v clusteru.
* Konfigurace vlastní součásti, které budou používat úložiště objektů Blob v Azure

    Vlastní komponenty, které instalujete na uzlech clusteru může mít výchozí konfiguraci, kterou chcete používat Hadoop Distributed File System (HDFS) úložiště. Měli byste změnit konfiguraci místo toho používat úložiště objektů Blob Azure. Na obnovení z Image clusteru systému souborů HDFS získá formátu a by ztratíte všechna data, která je uložena existuje. Použití úložiště objektů Blob v Azure místo toho zajišťuje, aby vaše data se uchovávají.

## <a name="common-usage-patterns"></a>Obecné vzory využití
Tato část obsahuje pokyny k implementaci některých běžných vzorů využití, které se mohou vyskytnout při zápisu vlastních skriptů.

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí
Často v vývoj akcí skriptů, si myslíte, že třeba nutnost nastavení proměnných prostředí. Například je nejpravděpodobnější scénář při stahování binární z externího webu, nainstalujte ji na clusteru a přidejte umístění, kde je instalována do proměnné prostředí vaší 'PATH'. Následující fragment kódu ukazuje, jak nastavení proměnných prostředí ve vlastních skriptů.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Tento příkaz nastaví proměnnou prostředí **MDS_RUNNER_CUSTOM_CLUSTER** na hodnotu "true" a také nastaví rozsah této proměnné můžete být celého systému. Je důležité, aby proměnné prostředí se nastavují v příslušné oboru – počítače nebo uživatele. Odkazovat [sem] [ 1] Další informace o nastavení proměnných prostředí.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Přístup k umístění, kde jsou uloženy vlastní skripty
Skripty použít pro přizpůsobení cluster musí buď nacházet ve výchozí účet úložiště pro cluster nebo ve veřejném kontejneru jen pro čtení na jiný účet úložiště. Pokud skript odkazuje na prostředky, které se nacházejí jinde prostředky musí být veřejně čitelné. Můžete například chtít přístup k souboru a uložte ho pomocí příkazu SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

V tomto příkladu, musíte zajistit, aby kontejneru `somecontainer` v účtu úložiště `somestorageaccount` veřejně přístupný. Skript, jinak hodnota vyhodí výjimku "Nebyl nalezen" a selhání.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Předat parametry do rutiny přidat AzureRmHDInsightScriptAction
Chcete-li předat do rutiny přidat AzureRmHDInsightScriptAction několik parametrů, je potřeba formátu řetězcovou hodnotu tak, aby obsahovala všechny parametry pro skript. Příklad:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

nebo

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Throw – výjimka pro nasazení clusteru se nezdařilo
Pokud chcete získat přesně informováni o nebylo úspěšné skutečnost, že přizpůsobení clusteru podle očekávání, je nutné vyvolat výjimku a selhání vytvoření clusteru. Můžete například chtít zpracovat soubor, pokud existuje a zpracování chyby případu, kdy soubor neexistuje. To by zajistěte, aby skript ukončí řádně a stav clusteru správně je známý. Následující fragment kódu poskytuje příklad toho, jak to můžete udělat:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

V tento fragment kódu Pokud soubor neexistuje, by vedlo ke stavu, kde skript ve skutečnosti řádně ukončí po tisku chybovou zprávu, a cluster dosáhne stavu spuštěno za předpokladu, že ji "úspěšně" dokončit proces přizpůsobení clusteru. Pokud chcete lépe informováni o skutečnost, že cluster přizpůsobení v podstatě se nezdařilo z důvodu chybějícího souboru očekávaným způsobem, je vhodnější a vyvolána výjimka, selžou krok přizpůsobení clusteru. K dosažení tohoto cíle musíte použít následující fragment kódu ukázka.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Kontrolní seznam pro nasazení akce skriptu
Zde jsou kroky, které jsme trvalo při přípravě nasazení těchto skriptů:

1. Uveďte soubory, které obsahují vlastní skripty na místě, která je přístupná na uzlech clusteru během nasazení. To může být libovolná z výchozí nebo další účty úložiště zadaný v době nasazení clusteru nebo jiných veřejně přístupná úložiště kontejneru.
2. Přidejte kontroly na skripty a ujistěte se, že spouštění idempotently, tak, aby skript můžete spustit několikrát na stejném uzlu.
3. Použití `Write-Output` rutiny Azure Powershellu tisknout do STDOUT a také STDERR. Nepoužívejte `Write-Host`.
4. Použijte složku dočasného souboru, jako například `$env:TEMP`, abyste mohli udržovat stažený soubor používá skripty a pak vyčištění je po mají spouštět skripty.
5. Nainstalujte jenom na D:\ nebo C:\apps vlastní software. Jiných umístění na jednotce C: není vhodné používat, protože se jedná o vyhrazené. Instalace souborů na jednotce C: mimo složku C:\apps může vést k selhání instalace během reimages uzlu.
6. V případě, že nastavení na úrovni operačního systému nebo Hadoop služby konfigurační soubory se změnily, můžete tak, aby se vyzvedávat všechna nastavení, úrovni operačního systému, jako je například proměnné prostředí, které jsou nastavené ve skriptech restartovat služby HDInsight.

## <a name="debug-custom-scripts"></a>Ladění vlastních skriptů
Spolu s další výstupu v výchozí účet úložiště, který jste zadali pro clusteru při jeho vytváření jsou uložené v souborech protokolů chyb skriptu. Protokoly jsou uložené v tabulce s názvem *u < \cluster-name-fragment >< \time-stamp > setuplog*. Toto jsou agregovaná protokoly, které mají záznamy ze všech uzlů (hlavního uzlu a pracovní uzly), na kterých bude skript spuštěn v clusteru.

Snadný způsob, jak v protokolech je používat nástroje HDInsight pro Visual Studio. Instalace nástrojů, najdete v části [začněte používat nástroje Visual Studio Hadoop pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Zkontrolujte protokol pomocí sady Visual Studio**

1. Otevřete sadu Visual Studio.
2. Klikněte na tlačítko **zobrazení**a potom klikněte na **Průzkumníka serveru**.
3. Klikněte pravým tlačítkem na "Azure", klikněte na tlačítko Připojit k **předplatná Microsoft Azure**a pak zadejte svoje přihlašovací údaje.
4. Rozbalte položku **úložiště**, rozbalte účet úložiště Azure používat jako výchozí systém souborů, rozbalte položku **tabulky**a potom dvakrát klikněte na název tabulky.

Můžete také vzdáleného do uzlů clusteru zobrazíte STDOUT a STDERR pro vlastní skripty. Protokoly na každém uzlu jsou určené jenom pro tento uzel a jsou zaznamenány do **C:\HDInsightLogs\DeploymentAgent.log**. Tyto soubory protokolu zaznamenejte všechny výstupy z vlastních skriptů. Fragment kódu příklad protokolu pro akci skriptu Spark vypadá takto:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Tento protokol je jasné, že akce skriptu Spark byla provedena ve virtuálním počítači s názvem HEADNODE0 a že žádné výjimky došlo k během provádění.

V případě, že dojde k chybě provádění, výstup popisující je také součástí tohoto souboru protokolu. Informací uvedených v těchto protokolech by měl být užitečné při ladění skriptu problémy, které by mohlo dojít.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přizpůsobení clusterů HDInsight pomocí akce skriptu][hdinsight-cluster-customize]
* [Nainstalovat a používat Spark v HDInsight clustery][hdinsight-install-spark]
* [Nainstalovat a používat Solr v clusterech HDInsight](hdinsight-hadoop-solr-install.md).
* [Nainstalovat a používat Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
