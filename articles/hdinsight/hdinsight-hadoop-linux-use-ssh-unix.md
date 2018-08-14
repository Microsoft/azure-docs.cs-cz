---
title: Použití SSH se systémem Hadoop – Azure HDInsight
description: Pro přístup k systému HDInsight můžete použít Secure Shell (SSH). V tomto dokumentu najdete informace o připojení ke službě HDInsight pomocí příkazů SSH a SCP z klientů se systémem Windows, Linux, Unix nebo macOS.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
keywords: příkazy hadoop v linuxu, příkazy hadoop linux, hadoop macos, ssh hadoop, ssh hadoop cluster
ms.service: hdinsight
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 589e6d56fca1e987cb9fd086d76dc6fb8a686333
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594343"
---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Připojení k HDInsightu (Hadoop) pomocí protokolu SSH

Zjistěte, jak používat [SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) pro zabezpečené připojení ke clusteru Hadoop v Azure HDInsight. 

HDInsight může pro uzly v clusteru Hadoop jako operační systém využívat Linux (Ubuntu). Následující tabulka obsahuje informace o adrese a portu potřebné pro připojení ke službě HDInsight v Linuxu pomocí klienta SSH:

| Adresa | Port | Připojení... |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Hraniční uzel (Služby ML v HDInsightu) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Hraniční uzel (všechny ostatní typy clusteru, pokud hraniční uzel existuje) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Primární hlavní uzel |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Sekundární hlavní uzel |

> [!NOTE]
> Parametr `<edgenodename>` nahraďte názvem hraničního uzlu.
>
> Nahraďte `<clustername>` názvem svého clusteru.
>
> Pokud cluster obsahuje hraniční uzel, doporučujeme vám __připojovat se k hraničnímu uzlu vždy__ pomocí příkazu SSH. Hlavní uzly hostují služby, které jsou pro stav clusteru Hadoop klíčové. Na hraničním uzlu se spustí jenom to, co na něj umístíte.
>
> Další informace o použití hraničních uzlů najdete v tématu věnovaném [použití hraničních uzlů v HDInsightu](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]
> Když se poprvé připojíte ke službě HDInsight, ve vašem klientovi SSH se může zobrazit upozornění na nemožnost potvrzení pravosti hostitele. Po zobrazení výzvy vyberte yes (ano) a hostitel se přidá na seznam důvěryhodných serverů vašeho klienta SSH.
>
> Pokud jste se dříve připojili k serveru se stejným názvem, může se zobrazit upozornění na neshodu uloženého klíče hostitele s klíčem hostitele serveru. Postup pro odebrání existující položky pro název serveru najdete v dokumentaci k vašemu klientovi SSH.

## <a name="ssh-clients"></a>Klienti SSH

Systémy Linux, Unix a macOS nabízí příkazy `ssh` a `scp`. Klient `ssh` se v systému založeném na Linuxu nebo Unixu běžně používá k vytvoření vzdálené relace příkazového řádku. Klient `scp` slouží k bezpečnému kopírování souborů mezi vaším klientem a vzdáleným systémem.

Microsoft Windows ve výchozím nastavení neinstaluje žádné klienty SSH. Klienti `ssh` a `scp` jsou ve Windows k dispozici prostřednictvím následujících balíčků:

* Klient OpenSSH (Beta verze): Ve Windows 10 Fall Creators Update přejděte do __Nastavení__ > __Aplikace a funkce__ > __Spravovat volitelné funkce__ > __Přidat funkci__ a vyberte __Klient OpenSSH__. 

    > [!NOTE]
    > Pokud po povolení této funkce nejsou v PowerShellu dostupné příkazy `ssh` a `scp`, odhlaste se a znovu se přihlaste.

* [Bash na Ubuntu ve Windows 10:](https://msdn.microsoft.com/commandline/wsl/about) Příkazy `ssh` a `scp` jsou dostupné prostřednictvím Bashe na příkazovém řádku Windows.

* [Klient OpenSSH (beta)](https://blogs.msdn.microsoft.com/powershell/2017/12/15/using-the-openssh-beta-in-windows-10-fall-creators-update-and-windows-server-1709/): Toto je volitelná funkce představená v aktualizaci Windows 10 Fall Creators Update.

* [Azure Cloud Shell](../cloud-shell/quickstart.md): Balíček Cloud Shell přináší prostředí Bashe v prohlížeči a nabízí `ssh`, `scp` a další běžné linuxové příkazy.

* [Git (https://git-scm.com/)](https://git-scm.com/): Příkazy `ssh` a `scp` jsou k dispozici prostřednictvím příkazového řádku GitBash.

Můžete také použít několik grafických klientů SSH, jako je [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) nebo [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). I když je možné tyto klienty použít pro připojení k HDInsightu, proces připojení se od použití nástroje `ssh` liší. Další informace najdete v dokumentaci ke grafickému klientu, který používáte.

## <a id="sshkey"></a>Ověřování: Klíče SSH

Klíče SSH používají k ověřování relací SSH [šifrování s veřejným klíčem](https://en.wikipedia.org/wiki/Public-key_cryptography). Klíče SSH jsou bezpečnější než hesla a nabízí snadný způsob, jak zabezpečit přístup ke clusteru Hadoop.

Pokud je váš účet SSH zabezpečený pomocí klíče, klient musí při připojení poskytnout odpovídající privátní klíč:

* Většinu klientů jde nakonfigurovat k použití __výchozího klíče__. Například klient `ssh` v prostředích Linux a Unix hledá privátní klíč v `~/.ssh/id_rsa`.

* Můžete zadat __cestu k privátnímu klíči__. U klienta `ssh` se k zadání cesty k privátnímu klíči využívá parametr `-i`. Například, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Pokud máte __více privátních klíčů__, které používáte na různých serverech, zvažte používání nástroje, jako je [SSH agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). Nástroj `ssh-agent` může sloužit k automatickému výběru správného klíče při navazování relace SSH.

> [!IMPORTANT]
>
> Pokud privátní klíč zabezpečíte pomocí přístupového hesla, musíte při použití klíče zadat toto heslo. Nástroje, jako je `ssh-agent`, mohou pro usnadnění práce ukládat hesla do mezipaměti.

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K vytvoření souborů veřejného a privátního klíče použijte příkaz `ssh-keygen`. Následující příkaz generuje 2048bitový pár klíčů RSA, který je možné použít s HDInsightem:

    ssh-keygen -t rsa -b 2048

Během procesu vytváření klíčů se zobrazí výzva k zadání informací, třeba kde jsou klíče uložené nebo jestli se má použít přístupové heslo. Po dokončení tohoto procesu se vytvoří dva soubory: veřejný klíč a privátní klíč.

* __Veřejný klíč__ se používá k vytvoření clusteru HDInsight. Veřejný klíč má příponu `.pub`.

* __Privátní klíč__ se používá k ověření vašeho klienta pro cluster HDInsight.

> [!IMPORTANT]
> Klíče můžete zabezpečit pomocí přístupového hesla. Přístupové heslo je ve skutečnosti heslo k privátnímu klíči. I kdyby někdo získal váš privátní klíč, může ho použít jenom v případě, že má přístupové heslo.

### <a name="create-hdinsight-using-the-public-key"></a>Vytvoření HDInsightu s využitím veřejného klíče

| Metoda vytvoření | Jak použít veřejný klíč |
| ------- | ------- |
| **Azure Portal** | Zrušte zaškrtnutí políčka __Použít stejné heslo jako pro přihlášení ke clusteru__ a potom jako typ ověřování SSH vyberte __Veřejný klíč__. Nakonec vyberte soubor veřejného klíče nebo vložte testový obsah tohoto souboru do pole __Veřejný klíč SSH__.</br>![Dialogové okno Veřejný klíč SSH při vytváření clusteru HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Použijte parametr `-SshPublicKey` rutiny `New-AzureRmHdinsightCluster` a obsah veřejného klíče předejte jako řetězec.|
| **Azure CLI 1.0** | Použijte parametr `--sshPublicKey` příkazu `azure hdinsight cluster create` a obsah veřejného klíče předejte jako řetězec. |
| **Šablona Resource Manageru** | Příklad použití klíčů SSH s využití šablony najdete v části věnované [nasazení HDInsightu v Linuxu pomocí klíče SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). Element `publicKeys` v souboru [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) slouží při vytváření clusteru k předání klíčů do Azure. |

## <a id="sshpassword"></a>Ověřování: Heslo

Účty SSH je možné zabezpečit pomocí hesla. Když se připojíte k HDInsightu pomocí SSH, zobrazí se výzva k zadání hesla.

> [!WARNING]
> Microsoft nedoporučuje pro SSH používat ověřování heslem. Hesla se dají uhádnout a můžou se stát terčem útoku hrubou silou. Místo toho doporučujeme využívat [klíče SSH k ověřování](#sshkey).

> [!IMPORTANT]
> Platnost hesla účtu SSH vyprší 70 dní po vytvoření clusteru HDInsight. Pokud platnost hesla vyprší, můžete ho změnit pomocí informací v dokumentu [Správa HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Vytvoření HDInsight s využitím hesla

| Metoda vytvoření | Jak zadat heslo |
| --------------- | ---------------- |
| **Azure Portal** | Ve výchozím nastavení má uživatelský účet SSH stejné heslo jako účet pro přihlášení ke clusteru. Pokud chcete použít jiné heslo, zrušte zaškrtnutí políčka __Použít stejné heslo jako pro přihlášení ke clusteru__ a potom do pole __Heslo SSH__ zadejte požadované heslo.</br>![Dialogové okno Heslo SSH při vytváření clusteru HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Použijte parametr `--SshCredential` rutiny `New-AzureRmHdinsightCluster` a předejte objekt `PSCredential`, který obsahuje heslo a jméno uživatelského účtu SSH. |
| **Azure CLI 1.0** | Použijte parametr `--sshPassword` příkazu `azure hdinsight cluster create` a zadejte hodnotu hesla. |
| **Šablona Resource Manageru** | Příklad použití hesla s využitím šablony najdete v části věnované [nasazení HDInsightu v Linuxu pomocí hesla SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). Element `linuxOperatingSystemProfile` v souboru [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) slouží při vytváření clusteru k předání hesla a názvu učtu SSH do Azure.|

### <a name="change-the-ssh-password"></a>Změna hesla SSH

Informace o změně hesla uživatelského účtu SSH najdete v dokumentu věnovaném [správě HDInsightu](hdinsight-administer-use-portal-linux.md#change-passwords) v části __Změna hesel__.

## <a id="domainjoined"></a>Ověřování: HDInsight připojený k doméně

Pokud používáte __cluster HDInsight připojený k doméně__, musíte po připojení pomocí místního uživatele protokolu SSH použít příkaz `kinit`. Tento příkaz vás vyzve k zadání hesla a uživatele domény a ověří platnost vaší relace s využitím domény služby Azure Active Directory přidružené ke clusteru.

U každého uzlu připojeného k doméně (např. hlavního uzlu, hraničního uzlu) můžete povolit ověřování Kerberos, aby protokol SSH mohl používat doménový účet. To vyžaduje úpravu konfiguračního souboru sshd:
```bash
sudo vi /etc/ssh/sshd_config
```
Zrušte komentář a změňte `KerberosAuthentication` na `yes`.

```bash
sudo service sshd restart
```

Kdykoli budete potřebovat ověřit, zda se ověřování Kerberos zdařilo, použijte příkaz `klist`.

Další informace najdete v tématu [Konfigurace clusterů HDInsight připojených k doméně](./domain-joined/apache-domain-joined-configure.md).

## <a name="connect-to-nodes"></a>Připojení k uzlům

Hlavní uzly a hraniční uzel (pokud existuje) jsou přístupné přes internet na portech 22 a 23.

* Při připojování k __hlavním uzlům__ použijte port __22__ pro připojení k primárnímu hlavnímu uzlu a port __23__ pro připojení k sekundárnímu hlavnímu uzlu. Plně kvalifikovaný název domény, který byste měli použít, je `clustername-ssh.azurehdinsight.net`, kde `clustername` je název vašeho clusteru.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* Při připojování k __hraničnímu uzlu__ použijte port 22. Plně kvalifikovaný název domény je `edgenodename.clustername-ssh.azurehdinsight.net`, kde `edgenodename` je název zadaný při vytváření hraničního uzlu. `clustername` je název clusteru.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> V předchozích příkladech se předpokládá, že používáte ověřování heslem nebo že automaticky probíhá ověřování certifikátů. Pokud k ověřování používáte pár klíč-hodnota SSH a certifikát se nepoužívá automaticky, použijte parametr `-i` k zadání privátního klíče. Například, `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Jakmile budete připojeni, příkazový řádek se změní a zobrazí uživatelské jméno SSH a uzel, ke kterému jste připojeni. Například po připojení k primárnímu hlavnímu uzlu jako `sshuser` se na příkazovém řádku zobrazí `sshuser@hn0-clustername:~$`.

### <a name="connect-to-worker-and-zookeeper-nodes"></a>Připojení k pracovním uzlům a uzlům Zookeeper

Pracovní uzly a uzly Zookeeper nejsou přístupné přímo z internetu. Lze se k nim dostat z hlavních nebo hraničních uzlů clusteru. Toto jsou obecné kroky pro připojení k ostatním uzlům:

1. Pomocí SSH se připojte k hlavnímu nebo hraničnímu uzlu:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. V rámci připojení SSH k hlavnímu nebo hraničnímu uzlu použijte příkaz `ssh` pro připojení k pracovnímu uzlu v clusteru:

        ssh sshuser@wn0-myhdi

    Pokud chcete načíst seznam názvů uzlů, přečtěte si dokument [Správa HDInsight pomocí rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Pokud je účet SSH zabezpečený __heslem__, zadejte toto heslo při připojování.

Pokud je účet SSH zabezpečený __klíči SSH__, zkontrolujte, jestli je na klientovi zapnuté přesměrování SSH.

> [!NOTE]
> Dalším možným způsobem přímého přístupu ke všem uzlům clusteru je instalace HDInsightu do služby Azure Virtual Network. Potom můžete váš vzdálený počítač připojit ke stejné virtuální síti a získat přímý přístup ke všem uzlům v clusteru.
>
> Další informace najdete v tématu [Použití virtuální sítě s HDInsightem](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Konfigurace přesměrování agenta SSH

> [!IMPORTANT]
> V následujících krocích se předpokládá využití systému založeného na Linuxu nebo UNIXu a funkce Bash on Windows 10. Pokud tyto kroky pro váš systém nefungují, možná budete nahlédnout do dokumentace k příslušnému klientovi SSH.

1. V textovém editoru otevřete `~/.ssh/config`. Pokud tento soubor neexistuje, můžete ho vytvořit tak, že na příkazovém řádku zadáte `touch ~/.ssh/config`.

2. Do souboru `config` přidejte následující text.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Informace o __hostiteli__ nahraďte adresou uzlu, ke kterému se připojujete pomocí SSH. Předchozí příklad používá hraniční uzel. Tato položka nakonfiguruje přesměrování agenta SSH pro zadaný uzel.

3. Proveďte test přesměrování agenta SSH pomocí následujícího příkazu z terminálu:

        echo "$SSH_AUTH_SOCK"

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Když se nic nevrátí, znamená to, že nástroj `ssh-agent` není spuštěný. Další informace o spouštěcích skriptech agenta najdete v tématu [Použití nástroje SSH agent s SSH (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) nebo v dokumentaci ke klientovi SSH.

4. Jakmile si ověříte, že  se **ssh-agent** spustil, podle následujícího postupu přidejte svůj privátní klíč SSH k agentovi:

        ssh-add ~/.ssh/id_rsa

    Pokud je privátní klíč uložen v jiném souboru, nahraďte `~/.ssh/id_rsa` cestou k souboru.

5. Připojte se k hlavním uzlům nebo hraničnímu uzlu clusteru pomocí protokolu SSH. Potom se pomocí příkazu SSH k připojte k pracovnímu uzlu nebo k uzlu Zookeeper. Připojení se naváže s využitím přesměrovaného klíče.

## <a name="copy-files"></a>Kopírování souborů

Ke kopírování souborů na jednotlivé uzly clusteru nebo z nich je možné použít nástroj `scp`. Například následující příkaz zkopíruje soubor `test.txt` z místního systému na primární hlavní uzel:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Protože po `:` není zadaná žádná cesta, soubor se umístí do domovského adresáře `sshuser`.

Následující příklad zkopíruje soubor `test.txt` z domovského adresáře `sshuser` na primárním hlavním uzlu do místního systému:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]
> Nástroj `scp` má přístup pouze k systému souborů jednotlivých uzlů v rámci clusteru. Není možné ho použít pro přístup k datům v úložišti clusteru kompatibilním se systémem HDFS.
>
> Pokud potřebujete nahrát prostředek pro použití z relace SSH, použijte `scp`. Nahrajte například skript Pythonu a potom tento skript spusťte z relace SSH.
>
> Informace o přímém načítání dat do úložiště kompatibilního se systémem HDFS najdete v následujících dokumentech:
>
> * [HDInsight využívající Azure Storage](hdinsight-hadoop-use-blob-storage.md)
>
> * [HDInsight využívající Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md)

## <a name="next-steps"></a>Další kroky

* [Použití tunelování SSH s HDInsightem](hdinsight-linux-ambari-ssh-tunnel.md)
* [Použití virtuální sítě s HDInsightem](hdinsight-extend-hadoop-virtual-network.md)
* [Použití hraničních uzlů v HDInsightu](hdinsight-apps-use-edge-node.md#access-an-edge-node)
