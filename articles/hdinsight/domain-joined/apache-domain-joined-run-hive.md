---
title: Zásady Apache Hive v Apache Rangeru – Azure HDInsight
description: Zjistěte, jak nakonfigurovat zásady Apache Ranger pro Hive ve službě Azure HDInsight s balíčkem podnikového zabezpečení.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196922"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurace zásad Apache Hivu ve službě HDInsight s balíčkem zabezpečení podniku

Přečtěte si, jak nakonfigurovat zásady Apache Ranger pro Apache Hive. V tomto článku vytvoříte dvě zásady Ranger pro omezení přístupu k hivesampletable. Hivesampletable je součástí clusterů HDInsight. Po konfiguraci zásad se pomocí excelu a ovladače ODBC můžete připojit k tabulkám Hive v HDInsightu.

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight s balíčkem podnikového zabezpečení. Viz [Konfigurace clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-configure.md).
* Pracovní stanice s Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone nebo Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger
**Připojení k uživatelskému rozhraní správce Ranger**

1. Z prohlížeče přejděte na uživatelské rozhraní `https://CLUSTERNAME.azurehdinsight.net/Ranger/` správce rangeru na adrese, kde CLUSTERNAME je název clusteru.

   > [!NOTE]  
   > Ranger používá jiná pověření než cluster Apache Hadoop. Chcete-li zabránit prohlížečům používajícím přihlašovací údaje Hadoop uložené v mezipaměti, použijte nové okno prohlížeče InPrivate pro připojení k uživatelskému rozhraní správce rangerů.

2. Přihlaste se pomocí doménového uživatelského jména a hesla správce clusteru:

    ![Domovská stránka HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    V současné době Ranger funguje pouze s Yarn a Hivem.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

Informace o vytvoření rozhraní HIVERUser1 a hiveuser2 naleznete v [tématu Vytvoření clusteru HDInsight pomocí esp](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Používáte dva uživatelské účty v tomto článku.

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger

V této části vytvoříte dvě zásady Ranger pro přístup k hivesampletable. Udělíte oprávnění Vybrat na různé sady sloupců. Oba uživatelé byli vytvořeni pomocí [vytvoření clusteru HDInsight s ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). V další části otestujete dvě zásady v aplikaci Excel.

**Vytvoření zásad Ranger**

1. Otevřete uživatelské rozhraní správce Ranger. Viz Připojení k uživatelskému rozhraní správce Apache Ranger.
2. Vyberte **CLUSTERNAME_Hive**v části **Úl**. Měly by se zobrazit dvě předem nakonfigurované zásady.
3. Vyberte **Přidat nové zásady**a zadejte následující hodnoty:

    |Vlastnost |Hodnota |
    |---|---|
    |Název zásady|čtení-hivesampletable-vše|
    |Databáze úlů|default|
    |tabulka|hivesampletable|
    |Sloupec úlu|*|
    |Vybrat uživatele|hiveuser1|
    |Oprávnění|Vyberte|

    ![Konfigurace zásad HDInsight ESP Ranger Hive](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Pokud uživatel domény v části Select User (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.

4. Chcete-li zásadu uložit, vyberte **přidat.**

5. Zopakujte poslední dva kroky a vytvořte další zásadu s následujícími vlastnostmi:

    |Vlastnost |Hodnota |
    |---|---|
    |Název zásady|čtení-hivesampletable-devicemake|
    |Databáze úlů|default|
    |tabulka|hivesampletable|
    |Sloupec Úl|clientid, devicemake|
    |Vybrat uživatele|hiveuser2|
    |Oprávnění|Vyberte|

## <a name="create-hive-odbc-data-source"></a>Vytvoření zdroje dat Hive ODBC

Pokyny najdete v tématu [Vytvoření zdroje dat Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Vlastnost  |Popis |
 | --- | --- |
 | Název zdroje dat | Zadejte název zdroje dat. |
 | Hostitel | Zadejte CLUSTERNAME.azurehdinsight.net. Například mujHDICluster.azurehdinsight.net. |
 | Port | Použijte **443**. (Tento port se změnil z 563 na 443.) |
 | Databáze | Použijte **Výchozí**. |
 | Typ serveru Hive | Vyberte **Hive Server 2**. |
 | Mechanismus | Vyberte **Služba Azure HDInsight** |
 | Cesta HTTP | Ponechte prázdné. |
 | Uživatelské jméno | Zadejte hiveuser1@contoso158.onmicrosoft.com. Aktualizujte název domény, pokud se liší. |
 | Heslo | Zadejte heslo uživatele hiveuser1. |

Nezapomeňte před uložením zdroje dat kliknout na **Otestovat**.

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu ze služby HDInsight

V poslední části jste nakonfigurovali dvě zásady.  Uživatel hiveuser1 má oprávnění Vybrat na všech sloupcích a hiveuser2 má oprávnění Vybrat na dvou sloupcích. V této části se budete vydávat za tyto dva uživatele za účelem importu dat do Excelu.

1. V Excelu otevřete nový nebo existující sešit.

1. Na kartě **Data** přejděte na **Najdi z** > **jiných zdrojů** > **z ODBC** a spusťte okno Z **ODBC.**

    ![Průvodce otevřeným datovým připojením](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. V rozevíracím seznamu vyberte název zdroje dat, který jste vytvořili v poslední části, a pak vyberte **OK**.

1. Při prvním použití se otevře dialogové okno **ovladače ROZHRANÍ ODBC.** V levé nabídce vyberte **Windows.** Pak vyberte **Připojit,** chcete-li otevřít okno **Navigátoru.**

1. Počkejte, než se otevře dialogové okno **Vybrat databázi a tabulku**. Může to trvat několik sekund.

1. Vyberte **hivesampletable**a pak vyberte **Další**.

1. Vyberte **Finish** (Dokončit).

1. V dialogovém okně **Import dat** můžete změnit, nebo zadat dotaz. Chcete-li tak učinit, vyberte **možnost Vlastnosti**. Může to trvat několik sekund.

1. Vyberte kartu **Definice.** Text příkazu je:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Podle zásad Ranger, které jste nadefinovali, má uživatel hiveuser1 oprávnění Vybrat na všech sloupcích.  Takže tento dotaz pracuje s pověření matuser1, ale tento dotaz nefunguje s pověření matuser2.

1. Výběrem **možnosti OK** zavřete dialogové okno Vlastnosti připojení.

1. Výběrem **možnosti OK** zavřete dialogové okno **Importovat data.**  

1. Znovu zadejte heslo uživatele hiveuser1 a pak klikněte na **OK**. Import dat do Excelu trvá několik sekund. Po dokončení uvidíte 11 sloupců dat.

Chcete-li otestovat druhou zásadu (read-hivesampletable-devicemake), které jste vytvořili v poslední části

1. Přidejte v Excelu nový list.
2. K importu dat použijte předchozí postup.  Jedinou změnou, kterou provedete, je použití pověření hiveuser2 namísto hiveuser1. To se nezdaří, protože hiveuser2 má oprávnění k zobrazení dva sloupce. Měla by se zobrazit následující chyba:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. K importu dat použijte stejný postup. Tentokrát použijte přihlašovací údaje uživatele hiveuser2 a také změňte příkaz SELECT z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po dokončení se zobrazí dva sloupce importovaných dat.

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci clusteru HDInsight pomocí balíčku Enterprise Security Package [najdete v tématu Konfigurace clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-configure.md).
* Správa clusteru HDInsight pomocí ESP najdete [v tématu Správa clusterů HDInsight pomocí ESP](apache-domain-joined-manage.md).
* Spuštění dotazů Hive pomocí SSH v clusterech HDInsight s ESP najdete [v tématu Použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Informace o připojení hive pomocí Hive JDBC najdete v tématu [Připojení k Apache Hive na Azure HDInsight pomocí ovladače Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Informace o připojení excelu k Hadoopu pomocí hive ODBC najdete v tématu [Připojení Excelu k Apache Hadoop s jednotkou Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Pokud se informace o připojení Excelu k Hadoopu pomocí Power Query připojují [k Připojení Excelu k Apache Hadoop pomocí Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
