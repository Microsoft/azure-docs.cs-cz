---
title: Použití SSH tunelového propojení pro přístup k Azure HDInsight | Microsoft Docs
description: Další informace o použití tunelového propojení SSH bezpečně procházet webové prostředky, které jsou hostované na uzly HDInsight se systémem Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: larryfr
ms.openlocfilehash: 05e06d6ed8c2a3bec0d12f81aae6f7022a56b942
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Pomocí tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Ambari, JobHistory, NameNode, Oozie a jiným webovým uživatelská rozhraní

Clustery HDInsight se systémem Linux poskytnout přístup k webovému uživatelskému rozhraní Ambari přes Internet, ale nejsou některé funkce uživatelského rozhraní. Například webového uživatelského rozhraní pro jiné služby, které jsou prezentované pomocí Ambari. Pro plnou funkčnost webovému uživatelskému rozhraní Ambari je nutné použít tunelového propojení SSH do clusteru head.

## <a name="why-use-an-ssh-tunnel"></a>Proč používat tunelového propojení SSH

Několik nabídek v Ambari fungovat pouze pomocí tunelového propojení SSH. Tyto nabídky spoléhají na weby a služby běžící na jiné typy uzlů, například uzlů pracovního procesu. Často není zabezpečená tyto webové servery, takže není bezpečné přímo vystavit je na Internetu.

Následující uživatelská rozhraní Web vyžadovat tunelového propojení SSH:

* JobHistory
* NameNode
* Zásobníky vlákna
* Oozie webového uživatelského rozhraní
* Hlavní HBase a protokoly uživatelského rozhraní

Pokud pomocí akcí skriptů přizpůsobení vašeho clusteru, vyžadují jakékoli služby nebo nástroje, které nainstalujete, které zveřejňují webového uživatelského rozhraní tunelového propojení SSH. Například pokud nainstalujete Hue pomocí akce skriptu, musíte použít tunelového propojení SSH pro přístup k webu Hue uživatelského rozhraní.

> [!IMPORTANT]
> Pokud máte přímý přístup do HDInsight prostřednictvím virtuální sítě, není potřeba použít tunely SSH. Příklad přímý přístup k HDInsight prostřednictvím virtuální sítě, naleznete v části [HDInsight připojit k místní síti](connect-on-premises-network.md) dokumentu.

## <a name="what-is-an-ssh-tunnel"></a>Co je tunelového propojení SSH

[Secure Shell (SSH) tunelování](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) směruje provoz posílají do portu na místní pracovní stanici. Přenos je směrován přes připojení SSH pro váš hlavního uzlu clusteru HDInsight. Žádost se vyřeší, jako by bylo provedeno z hlavního uzlu. Odpověď se pak směruje zpět prostřednictvím tunelu do pracovní stanice.

## <a name="prerequisites"></a>Požadavky

* Klientem SSH. Většina operačních systémů poskytovat klientem SSH prostřednictvím `ssh` příkaz. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Webový prohlížeč, který může být nakonfigurován k používání SOCKS5 proxy.

    > [!WARNING]
    > Podpora proxy SOCKS součástí systému Windows nepodporuje SOCKS5 a nefunguje s kroky v tomto dokumentu. Následující prohlížeče závisí na nastavení proxy serveru systému Windows a aktuálně nefungují s kroky v tomto dokumentu:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome také závisí na nastavení proxy serveru systému Windows. Můžete však nainstalovat rozšíření, které podporují SOCKS5. Doporučujeme, abyste [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Vytvořit tunelové propojení příkazu SSH

Použijte následující příkaz pro vytvoření SSH tunelování, pomocí `ssh` příkaz. Nahraďte **uživatelské jméno** s uživatelem SSH pro váš HDInsight cluster a nahraďte **CLUSTERNAME** s názvem clusteru HDInsight:

```bash
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

Tento příkaz vytvoří připojení, který směruje provoz do místního portu 9876 do clusteru prostřednictvím SSH. Dostupné možnosti:

* **D 9876** -místní port, který směruje provoz prostřednictvím tunelu.
* **C** -komprimovat všechna data, protože webový provoz je většinou text.
* **2** -force SSH pokusit protocol verze 2 pouze.
* **q** -tichém režimu.
* **T** -zakázat pseudo tty přidělení, protože se právě předávání port.
* **n** -zabránit čtení STDIN, protože se právě předávání port.
* **N** -nespouštějte vzdáleného příkazu, protože se právě předávání port.
* **f** -spuštěný na pozadí.

Po dokončení příkazu posílají do portu 9876 v místním počítači provoz se směruje na hlavního uzlu clusteru.

## <a name="useputty"></a>Vytvořit tunelové propojení PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) je grafický klient SSH pro systém Windows. Pro vytvoření tunelu SSH pomocí klienta PuTTY použijte následující postup:

1. Otevřete PuTTY a zadejte informace o připojení. Pokud nejste obeznámeni s PuTTY, najdete v článku [PuTTY dokumentace (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. V **kategorie** nalevo dialogového okna, rozbalte položku **připojení**, rozbalte položku **SSH**a potom vyberte **tunely**.

3. Zadejte následující informace na **možnosti řízení SSH port předávání** formuláře:
   
   * **Zdrojový port** – port na straně klienta, který chcete přesměrovat. Například **9876**.

   * **Cílový** -SSH adresu pro cluster HDInsight se systémem Linux. Například **mycluster-ssh.azurehdinsight.net**.

   * **Dynamicky** – umožňuje dynamické směrování proxy SOCKS.
     
     ![Obrázek možnosti tunelové propojení](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Klikněte na tlačítko **přidat** přidejte nastavení a potom klikněte na **otevřete** otevřít připojení SSH.

5. Pokud budete vyzváni, přihlaste se k serveru.

## <a name="use-the-tunnel-from-your-browser"></a>Používání tunelového propojení z prohlížeče

> [!IMPORTANT]
> Postup v této části pomocí prohlížeče Mozilla FireFox, protože poskytuje stejné nastavení proxy serveru ve všech platformách. Jiné moderní prohlížeče, jako je například Google Chrome, může vyžadovat rozšíření například FoxyProxy pro práci s tunelové propojení.

1. Prohlížeč nakonfigurovat pro použití **localhost** a port, který jste použili při vytvoření tunelu jako **SOCKS v5** proxy serveru. Zde je, jak vypadají nastavení Firefox. Pokud jste použili jiný port než 9876, změňte port, které jste použili:
   
    ![Obrázek nastavení Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Výběr **DNS vzdálené** přeloží požadavky systému DNS (Domain Name) pomocí clusteru HDInsight. Toto nastavení vyřeší DNS pomocí hlavního uzlu clusteru.

2. Ověřte, že funguje tunelu návštěvou lokality [ http://www.whatismyip.com/ ](http://www.whatismyip.com/). Vrácená IP adresa by měl být jeden používané datového centra Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Ověřte si u webovému uživatelskému rozhraní Ambari

Po vytvoření clusteru ověřte, že jste z webu Ambari přístup webové služby uživatelská pomocí následujících kroků:

1. V prohlížeči přejděte na http://headnodehost:8080. `headnodehost` Adresa je odeslána přes tunel ke clusteru a vyřešte k headnode, která Ambari běží na. Po zobrazení výzvy zadejte uživatelské jméno správce (správce) a heslo pro váš cluster. Můžete být vyzváni podruhé pomocí Ambari webového uživatelského rozhraní. Pokud ano, zadejte informace znovu.

   > [!NOTE]
   > Při použití http://headnodehost:8080 adresu, které se připojte ke clusteru, se připojují prostřednictvím tunelu. Komunikace je zabezpečena pomocí tunelového propojení SSH místo protokolu HTTPS. Chcete-li připojit přes internet pomocí protokolu HTTPS, použijte https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název clusteru.

2. Webové uživatelské rozhraní Ambari vyberte ze seznamu na levé straně stránky HDFS.

    ![Bitové kopie s HDFS vybrané](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Když se zobrazí informace o službě HDFS, vyberte **rychlé odkazy**. Zobrazí se seznam hlavních uzlech clusteru. Vyberte jeden z hlavních uzlech a pak vyberte **uživatelského rozhraní NameNode**.

    ![Rozšířit bitové kopie s nabídce Rychlé odkazy](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Když vyberete __rychlé odkazy__, může dojít k čekání indikátoru. K tomuto stavu může dojít, pokud máte pomalé připojení k Internetu. Počkejte minutu nebo dvě pro data, která mají být přijata ze serveru, a akci opakujte seznamu.
   >
   > Některé položky v **rychlé odkazy** nabídky, může být zkrácené pravé straně obrazovky. Pokud ano, rozbalte nabídku pomocí myši a pomocí klávesy šipka doprava přejděte na obrazovce napravo na zobrazení další obrazovky nabídky.

4. Zobrazí se stránka podobná na následujícím obrázku:

    ![Obrázek NameNode uživatelského rozhraní](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Všimněte si adresu URL pro tuto stránku; by měl vypadat přibližně **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Tento identifikátor URI interní plně kvalifikovaný název domény (FQDN) je pomocí uzlu a je k dispozici pouze při používání tunelového propojení SSH.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postup vytvoření a používání tunelového propojení SSH, najdete v následujícím dokumentu pro další způsoby použití Ambari:

* [Správa clusterů HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)

Další informace o používání SSH s HDInsight, naleznete v části [použití SSH s HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

