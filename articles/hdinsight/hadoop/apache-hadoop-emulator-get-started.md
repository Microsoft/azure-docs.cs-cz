---
title: Naučte se používat pískoviště Apache Hadoop, emulátor - Azure HDInsight
description: 'Pokud se chcete začít učit používat ekosystém Apache Hadoop, můžete nastavit oblast sandbox Hadoop od Hortonworks na virtuálním počítači Azure. '
keywords: hadoop emulátor, hadoop pískoviště
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73044773"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Začínáme s pískovištěm Apache Hadoop, emulátorem na virtuálním počítači

Naučte se nainstalovat pískoviště Apache Hadoop od společnosti Hortonworks na virtuální mši, abyste se dozvěděli o ekosystému Hadoop. Izolovaného prostoru poskytuje prostředí místního rozvoje se dozvědět o Hadoop, Hadoop Distribuovaný souborový systém (HDFS), a odeslání úlohy. Jakmile se seznámíte s Hadoopem, můžete začít používat Hadoop v Azure vytvořením clusteru HDInsight. Další informace o tom, jak začít, najdete [v tématu Začínáme s Hadoopem na HDInsightu](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Požadavky

* [Oracle VirtualBox](https://www.virtualbox.org/). Stáhněte si a nainstalujte [jej zde](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Stažení a instalace virtuálního počítače

1. Přejděte ke [stažení služby Cloudera](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Klikněte na **VIRTUALBOX** v části **Zvolit typ instalace** a stáhněte si nejnovější sandbox Hortonworks na virtuálním počítači. Přihlaste se nebo vyplňte formulář zájmu o produkt.

1. Klikněte na tlačítko **HDP SANDBOX (NEJNOVĚJŠÍ)** pro zahájení stahování.

Pokyny k nastavení izolovaného prostoru naleznete v [příručce Pro nasazení a instalaci izolovaného prostoru](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Chcete-li stáhnout starší škálu HDP, přečtěte si odkazy v části **Starší verze**.

## <a name="start-the-virtual-machine"></a>Spuštění virtuálního počítače

1. Otevřete virtualbox virtuálního počítače Oracle.
1. V nabídce **Soubor** klikněte na **Importovat zařízení**a pak zadejte obrázek Hortonworks Sandbox.
1. Vyberte sandbox Hortonworks, klepněte na tlačítko **Start**a potom na **normální spuštění**. Jakmile virtuální počítač dokončí proces spouštění, zobrazí se přihlašovací pokyny.

    ![virtualbox manažer normální start](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Otevřete webový prohlížeč a přejděte na `http://127.0.0.1:8888`zobrazenou adresu URL (obvykle).

## <a name="set-sandbox-passwords"></a>Nastavení hesel izolovaného prostoru

1. V kroku **Začínáme** na stránce Hortonworks Sandbox vyberte **Zobrazit rozšířené možnosti**. Informace na této stránce slouží k přihlášení do izolovaného prostoru pomocí ssh. Použijte zadaný název a heslo.

   > [!NOTE]
   > Pokud nemáte nainstalovaný klient SSH, můžete použít webové SSH poskytované na virtuální **http://localhost:4200/** mši na .

    Při prvním připojení pomocí ssh, budete vyzváni ke změně hesla pro kořenový účet. Zadejte nové heslo, které používáte při přihlášení pomocí SSH.

2. Po přihlášení zadejte následující příkaz:

        ambari-admin-password-reset

    Po zobrazení výzvy zadejte heslo pro účet správce Ambari. Používá se při přístupu k webovému uživatelskému uživatelskému uživatelskému panelu Ambari.

## <a name="use-hive-commands"></a>Použití příkazů Hive

1. Z připojení SSH k izolovanému prostoru spusťte prostředí Hive pomocí následujícího příkazu:

        hive
2. Po spuštění prostředí zobrazte tabulky, které jsou dodávány s pískovištěm, následujícím použitím:

        show tables;
3. Pomocí následujícího příkazu načtěte z `sample_07` tabulky 10 řádků:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak používat Visual Studio v sandboxu Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)

* [Učení lana Hortonworks Pískoviště](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop tutorial - Začínáme s HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
