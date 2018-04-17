---
title: Vývoj akcí skriptů v HDInsight se systémem Linux - Azure | Microsoft Docs
description: Naučte se používat skripty Bash přizpůsobit clustery HDInsight se systémem Linux. Funkci akce skriptu HDInsight umožňuje spouštět skripty během nebo po vytvoření clusteru. Skripty lze změnit nastavení konfigurace clusteru nebo nainstalovat další software.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: d5df67021e997df3a6344701f50be4871a11386d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="script-action-development-with-hdinsight"></a>Vývoj akcí skriptů v prostředí HDInsight

Zjistěte, jak přizpůsobit pomocí skriptů Bash clusteru HDInsight. Akce skriptů jsou způsob, jak přizpůsobit HDInsight během nebo po vytvoření clusteru.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Jaké jsou akce skriptu

Akce skriptů jsou skripty Bash, které Azure je spuštěná na uzlech clusteru provést změny konfigurace nebo instalace softwaru. Akce skriptu se spustí jako kořenového adresáře a poskytuje úplná přístupová práva pro uzly clusteru.

Skript akce se dá použít prostřednictvím následujících metod:

| Pomocí této metody můžete použít skript... | Při vytvoření clusteru... | Na clusteru s podporou spuštěné... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure CLI 1.0 |&nbsp; |✓ |
| Sada HDInsight .NET SDK |✓ |✓ |
| Šablona Azure Resource Manageru |✓ |&nbsp; |

Další informace o použití těchto metod akcí skriptů naleznete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Osvědčené postupy pro vývoj skriptů

Při vývoji vlastních skriptů pro cluster služby HDInsight, existuje několik doporučených postupech pro mějte na paměti:

* [Cílová verze Hadoop](#bPS1)
* [Cílové verze operačního systému](#bps10)
* [Zadejte stabilní odkazy na zdroje skriptu](#bPS2)
* [Použití předem zkompilovat prostředky](#bPS4)
* [Zajistěte, aby byl skript přizpůsobení clusteru idempotent](#bPS3)
* [Zajištění vysoké dostupnosti architektury clusteru](#bPS5)
* [Konfigurace vlastní součásti, které budou používat úložiště objektů Blob v Azure](#bPS6)
* [Zapisuje informace do STDOUT a STDERR](#bPS7)
* [Ukládat soubory ve formátu ASCII s LF konce řádků](#bps8)
* [Logika opakovaných pokusů použít k obnovení z přechodné chyby](#bps9)

> [!IMPORTANT]
> Akce skriptu musíte dokončit během 60 minut nebo proces selže. Při zřizování uzlu, bude skript spuštěn souběžně jiné procesy instalací a konfigurací. Soutěž o zdroje, jako je například šířky pásma procesoru čas nebo v síti může způsobit skript trvá déle než ve vašem vývojovém prostředí dokončit.

### <a name="bPS1"></a>Cílová verze Hadoop

Různé verze HDInsight mají různé verze služby Hadoop a nainstalovány součásti. Pokud váš skript očekává na konkrétní verzi služeb nebo součásti, byste měli skript používat jenom k verzi HDInsight, který obsahuje požadované součásti. Můžete najít informace o verze součástí, které jsou součástí HDInsight pomocí [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md) dokumentu.

### <a name="bps10"></a> Cílové verze operačního systému

HDInsight se systémem Linux je založena na rozdělení Ubuntu Linux. Různé verze HDInsight využívají různé verze Ubuntu, což může změnit chování vašeho skriptu. Například vychází HDInsight 3.4 a starší verze Ubuntu, které používají Upstart. Verze 3.5 a vyšší jsou založené na 16.04 Ubuntu, který používá Systemd. Systemd a Upstart závisí na jiné příkazy, tak budou zasílány váš skript pro práci s obě.

Další důležité rozdíl mezi HDInsight 3.4 a 3.5 je, že `JAVA_HOME` nyní odkazuje na Java 8.

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

Můžete najít úplné skript, který obsahuje tyto fragmenty kódu v https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Verze Ubuntu, který se používá v prostředí HDInsight, najdete v článku [verze komponenty HDInsight](hdinsight-component-versioning.md) dokumentu.

Chcete-li pochopit rozdíly mezi Systemd a Upstart, přečtěte si téma [Systemd pro uživatele Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Zadejte stabilní odkazy na zdroje skriptu

Skript a související prostředky musí zůstat k dispozici v rámci dobu životnosti clusteru. Pokud Přidání nových uzlů do clusteru během operace škálování, je nutné tyto prostředky.

Osvědčeným postupem je ke stažení a archivaci vše v účtu Azure Storage na vaše předplatné.

> [!IMPORTANT]
> Účet úložiště používané musí být výchozí účet úložiště pro cluster nebo kontejner veřejné, jen pro čtení na jiný účet úložiště.

Například ukázky od společnosti Microsoft jsou uloženy v [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) účet úložiště. Toto umístění je veřejný, jen pro čtení kontejner udržovat týmem HDInsight.

### <a name="bPS4"></a>Použití předem zkompilovat prostředky

Pokud chcete zkrátit dobu potřebnou pro spuštění skriptu, vyhněte se operace, které zkompilovat prostředky ze zdrojového kódu. Například předem zkompilovat prostředky a uložit je do objektu blob účet úložiště Azure ve stejném datovém centru jako HDInsight.

### <a name="bPS3"></a>Zajistěte, aby byl skript přizpůsobení clusteru idempotent

Skripty musí být idempotent. Pokud se skript spustí vícekrát, měla by vrátit clusteru do stejného stavu pokaždé, když.

Například skript, který upravuje konfigurační soubory by neměly přidat duplicitní položky, pokud byl spuštěn vícekrát.

### <a name="bPS5"></a>Zajištění vysoké dostupnosti architektury clusteru

Linuxové clustery HDInsight poskytují dva head uzly, které jsou aktivní v rámci clusteru, a spusťte akcí skriptů v obou uzlech. Pokud součásti, které nainstalujete očekává pouze jeden hlavní uzel, neinstalujte součásti v obou uzlech head.

> [!IMPORTANT]
> Služby, které jsou k dispozici jako součást služby HDInsight jsou navrženy pro převzetí služeb při selhání mezi dvěma uzly head podle potřeby. Tato funkce není rozšířené k vlastní součásti nainstalované prostřednictvím akce skriptu. Pokud potřebujete vysokou dostupnost pro vlastní součásti, je nutné implementovat vlastní mechanismus převzetí služeb při selhání.

### <a name="bPS6"></a>Konfigurace vlastní součásti, které budou používat úložiště objektů Blob v Azure

Součásti, které můžete nainstalovat na cluster může mít výchozí konfigurace, který používá Hadoop Distributed File System (HDFS) úložiště. HDInsight používá jako výchozí úložiště Azure Storage nebo Data Lake Store. Zadejte oba systém HDFS kompatibilní souborů, která je uchována data i v případě odstranění clusteru. Musíte nakonfigurovat komponenty instalaci pro použití WASB nebo ADL místo HDFS.

Pro většinu operací není potřeba zadat systému souborů. Například následující zkopíruje giraph-examples.jar soubor z místního systému souborů do úložiště clusteru:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

V tomto příkladu `hdfs` příkaz transparentně používá výchozí úložiště clusteru. Pro některé operace budete možná muset zadat identifikátor URI. Například `adl:///example/jars` pro Data Lake Store nebo `wasb:///example/jars` pro Azure Storage.

### <a name="bPS7"></a>Zapisuje informace do STDOUT a STDERR

HDInsight zaznamená výstup skriptu, který je zapsán do STDOUT a STDERR. Můžete zobrazit tyto informace pomocí Ambari webového uživatelského rozhraní.

> [!NOTE]
> Ambari je dostupné pouze při cluster se úspěšně vytvořil. Pokud použijete akci skriptu během vytváření clusteru a vytvoření selže, najdete v části řešení potíží [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) pro další způsoby, jak přístup k informacím o protokolu.

Většina nástrojů a instalační balíčky již zapisovat informace do STDOUT a STDERR, ale můžete chtít přidat další protokolování. Chcete-li odeslat text do STDOUT, použijte `echo`. Příklad:

```bash
echo "Getting ready to install Foo"
```

Ve výchozím nastavení `echo` odešle řetězec STDOUT. Pro přesměrování je na STDERR, přidejte `>&2` před `echo`. Příklad:

```bash
>&2 echo "An error occurred installing Foo"
```

To přesměruje informace místo zapsána do STDOUT do STDERR (2). Další informace o přesměrování vstupů/výstupů, najdete v části [ http://www.tldp.org/LDP/abs/html/io-redirection.html ](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Další informace o zobrazení informací zaznamenaných akcí skriptů naleznete v tématu [clusterů HDInsight přizpůsobit pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Ukládat soubory ve formátu ASCII s LF konce řádků

Skripty bash být uloženy ve formě formátu ASCII, se ukončila příkazem LF řádky. Soubory, které jsou uloženy jako UTF-8, nebo použijte Line FEED jako ukončuje řádku může selhat s následující chybou:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Logika opakovaných pokusů použít k obnovení z přechodné chyby

Při stahování souborů, instalace balíčků pomocí výstižný get nebo jiné akce, které přenášet data přes internet, akce se pravděpodobně nezdaří z důvodu přechodné chyby sítě. Vzdálený prostředek, který komunikuje se například může být právě převzetí služeb při selhání zálohování uzlu.

Chcete-li váš skript odolné přechodné chyby, můžete implementovat logiku opakovaných pokusů. Následující funkce ukazuje, jak implementovat logiku opakovaných pokusů. Se pokusí operaci třikrát než selže.

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

Následující příklady ukazují, jak použít tuto funkci.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Pomocné metody pro vlastní skripty

Pomocné metody akcí skriptů jsou nástroje, které můžete použít při zápisu vlastních skriptů. Tyto metody jsou součástí[ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skriptu. Stáhnout a použít je jako součást vašeho skriptu, použijte následující:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Následující pomocníky dostupné k použití ve vašem skriptu:

| Použití pomocné rutiny | Popis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Stahování souboru ze zdroje URI do zadaná cesta k souboru. Ve výchozím nastavení je nepřepíše existující soubor. |
| `untar_file TARFILE DESTDIR` |Extrahuje vkládání soubor (pomocí `-xf`) do cílového adresáře. |
| `test_is_headnode` |Pokud byla spuštěna hlavního uzlu clusteru, návratový 1; jinak hodnota 0. |
| `test_is_datanode` |Pokud je aktuální uzel uzlem dat (pracovník), vrátí 1; jinak hodnota 0. |
| `test_is_first_datanode` |Pokud je aktuální uzel první data (pracovník) uzlu (s názvem workernode0) vrátí 1; jinak hodnota 0. |
| `get_headnodes` |Vrátí název plně kvalifikované domény headnodes v clusteru. Názvy jsou oddělený čárkami. Chyba se vrátí prázdný řetězec. |
| `get_primary_headnode` |Získá název plně kvalifikované domény primární headnode. Chyba se vrátí prázdný řetězec. |
| `get_secondary_headnode` |Získá název plně kvalifikované domény sekundární headnode. Chyba se vrátí prázdný řetězec. |
| `get_primary_headnode_number` |Získá číselnou příponou primární headnode. Chyba se vrátí prázdný řetězec. |
| `get_secondary_headnode_number` |Získá číselnou příponou sekundární headnode. Chyba se vrátí prázdný řetězec. |

## <a name="commonusage"></a>Obecné vzory využití

Tato část obsahuje pokyny k implementaci některých běžných vzorů využití, které se mohou vyskytnout při zápisu vlastních skriptů.

### <a name="passing-parameters-to-a-script"></a>Předávání parametrů skriptu

V některých případech může váš skript vyžadovat parametry. Například můžete potřebovat heslo správce pro cluster při pomocí nástroje Ambari REST API.

Parametry předané do skriptu se označují jako *poziční parametry*a jsou přiřazeny k `$1` pro první parametr `$2` pro druhý a proto v. `$0` obsahuje název samotný skript.

Hodnoty předány do skriptu jako parametry by měl být uzavřen v jednoduchých uvozovkách ('). Tím zajistíte, že předaný hodnota je považována za literál.

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Nastavení proměnné prostředí provádí následující příkaz:

    VARIABLENAME=value

Kde NÁZEVPROMĚNNÉ je název proměnné. Chcete-li získat přístup k proměnné, použijte `$VARIABLENAME`. Například pokud chcete přiřadit hodnotu poskytované poziční parametr jako proměnnou prostředí s názvem heslo, použijte následující příkaz:

    PASSWORD=$1

Poté můžete použít následující přístup k informacím `$PASSWORD`.

Proměnné prostředí nastavit v rámci skriptu existovat pouze v rámci oboru skriptu. V některých případech musíte přidat systémové proměnné, které se uchová po dokončení skriptu. Přidání proměnné systémového prostředí, přidat proměnnou `/etc/environment`. Například následující příkaz přidá `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Přístup k umístění, kde jsou uloženy vlastní skripty

Skripty použít pro přizpůsobení clusteru, musí být uložen v jedné z následujících umístění:

* __Účet úložiště Azure__ který je přidružen cluster.

* __Účtu další úložiště__ přidružen ke clusteru.

* A __veřejně čitelné URI__. Například adresa URL pro data uložená na OneDrive, Dropbox nebo jiný soubor, který je hostitelem služby.

* __Účtu Azure Data Lake Store__ který je přidružen HDInsight cluster. Další informace o používání Azure Data Lake Store s HDInsight naleznete v tématu [vytvoření clusteru HDInsight s Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > Objekt služby, které HDInsight používá pro přístup k Data Lake Store, musí mít přístup pro čtení do skriptu.

Prostředky používané ve skriptu musí být také veřejně dostupné.

Ukládání souborů v účtu úložiště Azure nebo Azure Data Lake Store poskytuje rychlý přístup, jako obě sítě Azure.

> [!NOTE]
> Formát identifikátoru URI, slouží k odkazování skript se liší v závislosti na službě, kterou právě používá. Pro účty úložiště přidružené ke clusteru HDInsight, použijte `wasb://` nebo `wasbs://`. Veřejně čitelné identifikátory URI, použijte `http://` nebo `https://`. Pro Data Lake Store, použijte `adl://`.

### <a name="checking-the-operating-system-version"></a>Kontrola verze operačního systému

Různé verze HDInsight spoléhají na určité verze Ubuntu. Mohou existovat rozdíly mezi verzemi operačního systému, které musí vyhledat ve vašem skriptu. Potřebujete například nainstalovat binární soubor, který je vázaný na verze Ubuntu.

Pokud chcete zkontrolovat verzi operačního systému, použijte `lsb_release`. Například následující skript ukazuje, jak odkazovat na konkrétní vkládání soubor v závislosti na verzi operačního systému:

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

## <a name="deployScript"></a>Kontrolní seznam pro nasazení akce skriptu

Zde jsou proveďte kroky při přípravě nasazení skriptu:

* Uveďte soubory, které obsahují vlastní skripty na místě, která je přístupná na uzlech clusteru během nasazení. Například výchozí úložiště pro cluster. Soubory můžete také uloženy ve veřejně čitelné hostitelských služeb.
* Ověřte, že skript idempotent. To uděláte skript, který chcete spustit několikrát na stejném uzlu.
* Pomocí TMP dočasný soubor directory zachovat stažené soubory, které skripty používají a pak vyčištění je po mají spouštět skripty.
* Pokud se změní nastavení na úrovni operačního systému nebo soubory konfigurace služby Hadoop, můžete restartovat služby HDInsight.

## <a name="runScriptAction"></a>Jak spustit akci skriptu

Akce skriptu můžete použít k přizpůsobení clusterů HDInsight pomocí následujících metod:

* Azure Portal
* Azure PowerShell
* Šablony Azure Resource Manageru
* .NET SDK služby HDInsight.

Další informace o použití jednotlivých metod najdete v tématu [způsob použití akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Ukázky vlastních skriptů

Společnost Microsoft poskytuje ukázkové skripty pro instalaci součásti v clusteru HDInsight. V následujících tématech pro další příklad akce skriptu.

* [Nainstalovat a používat Hue clustery prostředí HDInsight](hdinsight-hadoop-hue-linux.md)
* [Nainstalovat a používat Solr clustery prostředí HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Nainstalovat a používat Giraph clustery prostředí HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Nainstalovat nebo upgradovat Mono na clustery HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Řešení potíží

Tady jsou chyby, se můžete setkat při používání skripty, které jste si vytvořili:

**Chyba**: `$'\r': command not found`. Někdy následuje `syntax error: unexpected end of file`.

*Příčina*: k této chybě dojde, pokud řádky ve skriptu končit Line FEED. Systémy UNIX očekávat pouze LF jako ukončování řádků.

Tento problém nejčastěji nastane, když skript je vytvořené v prostředí Windows, protože Line FEED je běžné řádku ukončení pro mnoho textové editory v systému Windows.

*Řešení*: Pokud je možnost v textovém editoru, vyberte formát systému Unix nebo LF pro ukončování řádků. Ke změně Line FEED LF může také použít následující příkazy v systému Unix:

> [!NOTE]
> V tom, že by se měl změnit konce řádků Line FEED na LF jsou přibližně ekvivalentem následující příkazy. Vyberte jeden podle nástrojů dostupných v systému.

| Příkaz | Poznámky |
| --- | --- |
| `unix2dos -b INFILE` |Původní soubor je zálohovaný s. BAK rozšíření |
| `tr -d '\r' < INFILE > OUTFILE` |VÝSTUPNÍ_SOUBOR obsahuje verzi s pouze LF zakončení |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Upraví soubor přímo |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |VÝSTUPNÍ_SOUBOR obsahuje verzi s pouze LF zakončení. |

**Chyba**: `line 1: #!/usr/bin/env: No such file or directory`.

*Příčina*: k této chybě dojde, když skript se uložil jako UTF-8 s značky pořadí bajtů (BOM).

*Řešení*: uložte soubor ve formátu ASCII nebo jako UTF-8 bez Kusovník. Vytvoření souboru bez Kusovníku může také použít následující příkaz v systému Linux nebo Unix:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Nahraďte `INFILE` v souboru s Kusovníku. `OUTFILE` musí být nový název souboru, který obsahuje skript bez Kusovníku.

## <a name="seeAlso"></a>Další kroky

* Zjistěte, jak [clusterů HDInsight přizpůsobit pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
* Použití [referenční informace sady SDK rozhraní .NET HDInsight](https://msdn.microsoft.com/library/mt271028.aspx) Další informace o vytváření aplikací rozhraní .NET, které spravují HDInsight
* Použít [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) se dozvíte, jak provádět akce správy v clusterech prostředí HDInsight pomocí REST.
