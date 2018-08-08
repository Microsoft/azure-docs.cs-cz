---
title: Vývoj akcí skriptů v HDInsight – Azure
description: Zjistěte, jak přizpůsobit clustery Hadoop pomocí skriptových akcí. Akce skriptu lze použít k instalaci dalšího softwaru, které běží na clusteru Hadoop nebo změnit konfiguraci aplikace nainstalované v clusteru.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: f26a11e623148eecc8096b45c6ac9df68b8e102a
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599152"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Vývoj skriptových akcí skriptů pro clustery se systémem Windows pro HDInsight
Zjistěte, jak psát skripty akci skriptu pro HDInsight. Informace o pomocí skriptových akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md). Stejného článku napsané pro clustery HDInsight se systémem Linux, najdete v části [vývoj skriptových akcí skriptů pro HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat pouze pro clustery HDInsight se systémem Windows. HDInsight je dostupná jenom ve Windows pro verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o použití akcí skriptů v clusterech se systémem Linux najdete v tématu [vývoj akcí skriptů v HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



Akce skriptu lze použít k instalaci dalšího softwaru, které běží na clusteru Hadoop nebo změnit konfiguraci aplikace nainstalované v clusteru. Akce skriptů jsou skripty, které běží na uzlech clusteru při nasazení clusterů HDInsight, a proveden, jakmile uzly v clusteru dokončete konfiguraci HDInsight. Akce skriptu se spouští v oprávnění k účtu správce systému a poskytuje úplná přístupová práva k uzlům clusteru. Každý cluster můžete zadat seznam akcí skriptů, které mají být provedeny v pořadí, ve kterém jsou uvedeny.

> [!NOTE]
> Pokud se setkáte se následující chybová zpráva:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: Termín 'Save-HDIFile' nebyl rozpoznán jako název rutiny, funkce, soubor skriptu nebo spustitelného programu. Zkontrolujte, zda název, nebo pokud cesty byl zahrnut, ověřte správnost cesty a zkuste to znovu.
> Je, protože jste nezahrnuli pomocné metody.  Zobrazit [pomocné metody pro vlastní skripty](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Ukázkové skripty
Akci skriptu pro vytváření clusterů HDInsight v operačním systému Windows, je skript Azure Powershellu. Následující skript je ukázka konfigurace lokality konfigurační soubory:

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

Skript používá čtyři parametry, název konfiguračního souboru, vlastnosti, kterou chcete upravit, hodnota, kterou chcete nastavit a popis. Příklad:

    hive-site.xml hive.metastore.client.socket.timeout 90

Tyto parametry nastavte hive.metastore.client.socket.timeout hodnotu na 90 v souboru hive-site.xml.  Výchozí hodnota je 60 sekund.

Tento ukázkový skript najdete také v [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight poskytuje několik skriptů k instalaci dalších komponent v clusterech HDInsight:

| Název | Skript |
| --- | --- |
| **Nainstalovat Spark** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Zobrazit [instalace a použití Spark na HDInsight clusterů][hdinsight-install-spark]. |
| **Nainstalovat jazyk R** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Zobrazit [instalace a použití R na clusterech HDInsight](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **Nainstalovat Solr** | `https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1`. Zobrazit [instalace a použití Solru na HDInsight clusterů](hdinsight-hadoop-solr-install.md). |
| **Nainstalovat Giraph** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Zobrazit [instalace a použití Giraphu na HDInsight clusterů](hdinsight-hadoop-giraph-install.md). |
| **Přednačíst knihovny Hive** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Zobrazit [knihovny přidat Hive v clusterech HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


Akce skriptu je možné nasadit z portálu Azure portal, prostředí Azure PowerShell nebo pomocí sady HDInsight .NET SDK.  Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu][hdinsight-cluster-customize].

> [!NOTE]
> Ukázkové skripty fungují jenom s verze clusteru HDInsight verze 3.1 nebo vyšší. Další informace o verzích clusterů HDInsight, naleznete v tématu [verze clusterů HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Pomocné metody pro vlastní skripty
Pomocné metody akcí skriptů jsou nástroje, které můžete použít při zápisu vlastních skriptů. Tyto metody jsou definovány v [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)a mohou být zahrnuty do skriptu následující ukázka:

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

Tady jsou pomocné metody, které jsou k dispozici v tento skript:

| Pomocná metoda | Popis |
| --- | --- |
| **Save-HDIFile** |Stáhne soubor do umístění na místním disku, který je přidružený k uzlu virtuálního počítače Azure se přiřazuje z zadaný identifikátor URI (Uniform Resource). |
| **Expand-HDIZippedFile** |Rozbalte soubor ZIP. |
| **Invoke-HDICmdScript** |Spusťte skript z cmd.exe. |
| **Write-HDILog** |Zapisovat výstup z vlastního skriptu používané pro akci skriptu. |
| **Get-Services** |Získáte seznam služeb, které běží na počítači, ve kterém se skript spustí. |
| **Get-Service** |Název konkrétní služby jako vstup, získat podrobné informace pro konkrétní službu (název služby, zpracování ID, stav, atd.) na počítači, ve kterém se skript spustí. |
| **Get-HDIServices** |Získáte seznam služeb HDInsight, které jsou spuštěné v počítači, ve kterém se skript spustí. |
| **Get-HDIService** |S konkrétním názvem služby HDInsight jako vstup, získat podrobné informace pro konkrétní službu (název služby, zpracování ID, stav, atd.) na počítači, ve kterém se skript spustí. |
| **Get-ServicesRunning** |Získání seznamu služeb, které jsou spuštěny v počítači, ve kterém se skript spustí. |
| **Get-ServiceRunning** |Kontrola, zda konkrétní službu (podle názvu) je spuštěná v počítači, ve kterém se skript spustí. |
| **Get-HDIServicesRunning** |Získáte seznam služeb HDInsight, které jsou spuštěné v počítači, ve kterém se skript spustí. |
| **Get-HDIServiceRunning** |Kontrola, zda konkrétní služby HDInsight (podle názvu) je spuštěná v počítači, ve kterém se skript spustí. |
| **Get-HDIHadoopVersion** |Získání verze nainstalované na počítači, ve kterém se skript spustí Hadoop. |
| **Test-IsHDIHeadNode** |Zkontrolujte, zda je počítač, ve kterém se skript spustí hlavního uzlu. |
| **Test-IsActiveHDIHeadNode** |Zkontrolujte, zda je počítač, ve kterém se skript spustí aktivní hlavní uzel. |
| **Test-IsHDIDataNode** |Zkontrolujte, zda je počítač, ve kterém se skript spustí datový uzel. |
| **Edit-HDIConfigFile** |Upravte konfigurační soubory hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml nebo yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Osvědčené postupy pro vývoj skriptů
Když vyvíjíte vlastní skript pro HDInsight cluster, existuje několik osvědčených postupů brát v úvahu:

* Kontrola verze systému Hadoop

    Jenom HDInsight verze 3.1 (Hadoop 2.4) a vyšší podporu pomocí akce skriptu k instalaci vlastních komponent clusteru. Vlastní skript, je nutné použít **Get-HDIHadoopVersion** pomocnou metodu, pokud chcete zkontrolovat verzi Hadoop před pokračováním v provádění dalších úloh ve skriptu.
* Stabilní odkazy skriptu prostředků

    Uživatelé měli ujistit, že všechny skripty a další artefakty používaných pro přizpůsobení clusteru nadále k dispozici v průběhu životního cyklu clusteru a, že verze těchto souborů se nezmění po dobu trvání. Tyto prostředky jsou povinné, pokud obnovování z Image uzly v clusteru se vyžaduje. Osvědčeným postupem je ke stažení a archivovat vše, co v účtu úložiště, který řídí uživatele. Tento účet může být výchozí účet úložiště nebo některé z dalších účtů úložiště zadaný v době nasazování pro vlastní cluster.
    Ve Spark a R přizpůsobit clusteru ukázky, dokumentace, například, pokud je místní kopie prostředků v tomto účtu úložiště: https://hdiconfigactions.blob.core.windows.net/.
* Ujistěte se, že cluster přizpůsobení skript je idempotentní

    Musíte očekávat, že uzly clusteru HDInsight se znovu připojit bitovou kopii po celou dobu životnosti clusteru. Přizpůsobení skript clusteru se spustí pokaždé, když clusteru je obnoví z Image. Tento skript musí být navržen tak, že přizpůsobit idempotentní v tom smyslu, že při obnovování z Image, skript se ujistěte, že cluster se vrátí do stejného stavu, ve kterém byla poslední po skript spustili poprvé, kdy byla původně vytvořena clusteru. Například pokud vlastní skript nainstalovat aplikace na D:\AppLocation při prvním spuštění, na každé následné spuštění, při obnovování z Image, skript by měl zkontrolujte, jestli aplikace existuje v umístění D:\AppLocation předtím, než budete pokračovat s jinými kroky skript.
* Instalace vlastních součástech optimální umístění

    Když se uzly clusteru se znovu připojit bitovou kopii, prostředek jednotky C:\ a systémové jednotce D:\ můžete naformátována, čímž dojde ke ztrátě dat a aplikace nainstalované v těchto jednotek. To může také dojít, pokud ocitne mimo provoz do virtuálních počítačů (VM) Azure uzlu, který je součástí clusteru a nahrazuje nový uzel. Součásti můžete nainstalovat na jednotku D:\, nebo v umístění C:\apps v clusteru. Všechna místa na jednotce C:\, jsou vyhrazena. Zadejte umístění, kde se aplikace nebo knihovny nainstalovaný v clusteru přizpůsobení skriptu.
* Zajištění vysoké dostupnosti architektury clusteru

    HDInsight obsahuje architekturu aktivní-pasivní vysoká dostupnost pro zajištění vysoké dostupnosti, ve kterém jedním z hlavního uzlu je v aktivním režimu (ve kterém jsou spuštěny služby HDInsight) a k hlavnímu uzlu je v pohotovostním režimu (v HDInsight, které nejsou spuštěny). Uzly mezi aktivními a pasivními režimy přepínat, pokud jsou přerušení služby HDInsight. Pokud akci skriptu se používá k instalaci služby na oba hlavní uzly pro zajištění vysoké dostupnosti, mějte na paměti, že mechanismus převzetí služeb při selhání HDInsight není možné automaticky převzít služby při selhání tyto služby uživatelé nainstalují. Proto uživatel nainstaloval služby na HDInsight hlavní uzly, u kterých se očekává k zajištění vysoké dostupnosti musí mít své vlastní mechanismus převzetí služeb při selhání, pokud v režimu aktivní pasivní nebo v režimu aktivní aktivní.

    Příkaz akce skriptu HDInsight spustí na oba hlavní uzly při roli Hlavní uzel je zadán jako hodnotu *ClusterRoleCollection* parametru. Proto při navrhování vlastních skriptů, ujistěte se, že váš skript se vědět, toto nastavení. Byste neměli spouštět na problémy, kde je nainstalovaný a spuštěný oba hlavní uzly na stejné služby a jejich skončit vzájemně konkurují. Také mějte na paměti, že data nejsou ztracena během obnovování z Image, takže software nainstalován pomocí akce skriptu musí být odolné vůči tyto události. Aplikace by se měly navrhovat pro práci s daty s vysokou dostupností, která je distribuovaná napříč mnoha uzly. Až 1/5 uzlů v clusteru můžete obnovit z Image ve stejnou dobu.
* Konfigurace vlastních součástech používání úložiště objektů Blob v Azure

    Vlastní komponenty, které instalujete na uzlech clusteru může být výchozí konfiguraci, kterou chcete použít soubor systému HDFS (Hadoop Distributed) úložiště. Měli byste změnit konfiguraci místo toho použít úložiště objektů Blob v Azure. Na clusteru obnovení z Image získá ve formátu systému souborů HDFS a ztratí všechna data, která je uložena existuje. Použití úložiště objektů Blob v Azure místo toho zajistí, že vaše data uchovávat.

## <a name="common-usage-patterns"></a>Běžné vzory využití
Tato část obsahuje pokyny k implementaci některých běžných vzorů využití, které můžete narazit na při zápisu vlastních skriptů.

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí
Často v vývoj skriptových akcí, máte pocit, že potřeba nastavit proměnné prostředí. Nejpravděpodobnější situací pro instanci, je při stahování binární soubor z externího, nainstalujte ho na clusteru a přidejte umístění, kde je nainstalovaný do proměnné prostředí 'PATH'. Následující fragment kódu ukazuje, jak nastavit proměnné prostředí ve vlastních skriptů.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Tento příkaz nastaví proměnnou prostředí **MDS_RUNNER_CUSTOM_CLUSTER** na hodnotu "true" a také nastaví rozsah této proměnné na úrovni počítače. Je důležité, aby proměnné prostředí se nastavují v oboru odpovídající – počítače nebo uživatele. Přečtěte si [tady] [ 1] Další informace o nastavení proměnných prostředí.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Přístup k umístění, kde jsou uloženy vlastních skriptů
Skripty používané k úpravám cluster musí buď být ve výchozí účet úložiště pro cluster nebo ve veřejném kontejneru jen pro čtení na jiný účet úložiště. Pokud vaše skripty využívají prostředky jinde prostředky musí být veřejně čitelné. Můžete například chtít přístup k souboru a uložit ho pomocí příkazu SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

V tomto příkladu musíte zajistit, aby kontejneru `somecontainer` v účtu úložiště `somestorageaccount` je veřejně dostupná. V opačném případě skript výjimku "Nebyl nalezen" a selhání.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Předání parametrů do rutiny Add-AzureRmHDInsightScriptAction
K rutině Add-AzureRmHDInsightScriptAction předat více parametrů, budete muset formátování řetězcovou hodnotu obsahující všechny parametry skriptu. Příklad:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

nebo

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Vyvolat výjimku pro nasazení clusteru se nezdařilo
Pokud chcete získat přesné upozorněni nebyla úspěšná na skutečnost, že přizpůsobení clusteru podle očekávání, je potřeba vytvořit výjimku a navrácení služeb po vytvoření clusteru. Můžete například chtít zpracovat soubor, pokud existuje a zpracování chyb případu, pokud soubor neexistuje. To zajistí, že skript řádně ukončí a stav clusteru správně se označuje. Následující fragment kódu obsahuje příklad toho, jak toho dosáhnout:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

V tomto fragmentu kódu Pokud soubor neexistuje, by vedlo ke stavu, ve kterém skript ve skutečnosti řádně ukončí potom, co Tisk chybové zprávě a cluster dosáhne spuštěném stavu, za předpokladu, že "úspěšně" dokončit proces přizpůsobení clusteru. Pokud chcete přesně upozorněni na skutečnost, že přizpůsobení v podstatě clusteru se nezdařilo z důvodu chybějícího souboru očekávaným způsobem, je vhodnější vyvolat výjimku a navrácení služeb po kroku vlastního nastavení clusteru. Toho lze dosáhnout musíte použít následující fragment kódu ukázky.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Kontrolní seznam pro nasazení skriptových akcí
Tady jsou kroky, které jsme podnikli při přípravě nasazení těchto skriptů:

1. Umístěte soubory, které obsahují vlastní skripty na místě, které je přístupné uzly clusteru během nasazení. To může být výchozí nebo další účty úložiště zadaný v době nasazení clusteru nebo jiném kontejneru veřejně dostupné úložiště.
2. Přidáte kontroly na skripty, abyste měli jistotu, že se provedou idempotently, tak, aby skript můžete spustit více než jednou ve stejném uzlu.
3. Použití `Write-Output` rutiny Azure Powershellu pro tisk do STDOUT i STDERR. Nepoužívejte `Write-Host`.
4. Použít složce dočasných souborů, například `$env:TEMP`, abyste mohli zachovat staženého souboru, které skripty používají a následnému vyčištění po jejich po uzavřeli dohodu o skripty.
5. Instalujte vlastní software pouze na D:\ nebo C:\apps. Ty jsou vyhrazené se nemá používat jiné umístění na jednotce C:. Instalace souborů na jednotce C: mimo složku C:\apps může způsobit chyby instalačního programu při obnoví uzlu.
6. V případě, že nastavení na úrovni operačního systému nebo Hadoop služby konfigurační soubory byly změněny, můžete chtít restartujte služby HDInsight, takže můžete vybrat nastavení jakékoli úrovni operačního systému, jako jsou proměnné prostředí nastavené ve skriptech.

## <a name="debug-custom-scripts"></a>Ladit skripty
Protokoly chyb skript ukládají společně s další výstup do výchozího účtu úložiště, který jste zadali pro clusteru při jeho vytvoření. Protokoly se ukládají v tabulce s názvem *u < \cluster-name-fragment >< \time-stamp > setuplog*. Jedná se o agregované protokoly, které mají záznamy ze všech uzlů (hlavní uzel a pracovní uzly), na kterých se skript spustí v clusteru.

Použití nástrojů HDInsight pro Visual Studio je snadný způsob, jak v protokolech. Instalace nástrojů, naleznete v tématu [začněte používat nástroje Visual Studio Hadoop pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Chcete-li zkontrolovat protokol, pomocí sady Visual Studio**

1. Otevřete sadu Visual Studio.
2. Klikněte na tlačítko **zobrazení**a potom klikněte na tlačítko **Průzkumníka serveru**.
3. Klikněte pravým tlačítkem na "Azure", klikněte na připojit k **předplatná Microsoft Azure**a pak zadejte svoje přihlašovací údaje.
4. Rozbalte **úložiště**, rozbalte účet úložiště Azure používat jako výchozí systém souborů, rozbalte položku **tabulky**a potom dvakrát klikněte na název tabulky.

Můžete také vzdálené do uzlů clusteru, čímž zobrazíte STDOUT a STDERR pro vlastní skripty. Protokoly na každém uzlu jsou určené jenom pro tento uzel a jsou zaznamenány do **C:\HDInsightLogs\DeploymentAgent.log**. Tyto soubory protokolu zaznamenat všechny výstupy z vlastního skriptu. Fragment kódu příkladu protokolu pro akci skriptu Spark vypadá takto:

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


V tomto protokolu je jasné, že byla provedena akce skriptu Spark na virtuálním počítači s názvem HEADNODE0 a že žádné výjimky byly vyvolány během provádění.

V případě, že dojde k selhání spuštění, výstup popisující ho také obsažené v tomto souboru protokolu. Informace uvedené v těchto protokolech by měl být užitečné při ladění skriptu problémy, které mohou nastat.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přizpůsobení clusterů HDInsight pomocí skriptových akcí][hdinsight-cluster-customize]
* [Instalace a použití Sparku na clusterech HDInsight][hdinsight-install-spark]
* [Instalace a použití Solru na clusterech HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalace a použití Giraphu na clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
