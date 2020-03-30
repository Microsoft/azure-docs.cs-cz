---
title: Přístup k Azure HDInsight pomocí tunelového propojení SSH
description: Naučte se používat tunel SSH k bezpečnému procházení webových prostředků hostovaných na uzlech HDInsight založených na Linuxu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991355"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Použití tunelového propojení SSH pro přístup k webovému uživatelskému uživatelskému uživatelskému uživatelskému uživatelskému uživatelskému nastavení Apache Ambari, JobHistory, NameNode, Apache Oozie a dalších ui

Clustery HDInsight poskytují přístup k webovému uživatelskému rozhraní Apache Ambari přes Internet, ale některé funkce vyžadují tunel SSH. Například webové uživatelské uživatelské okno pro službu Apache Oozie nelze přistupovat přes internet bez tunelu SSh.

## <a name="why-use-an-ssh-tunnel"></a>Proč používat tunel SSH

Několik menu v Ambari pracuje pouze tunelem SSH. Tyto nabídky spoléhají na webové servery a služby spuštěné na jiných typech uzlů, jako jsou pracovní uzly.

Následující webová una vyžadují tunelové propojení SSH:

* Historie úloh
* NameNode
* Zásobníky vláken
* Webové uživatelské uzlina Oozie
* Hlavní a protokolové hlavní a protokolové umělá iv

Pokud k přizpůsobení clusteru používáte akce skriptu, vyžadují všechny služby nebo nástroje, které nainstalujete a které zveřejňují webovou službu, tunelové propojení SSH. Pokud například nainstalujete Hue pomocí akce skriptu, musíte použít tunel SSH pro přístup k webovému uživatelskému uživatelskému uživatelskému uživatelskému nastavení Hue.

> [!IMPORTANT]  
> Pokud máte přímý přístup k HDInsight prostřednictvím virtuální sítě, není nutné používat tunely SSH. Příklad přímého přístupu k HDInsightprostřednictvím virtuální sítě najdete v tématu [Připojení HDInsightu k místnímu síťovému](connect-on-premises-network.md) dokumentu.

## <a name="what-is-an-ssh-tunnel"></a>Co je tunel SSH

[Zabezpečené shell (SSH) tunelové propojení](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) spojuje port v místním počítači s hlavním uzlem na HDInsight. Přenosy odeslané do místního portu jsou směrovány prostřednictvím připojení SSH k hlavnímu uzlu. Požadavek je vyřešen, jako by pochází z hlavního uzlu. Odpověď je pak směrována zpět tunelem na pracovní stanici.

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Webový prohlížeč, který lze nakonfigurovat tak, aby používal proxy server SOCKS5.

    > [!WARNING]  
    > Podpora proxy SOCKS integrovaná v nastavení Internetu systému Windows nepodporuje SOCKS5 a nefunguje s kroky v tomto dokumentu. Následující prohlížeče spoléhají na nastavení serveru proxy systému Windows a v současné době nepracují s kroky v tomto dokumentu:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome také spoléhá na nastavení proxy systému Windows. Můžete však nainstalovat rozšíření, která podporují SOCKS5. Doporučujeme [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Vytvoření tunelového propojení pomocí příkazu SSH

Pomocí následujícího příkazu vytvořte tunel `ssh` SSH pomocí příkazu. Nahraďte `sshuser` uživatelem SSH pro cluster `CLUSTERNAME` HDInsight a nahraďte názvem clusteru HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Tento příkaz vytvoří připojení, které směruje provoz na místní port 9876 do clusteru přes SSH. Dostupné možnosti:

* **D 9876** - Místní přístav, který směruje dopravu tunelem.
* **C** - Komprimovat všechna data, protože webový provoz je většinou text.
* **2** - Force SSH vyzkoušet protokol verze 2 pouze.
* **q** - Tichý režim.
* **T** - Zakázat pseudo-tty přidělení, protože jste právě předávání portu.
* **n** - Zabránit čtení STDIN, protože jste právě předávání portu.
* **N** - Nespouštějte vzdálený příkaz, protože právě předáváte port.
* **f** - Spustit na pozadí.

Po dokončení příkazu je provoz odeslaný na port 9876 v místním počítači směrován do hlavního uzlu clusteru.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Vytvoření tunelu pomocí PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) je grafický SSH klient pro Windows. Pokud nejste obeznámeni s PuTTY, podívejte se na [dokumentaci PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Pomocí následujících kroků vytvořte tunel SSH pomocí putty:

### <a name="create-or-load-a-session"></a>Vytvoření nebo načtení relace

1. Otevřete PuTTY a ujistěte **se, že** je v levém menu vybrána možnost Session. Pokud jste již relaci uložili, vyberte název relace ze seznamu **Uložené relace** a vyberte **Načíst**.

1. Pokud ještě nemáte uloženou relaci, zadejte informace o připojení:

    |Vlastnost |Hodnota |
    |---|---|
    |Název hostitele (nebo IP adresa)|Adresa SSH pro cluster HDInsight. Například **mycluster-ssh.azurehdinsight.net**.|
    |Port|22|
    |Typ připojení|SSH|

1. Vyberte **Uložit**.

    ![HDInsight vytvořit tmel relace](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. V části **Kategorie** nalevo od dialogového okna rozbalte **položku Připojení**, rozbalte **položku SSH**a vyberte **položku Tunely**.

1. Zadejte následující informace o **možnostech řízení** ssh port předávání formuláře:

    |Vlastnost |Hodnota |
    |---|---|
    |Zdrojový port|Port na klienta, který chcete předat dál. Například **9876**.|
    |Cíl|Adresa SSH pro cluster HDInsight. Například **mycluster-ssh.azurehdinsight.net**.|
    |Dynamická|Umožňuje dynamické směrování proxy SOCKS.|

    ![Možnosti tunelového propojení PuTTY Configuration](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Vyberte **Přidat,** chcete-li přidat nastavení, a pak vyberte **Otevřít,** chcete-li otevřít připojení SSH.

1. Po zobrazení výzvy se přihlaste k serveru.

## <a name="use-the-tunnel-from-your-browser"></a>Použití tunelu z prohlížeče

> [!IMPORTANT]  
> Kroky v této části používají prohlížeč Mozilla FireFox, protože poskytuje stejné nastavení proxy serveru na všech platformách. Jiné moderní prohlížeče, jako je google chrome, mohou vyžadovat rozšíření, jako je FoxyProxy pro práci s tunelem.

1. Nakonfigurujte prohlížeč tak, aby používal **localhost** a port, který jste použili při vytváření tunelu jako proxy **serveru SOCKS v5.** Zde je to, co nastavení Firefoxu vypadat. Pokud jste použili jiný port než 9876, změňte port na ten, který jste použili:

    ![firefox nastavení proxy prohlížeče](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Výběr **vzdálenéslužby DNS** vyřeší požadavky dns (Domain Name System) pomocí clusteru HDInsight. Toto nastavení řeší službu DNS pomocí hlavního uzlu clusteru.

2. Ověřte, zda tunelové propojení [https://www.whatismyip.com/](https://www.whatismyip.com/)funguje, a to tak, že navštívíte web, například . Vrácená ADRESA IP by měla být použitá datovým centrem Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Ověření pomocí webového uživatelského rozhraní Ambari

Po vytvoření clusteru ověřte pomocí následujících kroků, zda máte přístup k webovým rozhraním služby z webu Ambari:

1. V prohlížeči přejděte na `http://headnodehost:8080`. Adresa `headnodehost` je odeslána tunelovým propojením do clusteru a překládá na hlavní uzel, na který je spuštěna ambari. Po zobrazení výzvy zadejte uživatelské jméno správce (správce) a heslo pro váš cluster. Webové uživatelské uživatelské místo Ambari vás může vyzvat podruhé. Pokud ano, zadejte informace znovu.

   > [!NOTE]  
   > Při použití `http://headnodehost:8080` adresy pro připojení ke clusteru se připojujete tunelovým propojením. Komunikace je zabezpečena pomocí tunelového propojení SSH namísto PROTOKOLU HTTPS. Chcete-li se připojit přes `https://clustername.azurehdinsight.net`internet `clustername` pomocí protokolu HTTPS, použijte název clusteru .

2. Ze webového uživatelského rozhraní Ambari vyberte hdfs ze seznamu na levé straně stránky.

    ![Apache Ambari hdfs služba vybrána](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Po zobrazení informací o službě HDFS vyberte **možnost Rychlé odkazy**. Zobrazí se seznam uzly hlavy clusteru. Vyberte jeden z hlavních uzlů a pak vyberte **NameNode UI**.

    ![Obrázek s rozbalenou nabídkou Rychlé odkazy](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Když vyberete __rychlé odkazy__, může se stát, že se zobrazí indikátor čekání. Tato podmínka může nastat, pokud máte pomalé připojení k internetu. Počkejte minutu nebo dvě na data, která mají být přijata ze serveru, a opakujte seznam.
    >
    > Některé položky v nabídce **Rychlé odkazy** mohou být odříznuty pravou stranou obrazovky. Pokud ano, rozbalte nabídku pomocí myši a pomocí klávesy se šipkou vpravo posuňte obrazovku doprava, abyste viděli zbytek nabídky.

4. Zobrazí se stránka podobná následujícímu obrázku:

    ![Obrázek uživatelského uživatelského uživatelského pole Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Všimněte si adresy URL této stránky. měla by být `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`podobná . Tento identifikátor URI používá interní plně kvalifikovaný název domény (FQDN) uzlu a je přístupný pouze při použití tunelového propojení SSH.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet a používat tunel SSH, podívejte se na následující dokument, kde najdete další způsoby použití Ambari:

* [Správa clusterů HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
