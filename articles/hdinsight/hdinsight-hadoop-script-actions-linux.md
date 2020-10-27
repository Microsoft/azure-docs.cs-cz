---
title: Vývoj akcí skriptů pro přizpůsobení clusterů Azure HDInsight
description: Naučte se používat skripty bash k přizpůsobení clusterů HDInsight. Akce skriptu umožňují spouštět skripty během nebo po vytvoření clusteru, aby bylo možné změnit nastavení konfigurace clusteru nebo nainstalovat další software.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: c392ad7a098116a8f2224d6844d38dc40e01d753
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545986"
---
# <a name="script-action-development-with-hdinsight"></a>Vývoj akcí skriptů pomocí HDInsight

Naučte se, jak pomocí skriptů bash přizpůsobit cluster HDInsight. Akce skriptu představují způsob přizpůsobení služby HDInsight během nebo po vytvoření clusteru.

## <a name="what-are-script-actions"></a>Co jsou akce skriptů

Akce skriptů jsou skripty bash, které Azure spouští na uzlech clusteru, aby mohl provádět změny konfigurace nebo instalovat software. Akce skriptu se spouští jako kořenová a poskytuje přístup k uzlům clusteru s úplnými oprávněními.

Akce skriptu lze použít prostřednictvím následujících metod:

| Tuto metodu použijte, chcete-li použít skript... | Během vytváření clusteru... | Ve spuštěném clusteru... |
| --- |:---:|:---:|
| portál Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure Classic CLI |&nbsp; |✓ |
| Sada HDInsight .NET SDK |✓ |✓ |
| Šablona Azure Resource Manageru |✓ |&nbsp; |

Další informace o použití těchto metod pro aplikování akcí skriptu najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Osvědčené postupy pro vývoj skriptů

Při vývoji vlastního skriptu pro cluster HDInsight je potřeba mít na paměti několik osvědčených postupů:

* [Cílová verze Apache Hadoop](#bPS1)
* [Cíl verze operačního systému](#bps10)
* [Poskytování stabilních odkazů na zdroje skriptů](#bPS2)
* [Použití předem kompilovaných prostředků](#bPS4)
* [Zajistěte, aby byl skript vlastního nastavení clusteru idempotentní.](#bPS3)
* [Zajištění vysoké dostupnosti architektury clusteru](#bPS5)
* [Konfigurace vlastních komponent pro použití služby Azure Blob Storage](#bPS6)
* [Zápis informací do STDOUT a STDERR](#bPS7)
* [Uložení souborů jako ASCII pomocí konců řádků LF](#bps8)
* [Pro obnovení z přechodných chyb použít logiku opakování](#bps9)

> [!IMPORTANT]  
> Akce skriptu musí být dokončeny během 60 minut nebo se proces nezdařil. Během zřizování uzlů se skript spouští souběžně s jinými procesy nastavení a konfigurace. Soutěž na prostředky, jako je čas procesoru nebo šířka pásma sítě, může způsobit, že se dokončení skriptu bude trvat déle, než ve vývojovém prostředí.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Cílová verze Apache Hadoop

Různé verze služby HDInsight mají nainstalované různé verze služeb a součástí systému Hadoop. Pokud váš skript očekává určitou verzi služby nebo komponenty, měli byste použít jenom skript s verzí HDInsight, která zahrnuje požadované součásti. Můžete najít informace o verzích komponent, které jsou součástí HDInsight, pomocí dokumentu [správy verzí komponent HDInsight](hdinsight-component-versioning.md) .

### <a name="checking-the-operating-system-version"></a>Kontrola verze operačního systému

Různé verze HDInsight spoléhají na konkrétní verze Ubuntu. Mezi verzemi operačních systémů, které musíte ve svém skriptu vyhledat, můžou být rozdíly. Například může být nutné nainstalovat binární soubor, který je svázán s verzí Ubuntu.

Chcete-li zjistit verzi operačního systému, použijte `lsb_release` . Například následující skript ukazuje, jak odkazovat na konkrétní soubor tar v závislosti na verzi operačního systému:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> Cílení na verzi operačního systému

HDInsight je založený na distribuci Ubuntu Linux. Různé verze služby HDInsight spoléhají na různé verze Ubuntu, což může změnit způsob, jakým se skript chová. Například HDInsight 3,4 a starší jsou založené na verzích Ubuntu, které používají příkaz Spustit jako. Verze 3,5 a vyšší jsou založené na Ubuntu 16,04, který používá systém. Systém a spuštění jsou závislé na různých příkazech, takže by měl být vytvořen skript pro práci s oběma.

Dalším důležitým rozdílem mezi HDInsight 3,4 a 3,5 je to, že `JAVA_HOME` teď odkazuje na Java 8. Následující kód demonstruje, jak zjistit, jestli skript běží na Ubuntu 14 nebo 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Úplný skript, který obsahuje tyto fragmenty kódu, můžete najít na adrese https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

Verze Ubuntu, kterou používá HDInsight, najdete v dokumentu [verze součásti HDInsight](hdinsight-component-versioning.md) .

Chcete-li porozumět rozdílům mezi systémem a nastartem, přečtěte si téma [systém pro uživatele pro zahájení](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Poskytování stabilních odkazů na zdroje skriptů

Skript a přidružené prostředky musí zůstat dostupné po celou dobu životnosti clusteru. Tyto prostředky jsou požadovány v případě, že během operace škálování dojde k přidání nových uzlů do clusteru.

Osvědčeným postupem je stažení a archivace všeho v účtu Azure Storage v rámci vašeho předplatného.

> [!IMPORTANT]  
> Použitý účet úložiště musí být výchozím účtem úložiště pro cluster nebo veřejným kontejnerem jen pro čtení v jakémkoli jiném účtu úložiště.

Například ukázky poskytované společností Microsoft jsou uloženy v [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) účtu úložiště. Toto umístění je veřejný kontejner, který je jen pro čtení a který je spravován týmem HDInsight.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Použití předem kompilovaných prostředků

Chcete-li zkrátit dobu potřebnou ke spuštění skriptu, vyhněte se operacím, které zkompiluje prostředky ze zdrojového kódu. Představte si například předkompilování prostředků a uložte je do objektu blob Azure Storage účtu ve stejném datovém centru jako HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Zajistěte, aby byl skript vlastního nastavení clusteru idempotentní.

Skripty musí být idempotentní. Pokud se skript spouští několikrát, měl by cluster vracet do stejného stavu kdykoli.

Například skript, který upravuje konfigurační soubory, by neměl přidat duplicitní položky, pokud se spustilo několikrát.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Zajištění vysoké dostupnosti architektury clusteru

Clustery HDInsight se systémem Linux poskytují dva hlavní uzly, které jsou aktivní v rámci clusteru, a akce skriptů spouštěné na obou uzlech. Pokud komponenty, které nainstalujete, očekávají jenom jeden hlavní uzel, neinstalujte komponenty na oba hlavní uzly.

> [!IMPORTANT]  
> Služby poskytované jako součást služby HDInsight jsou navržené tak, aby v případě potřeby převzaly mezi oběma hlavními uzly. Tato funkce se nerozšířila na vlastní součásti nainstalované prostřednictvím akcí skriptů. Pokud potřebujete vysokou dostupnost pro vlastní komponenty, musíte implementovat vlastní mechanismus převzetí služeb při selhání.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Konfigurace vlastních komponent pro použití služby Azure Blob Storage

Komponenty, které nainstalujete na cluster, můžou mít výchozí konfiguraci, která používá úložiště Apache Hadoop systém souborů DFS (Distributed File System) (HDFS). HDInsight používá jako výchozí úložiště buď Azure Storage, nebo Data Lake Storage. Oba poskytují systém souborů kompatibilní se systémem HDFS, který uchovává data i v případě, že se cluster odstraní. Možná budete muset nakonfigurovat komponenty, které nainstalujete, aby se místo HDFS používaly WASB nebo ADL.

Pro většinu operací nemusíte zadávat systém souborů. Následující příkaz například zkopíruje soubor Hadoop-Common. jar z místního systému souborů do úložiště clusteru:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

V tomto příkladu `hdfs` příkaz transparentně používá výchozí úložiště clusteru. Pro některé operace možná budete muset zadat identifikátor URI. Například `adl:///example/jars` pro Azure Data Lake Storage Gen1 pro `abfs:///example/jars` Data Lake Storage Gen2 nebo `wasb:///example/jars` pro Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Zápis informací do STDOUT a STDERR

HDInsight protokoluje výstup skriptu, který je zapsaný do STDOUT a STDERR. Tyto informace můžete zobrazit pomocí webového uživatelského rozhraní Ambari.

> [!NOTE]  
> Apache Ambari je k dispozici pouze v případě, že byl cluster úspěšně vytvořen. Použijete-li během vytváření clusteru akci skriptu a vytváření se nepovede, přečtěte si téma [řešení potíží se skripty](./troubleshoot-script-action.md) v případě jiných způsobů přístupu k protokolovaným informacím.

Většina nástrojů a instalačních balíčků již zapisuje informace do STDOUT a STDERR, ale možná budete chtít přidat další protokolování. Chcete-li odeslat text do STDOUT, použijte `echo` . Příklad:

```bash
echo "Getting ready to install Foo"
```

Ve výchozím nastavení `echo` odešle řetězec do STDOUT. Pokud ho chcete směrovat do STDERR, přidejte ho do `>&2` `echo` . Příklad:

```bash
>&2 echo "An error occurred installing Foo"
```

Tento postup přesměruje informace zapsané do STDOUT na STDERR (2). Další informace o přesměrování v/v naleznete v tématu [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) .

Další informace o zobrazení informací protokolovaných akcemi skriptu najdete v tématu [řešení potíží se skripty](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Uložení souborů jako ASCII pomocí konců řádků LF

Bash skripty by měly být uložené ve formátu ASCII a řádky zakončené znakem LF. Soubory, které jsou uložené jako UTF-8, nebo v případě konce řádku použijte CRLF, může dojít k následující chybě:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Pro obnovení z přechodných chyb použít logiku opakování

Když stahujete soubory, instalujete balíčky pomocí apt-get nebo jiné akce, které přenášejí data prostřednictvím Internetu, může akce selhat kvůli přechodným chybám v síti. Například vzdálený prostředek, který komunikujete, může být v procesu převzetí služeb při selhání do záložního uzlu.

Aby váš skript byl odolný vůči přechodným chybám, můžete implementovat logiku opakování. Následující funkce ukazuje, jak implementovat logiku opakování. Opakuje operaci třikrát předtím, než vyprší jejich selhání.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Následující příklady ukazují, jak používat tuto funkci.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Pomocné metody pro vlastní skripty

Pomocné metody pro akce skriptu jsou nástroje, které můžete použít při psaní vlastních skriptů. Tyto metody jsou obsaženy ve [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skriptu. K jejich stažení a použití jako součást skriptu použijte následující:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Následující pomocníky jsou k dispozici pro použití ve skriptu:

| Použití pomocníka | Popis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Stáhne soubor ze zdrojového identifikátoru URI do zadané cesty k souboru. Ve výchozím nastavení nedojde k přepsání stávajícího souboru. |
| `untar_file TARFILE DESTDIR` |Extrahuje soubor tar (using `-xf` ) do cílového adresáře. |
| `test_is_headnode` |V případě, že je hlavní uzel clusteru spuštěný, vrátí 1; v opačném případě 0. |
| `test_is_datanode` |Pokud je aktuálním uzlem uzel data (Worker), vrátí hodnotu 1. v opačném případě 0. |
| `test_is_first_datanode` |Pokud je aktuální uzel prvním uzlem dat (Worker) (s názvem workernode0), vrátí hodnotu 1. v opačném případě 0. |
| `get_headnodes` |Vrátí plně kvalifikovaný název domény hlavních v clusteru. Názvy jsou odděleny čárkami. Při chybě se vrátí prázdný řetězec. |
| `get_primary_headnode` |Získá plně kvalifikovaný název domény primární hlavnímu uzlu. Při chybě se vrátí prázdný řetězec. |
| `get_secondary_headnode` |Získá plně kvalifikovaný název domény sekundárního hlavnímu uzluu. Při chybě se vrátí prázdný řetězec. |
| `get_primary_headnode_number` |Získá číselnou příponu primárního hlavnímu uzluu. Při chybě se vrátí prázdný řetězec. |
| `get_secondary_headnode_number` |Získá číselnou příponu sekundárního hlavnímu uzluu. Při chybě se vrátí prázdný řetězec. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Běžné vzorce použití

V této části najdete pokyny k implementaci některých běžných vzorů použití, ke kterým můžete při psaní vlastního skriptu použít.

### <a name="passing-parameters-to-a-script"></a>Předávání parametrů skriptu

V některých případech může skript vyžadovat parametry. Při použití REST API Ambari můžete například potřebovat heslo správce clusteru.

Parametry předané skriptu jsou známé jako *poziční parametry* a jsou přiřazeny k `$1` pro první parametr, `$2` pro druhý a tak dále. `$0` obsahuje název samotného skriptu.

Hodnoty předané skriptu jako parametry by měly být uzavřeny jednoduchými uvozovkami ('). Tím zajistíte, že předaná hodnota je považována za literál.

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Nastavení proměnné prostředí se provádí následujícím příkazem:

```bash
VARIABLENAME=value
```

WHERE proměnná je název proměnné. Pro přístup k proměnné použijte `$VARIABLENAME` . Například chcete-li přiřadit hodnotu poskytnutou pozičním parametrem jako proměnnou prostředí s názvem PASSWORD, použijte následující příkaz:

```bash
PASSWORD=$1
```

Následný přístup k informacím pak může použít `$PASSWORD` .

Proměnné prostředí nastavené v rámci skriptu existují pouze v rámci rozsahu skriptu. V některých případech může být nutné přidat proměnné prostředí v rámci systému, které budou zachovány po dokončení skriptu. Chcete-li přidat proměnné prostředí v rámci systému, přidejte proměnnou do `/etc/environment` . Například následující příkaz přidá `HADOOP_CONF_DIR` :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Přístup k umístěním, kde jsou uloženy vlastní skripty

Skripty používané k přizpůsobení clusteru musí být uloženy v jednom z následujících umístění:

* __Účet Azure Storage__ , který je spojený s clusterem.

* __Další účet úložiště__ spojený s clusterem.

* __Veřejně čitelný identifikátor URI__ . Například adresa URL pro data uložená na OneDrivu, Dropboxu nebo jiné službě hostování souborů.

* __Účet Azure Data Lake Storage__ , který je spojený s clusterem HDInsight. Další informace o použití Azure Data Lake Storage se službou HDInsight najdete v tématu [rychlý Start: nastavení clusterů ve službě HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Služba HDInsight instančního objektu používá pro přístup Data Lake Storage musí mít ke skriptu oprávnění ke čtení.

Prostředky používané skriptem musí být také veřejně dostupné.

Ukládání souborů v účtu Azure Storage nebo Azure Data Lake Storage poskytuje rychlý přístup v rámci sítě Azure.

> [!NOTE]  
> Formát identifikátoru URI, který se používá k odkazování na skript, se liší v závislosti na používané službě. Pro účty úložiště přidružené k clusteru HDInsight použijte `wasb://` nebo `wasbs://` . Pro veřejně čitelné identifikátory URI použijte `http://` nebo `https://` . Pro Data Lake Storage použijte `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Kontrolní seznam pro nasazení akce skriptu

Tady jsou kroky, které je potřeba provést při přípravě nasazení skriptu:

* Soubory, které obsahují vlastní skripty, umístěte do místa, které je přístupné pro uzly clusteru během nasazování. Například výchozí úložiště pro cluster. Soubory mohou být také uloženy ve veřejně čitelných hostitelských službách.
* Ověřte, že je skript idempotentní. To umožňuje, aby se skript ve stejném uzlu spustil víckrát.
* Pomocí dočasného adresářového souboru adresáře/TMP Udržujte stažené soubory používané skripty a pak je vyčistěte po provedení skriptů.
* Pokud se změní nastavení na úrovni operačního systému nebo konfigurační soubory služby Hadoop, budete možná chtít restartovat služby HDInsight.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Spuštění akce skriptu

Pomocí akcí skriptů můžete přizpůsobit clustery HDInsight následujícími způsoby:

* portál Azure
* Azure PowerShell
* Šablony Azure Resource Manageru
* Sada SDK pro HDInsight .NET.

Další informace o použití jednotlivých metod naleznete v tématu [How to use Script Action](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Ukázky vlastních skriptů

Společnost Microsoft poskytuje ukázkové skripty pro instalaci komponent do clusteru HDInsight. Podívejte se [na téma Instalace a použití odstínu v clusterech HDInsight](hdinsight-hadoop-hue-linux.md) jako příklad akce skriptu.

## <a name="troubleshooting"></a>Řešení potíží

V následujícím seznamu jsou chyby, které se můžou při používání skriptů, které jste vyvinuli, nacházet:

**Chyba** : `$'\r': command not found` . Někdy následováno `syntax error: unexpected end of file` .

*Příčina* : Tato chyba je způsobena tím, že řádky ve skriptu končí znakem CRLF. Systémy UNIX očekávají jako konec řádku pouze LF.

K tomuto problému často dochází, když je skript vytvořen v prostředí systému Windows, protože znak CRLF je obvyklým řádkem, který končí mnoho textových editorů v systému Windows.

*Řešení* : Pokud se jedná o možnost v textovém editoru, vyberte pro konec řádku možnost formát systému UNIX nebo LF. V systému UNIX můžete také použít následující příkazy, abyste změnili znak CRLF na LF:

> [!NOTE]  
> Následující příkazy jsou přibližně stejné jako v tom, že by měly změnit čáru CRLF zakončení na LF. Vyberte jednu z nástrojů, které jsou k dispozici ve vašem systému.

| Příkaz | Poznámky |
| --- | --- |
| `unix2dos -b INFILE` |Původní soubor je zálohovaný pomocí. Rozšíření BAK |
| `tr -d '\r' < INFILE > OUTFILE` |SOUBOR s informacemi o verzi obsahující jenom konce LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Upraví soubor přímo. |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |SOUBOR s informacemi o verzi obsahuje jenom konce LF. |

**Chyba** : `line 1: #!/usr/bin/env: No such file or directory` .

*Příčina* : k této chybě dochází, když byl skript uložen jako UTF-8 s označením pořadí bajtů (BOM).

*Řešení* : Uložte soubor buď jako ASCII, nebo jako UTF-8 bez kusovníku. V systému Linux nebo UNIX můžete také použít následující příkaz k vytvoření souboru bez tohoto kusovníku:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

Nahraďte souborem, který `INFILE` obsahuje kusovník. `OUTFILE` měl by se jednat o nový název souboru, který obsahuje skript bez tohoto kusovníku.

## <a name="next-steps"></a><a name="seeAlso"></a>Další kroky

* Postup [Přizpůsobení clusterů HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
* Další informace o vytváření aplikací .NET, které spravují HDInsight, najdete v referenčních informacích k [sadě HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight) .
* Pomocí [REST API HDInsight](/rest/api/hdinsight/) se naučíte, jak používat REST k provádění akcí správy v clusterech HDInsight.