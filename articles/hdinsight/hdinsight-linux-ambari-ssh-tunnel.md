---
title: Použití SSH tunelové propojení pro přístup k Azure HDInsight
description: Zjistěte, jak bezpečně procházet webové stránky hostované na svých uzlech založených na Linuxu HDInsight pomocí tunelu SSH.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/15/2018
ms.author: hrasheed
ms.openlocfilehash: 03c86aa069300f88b61752ebd3223e424f6e9c96
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382613"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Použití tunelování SSH pro přístup k webové uživatelské rozhraní Apache Ambari, JobHistory, NameNode, Apache Oozie a dalším webovým uživatelským rozhraním

Clustery HDInsight poskytují přístup k webové uživatelské rozhraní Apache Ambari přes Internet, ale některé funkce vyžadují tunelu SSH. Například ve webovém uživatelském rozhraní pro službu Apache Oozie nelze přistupovat prostřednictvím Internetu bez tunelu SSh.

## <a name="why-use-an-ssh-tunnel"></a>Proč používat tunelového propojení SSH

Některé z nabídek v Ambari fungovat jenom prostřednictvím tunelu SSH. Tyto nabídky závisí na webové servery a služby spuštěné na jiné typy uzlů, jako je například pracovních uzlů.

Následující webových uživatelských rozhraní vyžadovat tunelu SSH:

* JobHistory
* NameNode
* Zásobníky vlákna
* Oozie webového uživatelského rozhraní
* Hlavní server HBase a protokoly uživatelského rozhraní

Přizpůsobení clusteru pomocí akce skriptu, žádné služby ani nástroje, které nainstalujete, která zpřístupňují webové služby vyžadovat tunelu SSH. Například pokud instalace aplikace Hue pomocí akce skriptu musíte použít tunelového propojení SSH pro přístup k uživatelské rozhraní webu Hue.

> [!IMPORTANT]  
> Pokud budete mít přímý přístup k HDInsight prostřednictvím virtuální sítě, není potřeba použít tunelových propojení SSH. Příklad přímý přístup k HDInsight prostřednictvím virtuální sítě, najdete v článku [HDInsight připojit k místní síti](connect-on-premises-network.md) dokumentu.

## <a name="what-is-an-ssh-tunnel"></a>Co je tunelové propojení SSH

[Secure Shell (SSH) tunelování](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) portu v místním počítači se připojí k hlavnímu uzlu v HDInsight. Provoz odeslaný na místní port je směrován přes připojení SSH k hlavnímu uzlu. Žádost se vyřeší, jako kdyby pocházel z hlavního uzlu. Odpověď je pak směrován zpět pomocí tunelového připojení k pracovní stanici.

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Většina operačních systémů poskytuje klienta SSH prostřednictvím `ssh` příkazu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Webový prohlížeč, který může být nakonfigurován pro použití SOCKS5 proxy.

    > [!WARNING]  
    > Podpora proxy SOCKS integrovaný do Windows Internet nastavení nepodporuje SOCKS5 a nefunguje s kroky v tomto dokumentu. Následující prohlížeče závisí na nastavení proxy serveru Windows a aktuálně nefungují s kroky v tomto dokumentu:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome také závisí na nastavení proxy serveru Windows. Můžete však nainstalovat rozšíření, které podporují SOCKS5. Doporučujeme [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Vytvořit tunel pomocí příkazu SSH

Pomocí následujícího příkazu vytvořte SSH tunelovat pomocí `ssh` příkazu. Nahraďte **sshuser** s uživatele SSH pro váš HDInsight cluster a nahradit **clustername** s názvem vašeho clusteru HDInsight:

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Tento příkaz vytvoří připojení, která směruje provoz na místní port 9876 ke clusteru přes SSH. Dostupné možnosti:

* **D 9876** – místní port, který směruje provoz přes tunelové propojení.
* **C** -komprimovat všechna data, protože webový provoz je většinou text.
* **2** – platnost SSH vyzkoušet protokol pouze verze 2.
* **q** -tichý režim.
* **T** -zakázat přidělení pseudo tty, protože se právě přesměrování portu.
* **n** – brání čtení STDIN, protože se právě přesměrování portu.
* **N** -není spustit vzdálený příkaz, protože se právě přesměrování portu.
* **f** -běžet na pozadí.

Po dokončení příkazu se směruje provoz odeslaný na portu 9876 místního počítače k hlavnímu uzlu clusteru.

## <a name="useputty"></a>Vytvořit tunel pomocí PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) je grafický klient SSH pro Windows. Pokud nejste obeznámeni s PuTTY, přečtěte si článek [PuTTY dokumentaci](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Použijte následující postup k vytvoření tunelu SSH pomocí PuTTY:

### <a name="create-or-load-a-session"></a>Vytvořit nebo načíst relaci

1. Otevřete PuTTY a ověřte **relace** je vybrána v nabídce vlevo. Pokud již máte uložené relace, vyberte název relace, od **uložit relací** seznamu a klikněte na tlačítko **zatížení**.

1. Pokud ještě nemáte uložený relace, zadejte informace o připojení:
    * **Název hostitele (nebo IP adresa)** -SSH adres pro HDInsight cluster. Například **mycluster-ssh.azurehdinsight.net**
    * **Port** – 22.
    * **Typ připojení** - SSH
1. Klikněte na **Uložit**.

    ![Vytvořte relaci SSH](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

2. V **kategorie** části nalevo od dialogového okna, rozbalte **připojení**, rozbalte **SSH**a pak vyberte **tunely**.

3. Zadejte následující informace o **možnostech řízení SSH port předávání** formuláře:
   
   * **Zdrojový port** – port na straně klienta, který chcete přesměrovat. Například **9876**.

   * **Určení** -SSH adres pro HDInsight cluster. Například **mycluster-ssh.azurehdinsight.net**.

   * **Dynamicky** – umožňuje dynamické směrování proxy SOCKS.
     
     ![Obrázek možnosti tunelového propojení](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Klikněte na tlačítko **přidat** přidat nastavení a potom klikněte na **otevřete** otevřete připojení SSH.

5. Po zobrazení výzvy, přihlaste se k serveru.

## <a name="use-the-tunnel-from-your-browser"></a>Použití tunelového propojení z prohlížeče

> [!IMPORTANT]  
> Kroky v této části pomocí prohlížeče Mozilla FireFox, protože nabízí stejné nastavení proxy serveru na všech platformách. Další moderních prohlížečů, jako je například Google Chrome, můžou vyžadovat rozšíření například FoxyProxy pro práci s tunelové propojení.

1. Prohlížeč nakonfigurovat pro použití **localhost** a port, který jste použili při vytváření tunelového propojení jako **SOCKS v5** proxy serveru. Tady je vypadat nastavení Firefox. Pokud jste použili jiný port než 9876, změňte port, který jste použili:
   
    ![Obrázek nastavení Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > Výběr **vzdálený DNS** řeší požadavky systému DNS (Domain Name) pomocí clusteru HDInsight. Toto nastavení řeší DNS pomocí hlavního uzlu clusteru.

2. Zkontrolujte, jestli funguje tunelu návštěvou webu [ https://www.whatismyip.com/ ](https://www.whatismyip.com/). Vrácená IP adresa by měl být použitý v datacentru Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Ujistěte se u webové uživatelské rozhraní Ambari

Po vytvoření clusteru ověřte, že se dá dostat služby webovým uživatelským rozhraním z webového rozhraní Ambari pomocí následujících kroků:

1. V prohlížeči přejděte na http\:/ / headnodehost:8080. `headnodehost` Adresa je odeslán přes tunel ke clusteru a vyřešit k hlavnímu uzlu, na kterém běží Ambari na. Po zobrazení výzvy zadejte uživatelské jméno správce (správce) a heslo pro váš cluster. Můžete být vyzváni podruhé pomocí webového uživatelského rozhraní Ambari. Pokud ano, zadejte znovu informace.

   > [!NOTE]  
   > Při použití http\://headnodehost:8080 adresu pro připojení ke clusteru, kterým se připojujete přes tunelové propojení. Komunikace je zabezpečená pomocí tunelu SSH místo protokolu HTTPS. Pro připojení přes internet pomocí protokolu HTTPS, použijte protokol https\:/ / clustername.azurehdinsight.net, kde **clustername** je název clusteru.

2. Webové uživatelské rozhraní Ambari vyberte ze seznamu na levé straně stránky HDFS.

    ![Obrázek s HDFS vybrané](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Jakmile se zobrazí informace o službě HDFS, vyberte **rychlé odkazy**. Zobrazí se seznam hlavním uzlům clusteru. Vyberte jeden z hlavních uzlů a pak vyberte **uživatelského rozhraní NameNode**.

    ![Image rozbalila se nabídka rychlé odkazy](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]  
   > Když vyberete __rychlé odkazy__, můžete obdržet ukazatele čekání. K tomuto stavu může dojít, pokud máte pomalé připojení k Internetu. Počkejte minutu nebo dvě data, která mají být přijata ze serveru, a akci opakujte seznamu.
   >
   > Některé položky v **rychlé odkazy** nabídky může být oříznou podle pravé straně obrazovky. Pokud ano, rozbalte nabídku pomocí myši a použijte klávesy se šipkou doprava přejděte na obrazovce doprava a zobrazit zbývající nabídky.

4. Zobrazí se stránka podobná na následujícím obrázku:

    ![Obrázek NameNode uživatelského rozhraní](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]  
   > Všimněte si, že adresa URL pro tuto stránku; by měl být podobný **http\://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Pomocí tohoto identifikátoru URI používá interní plně kvalifikovaný název domény (FQDN) uzlu a je dostupný jenom při použití tunelu SSH.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytváření a používání tunelu SSH, najdete v následujícím dokumentu pro jiné způsoby, jak použít Ambari:

* [Správa clusterů HDInsight s použitím Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Další informace o použití SSH s HDInsight naleznete v tématu [použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

