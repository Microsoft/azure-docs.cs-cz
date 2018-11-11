---
title: Zjistěte, používá sandboxu - emulátor – Azure HDInsight Hadoop
description: 'Pokud chcete začít seznamování se se používat v ekosystému Hadoop, můžete nastavit izolovaného prostoru Hadoop od Hortonworks na virtuálním počítači Azure. '
keywords: emulátor systému hadoop, hadoop sandboxu
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: add1833671b995e747af6a447c65114d1add76e9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013101"
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Začínáme s Hadoop sandboxu, emulátoru na virtuálním počítači

Zjistěte, jak nainstalovat izolovaného prostoru Hadoop od Hortonworks na virtuální počítač, aby Seznamte se s ekosystémem Hadoop. Izolovaný prostor poskytuje místní vývojové prostředí pro další informace o Hadoopu, soubor systému HDFS (Hadoop Distributed) a odeslání úlohy. Jakmile jste se seznámili s Hadoopem, chcete začít používat Hadoop v Azure tak, že vytvoříte HDInsight cluster. Další informace o tom, jak začít, najdete v části [Začínáme s Hadoop v HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Požadavky
* [Oracle VirtualBox](https://www.virtualbox.org/). Stáhněte si a nainstalujte ji z [tady](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Stažení a instalaci virtuálního počítače
1. Přejděte [stáhne Hortonworks](http://hortonworks.com/downloads/#sandbox).

2. Klikněte na tlačítko **stáhnout VIRTUALBOX** stáhnout nejnovější Hortonworks Sandbox na virtuálním počítači. Zobrazí se výzva k registraci se Hortonworks, před zahájením stahování. Trvat jednu až dvě hodiny ke stažení v závislosti na rychlosti sítě.
   
    ![Obrázek odkazu pro stažení Hortonworks Sandboxu pro VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Ze stejné webové stránky, klikněte na tlačítko **Import na virtuální pole** odkaz ke stažení PDF, který obsahuje pokyny k instalaci pro virtuální počítač.

Stažení starší verze sandboxu HDP, rozbalte archiv:

![Archiv Hortonworks Sandbox](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Spustit virtuální počítač

1. Otevřete Oracle VM VirtualBox.
2. Z **souboru** nabídky, klikněte na tlačítko **Import zařízení**a pak zadejte image Hortonworks Sandbox.
1. Vyberte Hortonworks Sandbox, klikněte na **Start**a potom **normální spuštění**. Po dokončení procesu spouštění virtuálního počítače se zobrazí pokyny k přihlášení.
   
    ![Normální spuštění](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Otevřete webový prohlížeč a přejděte na adresu URL zobrazené (obvykle http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Nastavení hesla izolovaného prostoru

1. Z **Začínáme** krok Hortonworks Sandbox stránky vyberte **zobrazit pokročilé možnosti**. Pomocí informací na této stránce pro přihlášení do izolovaného prostoru pomocí protokolu SSH. Použijte název a heslo, které jsou k dispozici.
   
   > [!NOTE]
   > Pokud nemáte nainstalovaný klient SSH, můžete použít SSH webové poskytnuté na virtuální počítač na **http://localhost:4200/**.
   > 
   
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
* [Učení LAN Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Kurz Hadoopu – Začínáme s HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

