---
title: Použití SSH se systémem Hadoop – Azure HDInsight
description: Pro přístup k systému HDInsight můžete použít Secure Shell (SSH). Tento dokument poskytuje informace o připojení ke službě HDInsight pomocí příkazů SSH z klientů Windows, Linux, UNIX nebo macOS.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: 074b1571cea6c102a00fcefe7934cad0ded8458d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087652"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH

Naučte se používat [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) k zabezpečenému připojení Apache Hadoop v Azure HDInsight. Informace o připojení přes virtuální síť najdete v tématu [Architektura virtuální sítě Azure HDInsight](./hdinsight-virtual-network-architecture.md). Viz také [Plánování nasazení virtuální sítě pro clustery Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

Následující tabulka obsahuje informace o adrese a portu potřebné pro připojení ke službě HDInsight pomocí klienta SSH:

| Adresa | Port | Připojení... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Primární hlavní uzel |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Sekundární hlavní uzel |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | hraniční uzel (služby ML v HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | hraniční uzel (jakýkoli jiný typ clusteru, pokud existuje hraniční uzel) |

Nahraďte `<clustername>` názvem svého clusteru. Parametr `<edgenodename>` nahraďte názvem hraničního uzlu.

Pokud cluster obsahuje hraniční uzel, doporučujeme vám __připojovat se k hraničnímu uzlu vždy__ pomocí příkazu SSH. Hlavní uzly hostují služby, které jsou pro stav clusteru Hadoop klíčové. Na hraničním uzlu se spustí jenom to, co na něj umístíte. Další informace o použití hraničních uzlů najdete v tématu věnovaném [použití hraničních uzlů v HDInsightu](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Když se poprvé připojíte ke službě HDInsight, ve vašem klientovi SSH se může zobrazit upozornění na nemožnost potvrzení pravosti hostitele. Po zobrazení výzvy vyberte yes (ano) a hostitel se přidá na seznam důvěryhodných serverů vašeho klienta SSH.
>
> Pokud jste se dříve připojili k serveru se stejným názvem, může se zobrazit upozornění na neshodu uloženého klíče hostitele s klíčem hostitele serveru. Postup pro odebrání existující položky pro název serveru najdete v dokumentaci k vašemu klientovi SSH.

## <a name="ssh-clients"></a>Klienti SSH

Systémy Linux, Unix a macOS nabízí příkazy `ssh` a `scp`. Klient `ssh` se v systému založeném na Linuxu nebo Unixu běžně používá k vytvoření vzdálené relace příkazového řádku. Klient `scp` slouží k bezpečnému kopírování souborů mezi vaším klientem a vzdáleným systémem.

Microsoft Windows ve výchozím nastavení neinstaluje žádné klienty SSH. Klienti `ssh` a `scp` jsou ve Windows k dispozici prostřednictvím následujících balíčků:

* [Klient OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse) Tento klient je volitelnou funkcí představenou ve Windows 10 – aktualizace tvůrců.

* [Bash na Ubuntu ve Windows 10](https://docs.microsoft.com/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). Cloud Shell poskytuje prostředí bash v prohlížeči.

* [Git](https://git-scm.com/).

K dispozici je také několik grafických klientů SSH, jako [jsou například výstupy a](https://www.chiark.greenend.org.uk/~sgtatham/putty/) [MobaXterm](https://mobaxterm.mobatek.net/). I když je možné tyto klienty použít pro připojení k HDInsightu, proces připojení se od použití nástroje `ssh` liší. Další informace najdete v dokumentaci k grafickému klientovi, který používáte.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Ověřování: Klíče SSH

Klíče SSH používají [kryptografii s veřejným klíčem](https://en.wikipedia.org/wiki/Public-key_cryptography) k ověřování relací SSH. Klíče SSH jsou bezpečnější než hesla a nabízí snadný způsob, jak zabezpečit přístup ke clusteru Hadoop.

Pokud je váš účet SSH zabezpečený pomocí klíče, klient musí při připojení poskytnout odpovídající privátní klíč:

* Většinu klientů jde nakonfigurovat k použití __výchozího klíče__. Například klient `ssh` v prostředích Linux a Unix hledá privátní klíč v `~/.ssh/id_rsa`.

* Můžete zadat __cestu k privátnímu klíči__. U klienta `ssh` se k zadání cesty k privátnímu klíči využívá parametr `-i`. Například, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Pokud máte __více privátních klíčů__, které používáte na různých serverech, zvažte používání nástroje, jako je [SSH agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). Nástroj `ssh-agent` může sloužit k automatickému výběru správného klíče při navazování relace SSH.

> [!IMPORTANT]  
> Pokud privátní klíč zabezpečíte pomocí přístupového hesla, musíte při použití klíče zadat toto heslo. Nástroje, jako je `ssh-agent`, mohou pro usnadnění práce ukládat hesla do mezipaměti.

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K vytvoření souborů veřejného a privátního klíče použijte příkaz `ssh-keygen`. Následující příkaz generuje 2048bitový pár klíčů RSA, který je možné použít s HDInsightem:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Během procesu vytváření klíčů budete vyzváni k zadání informací. třeba kde jsou klíče uložené nebo jestli se má použít přístupové heslo. Po dokončení tohoto procesu se vytvoří dva soubory: veřejný klíč a privátní klíč.

* __Veřejný klíč__ se používá k vytvoření clusteru HDInsight. Veřejný klíč má příponu `.pub`.

* __Privátní klíč__ se používá k ověření vašeho klienta pro cluster HDInsight.

> [!IMPORTANT]  
> Klíče můžete zabezpečit pomocí přístupového hesla. Přístupové heslo je ve skutečnosti heslo k privátnímu klíči. I kdyby někdo získal váš privátní klíč, může ho použít jenom v případě, že má přístupové heslo.

### <a name="create-hdinsight-using-the-public-key"></a>Vytvoření HDInsightu s využitím veřejného klíče

| Metoda vytvoření | Jak použít veřejný klíč |
| ------- | ------- |
| portál Azure | Zrušte zaškrtnutí políčka __použít heslo přihlášení clusteru pro SSH__a pak jako typ ověřování SSH vyberte __veřejný klíč__ . Nakonec vyberte soubor veřejného klíče nebo vložte testový obsah tohoto souboru do pole __Veřejný klíč SSH__.</br>![Dialogové okno Veřejný klíč SSH při vytváření clusteru HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | Použijte `-SshPublicKey` parametr rutiny [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) a obsah veřejného klíče předejte jako řetězec.|
| Azure CLI | Použijte `--sshPublicKey` parametr [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) příkazu a předejte obsah veřejného klíče jako řetězec. |
| Šablona Resource Manageru | Příklad použití klíčů SSH s využití šablony najdete v části věnované [nasazení HDInsightu v Linuxu pomocí klíče SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). Element `publicKeys` v souboru [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) slouží při vytváření clusteru k předání klíčů do Azure. |

## <a name="authentication-password"></a>Ověřování: Heslo

Účty SSH je možné zabezpečit pomocí hesla. Když se připojíte ke službě HDInsight pomocí SSH, budete vyzváni k zadání hesla.

> [!WARNING]  
> Microsoft nedoporučuje pro SSH používat ověřování heslem. Hesla se dají uhádnout a můžou se stát terčem útoku hrubou silou. Místo toho doporučujeme využívat [klíče SSH k ověřování](#sshkey).

> [!IMPORTANT]  
> Platnost hesla účtu SSH vyprší 70 dní po vytvoření clusteru HDInsight. Pokud platnost hesla vyprší, můžete ho změnit pomocí informací v dokumentu [Správa HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Vytvoření HDInsight s využitím hesla

| Metoda vytvoření | Jak zadat heslo |
| --------------- | ---------------- |
| portál Azure | Ve výchozím nastavení má uživatelský účet SSH stejné heslo jako účet pro přihlášení ke clusteru. Chcete-li použít jiné heslo, zrušte políčko __použít heslo přihlášení clusteru pro SSH__a potom zadejte heslo do pole __heslo SSH__ .</br>![Dialogové okno Heslo SSH při vytváření clusteru HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | Použijte `--SshCredential` parametr rutiny [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) a předejte `PSCredential` objekt, který obsahuje název a heslo uživatelského účtu SSH. |
| Azure CLI | Použijte `--ssh-password` parametr [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) příkazu a zadejte hodnotu pro heslo. |
| Šablona Resource Manageru | Příklad použití hesla s využitím šablony najdete v části věnované [nasazení HDInsightu v Linuxu pomocí hesla SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). Element `linuxOperatingSystemProfile` v souboru [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) slouží při vytváření clusteru k předání hesla a názvu učtu SSH do Azure.|

### <a name="change-the-ssh-password"></a>Změna hesla SSH

Informace o změně hesla uživatelského účtu SSH najdete v dokumentu věnovaném [správě HDInsightu](hdinsight-administer-use-portal-linux.md#change-passwords) v části __Změna hesel__.

## <a name="authentication-domain-joined-hdinsight"></a>HDInsight připojené k doméně ověřování

Pokud používáte __cluster HDInsight připojený k doméně__, musíte použít `kinit` příkaz po připojení k místnímu uživateli SSH. Tento příkaz vás vyzve k zadání hesla a uživatele domény a ověří platnost vaší relace s využitím domény služby Azure Active Directory přidružené ke clusteru.

Ověřování protokolem Kerberos můžete také povolit na všech uzlech připojených k doméně (například hlavní uzel, hraniční uzel) k SSH pomocí účtu domény. To vyžaduje úpravu konfiguračního souboru sshd:

```bash
sudo vi /etc/ssh/sshd_config
```

Zrušte komentář u položky `KerberosAuthentication` a změňte její hodnotu na `yes`.

```bash
sudo service sshd restart
```

Pomocí `klist` příkazu ověřte, zda bylo ověření protokolu Kerberos úspěšné.

Další informace najdete v tématu [Konfigurace clusterů HDInsight připojených k doméně](./domain-joined/apache-domain-joined-configure.md).

## <a name="connect-to-nodes"></a>Připojení k uzlům

Hlavní uzly a hraniční uzel (pokud existuje) je k dispozici přes Internet na portech 22 a 23.

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

Po připojení se výzva změní, aby označovala uživatelské jméno SSH a uzel, ke kterému jste se připojili. Například po připojení k primárnímu hlavnímu uzlu jako `sshuser` se na příkazovém řádku zobrazí `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Připojení k uzlům pracovních procesů a Apache Zookeeper

Pracovní uzly a uzly Zookeeper nejsou přímo přístupné z Internetu. Lze se k nim dostat z hlavních nebo hraničních uzlů clusteru. Toto jsou obecné kroky pro připojení k ostatním uzlům:

1. Pomocí SSH se připojte k hlavnímu nebo hraničnímu uzlu:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. V rámci připojení SSH k hlavnímu nebo hraničnímu uzlu použijte příkaz `ssh` pro připojení k pracovnímu uzlu v clusteru:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Pokud chcete načíst seznam názvů uzlů, přečtěte si téma [Správa HDInsight pomocí dokumentu REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) .

Pokud je účet SSH zabezpečený __heslem__, zadejte toto heslo při připojování.

Pokud je účet SSH zabezpečený __klíči SSH__, zkontrolujte, jestli je na klientovi zapnuté přesměrování SSH.

> [!NOTE]  
> Dalším možným způsobem přímého přístupu ke všem uzlům clusteru je instalace HDInsightu do služby Azure Virtual Network. Potom můžete váš vzdálený počítač připojit ke stejné virtuální síti a získat přímý přístup ke všem uzlům v clusteru.
>
> Další informace najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>Konfigurace přesměrování agenta SSH

> [!IMPORTANT]  
> V následujících krocích se předpokládá využití systému založeného na Linuxu nebo UNIXu a funkce Bash on Windows 10. Pokud tyto kroky pro váš systém nefungují, možná budete nahlédnout do dokumentace k příslušnému klientovi SSH.

1. V textovém editoru otevřete `~/.ssh/config`. Pokud tento soubor neexistuje, můžete ho vytvořit tak, že na příkazovém řádku zadáte `touch ~/.ssh/config`.

2. Do souboru `config` přidejte následující text.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Informace o __hostiteli__ nahraďte adresou uzlu, ke kterému se připojujete pomocí SSH. Předchozí příklad používá hraniční uzel. Tato položka nakonfiguruje přesměrování agenta SSH pro zadaný uzel.

3. Proveďte test přesměrování agenta SSH pomocí následujícího příkazu z terminálu:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Pokud není nic vráceno, `ssh-agent` neběží. Další informace o spouštěcích skriptech agenta najdete v tématu [Použití nástroje SSH agent s SSH (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) nebo v dokumentaci ke klientovi SSH.

4. Jakmile ověříte, že je **Agent SSH** spuštěný, přidejte k agentovi privátní klíč SSH pomocí následujícího kódu:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Pokud je privátní klíč uložen v jiném souboru, nahraďte `~/.ssh/id_rsa` cestou k souboru.

5. Připojte se k hlavním uzlům nebo hraničnímu uzlu clusteru pomocí protokolu SSH. Potom se pomocí příkazu SSH k připojte k pracovnímu uzlu nebo k uzlu Zookeeper. Připojení se naváže s využitím přesměrovaného klíče.

## <a name="next-steps"></a>Další kroky

* [Použití tunelování SSH s HDInsightem](hdinsight-linux-ambari-ssh-tunnel.md)
* [Použití hraničních uzlů v HDInsightu](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [Použití SCP s HDInsightem](./use-scp.md)
