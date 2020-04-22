---
title: Vývoj akcí skriptů pro přizpůsobení clusterů Azure HDInsight
description: Přečtěte si, jak pomocí skriptů Bash přizpůsobit clustery HDInsight. Akce skriptu umožňují spouštět skripty během nebo po vytvoření clusteru, měnit nastavení konfigurace clusteru nebo instalovat další software.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: db37a56ffbf0cb64530f8f7af38841bac72c77d4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767553"
---
# <a name="script-action-development-with-hdinsight"></a>Vývoj akce skriptů s HDInsight

Přečtěte si, jak přizpůsobit cluster HDInsight pomocí skriptů Bash. Akce skriptu jsou způsob, jak přizpůsobit HDInsight během nebo po vytvoření clusteru.

## <a name="what-are-script-actions"></a>Co jsou akce skriptu

Akce skriptu jsou bash skripty, které Azure spouští na uzlech clusteru, aby mohly provádět změny konfigurace nebo instalovat software. Akce skriptu je spuštěna jako root a poskytuje úplná přístupová práva k uzlům clusteru.

Akce skriptu lze použít pomocí následujících metod:

| Pomocí této metody můžete použít skript... | Během vytváření clusteru... | Ve spuštěném clusteru... |
| --- |:---:|:---:|
| portál Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure Classic CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Šablona Azure Resource Manageru |✓ |&nbsp; |

Další informace o použití těchto metod k použití akcí skriptu naleznete v [tématu Přizpůsobení clusterů HDInsight pomocí akcí skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Doporučené postupy pro vývoj skriptů

Při vývoji vlastního skriptu pro cluster HDInsight je třeba mít na paměti několik osvědčených postupů:

* [Zaměřte se na verzi Apache Hadoop](#bPS1)
* [Cílení na verzi operačního systému](#bps10)
* [Poskytnutí stabilních odkazů na prostředky skriptu](#bPS2)
* [Použití předem zkompilovaných prostředků](#bPS4)
* [Ujistěte se, že skript přizpůsobení clusteru je idempotentní](#bPS3)
* [Zajištění vysoké dostupnosti architektury clusteru](#bPS5)
* [Konfigurace vlastních součástí pro použití úložiště objektů Blob Azure](#bPS6)
* [Zapsat informace stdout a STDERR](#bPS7)
* [Uložení souborů jako ASCII s koncovkami řádků LF](#bps8)
* [Použití logiky opakování k zotavení z přechodných chyb](#bps9)

> [!IMPORTANT]  
> Akce skriptu musí být dokončeny do 60 minut, jinak se proces nezdaří. Během zřizování uzlů se skript spouští souběžně s jinými procesy instalace a konfigurace. Konkurence pro prostředky, jako je například čas procesoru nebo šířka pásma sítě může způsobit skript trvat déle dokončit než ve vývojovém prostředí.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Zaměřte se na verzi Apache Hadoop

Různé verze HDInsight mají různé verze služeb Hadoop a nainstalovaných součástí. Pokud váš skript očekává určitou verzi služby nebo součásti, měli byste použít pouze skript s verzí HDInsight, která obsahuje požadované součásti. Informace o verzích komponent, které jsou součástí služby HDInsight, najdete v dokumentu pro [správu verzí komponent HDInsight.](hdinsight-component-versioning.md)

### <a name="checking-the-operating-system-version"></a>Kontrola verze operačního systému

Různé verze HDInsight spoléhají na konkrétní verze Ubuntu. Mezi verzemi operačního systému mohou existovat rozdíly, které je nutné zkontrolovat ve skriptu. Například možná budete muset nainstalovat binární soubor, který je vázán na verzi Ubuntu.

Chcete-li zkontrolovat verzi `lsb_release`operačního systému, použijte . Například následující skript ukazuje, jak odkazovat na konkrétní soubor tar v závislosti na verzi operačního systému:

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

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>Cílení na verzi operačního systému

HDInsight je založen na distribuci Ubuntu Linux. Různé verze HDInsight spoléhají na různé verze Ubuntu, což může změnit, jak se váš skript chová. Například HDInsight 3.4 a starší jsou založeny na verzích Ubuntu, které používají Upstart. Verze 3.5 a vyšší jsou založeny na Ubuntu 16.04, který používá Systemd. Systemd a Upstart spoléhají na různé příkazy, takže váš skript by měl být napsán pro práci s oběma.

Dalším důležitým rozdílem mezi HDInsight 3.4 `JAVA_HOME` a 3.5 je, že nyní odkazuje na Java 8. Následující kód ukazuje, jak zjistit, zda je skript spuštěn na Ubuntu 14 nebo 16:

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

Úplný skript, který obsahuje tyto úryvky, najdete na adrese https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Verze Ubuntu, kterou používá HDInsight, najdete v dokumentu [verze komponenty HDInsight.](hdinsight-component-versioning.md)

Chcete-li pochopit rozdíly mezi Systemd a Upstart, viz [Systemd pro uživatele povýšenec](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Poskytnutí stabilních odkazů na prostředky skriptu

Skript a přidružené prostředky musí zůstat k dispozici po celou dobu trvání clusteru. Tyto prostředky jsou vyžadovány, pokud nové uzly jsou přidány do clusteru během operací škálování.

Osvědčeným postupem je stáhnout a archivovat vše v účtu Azure Storage v rámci předplatného.

> [!IMPORTANT]  
> Použitý účet úložiště musí být výchozím účtem úložiště pro cluster nebo veřejným kontejnerem jen pro čtení v jakémkoli jiném účtu úložiště.

Například ukázky poskytované společností Microsoft [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) jsou uloženy v účtu úložiště. Toto umístění je veřejný kontejner jen pro čtení spravovaný týmem HDInsight.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Použití předem zkompilovaných prostředků

Chcete-li zkrátit dobu potřebné ke spuštění skriptu, vyhněte se operacím, které kompilují prostředky ze zdrojového kódu. Například předkompilujte prostředky a uložte je do objektu blob účtu Azure Storage ve stejném datovém centru jako HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Ujistěte se, že skript přizpůsobení clusteru je idempotentní

Skripty musí být idempotentní. Pokud skript spustí vícekrát, by měl vrátit clusterdo stejného stavu pokaždé.

Například skript, který upravuje konfigurační soubory, by neměl přidávat duplicitní položky, pokud byl spuštěn vícekrát.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Zajištění vysoké dostupnosti architektury clusteru

Clustery HDInsight založené na Linuxu poskytují dva hlavní uzly, které jsou aktivní v rámci clusteru, a akce skriptu běží na obou uzlech. Pokud instalují součásti, očekávejte pouze jeden hlavní uzel, neinstalujte součásti na oba hlavní uzly.

> [!IMPORTANT]  
> Služby poskytované jako součást HDInsight jsou navrženy tak, aby převzetí služeb při selhání mezi dvěma hlavní uzly podle potřeby. Tato funkce není rozšířena na vlastní součásti nainstalované prostřednictvím akcí skriptu. Pokud potřebujete vysokou dostupnost pro vlastní součásti, je nutné implementovat vlastní mechanismus převzetí služeb při selhání.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Konfigurace vlastních součástí pro použití úložiště objektů Blob Azure

Součásti, které nainstalujete do clusteru, mohou mít výchozí konfiguraci, která používá úložiště Distribuovaného souborového systému Apache Hadoop (HDFS). HDInsight používá jako výchozí úložiště azure storage nebo úložiště datových jezer. Obě poskytují systém souborů kompatibilní s HDFS, který uchovává data i v případě, že je cluster odstraněn. Možná budete muset nakonfigurovat součásti, které nainstalujete, aby místo HDFS používaly WASB nebo ADL.

U většiny operací není nutné zadávat systém souborů. Následující zkopíruje například soubor hadoop-common.jar z místního systému souborů do úložiště clusteru:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

V tomto příkladu `hdfs` příkaz transparentně používá výchozí úložiště clusteru. U některých operací může být nutné zadat identifikátor URI. Například `adl:///example/jars` pro Azure Data Lake `abfs:///example/jars` Storage Gen1, pro `wasb:///example/jars` data Lake Storage Gen2 nebo pro Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Zapsat informace stdout a STDERR

HDInsight protokoluje výstup skriptu, který je zapsán do STDOUT a STDERR. Tyto informace můžete zobrazit pomocí webového uživatelského uživatelského uživatelského panelu Ambari.

> [!NOTE]  
> Apache Ambari je k dispozici pouze v případě, že je cluster úspěšně vytvořen. Pokud při vytváření clusteru použijete akci skriptu a vytvoření se nezdaří, [přečtěte si článek Poradce při potížích s akcemi skriptu,](./troubleshoot-script-action.md) kde najdete další způsoby přístupu k protokolované informace.

Většina nástrojů a instalačníbalíčky již zapisovat informace STDOUT a STDERR, ale můžete chtít přidat další protokolování. Chcete-li odeslat text `echo`do stdout, použijte . Příklad:

```bash
echo "Getting ready to install Foo"
```

Ve výchozím `echo` nastavení odešle řetězec STDOUT. Chcete-li jej nasměrovat `>&2` na `echo`STDERR, přidejte před . Příklad:

```bash
>&2 echo "An error occurred installing Foo"
```

To přesměruje informace zapsané do STDOUT na STDERR (2) místo. Další informace o přesměrování vi. [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)

Další informace o zobrazení informací zaznamenaných pomocí akcí skriptu naleznete v [tématu Poradce při potížích s akcemi skriptu](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a>Uložení souborů jako ASCII s koncovkami řádků LF

Bash skripty by měly být uloženy ve formátu ASCII, s řádky ukončena LF. Soubory, které jsou uloženy jako UTF-8, nebo použít CRLF jako konec řádku může selhat s následující chybou:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a>Použití logiky opakování k zotavení z přechodných chyb

Při stahování souborů, instalaci balíčků pomocí apt-get nebo jiných akcí, které přenášejí data přes Internet, může akce selhat z důvodu přechodných chyb v síti. Například vzdálený prostředek, se kterým komunikujete, může být v procesu převzetí služeb při selhání do záložního uzlu.

Chcete-li skript odolné vůči přechodným chybám, můžete implementovat logiku opakování. Následující funkce ukazuje, jak implementovat logiku opakování. Zopakuje operaci třikrát před selháním.

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

Pomocné metody akce skriptu jsou nástroje, které můžete použít při psaní vlastních skriptů. Tyto metody jsou obsaženy ve skriptu. [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) Ke stažení a použití je použijte jako součást skriptu:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Následující pomocníci, kteří jsou k dispozici pro použití ve skriptu:

| Pomocné použití | Popis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Stáhne soubor ze zdrojového identifikátoru URI do zadané cesty k souboru. Ve výchozím nastavení nepřepíše existující soubor. |
| `untar_file TARFILE DESTDIR` |Extrahuje soubor tar `-xf`(pomocí) do cílového adresáře. |
| `test_is_headnode` |Pokud byl spuštěn na hlavním uzlu clusteru, vrátí 1; jinak 0. |
| `test_is_datanode` |Pokud je aktuální uzel datový (pracovní) uzel, vrátí 1; jinak 0. |
| `test_is_first_datanode` |Pokud aktuální uzel je první data (pracovní) uzel (s názvem workernode0) vrátit 1; jinak 0. |
| `get_headnodes` |Vraťte plně kvalifikovaný název domény hlavových uzlů v clusteru. Názvy jsou čárky oddělené. Při chybě je vrácen prázdný řetězec. |
| `get_primary_headnode` |Získá plně kvalifikovaný název domény primárního headnode. Při chybě je vrácen prázdný řetězec. |
| `get_secondary_headnode` |Získá plně kvalifikovaný název domény sekundární headnode. Při chybě je vrácen prázdný řetězec. |
| `get_primary_headnode_number` |Získá číselnou příponu primárního headnode. Při chybě je vrácen prázdný řetězec. |
| `get_secondary_headnode_number` |Získá číselnou příponu sekundární houfnici. Při chybě je vrácen prázdný řetězec. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Běžné vzorce používání

Tato část obsahuje pokyny k implementaci některé běžné vzory použití, které může narazit při psaní vlastní skript.

### <a name="passing-parameters-to-a-script"></a>Předávání parametrů skriptu

V některých případech může skript vyžadovat parametry. Například můžete potřebovat heslo správce pro cluster při použití rozhraní REST API Ambari.

Parametry předané skriptu jsou označovány jako *poziční parametry*a jsou přiřazeny pro `$1` první parametr, `$2` pro druhý a tak dále. `$0`obsahuje název samotného skriptu.

Hodnoty předané skriptu jako parametry by měly být uzavřeny jednoduchými uvozovkami ('). Tím zajistíte, že předaná hodnota je považována za literál.

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Nastavení proměnné prostředí se provádí následujícím příkazem:

    VARIABLENAME=value

Kde VARIABLENAME je název proměnné. Chcete-li získat `$VARIABLENAME`přístup k proměnné, použijte . Chcete-li například přiřadit hodnotu poskytovanou pozičním parametrem jako proměnnou prostředí s názvem PASSWORD, použijte následující příkaz:

    PASSWORD=$1

Následný přístup k informacím `$PASSWORD`by pak mohl být využit .

Proměnné prostředí nastavené ve skriptu existují pouze v rámci oboru skriptu. V některých případech může být nutné přidat systémové proměnné prostředí, které budou přetrvávat i po dokončení skriptu. Chcete-li přidat systémové proměnné prostředí, `/etc/environment`přidejte proměnnou do . Například následující příkaz `HADOOP_CONF_DIR`přidá :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Přístup k místům, kde jsou uloženy vlastní skripty

Skripty používané k přizpůsobení clusteru musí být uloženy v jednom z následujících umístění:

* __Účet Azure Storage,__ který je přidružený ke clusteru.

* __Další účet úložiště__ přidružený ke clusteru.

* __Veřejně čitelný identifikátor URI__. Například adresa URL dat uložených na OneDrivu, Dropboxu nebo jiné službě hostování souborů.

* __Účet Azure Data Lake Storage,__ který je přidružený ke clusteru HDInsight. Další informace o používání Azure Data Lake Storage s HDInsight najdete v [tématu Úvodní příručka: Nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > Instanční objekt HDInsight používá pro přístup k úložiště datového jezera musí mít přístup pro čtení ke skriptu.

Prostředky používané skriptem musí být také veřejně dostupné.

Ukládání souborů v účtu Azure Storage nebo Azure Data Lake Storage poskytuje rychlý přístup, jak v rámci sítě Azure.

> [!NOTE]  
> Formát identifikátoru URI používaný k odkazování na skript se liší v závislosti na používané službě. Pro účty úložiště přidružené ke clusteru HDInsight použijte `wasb://` nebo `wasbs://`. Pro veřejně čitelné identifikátory `http://` `https://`URI použijte nebo . Pro úložiště datových `adl://`jezer použijte .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Kontrolní seznam pro nasazení akce skriptu

Tady jsou kroky při přípravě na nasazení skriptu:

* Umístěte soubory, které obsahují vlastní skripty na místo, které je přístupné uzly clusteru během nasazení. Například výchozí úložiště pro cluster. Soubory mohou být také uloženy ve veřejně čitelných hostingových službách.
* Ověřte, zda je skript idempotentní. To umožňuje skript uvede vícekrát na stejném uzlu.
* Pomocí dočasného adresáře souborů /tmp uchovávejte stažené soubory používané skripty a poté je vyčistěte po spuštění skriptů.
* Pokud dojde ke změně nastavení na úrovni operačního systému nebo konfiguračních souborů služby Hadoop, můžete restartovat služby HDInsight.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Jak spustit akci skriptu

Akce skriptu můžete použít k přizpůsobení clusterů HDInsight pomocí následujících metod:

* portál Azure
* Azure PowerShell
* Šablony Azure Resource Manageru
* Sada HDInsight .NET SDK.

Další informace o použití jednotlivých metod naleznete v tématu [Použití akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Ukázky vlastních skriptů

Společnost Microsoft poskytuje ukázkové skripty pro instalaci součástí v clusteru HDInsight. Viz [Instalace a použití hue na clusterech HDInsight](hdinsight-hadoop-hue-linux.md) jako ukázková akce skriptu.

## <a name="troubleshooting"></a>Poradce při potížích

Níže jsou uvedeny chyby, se kterými se můžete sejít při používání skriptů, které jste vyvinuli:

**Chyba** `$'\r': command not found`: . Někdy následuje `syntax error: unexpected end of file`.

*Příčina*: Tato chyba je způsobena, když řádky ve skriptu končí CRLF. Unixové systémy očekávají jako konec linky pouze LF.

K tomuto problému nejčastěji dochází, když je skript vytvořen v prostředí systému Windows, protože CRLF je společný řádek končící pro mnoho textových editorů v systému Windows.

*Rozlišení*: Pokud je to možnost v textovém editoru, vyberte formát Unix nebo LF pro koncovku řádku. Můžete také použít následující příkazy v systému Unix změnit CRLF na LF:

> [!NOTE]  
> Následující příkazy jsou zhruba ekvivalentní v tom, že by měly změnit konce řádku CRLF na LF. Vyberte jeden na základě nástrojů dostupných ve vašem systému.

| Příkaz | Poznámky |
| --- | --- |
| `unix2dos -b INFILE` |Původní soubor je zálohován pomocí . Rozšíření BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE obsahuje verzi s pouze LF zakončeními |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Upraví soubor přímo |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE obsahuje verzi s pouze LF zakončení. |

**Chyba** `line 1: #!/usr/bin/env: No such file or directory`: .

*Příčina*: K této chybě dochází, když byl skript uložen jako UTF-8 s označením objednávky bajtů (BOM).

*Rozlišení*: Uložte soubor buď jako ASCII, nebo jako UTF-8 bez kusovníku. Můžete také použít následující příkaz na Linux nebo Unix systému k vytvoření souboru bez kusovníku:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Nahraďte `INFILE` souborem obsahujícím kusovník. `OUTFILE`by měl být nový název souboru, který obsahuje skript bez kusovníku.

## <a name="next-steps"></a><a name="seeAlso"></a>Další kroky

* Přečtěte [si, jak přizpůsobit clustery HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
* Další informace o vytváření aplikací .NET, které spravují HDInsight, naleznete v [odkazu hdinsight .NET SDK.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* Pomocí [rozhraní HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) se dozvíte, jak pomocí rest k provádění akcí správy v clusterech HDInsight.
