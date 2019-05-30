---
title: Zjistěte, používá Apache Hadoop izolovaného prostoru - emulátor – Azure HDInsight
description: 'Pokud chcete začít seznamování se se používat v ekosystému Apache Hadoop, můžete nastavit izolovaného prostoru Hadoop od Hortonworks na virtuálním počítači Azure. '
keywords: emulátor systému hadoop, hadoop sandboxu
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393139"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Začínáme s Apache Hadoop sandboxu, emulátoru na virtuálním počítači

Zjistěte, jak nainstalovat sandboxu Apache Hadoop od Hortonworks na virtuální počítač, aby Seznamte se s ekosystémem Hadoop. Izolovaný prostor poskytuje místní vývojové prostředí pro další informace o Hadoopu, soubor systému HDFS (Hadoop Distributed) a odeslání úlohy. Jakmile jste se seznámili s Hadoopem, chcete začít používat Hadoop v Azure tak, že vytvoříte HDInsight cluster. Další informace o tom, jak začít, najdete v části [Začínáme s Hadoop v HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Požadavky
* [Oracle VirtualBox](https://www.virtualbox.org/). Stáhněte si a nainstalujte ji z [tady](https://www.virtualbox.org/wiki/Downloads).


## <a name="download-and-install-the-virtual-machine"></a>Stažení a instalaci virtuálního počítače
1. Přejděte [stáhne Cloudera](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

2. Klikněte na tlačítko **VIRTUALBOX** pod **zvolte typ instalace** stáhnout nejnovější Hortonworks Sandbox na virtuálním počítači. Přihlaste se nebo vyplněním formuláře zájmu produktu.

1. Klikněte na tlačítko **HDP izolovaného prostoru (nejnovější)** zahájíte stahování.

Pokyny k vytvoření izolovaného prostoru v tématu [izolovaného prostoru nasazení a Instalační příručka](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Stažení starší verze sandboxu HDP, najdete na odkazech v rámci **starší verze**.

## <a name="start-the-virtual-machine"></a>Spustit virtuální počítač

1. Otevřete Oracle VM VirtualBox.
2. Z **souboru** nabídky, klikněte na tlačítko **Import zařízení**a pak zadejte image Hortonworks Sandbox.
1. Vyberte Hortonworks Sandbox, klikněte na **Start**a potom **normální spuštění**. Po dokončení procesu spouštění virtuálního počítače se zobrazí pokyny k přihlášení.

    ![Normální spuštění](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Otevřete webový prohlížeč a přejděte na adresu URL zobrazené (obvykle `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Nastavení hesla izolovaného prostoru

1. Z **Začínáme** krok Hortonworks Sandbox stránky vyberte **zobrazit pokročilé možnosti**. Pomocí informací na této stránce pro přihlášení do izolovaného prostoru pomocí protokolu SSH. Použijte název a heslo, které jsou k dispozici.

   > [!NOTE]
   > Pokud nemáte nainstalovaný klient SSH, můžete použít SSH webové poskytnuté na virtuální počítač na **http://localhost:4200/** .

    Při prvním připojení pomocí protokolu SSH, zobrazí se výzva ke změně hesla pro kořenový účet. Zadejte nové heslo, které používáte k přihlášení pomocí SSH.

2. Po přihlášení, zadejte následující příkaz:

        ambari-admin-password-reset

    Po zobrazení výzvy zadejte heslo pro účet správce Ambari. Používá se při přístupu k webovému uživatelskému rozhraní Ambari.

## <a name="use-hive-commands"></a>Použít příkazy Hive

1. Z připojení SSH k izolovanému prostoru pomocí následujícího příkazu spusťte prostředí Hive:

        hive
2. Po zahájení prostředí použijte následující postup k zobrazení tabulky, které jsou součástí izolovaného prostoru:

        show tables;
3. Použijte následující postup k načtení 10 řádků z `sample_07` tabulky:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Další postup
* [Zjistěte, jak pomocí sady Visual Studio s Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)
* [Učení LAN Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Kurz Hadoopu – Začínáme s HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

