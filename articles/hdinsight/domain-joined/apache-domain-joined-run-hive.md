---
title: Nakonfigurovat zásady Hive v HDInsight připojený k doméně – Azure
description: Zjistěte...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: bd99e5fda80663b37c60d972742b16c27b92cf55
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>Nakonfigurovat zásady Hive v HDInsight připojený k doméně
Zjistěte, jak nakonfigurovat zásady Apache Rangeru pro Hive. V tomto článku vytvoříte dvě zásady Ranger pro omezení přístupu k hivesampletable. Hivesampletable je součástí clusterů HDInsight. Po nakonfigurování zásad použijete Excel nebo ovladač ODBC a připojíte se k tabulkám Hivu ve službě HDInsight.

## <a name="prerequisites"></a>Požadavky
* Cluster HDInsight připojený k doméně. Viz [Konfigurace clusterů HDInsight připojených k doméně](apache-domain-joined-configure.md).
* Pracovní stanice s Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone nebo Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger
**Připojení k uživatelskému rozhraní správce Ranger**

1. V prohlížeči se připojte k uživatelskému rozhraní správce Ranger. Adresa URL je: https://&lt;název_clusteru>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Ranger používá jiné přihlašovací údaje než cluster Hadoop. Aby prohlížeče pomocí pověření uložených v mezipaměti Hadoop, použijte nové okno prohlížeče InPrivate se připojit k rozhraní škálu správce.
   >
   >
2. Přihlaste se pomocí doménového uživatelského jména a hesla správce clusteru:

    ![Domovská stránka Ranger služby HDInsight připojené k doméně](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    V současné době Ranger funguje pouze s Yarn a Hivem.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén
V tématu [vytvoření clusteru HDInsight se připojený k doméně](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster), informace o tom, jak vytvořit hiveruser1 a hiveuser2. V tomto kurzu použijete dvě uživatelské účty.

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger
V této části vytvoříte dvě škálu zásady pro přístup k hivesampletable. Udělíte oprávnění Vybrat na různé sady sloupců. Jak uživatelé byly vytvořené pomocí [vytvoření clusteru HDInsight se připojený k doméně](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster). V další části tyto dvě zásady otestujete v Excelu.

**Vytvoření zásad Ranger**

1. Otevřete uživatelské rozhraní správce Ranger. Viz [Připojení k uživatelskému rozhraní správce Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. V části **Hive** klikněte na **&lt;název_clusteru>_hive**. Měly by se zobrazit dvě předem nakonfigurované zásady.
3. Klikněte na **Add New Policy (Přidat novou zásadu)** a pak zadejte následující hodnoty:

   * Policy name (Název zásady): read-hivesampletable-all
   * Hive Database (Databáze Hivu): default (výchozí)
   * table (tabulka): hivesampletable
   * Hive column (Sloupec Hivu):*
   * Select User (Vybrat uživatele): hiveuser1
   * Permissions (Oprávnění): select (vybrat)

     ![Konfigurace zásady Hivu v Ranger služby HDInsight připojené k doméně](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Pokud uživatel domény v části Select User (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.
     >
     >
4. Kliknutím na **Přidat** uložte zásadu.
5. Zopakujte poslední dva kroky a vytvořte další zásadu s následujícími vlastnostmi:

   * Policy name (Název zásady): read-hivesampletable-devicemake
   * Hive Database (Databáze Hivu): default (výchozí)
   * table (tabulka): hivesampletable
   * Hive column (Sloupec Hivu): clientid, devicemake
   * Select User (Vybrat uživatele): hiveuser2
   * Permissions (Oprávnění): select (vybrat)

## <a name="create-hive-odbc-data-source"></a>Vytvoření zdroje dat Hive ODBC
Pokyny najdete v tématu [Vytvoření zdroje dat Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Vlastnost  |Popis |
 | --- | --- |
 | Název zdroje dat | Zadejte název zdroje dat. |
 | Hostitel | Zadejte &lt;název_clusteru_HDInsight>.azurehdinsight.net. Například mujHDICluster.azurehdinsight.net. |
 | Port | Použijte **443**. (Tento port se změnil z 563 na 443.) |
 | Databáze | Použijte **Výchozí**. |
 | Typ serveru Hive | Vyberte **Hive Server 2**. |
 | Mechanismus | Vyberte **Služba Azure HDInsight** |
 | Cesta HTTP | Ponechte prázdné. |
 | Uživatelské jméno | Zadejte hiveuser1@contoso158.onmicrosoft.com. Aktualizujte název domény, pokud se liší. |
 | Heslo | Zadejte heslo uživatele hiveuser1. |

Nezapomeňte před uložením zdroje dat kliknout na **Otestovat**.

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu ze služby HDInsight
V předchozí části jste nakonfigurovali dvě zásady.  Uživatel hiveuser1 má oprávnění Vybrat na všech sloupcích a hiveuser2 má oprávnění Vybrat na dvou sloupcích. V této části se budete vydávat za tyto dva uživatele za účelem importu dat do Excelu.

1. V Excelu otevřete nový nebo existující sešit.
2. Na kartě **Data** klikněte na **Z jiných zdrojů dat** a pak kliknutím na **Z Průvodce datovým připojením** spusťte **Průvodce datovým připojením**.

    ![Otevřete Průvodce připojením dat][img-hdi-simbahiveodbc.excel.dataconnection]
3. Jako zdroj dat vyberte **ODBC DSN** a pak klikněte na **Další**.
4. Ze zdrojů dat ODBC vyberte název zdroje dat, který jste vytvořili v předchozím kroku, a klikněte na **Další**.
5. Zadejte znovu heslo pro cluster v průvodci a potom klikněte na **OK**. Počkejte, než se otevře dialogové okno **Vybrat databázi a tabulku**. Může to trvat několik sekund.
6. Vyberte **hivesampletable** a pak klikněte na **Další**.
7. Klikněte na **Finish** (Dokončit).
8. V dialogovém okně **Import dat** můžete změnit, nebo zadat dotaz. To provedete kliknutím na **Vlastnosti**. Může to trvat několik sekund.
9. Klikněte na kartu **Definice**. Text příkazu je:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Podle zásad Ranger, které jste nadefinovali, má uživatel hiveuser1 oprávnění Vybrat na všech sloupcích.  Proto tento dotaz funguje s přihlašovacími údaji na hiveuser1, ale tento dotaz s přihlašovacími údaji na hiveuser2 nefunguje.

   ![Vlastnosti připojení][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Kliknutím na **OK** zavřete dialogové okno Vlastnosti připojení.
11. Kliknutím na **OK** zavřete dialogové okno **Import Dat**.  
12. Znovu zadejte heslo uživatele hiveuser1 a pak klikněte na **OK**. Import dat do Excelu trvá několik sekund. Po dokončení importu byste měli vidět 11 sloupců dat.

K testování druhá zásada (čtení devicemake hivesampletable), jste vytvořili v poslední části

1. Přidejte v Excelu nový list.
2. K importu dat použijte předchozí postup.  Použít přihlašovací údaje hiveuser2 na místo na hiveuser1 je pouze změny, které provedete. To se nezdaří, protože hiveuser2 má pouze oprávnění k zobrazení dva sloupce. Měla by se zobrazit následující chyba:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. K importu dat použijte stejný postup. Tentokrát použijte přihlašovací údaje uživatele hiveuser2 a také změňte příkaz SELECT z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po dokončení importu byste měli vidět naimportované dva sloupce dat.

## <a name="next-steps"></a>Další postup
* Pokud chcete konfigurovat cluster HDInsight připojený k doméně, přečtěte si téma [Konfigurace clusterů HDInsight připojených k doméně](apache-domain-joined-configure.md).
* Spravovat cluster HDInsight se připojený k doméně, najdete v části [clustery HDInsight spravovat doméně](apache-domain-joined-manage.md).
* Spuštění dotazů Hive pomocí protokolu SSH v clusterech HDInsight připojený k doméně, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Pokud se chcete připojit k Hivu pomocí Hive JDBC, přečtěte si téma [Připojení k Hivu ve službě Azure HDInsight pomocí ovladače Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
* Pokud chcete připojit Excel k systému Hadoop pomocí rozhraní Hive ODBC, přečtěte si téma [Připojení Excelu k systému Hadoop pomocí ovladače Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* Pokud chcete připojit Excel k systému Hadoop pomocí doplňku Power Query, přečtěte si téma [Připojení Excelu k systému Hadoop pomocí doplňku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
