---
title: Použití tunelového propojení SSH pro přístup k Azure HDInsight
description: Naučte se používat tunel SSH k bezpečnému procházení webových prostředků hostovaných na uzlech HDInsight se systémem Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: d976826fe90946697a32c5b1edb9dd323b01cc1c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105472"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Použití tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Apache Ambari, JobHistory, NameNode, Apache Oozie a dalším uživatelská rozhraní

Clustery HDInsight poskytují přístup k webovému uživatelskému rozhraní Apache Ambari přes Internet, ale některé funkce vyžadují tunel SSH. Například webové uživatelské rozhraní pro službu Apache Oozie není k dispozici přes Internet bez tunelu SSh.

## <a name="why-use-an-ssh-tunnel"></a>Proč používat tunel SSH

Některé z nabídek v Ambari fungují jenom prostřednictvím tunelu SSH. Tyto nabídky spoléhají na weby a služby spuštěné na jiných typech uzlů, jako jsou například pracovní uzly.

Následující web uživatelská rozhraní vyžaduje tunel SSH:

* JobHistory
* NameNode
* Zásobníky vláken
* Webové uživatelské rozhraní Oozie
* Uživatelské rozhraní HBase Master a protokolů

Pokud k přizpůsobení clusteru použijete akce skriptů, všechny služby nebo nástroje, které nainstalujete, které zveřejňují webovou službu, vyžadují tunel SSH. Pokud například nainstalujete odstín pomocí akce skriptu, je nutné použít tunelové propojení SSH pro přístup k webovému uživatelskému rozhraní odstínování.

> [!IMPORTANT]  
> Pokud máte přímý přístup k službě HDInsight přes virtuální síť, nemusíte používat tunely SSH. Příklad přímého přístupu ke službě HDInsight přes virtuální síť najdete v tématu [připojení HDInsight k místní síťovému](connect-on-premises-network.md) dokumentu.

## <a name="what-is-an-ssh-tunnel"></a>Co je tunel SSH

[Tunelové propojení Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) připojuje port na místním počítači k hlavnímu uzlu v HDInsight. Provoz odeslaný na místní port je směrován prostřednictvím připojení SSH k hlavnímu uzlu. Požadavek je vyřešen, jako kdyby vznikl na hlavním uzlu. Odpověď se pak přesměruje zpátky přes tunel na pracovní stanici.

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Webový prohlížeč, který se dá nakonfigurovat tak, aby používal proxy server SOCKS5.

    > [!WARNING]  
    > Podpora proxy serveru SOCKS integrovaná do nastavení Windows Internet nepodporuje SOCKS5 a nefunguje s kroky v tomto dokumentu. Následující prohlížeče spoléhají na nastavení proxy systému Windows a v současné době nefungují s kroky v tomto dokumentu:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome také spoléhá na nastavení proxy serveru Windows. Můžete ale nainstalovat rozšíření, která podporují SOCKS5. Doporučujeme [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Vytvoření tunelu pomocí příkazu SSH

Pomocí následujícího příkazu vytvořte tunel SSH pomocí `ssh` příkazu. Nahraďte `sshuser` uživatelem SSH pro váš cluster HDInsight a nahraďte `clustername` názvem vašeho clusteru HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Tento příkaz vytvoří připojení, které směruje provoz na místní port 9876 do clusteru přes protokol SSH. Dostupné možnosti:

* **D 9876** – místní port, který směruje provoz přes tunel.
* **C** – komprimuje všechna data, protože webový provoz je převážně text.
* **2** – vynutit, aby SSH vyzkoušel pouze protokol verze 2.
* v tichém režimu **q** .
* **T** – zakáže alokaci pseudo-TTY, protože právě přesměrováváte port.
* **n** – zabrání čtení ze standardního vstupu, protože právě přesměrováváte port.
* **N** – nespouštějte vzdálený příkaz, protože právě přesměrováváte port.
* **f** -spusťte na pozadí.

Po dokončení příkazu se provoz odeslaný do portu 9876 v místním počítači směruje do hlavního uzlu clusteru.

## <a name="useputty"></a>Vytvoření tunelu pomocí výstupu

[Graphics je grafický](https://www.chiark.greenend.org.uk/~sgtatham/putty) klient SSH pro Windows. Pokud nejste obeznámeni s výstupem, přečtěte si [dokumentaci k výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Pomocí následujících kroků vytvořte tunel SSH pomocí výstupu:

### <a name="create-or-load-a-session"></a>Vytvoření nebo načtení relace

1. Otevřete položku výstup a v nabídce vlevo vyberte možnost **relace** . Pokud jste již relaci uložili, vyberte název relace ze seznamu **uložených relací** a vyberte **načíst**.

1. Pokud ještě nemáte uloženou relaci, zadejte informace o připojení:
    * **Název hostitele (nebo IP adresa)** – adresa SSH pro cluster HDInsight. Například **mycluster-SSH.azurehdinsight.NET**
    * **Port** 22
    * **Typ připojení** – SSH

1. Vyberte **uložit**

    ![Relace vytvoření výstupu HDInsight](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. V části **kategorie** nalevo od dialogu rozbalte položku **připojení**, rozbalte položku **SSH**a potom vyberte možnost **tunely**.

1. Zadejte následující informace o **možnostech řízení formuláře předávání portů SSH** :

   * **Zdrojový port** – port na straně klienta, který chcete přesměrovat. Například **9876**.

   * **Cíl** – adresa SSH pro cluster HDInsight. Například **mycluster-ssh.azurehdinsight.net**.

   * **Dynamicky** – umožňuje dynamické směrování proxy SOCKS.

     ![Možnosti tunelového propojení Konfigurace výstupu](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Vyberte **Přidat** a přidejte nastavení a potom kliknutím na **otevřít** otevřete připojení SSH.

1. Po zobrazení výzvy se přihlaste k serveru.

## <a name="use-the-tunnel-from-your-browser"></a>Použití tunelu z prohlížeče

> [!IMPORTANT]  
> Kroky v této části používají prohlížeč Mozilla FireFox, protože nabízí stejné nastavení proxy serveru na všech platformách. Další moderní prohlížeče, jako je Google Chrome, můžou vyžadovat rozšíření, jako je například FoxyProxy, aby fungovalo s tunelem.

1. Nakonfigurujte prohlížeč tak, aby používal **localhost** a port, který jste použili při vytváření tunelu jako proxy **SOCKS V5** . Tady vidíte, jak vypadají nastavení Firefox. Pokud jste použili jiný port než 9876, změňte port na ten, který jste použili:

    ![prohlížeč Firefox – nastavení proxy serveru](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Výběr **vzdáleného DNS** překládá požadavky služby DNS (Domain Name System) pomocí clusteru HDInsight. Toto nastavení vyřeší DNS pomocí hlavního uzlu clusteru.

2. Ověřte, že tunel funguje na webu, jako je [https://www.whatismyip.com/](https://www.whatismyip.com/)například. Vrácená IP adresa by měla být ta, kterou používá Microsoft Azure datacentrum.

## <a name="verify-with-ambari-web-ui"></a>Ověření pomocí webového uživatelského rozhraní Ambari

Po vytvoření clusteru pomocí následujících kroků ověřte, že máte přístup ke službě Service web uživatelská rozhraní z webu Ambari:

1. V prohlížeči, přejít na `http://headnodehost:8080`. `headnodehost` Adresa se odešle přes tunel do clusteru a vyhodnotí se do hlavního uzlu, na kterém běží Ambari. Po zobrazení výzvy zadejte uživatelské jméno správce (správce) a heslo pro váš cluster. Může se stát, že webové uživatelské rozhraní Ambari vás pokaždé podruhé. V takovém případě zadejte informace znovu.

   > [!NOTE]  
   > Při použití `http://headnodehost:8080` adresy pro připojení ke clusteru se připojujete prostřednictvím tunelu. Komunikace je zabezpečená pomocí tunelu SSH místo HTTPS. Pokud se chcete připojit přes Internet pomocí protokolu HTTPS `https://clustername.azurehdinsight.net`, použijte `clustername` , kde je název clusteru.

2. Z webového uživatelského rozhraní Ambari vyberte HDFS ze seznamu na levé straně stránky.

    ![Vybraná služba Apache Ambari HDFS](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Po zobrazení informací o službě HDFS vyberte **Rychlé odkazy**. Zobrazí se seznam hlavních uzlů clusteru. Vyberte jeden z hlavních uzlů a pak vyberte **uživatelské rozhraní NameNode**.

    ![Obrázek s rozbalenou nabídkou QuickLinks](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Když vyberete __Rychlé odkazy__, může se zobrazit indikátor čekání. K tomuto stavu může dojít, pokud máte pomalé připojení k Internetu. Počkejte minutu nebo dvě, aby se data ze serveru přijímala, a pak zkuste seznam znovu.
    >
    > Některé položky v nabídce **Rychlé odkazy** mohou být na pravé straně obrazovky oříznuty. Pokud ano, rozbalte nabídku pomocí myši a použijte klávesu šipka vpravo k posunutí obrazovky doprava, abyste viděli zbytek nabídky.

4. Zobrazí se stránka podobná následujícímu obrázku:

    ![Obrázek uživatelského rozhraní Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Všimněte si adresy URL této stránky; měl by být podobný `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Tento identifikátor URI používá interní plně kvalifikovaný název domény (FQDN) uzlu a je přístupný pouze při použití tunelu SSH.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s postupem vytvoření a používání tunelu SSH, si přečtěte následující dokument, kde najdete další způsoby použití Ambari:

* [Správa clusterů HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
