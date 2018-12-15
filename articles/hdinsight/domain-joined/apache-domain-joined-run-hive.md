---
title: Konfigurace zásad Hivu ve s balíčkem Enterprise Security Package – Azure HDInsight
description: Zjistěte, jak nakonfigurovat zásady Apache Rangeru pro Hive ve službě Azure HDInsight s balíčkem Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 51e9d12d9f8801102997266cfd6699a367b11126
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435511"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Nakonfigurovat zásady Apache Hive v HDInsight s balíčkem Enterprise Security Package
Zjistěte, jak nakonfigurovat zásady Apache Rangeru pro Apache Hive. V tomto článku vytvoříte dvě zásady Ranger pro omezení přístupu k hivesampletable. Hivesampletable je součástí clusterů HDInsight. Po nakonfigurování zásad použijete Excel nebo ovladač ODBC a připojíte se k tabulkám Hivu ve službě HDInsight.

## <a name="prerequisites"></a>Požadavky
* Cluster HDInsight s balíčkem Enterprise Security Package. Zobrazit [konfigurace HDInsight clustery s ESP](apache-domain-joined-configure.md).
* Pracovní stanice s Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone nebo Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger
**Připojení k uživatelskému rozhraní správce Ranger**

1. V prohlížeči se připojte k uživatelskému rozhraní správce Ranger. Adresa URL je: https://&lt;název_clusteru>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > Ranger používá jiné přihlašovací údaje než cluster Apache Hadoop. Abyste zabránili prohlížeči v použití mezipaměti přihlašovacích údajů systému Hadoop, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí doménového uživatelského jména a hesla správce clusteru:

    ![Domovská stránka HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    V současné době Ranger funguje pouze s Yarn a Hivem.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén
Zobrazit [vytvořit HDInsight cluster s ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp), informace o tom, jak vytvořit uživatele hiveuser1 a hiveuser2. V tomto kurzu použijete dva uživatelské účty.

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger
V této části vytvoříte dvě zásady Ranger pro přistupování k hivesampletable. Udělíte oprávnění Vybrat na různé sady sloupců. Oba uživatelé byli vytvořeni pomocí [vytvořit HDInsight cluster s ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). V další části tyto dvě zásady otestujete v Excelu.

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

     ![Konfigurace zásady Hivu v Ranger HDInsight ESP](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

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
 | Uživatelské jméno | Zadejte hiveuser1@contoso158.onmicrosoft.com. Pokud se liší, aktualizujte název domény. |
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

   Podle zásad Ranger, které jste nadefinovali, má uživatel hiveuser1 oprávnění Vybrat na všech sloupcích.  Takže tento dotaz funguje s přihlašovacími údaji uživatele hiveuser1, ale tento dotaz nefunguje s přihlašovacími údaji uživatele hiveuser2.

   ![Vlastnosti připojení][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Kliknutím na **OK** zavřete dialogové okno Vlastnosti připojení.
11. Kliknutím na **OK** zavřete dialogové okno **Import Dat**.  
12. Znovu zadejte heslo uživatele hiveuser1 a pak klikněte na **OK**. Import dat do Excelu trvá několik sekund. Po dokončení importu byste měli vidět 11 sloupců dat.

Testování druhé zásady (read-hivesampletable-devicemake), který jste vytvořili v předchozí části

1. Přidejte v Excelu nový list.
2. K importu dat použijte předchozí postup.  Jediná změna, kterou provedete je pomocí přihlašovacích údajů uživatele hiveuser2 namísto uživatele hiveuser1. To se nezdaří, protože uživatel hiveuser2 má pouze oprávnění k zobrazení dvou sloupců. Měla by se zobrazit následující chyba:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. K importu dat použijte stejný postup. Tentokrát použijte přihlašovací údaje uživatele hiveuser2 a také změňte příkaz SELECT z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po dokončení importu byste měli vidět naimportované dva sloupce dat.

## <a name="next-steps"></a>Další postup
* Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package, naleznete v tématu [konfigurace HDInsight clustery s ESP](apache-domain-joined-configure.md).
* Cluster HDInsight s ESP, přečtěte si téma [spravovat HDInsight clustery s ESP](apache-domain-joined-manage.md).
* Spouštění dotazů Hive pomocí SSH na clusterech HDInsight s ESP, naleznete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Připojení Hivu pomocí Hive JDBC, naleznete v tématu [připojit k Apache Hive v Azure HDInsight pomocí ovladače Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Připojení Excelu k Hadoopu pomocí ovladače ODBC Hive, najdete v části [připojení Excelu k systému Apache Hadoop pomocí jednotky Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Připojení Excelu k Hadoopu pomocí Power Query, naleznete v tématu [připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
