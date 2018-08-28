---
title: Vývoj skriptových akcí s Linuxovým systémem HDInsight – Azure
description: Další informace o použití skripty Bash přizpůsobit clustery HDInsight založené na Linuxu. Funkci akce skriptu HDInsight umožňuje spouštět skripty během nebo po vytvoření clusteru. Skripty je možné změnit nastavení konfigurace clusteru nebo instalovat další software.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.openlocfilehash: 0225115fb6c74f736e6a5fba09414dc2ebafd84e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43104819"
---
# <a name="script-action-development-with-hdinsight"></a>Vývoj skriptových akcí s HDInsight

Zjistěte, jak přizpůsobit vašeho clusteru HDInsight pomocí skriptů prostředí Bash. Akce skriptů jsou způsob, jak přizpůsobit HDInsight během nebo po vytvoření clusteru.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Co jsou akce skriptu

Akce skriptů jsou skripty Bash, které Azure používá na uzlech clusteru pro provádění změn konfigurace nebo instalace softwaru. Akce skriptu je spuštěn jako uživatel root a poskytuje úplná přístupová práva k uzlům clusteru.

Akce skriptů se dá použít prostřednictvím následujících metod:

| Pomocí této metody můžete použít skript... | Při vytvoření clusteru... | Na spuštěný cluster... |
| --- |:---:|:---:|
| portál Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure CLI 1.0 |&nbsp; |✓ |
| Sada HDInsight .NET SDK |✓ |✓ |
| Šablona Azure Resource Manageru |✓ |&nbsp; |

Další informace o použití těchto metod lze aplikovat akce skriptu, najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Osvědčené postupy pro vývoj skriptů

Když vyvíjíte vlastní skript pro HDInsight cluster, existuje několik osvědčených postupů brát v úvahu:

* [Cílová verze systému Hadoop](#bPS1)
* [Cílení na verzi operačního systému](#bps10)
* [Stabilní odkazy skriptu prostředků](#bPS2)
* [Použití předem kompilovaných prostředků](#bPS4)
* [Ujistěte se, že cluster přizpůsobení skript je idempotentní](#bPS3)
* [Zajištění vysoké dostupnosti architektury clusteru](#bPS5)
* [Konfigurace vlastních součástech používání úložiště objektů Blob v Azure](#bPS6)
* [Zápis informací o do STDOUT a STDERR](#bPS7)
* [Ukládat soubory ve formátu ASCII s LF konce řádků](#bps8)
* [Použít logika opakovaných pokusů provést obnovení při přechodné chyby](#bps9)

> [!IMPORTANT]
> Akce se skripty musí dokončit během 60 minut nebo proces selže. Během zřizování uzlu, bude skript spuštěn současně jiných nastavení a konfigurace procesů. Soutěže pro prostředky, jako jsou procesor čas nebo síťové šířky pásma může způsobit skriptu trvá déle dokončit, než ve vašem vývojovém prostředí.

### <a name="bPS1"></a>Cílová verze systému Hadoop

Různé verze HDInsight mají různé verze služby Hadoop a nainstalované součásti. Pokud váš skript očekává, že konkrétní verzi služby nebo komponenty, byste měli skript používat jenom s verzí služby HDInsight, který obsahuje požadované součásti. Můžete najít informace o verzích komponenty součástí pomocí HDInsight [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md) dokumentu.

### <a name="bps10"></a> Cílení na verzi operačního systému

HDInsight se systémem Linux je založená na Ubuntu Linuxovou distribuci. Spolehněte se na různé verze Ubuntu, které mohou změnit chování vašich skriptů různých verzích HDInsight. Například vycházejí HDInsight 3.4 a starší verze Ubuntu, které používají Upstart. Verze 3.5 a vyšší jsou založené na Ubuntu 16.04, který používá Systemd. Systemd a Upstart využívají různé příkazy, aby váš skript by měly být napsány pro práci s oběma.

Další důležitý rozdíl mezi HDInsight 3.4 a 3.5 je, že `JAVA_HOME` nyní odkazuje na jazyce Java 8.

Verze operačního systému můžete zkontrolovat pomocí `lsb_release`. Následující kód ukazuje, jak určit, zda je skript spuštěn na Ubuntu 14 nebo 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
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

Můžete najít úplná skript, který obsahuje tyto fragmenty kódu v https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Verze Ubuntu, které používají HDInsight, najdete v článku [verzí komponenty HDInsight](hdinsight-component-versioning.md) dokumentu.

Rozdíly mezi Systemd a Upstart najdete v tématu [Systemd pro uživatele Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Stabilní odkazy skriptu prostředků

Skript a související prostředky musí zůstat k dispozici v rámci životnosti clusteru. Tyto prostředky jsou povinné, Pokud Přidání nových uzlů do clusteru během operace škálování.

Osvědčeným postupem je ke stažení a archivovat vše, co v účtu služby Azure Storage v rámci předplatného.

> [!IMPORTANT]
> Účet úložiště používané musí být výchozí účet úložiště pro cluster nebo veřejné, jen pro čtení kontejneru na jiný účet úložiště.

Například vzorky od Microsoftu jsou uloženy v [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) účtu úložiště. Toto umístění je veřejné, jen pro čtení kontejneru udržované týmem HDInsight.

### <a name="bPS4"></a>Použití předem kompilovaných prostředků

Pokud chcete snížit čas potřebný ke spuštění skriptu, vyhněte se operace, které se kompilují prostředky ze zdrojového kódu. Například před kompilací prostředků a uložit je do objektu blob pro účet služby Azure Storage ve stejném datovém centru jako HDInsight.

### <a name="bPS3"></a>Ujistěte se, že cluster přizpůsobení skript je idempotentní

Skripty musí být idempotentní. Pokud se skript spustí více než jednou, měla by vrátit clusteru do stejného stavu pokaždé, když.

Například skript, který upraví konfigurační soubory neměli byste přidávat duplicitní položky, pokud spuštění více než jednou.

### <a name="bPS5"></a>Zajištění vysoké dostupnosti architektury clusteru

Založené na Linuxu clustery HDInsight poskytují dva hlavní uzly, které jsou aktivní v rámci clusteru a akce skriptů, spusťte na obou uzlech. Pokud komponenty, které instalujete očekává pouze jeden hlavní uzel, neinstalujte součásti na oba hlavní uzly.

> [!IMPORTANT]
> Služby poskytované v rámci HDInsight jsou navrženy pro převzetí služeb při selhání mezi dva hlavní uzly podle potřeby. Tato funkce není rozšířené k vlastní komponenty nainstalované prostřednictvím akce skriptu. Pokud potřebujete vysokou dostupnost pro vlastní komponenty, musíte implementovat vlastní mechanismus převzetí služeb při selhání.

### <a name="bPS6"></a>Konfigurace vlastních součástech používání úložiště objektů Blob v Azure

Součásti, které instalujete na clusteru může být výchozí konfiguraci, která používá úložiště souboru systému HDFS (Hadoop Distributed). HDInsight používá Azure Storage nebo Data Lake Store jako výchozím úložištěm. Oba poskytují systému HDFS kompatibilní soubor, který bude zachován dat i v případě odstranění clusteru. Budete muset konfigurovat součásti instalujete používat WASB nebo ADL namísto HDFS.

Pro většinu operací není potřeba zadat v systému souborů. Například následující zkopíruje giraph-examples.jar soubor z místního systému souborů do úložiště clusteru:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

V tomto příkladu `hdfs` příkaz transparentně používá výchozí úložiště clusteru. Pro některé operace budete možná muset zadat identifikátor URI. Například `adl:///example/jars` pro Data Lake Store nebo `wasb:///example/jars` pro službu Azure Storage.

### <a name="bPS7"></a>Zápis informací o do STDOUT a STDERR

Výstup skriptu protokoly HDInsight, která jsou zapsána do STDOUT a STDERR. Můžete zobrazit tyto informace pomocí webového uživatelského rozhraní Ambari.

> [!NOTE]
> Ambari slouží pouze k dispozici, pokud je cluster úspěšně vytvořen. Pokud použijete akci skriptu během vytváření clusteru a vytvoření selže, naleznete v části řešení potíží [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) existují i jiné možnosti přístupu k zaznamenané informace.

Většina nástrojů a instalační balíčky již zápis informací o do STDOUT a STDERR, ale můžete chtít přidat další protokolování. Chcete-li odeslat text do STDOUT, použijte `echo`. Příklad:

```bash
echo "Getting ready to install Foo"
```

Ve výchozím nastavení `echo` odešle řetězec do STDOUT. Chcete-li ho přímo do STDERR, přidejte `>&2` před `echo`. Příklad:

```bash
>&2 echo "An error occurred installing Foo"
```

To přesměruje informací, zapsán do STDOUT do výstupu STDERR (2) místo toho. Další informace o přesměrování vstupně-výstupních operací, najdete v části [ http://www.tldp.org/LDP/abs/html/io-redirection.html ](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Další informace o zobrazení informacím protokolovaným v akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Ukládat soubory ve formátu ASCII s LF konce řádků

Skripty bash uskladněny ve formátu ASCII, řádky ukončen znakem LF. Soubory, které jsou uloženy jako UTF-8, nebo použít CRLF jako ukončení řádku může selhat kvůli následující chybě:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Použít logika opakovaných pokusů provést obnovení při přechodné chyby

Při stahování souborů, instalace balíčků pomocí apt-get, nebo jiné akce, které přenášejí data přes internet, tato akce může selhat z důvodu přechodných síťových chyb. Vzdálený prostředek, který komunikuje se například může být probíhá přebírání služeb při selhání uzlu zálohování.

Aby váš skript odolné pro přechodné chyby, můžete implementovat logiku opakování. Následující funkce ukazuje, jak implementovat logiku opakování. Zopakuje pokus o operaci třikrát před selháním.

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

Následující příklady ukazují, jak tuto funkci používat.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Pomocné metody pro vlastní skripty

Pomocné metody akcí skriptů jsou nástroje, které můžete použít při zápisu vlastních skriptů. Tyto metody jsou obsaženy v[ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skriptu. Stáhnout a použít je jako součást vašeho skriptu, použijte následující:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Následující pomocníky dostupné k použití ve vašem skriptu:

| Použití pomocné rutiny | Popis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Stáhne soubor z identifikátoru URI zdroje do zadané cesty k souboru. Ve výchozím nastavení to není přepis existujícího souboru. |
| `untar_file TARFILE DESTDIR` |Extrahuje soubor tar (pomocí `-xf`) do cílového adresáře. |
| `test_is_headnode` |Pokud byly spuštěny v hlavního uzlu clusteru, návratový 1; jinak 0. |
| `test_is_datanode` |Pokud je aktuální uzel uzlem data (pracovní), vrátí se 1; jinak 0. |
| `test_is_first_datanode` |Pokud aktuální uzel je první data (pracovní) uzlu (pojmenované workernode0) vrátí 1; jinak 0. |
| `get_headnodes` |Vrátí název plně kvalifikované domény hlavních uzlech v clusteru. Názvy jsou oddělené čárkami. Při chybě je vrácen prázdný řetězec. |
| `get_primary_headnode` |Získá název plně kvalifikované domény primárnímu hlavnímu uzlu. Při chybě je vrácen prázdný řetězec. |
| `get_secondary_headnode` |Získá název plně kvalifikované domény sekundární hlavní uzel. Při chybě je vrácen prázdný řetězec. |
| `get_primary_headnode_number` |Získá číselnou příponou primárnímu hlavnímu uzlu. Při chybě je vrácen prázdný řetězec. |
| `get_secondary_headnode_number` |Získá číselnou příponou sekundární hlavní uzel. Při chybě je vrácen prázdný řetězec. |

## <a name="commonusage"></a>Běžné vzory využití

Tato část obsahuje pokyny k implementaci některých běžných vzorů využití, které můžete narazit na při zápisu vlastních skriptů.

### <a name="passing-parameters-to-a-script"></a>Předávání parametrů skriptu

V některých případech může váš skript vyžadují parametry. Například můžete potřebovat heslo správce pro cluster při použití rozhraní Ambari REST API.

Parametry předané do skriptu jsou označovány jako *poziční parametry*a jsou přiřazeny k `$1` pro první parametr `$2` druhé a proto dostupné. `$0` obsahuje název samotný skript.

Hodnoty předány do skriptu jako parametry by měl být uzavřen v jednoduchých uvozovkách ('). Tím se zajistí, že předaná hodnota je považována za literál.

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Nastavení proměnné prostředí se provádí pomocí následujícího příkazu:

    VARIABLENAME=value

NÁZEVPROMĚNNÉ kde je název proměnné. Chcete-li získat přístup k proměnné, použijte `$VARIABLENAME`. Například pro přiřazení hodnoty poskytnuté pozičních parametrů jako proměnnou prostředí s názvem heslo, použijete následující příkaz:

    PASSWORD=$1

Pak můžete použít následující přístup k informacím `$PASSWORD`.

Proměnné prostředí nastavené v rámci skriptu existují pouze v rámci oboru skriptu. V některých případech budete muset přidat systémové proměnné, které se uloží po dokončení skriptu. Pro přidání systémové proměnné, přidejte proměnné do `/etc/environment`. Například následující příkaz přidá `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Přístup k umístění, kde jsou uloženy vlastních skriptů

Skripty používané k úpravám clusteru musí být uložen v jednom z následujících umístění:

* __Účtu služby Azure Storage__ , který je přidružen cluster.

* __Dalšího účtu úložiště__ přidružené ke clusteru.

* A __veřejně čitelné URI__. Například adresa URL k datům uloženým na Onedrivu, Dropboxu nebo jiný soubor, který je hostitelem služby.

* __Účtu Azure Data Lake Store__ , který je přidružen HDInsight cluster. Další informace o používání Azure Data Lake Store se službou HDInsight najdete v tématu [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]
    > Instanční objekt služby, kterou používá HDInsight pro přístup k Data Lake Store musí mít oprávnění ke čtení pro skript.

Prostředky využívané třídou skriptu musí být také veřejně dostupná.

Ukládání souborů do účtu Azure Storage nebo Azure Data Lake Store poskytuje rychlý přístup, jako v rámci sítě Azure.

> [!NOTE]
> Formát identifikátoru URI slouží jako odkaz na skript se liší v závislosti na používaných služeb. Pro účty úložiště přidružené ke clusteru HDInsight, použijte `wasb://` nebo `wasbs://`. Veřejně čitelné identifikátory URI, použijte `http://` nebo `https://`. Pro Data Lake Store, použijte `adl://`.

### <a name="checking-the-operating-system-version"></a>Kontroluje se verze operačního systému

Spolehněte se na konkrétní verze Ubuntu různých verzích HDInsight. Mohou existovat rozdíly mezi verzemi operačního systému, které musíte hledat ve skriptu. Například budete muset nainstalovat binární soubor, který se váže na verze Ubuntu.

Pokud chcete zkontrolovat verzi operačního systému, použít `lsb_release`. Například následující skript ukazuje, jak odkazovat na konkrétní cíl soubor v závislosti na verzi operačního systému:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Kontrolní seznam pro nasazení skriptových akcí

Tady jsou kroky vzít při přípravě nasazení skriptu:

* Umístěte soubory, které obsahují vlastní skripty na místě, které je přístupné uzly clusteru během nasazení. Například výchozí úložiště pro cluster. Soubory můžete také uloženy ve veřejně čitelné hostitelské služby.
* Ověřte, zda skript je idempotentní. Díky tomu skript, který se spustí více než jednou ve stejném uzlu.
* Pomocí TMP adresáře dočasných souborů stažených souborů, které skripty používají a následnému vyčištění po jejich po uzavřeli dohodu o skripty.
* Pokud se změní nastavení na úrovni operačního systému nebo konfigurační soubory služby Hadoop, můžete restartovat služby HDInsight.

## <a name="runScriptAction"></a>Jak spustit akci skriptu

Akce se skripty můžete použít k přizpůsobení clusterů HDInsight pomocí následujících metod:

* portál Azure
* Azure PowerShell
* Šablony Azure Resource Manageru
* Sady HDInsight .NET SDK.

Další informace o použití každé metody, naleznete v tématu [způsob použití akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Ukázky vlastních skriptů

Společnost Microsoft poskytuje ukázkové skripty pro instalaci součásti v clusteru HDInsight. V následujících tématech pro další příklad skriptových akcí.

* [Nainstalovat a používat rozhraní Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalace a použití Solru na clusterech HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalace a použití Giraphu na clusterech HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Instalace nebo upgrade Mono na clusterech HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Řešení potíží

Tady jsou chyby, které může dojít při používání skriptů, kterou jste vytvořili:

**Chyba**: `$'\r': command not found`. Někdy následovaný `syntax error: unexpected end of file`.

*Příčina*: k této chybě dojde, pokud řádků ve skriptu končit znaky CRLF. Systémy UNIX očekávat, že pouze LF jako ukončení řádku.

Tento problém nejčastěji nastane, pokud se vytváří skript v prostředí Windows CRLF je běžné řádku ukončení pro mnoho textových editorů na Windows.

*Rozlišení*: Pokud je možnost v textovém editoru, vyberte formát operačního systému Unix nebo LF pro ukončení řádku. Chcete-li změnit znaky CRLF LF může také použít následující příkazy v systému Unix:

> [!NOTE]
> Následující příkazy jsou zhruba ekvivalentní v tom, že by měl mění konce řádků CRLF k LF. Vyberte jednu podle nástrojů, které jsou k dispozici ve vašem systému.

| Příkaz | Poznámky |
| --- | --- |
| `unix2dos -b INFILE` |Původní soubor je zálohovaný s. BAK rozšíření |
| `tr -d '\r' < INFILE > OUTFILE` |VÝSTUPNÍ_SOUBOR obsahuje verzi s pouze LF konce |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Upraví soubor přímo |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |VÝSTUPNÍ_SOUBOR obsahuje verzi s pouze LF konce. |

**Chyba**: `line 1: #!/usr/bin/env: No such file or directory`.

*Příčina*: Tato chyba nastane, pokud skript byl uložen jako UTF-8 s značky pořadí bajtů (BOM).

*Rozlišení*: uložit soubor ve formátu ASCII nebo jako UTF-8 bez BOM. Vytvoření souboru bez BOM může také použít následující příkaz v systému Linux nebo Unix:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Nahraďte `INFILE` v souboru s BOM. `OUTFILE` nový název souboru, který obsahuje skript bez BOM by měl být.

## <a name="seeAlso"></a>Další kroky

* Zjistěte, jak [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
* Použití [referenční sady HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) Další informace o vytváření aplikací .NET, které spravují HDInsight
* Použít [rozhraní REST API služby HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) na další informace o použití REST k provádění akcí správy v clusterech HDInsight.
