---
title: Naučte se používat Apache Hadoop izolovaného prostoru (sandbox), emulátor – Azure HDInsight
description: 'Pokud chcete začít s používáním Apache Hadoop ekosystému, můžete nastavit izolovaný prostor Hadoop z Hortonworks na virtuálním počítači Azure. '
keywords: emulátor Hadoop, izolovaný prostor Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: eb286adfd7267a78fcf80bcf5ad34f8f1cc9f493
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946623"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Začínáme s Apache Hadoop izolovaným prostorem (sandbox), emulátor na virtuálním počítači

Přečtěte si, jak nainstalovat Apache Hadoop izolovaného prostoru (sandbox) z Hortonworks na virtuálním počítači, abyste se seznámili s ekosystémem Hadoop. Izolovaný prostor (sandbox) poskytuje místní vývojové prostředí pro další informace o Hadoop, Hadoop systém souborů DFS (Distributed File System) (HDFS) a odeslání úlohy. Jakmile budete s Hadoop obeznámeni, můžete začít používat Hadoop v Azure tím, že vytvoříte cluster HDInsight. Další informace o tom, jak začít, najdete v tématu Začínáme [se systémem Hadoop ve službě HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Požadavky

* [Oracle VirtualBox](https://www.virtualbox.org/). Stáhněte si ho a nainstalujte [odsud.](https://www.virtualbox.org/wiki/Downloads)

## <a name="download-and-install-the-virtual-machine"></a>Stažení a instalace virtuálního počítače

1. Přejděte na [Cloudera ke stažení](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Pokud si chcete stáhnout nejnovější izolovaný prostor Hortonworks na virtuálním počítači, klikněte na **VIRTUALBOX** v části **zvolit typ instalace** . Přihlaste se nebo dokončete formulář zájmu produktu.

1. Kliknutím na tlačítko **HDP SANDBOX (nejnovější)** zahajte stahování.

Pokyny k nastavení izolovaného prostoru naleznete v tématu [nasazení izolovaného prostoru a instalační příručka](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Pokud si chcete stáhnout starší verzi izolovaného prostoru pro HDP, přečtěte si odkazy v části **starší verze**.

## <a name="start-the-virtual-machine"></a>Spustit virtuální počítač

1. Otevřete VirtualBox virtuálního počítače Oracle.
1. V nabídce **soubor** klikněte na položku **importovat zařízení** a pak zadejte obrázek izolovaného prostoru (Hortonworks).
1. Vyberte izolovaný prostor Hortonworks, klikněte na **Start** a pak na **normální Start**. Po dokončení procesu spuštění virtuálního počítače se zobrazí pokyny pro přihlášení.

    ![normální začátek VirtualBox Manageru](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Otevřete webový prohlížeč a přejděte na zobrazenou adresu URL (obvykle `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>Nastavit hesla izolovaného prostoru

1. V kroku **Začínáme** na stránce izolovaného prostoru (Hortonworks) vyberte **Zobrazit upřesňující možnosti**. Pomocí informací na této stránce se přihlaste k izolovanému prostoru pomocí SSH. Použijte zadané jméno a heslo.

   > [!NOTE]
   > Pokud nemáte nainstalovaného klienta SSH, můžete použít webový protokol SSH, který je k dispozici na virtuálním počítači v **http://localhost:4200/** .

    Při prvním připojení pomocí SSH se zobrazí výzva, abyste změnili heslo pro kořenový účet. Zadejte nové heslo, které použijete při přihlášení pomocí SSH.

2. Po přihlášení zadejte následující příkaz:

    ```bash
    ambari-admin-password-reset
    ```

    Po zobrazení výzvy zadejte heslo pro účet správce Ambari. Tento přístup se používá při přístupu k webovému uživatelskému rozhraní Ambari.

## <a name="use-hive-commands"></a>Použití příkazů podregistru

1. Z připojení SSH k izolovanému prostoru (sandbox) spusťte prostředí pro podregistr pomocí následujícího příkazu:

    ```bash
    hive
    ```

2. Po spuštění prostředí použijte následující příkaz k zobrazení tabulek, které jsou k dispozici v izolovaném prostoru:

    ```hiveql
    show tables;
    ```

3. K načtení 10 řádků z tabulky použijte následující `sample_07` :

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Další kroky

* [Naučte se používat Visual Studio s izolovaným prostorem Hortonworks](./apache-hadoop-visual-studio-tools-get-started.md)

* [Učení LAN Hortonworks sandboxu](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Kurz Hadoop – Začínáme s HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)