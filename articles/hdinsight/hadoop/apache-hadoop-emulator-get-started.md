---
title: Další informace pomocí izolovaném prostoru – emulátor – Azure HDInsight Hadoop | Microsoft Docs
description: 'Pokud chcete spustit, získávání informací o použití ekosystému Hadoop, nastavením izolovaného prostoru Hadoop z Hortonworks na virtuální počítač Azure. '
keywords: hadoop emulátoru, izolovaného prostoru hadoop
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: ''
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 0b4daa8c832d40457b19ffe4aee1a365796b2435
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401128"
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Začínáme s Hadoop izolovaném prostoru, emulátoru na virtuálním počítači

Naučte se instalovat izolovaného prostoru Hadoop z Hortonworks na virtuálním počítači, další informace o ekosystému Hadoop. Izolovaný prostor poskytuje prostředí pro místní vývoj Další informace o Hadoop, Hadoop Distributed File System (HDFS) a odeslání úlohy. Jakmile se seznámíte s Hadoop, můžete začít používat Hadoop v Azure vytvořením clusteru služby HDInsight. Další informace o tom, jak začít, najdete v části [Začínáme s Hadoop v HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Požadavky
* [Oracle VirtualBox](https://www.virtualbox.org/). Stáhněte a nainstalujte ji z [zde](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Stáhněte a nainstalujte virtuální počítač
1. Vyhledejte [Hortonworks stáhne](http://hortonworks.com/downloads/#sandbox).

2. Klikněte na tlačítko **stáhnout VIRTUALBOX** stáhnout nejnovější Hortonworks karanténě na virtuálním počítači. Zobrazí se výzva k registraci s Hortonworks před zahájením stahování. Bude trvat jednu až dvě hodiny ke stažení v závislosti na rychlosti sítě.
   
    ![Propojit bitovou kopii pro stažení Hortonworks karanténě pro VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Stejné webové stránce, klikněte **Import na virtuální,** odkaz ke stažení PDF, který obsahuje pokyny k instalaci pro virtuální počítač.

Chcete-li stáhnout izolovaného prostoru starší verzi softwaru HDP, rozbalte archivu:

![Hortonworks karanténě archivu](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Spuštění virtuálního počítače

1. Otevřete VirtualBox Oracle virtuálních počítačů.
2. Z **soubor** nabídky, klikněte na tlačítko **Import zařízení**a pak zadejte bitovou kopii Hortonworks karanténě.
1. Vyberte Hortonworks karanténě, klikněte na **spustit**a potom **normální spuštění**. Po dokončení procesu spouštění virtuálního počítače zobrazí pokyny přihlášení.
   
    ![Normální spuštění](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Otevřete webový prohlížeč a přejděte na adresu URL zobrazené (obvykle http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Nastavit heslo izolovaného prostoru

1. Z **Začínáme** krok Hortonworks karanténě stránky, vyberte **pokročilé možnosti zobrazení**. Pomocí informací na této stránce pro přihlášení do izolovaného prostoru pomocí protokolu SSH. Pomocí zadaného názvu a hesla.
   
   > [!NOTE]
   > Pokud nemáte nainstalovaného klienta SSH, můžete použít webové SSH, uvedených v pro virtuální počítač na **http://localhost:4200/**.
   > 
   
    Při prvním připojení pomocí protokolu SSH, budete vyzváni ke změně hesla pro kořenový účet. Zadejte nové heslo, které můžete použít při přihlášení pomocí protokolu SSH.

2. Po přihlášení, zadejte následující příkaz:
   
        ambari-admin-password-reset
   
    Po zobrazení výzvy zadejte heslo pro účet správce Ambari. Používá se při přístupu k webové uživatelské rozhraní Ambari.

## <a name="use-hive-commands"></a>Použití Hive příkazů

1. Z připojení SSH k izolovanému prostoru použijte následující příkaz spusťte prostředí Hive:
   
        hive
2. Po zahájení prostředí, použijte následující postupy k zobrazení tabulky, které jsou k dispozici s izolovaný prostor:
   
        show tables;
3. K načtení 10 řádků z použijte následující postupy `sample_07` tabulce:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Další postup
* [Další informace o použití sady Visual Studio s Hortonworks karanténě](../hdinsight-hadoop-emulator-visual-studio.md)
* [Učení LAN Hortonworks izolovaného prostoru](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop kurz – Začínáme s HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

