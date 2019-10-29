---
title: Zásady Apache Hive v Apache Ranger – Azure HDInsight
description: Naučte se konfigurovat zásady Apache Ranger pro podregistr ve službě Azure HDInsight pomocí Balíček zabezpečení podniku.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e49e2d103fd9c91824c8e8a1603cddddf16366e1
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044874"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurace zásad Apache Hivu ve službě HDInsight s balíčkem zabezpečení podniku
Přečtěte si, jak nakonfigurovat zásady Apache Ranger pro Apache Hive. V tomto článku vytvoříte dvě zásady Ranger pro omezení přístupu k hivesampletable. Hivesampletable je součástí clusterů HDInsight. Po nakonfigurování zásad použijete Excel nebo ovladač ODBC a připojíte se k tabulkám Hivu ve službě HDInsight.

## <a name="prerequisites"></a>Předpoklady
* Cluster HDInsight se Balíček zabezpečení podniku. Viz téma [konfigurace clusterů HDInsight s](apache-domain-joined-configure.md)protokolem ESP.
* Pracovní stanice s Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone nebo Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger
**Připojení k uživatelskému rozhraní správce Ranger**

1. V prohlížeči se připojte k uživatelskému rozhraní správce Ranger. Adresa URL je: https://&lt;název_clusteru>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > Ranger používá jiné přihlašovací údaje než cluster Apache Hadoop. Pokud chcete uživatelům zabránit v ukládání přihlašovacích údajů do mezipaměti Hadoop, připojte se k uživatelskému rozhraní správce Ranger pomocí nového okna prohlížeče InPrivate.

2. Přihlaste se pomocí doménového uživatelského jména a hesla správce clusteru:

    ![Domovská stránka HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    V současné době Ranger funguje pouze s Yarn a Hivem.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén
Informace o tom, jak vytvářet hiveruser1 a hiveuser2, najdete v tématu [Vytvoření clusteru HDInsight s](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)protokolem ESP. V tomto článku použijete dva uživatelské účty.

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger
V této části vytvoříte dvě zásady Ranger pro přístup k hivesampletable. Udělíte oprávnění Vybrat na různé sady sloupců. Oba uživatelé byli vytvořeni pomocí [Vytvoření clusteru HDInsight s](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)protokolem ESP. V další části tyto dvě zásady otestujete v Excelu.

**Vytvoření zásad Ranger**

1. Otevřete uživatelské rozhraní správce Ranger. Viz připojení k uživatelskému rozhraní správce Apache Ranger.
2. V části **Hive** klikněte na **&lt;název_clusteru>_hive**. Měly by se zobrazit dvě předem nakonfigurované zásady.
3. Klikněte na **Add New Policy (Přidat novou zásadu)** a pak zadejte následující hodnoty:

   * Policy name (Název zásady): read-hivesampletable-all
   * Hive Database (Databáze Hivu): default (výchozí)
   * table (tabulka): hivesampletable
   * Hive column (Sloupec Hivu):*
   * Select User (Vybrat uživatele): hiveuser1
   * Permissions (Oprávnění): select (vybrat)

     ![Konfigurace zásad podregistru HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

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
5. V průvodci znovu zadejte heslo pro cluster a pak klikněte na **OK**. Počkejte, než se otevře dialogové okno **Vybrat databázi a tabulku**. Může to trvat několik sekund.
6. Vyberte **hivesampletable** a pak klikněte na **Další**.
7. Klikněte na **Finish** (Dokončit).
8. V dialogovém okně **Import dat** můžete změnit, nebo zadat dotaz. To provedete kliknutím na **Vlastnosti**. Může to trvat několik sekund.
9. Klikněte na kartu **definice** . Text příkazu je:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Podle zásad Ranger, které jste nadefinovali, má uživatel hiveuser1 oprávnění Vybrat na všech sloupcích.  Proto tento dotaz funguje s přihlašovacími údaji uživatele hiveuser1, ale tento dotaz nefunguje s přihlašovacími údaji uživatele hiveuser2.

   ![Vlastnosti připojení][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Kliknutím na **OK** zavřete dialogové okno Vlastnosti připojení.
11. Kliknutím na **OK** zavřete dialogové okno **Import Dat**.  
12. Znovu zadejte heslo uživatele hiveuser1 a pak klikněte na **OK**. Import dat do Excelu trvá několik sekund. Po dokončení importu byste měli vidět 11 sloupců dat.

Otestování druhé zásady (Read-hivesampletable-devicemake), kterou jste vytvořili v poslední části

1. Přidejte v Excelu nový list.
2. K importu dat použijte předchozí postup.  Jediná změna, kterou uděláte, je použití přihlašovacích údajů uživatele hiveuser2 místo uživatele hiveuser1. Tato operace se nezdařila, protože hiveuser2 má oprávnění pouze k zobrazení dvou sloupců. Měla by se zobrazit následující chyba:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. K importu dat použijte stejný postup. Tentokrát použijte přihlašovací údaje uživatele hiveuser2 a také změňte příkaz SELECT z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po dokončení importu byste měli vidět naimportované dva sloupce dat.

## <a name="next-steps"></a>Další kroky
* Informace o konfiguraci clusteru HDInsight s Balíček zabezpečení podniku najdete v tématu [konfigurace clusterů HDInsight s](apache-domain-joined-configure.md)protokolem ESP.
* Informace o správě clusteru HDInsight pomocí protokolu ESP najdete v tématu [Správa clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-manage.md).
* Informace o spouštění dotazů na podregistr pomocí SSH na clusterech HDInsight s protokolem ESP najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Informace o připojení podregistru pomocí JDBC pro podregistr najdete v tématu [připojení k Apache Hive v Azure HDInsight pomocí ovladače JDBC pro podregistr](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) .
* Informace o připojení aplikace Excel k systému Hadoop pomocí rozhraní ODBC pro podregistr najdete v tématu [připojení aplikace Excel k Apache Hadoop pomocí disku ODBC pro podregistr společnosti Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) .
* Informace o připojení aplikace Excel k systému Hadoop pomocí Power Query najdete v tématu [připojení aplikace Excel k Apache Hadoop pomocí Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
