---
title: Správa clusterů Balíček zabezpečení podniku – Azure HDInsight
description: Naučte se spravovat clustery Azure HDInsight pomocí Balíček zabezpečení podniku.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 35accc587fe197da751a8695e3ec0b21ea9fbbd4
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044921"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Správa clusterů HDInsight pomocí Balíček zabezpečení podniku
Seznamte se s uživateli a rolemi v HDInsight Balíček zabezpečení podniku (ESP) a Správa clusterů ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí VSCode

Běžný cluster můžete propojit pomocí spravovaného uživatelského jména v Apache Ambari, taky propojit cluster Apache Hadoop zabezpečení pomocí uživatelského jména domény (například: `user1@contoso.com`).

1. Otevřete paletu příkazů tak, že vyberete **CTRL + SHIFT + P**a pak zadáte **HDInsight: Propojte cluster**.

   ![Paleta příkazů, propojení clusteru](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Zadejte adresu URL clusteru HDInsight – > vstupní uživatelské jméno – > zadávání hesla – > vybrat typ clusteru – > se zobrazí informace o úspěchu, pokud bylo ověření úspěšné.

   ![Dialogové okno propojit kroky procesu clusteru](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Propojené uživatelské jméno a heslo se použijí, pokud se cluster přihlásí do předplatného Azure i v propojeném clusteru.

3. Propojený cluster můžete zobrazit pomocí **clusteru seznamu**příkazů. Nyní můžete odeslat skript do tohoto odkazovaného clusteru.

   ![Vypsat ověření výstupu příkazu clusteru](./media/apache-domain-joined-manage/hdinsight-linked-cluster.png "propojený cluster")

4. Cluster můžete také odpojit tím, že zadáte **HDInsight: odpojit cluster** od palety příkazů.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí IntelliJ

Běžný cluster můžete propojit pomocí Ambari spravovaného uživatelského jména, propojit také cluster systému Hadoop s zabezpečením pomocí uživatelského jména domény (například: `user1@contoso.com`).

1. Klikněte na **propojit cluster** z **Azure Exploreru**.

   ![propojit kontextovou nabídku clusteru IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**. Pokud se ověření nezdaří, je nutné ověřit uživatelské jméno a heslo. Volitelně můžete přidat účet úložiště, klíč úložiště a pak vybrat kontejner z kontejneru úložiště. Informace o úložišti jsou pro Průzkumníka služby Storage v levém stromu.

   ![IntelliJ dialogového okna pro připojení clusteru Azure Explorer](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > V případě, že se ke clusteru přihlásilo v předplatném Azure i v souvislosti s clusterem, používáme klíč propojeného úložiště, uživatelské jméno a heslo
   > 
   > ![Účet úložiště Azure Explorer v IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

3. Pokud jsou zadané informace správné, můžete zobrazit propojený cluster v uzlu **HDInsight** . Nyní můžete odeslat aplikaci do tohoto odkazovaného clusteru.

   ![IntelliJ propojeného clusteru Azure Explorer](./media/apache-domain-joined-manage/linked-cluster-intellij.png "propojený cluster IntelliJ]")

4. Cluster taky můžete odpojit od **Azure Exploreru**.

   ![Nepropojená IntelliJ clusteru v Průzkumníkovi Azure](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí Eclipse

Běžný cluster můžete propojit pomocí Ambari spravovaného uživatelského jména, propojit také cluster systému Hadoop s zabezpečením pomocí uživatelského jména domény (například: `user1@contoso.com`).

1. Klikněte na **propojit cluster** z **Azure Exploreru**.

   ![propojit kontextovou nabídku clusteru](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**a potom klikněte na tlačítko OK pro propojení clusteru. Volitelně můžete zadat účet úložiště, klíč úložiště a pak vybrat kontejner úložiště pro Průzkumníka služby Storage, aby fungoval v levém stromovém zobrazení.

   ![Propojování clusterů v aplikaci Azure Explorer – zatmění](./media/apache-domain-joined-manage/link-cluster-dialog1.png)

   > [!NOTE]  
   > V případě, že se ke clusteru přihlásilo v předplatném Azure i v souvislosti s clusterem, používáme klíč propojeného úložiště, uživatelské jméno a heslo
   > 
   > ![Účet úložiště Azure Explorer v zatmění](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Odkazovaný cluster můžete zobrazit v uzlu **HDInsight** po kliknutí na tlačítko OK, pokud jsou zadané informace správné. Nyní můžete odeslat aplikaci do tohoto odkazovaného clusteru.

   ![Zatmění propojených clusterů v Průzkumníkovi Azure](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Cluster taky můžete odpojit od **Azure Exploreru**.
   
   ![Odkaz na cluster nepropojený s Průzkumníkem Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Přístup ke clusterům pomocí Balíček zabezpečení podniku.

Balíček zabezpečení podniku (dříve označované jako HDInsight Premium) poskytují více uživatelům přístup ke clusteru, ve kterém je ověřování prováděno pomocí služby Active Directory a ověřování pomocí seznamů ACL pro Apache Ranger a Storage (seznamy ACL pro ADLS). Autorizace zajišťuje zabezpečené hranice mezi více uživateli a umožňuje přístup k datům na základě zásad autorizace jenom privilegovaným uživatelům.

Zabezpečení a izolace uživatelů jsou důležité pro cluster HDInsight s Balíček zabezpečení podniku. Aby bylo možné tyto požadavky splnit, je blokován přístup SSH ke clusteru s Balíček zabezpečení podniku. V následující tabulce jsou uvedeny doporučené metody přístupu pro jednotlivé typy clusterů:

|Úloha|Scénář|Metoda přístupu|
|--------|--------|-------------|
|Apache Hadoop|Podregistr – interaktivní úlohy a dotazy  |<ul><li>[Beeline](#beeline)</li><li>[Zobrazení podregistru](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktivní úlohy/dotazy, PySpark Interactive|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin s Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Zobrazení podregistru](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scénáře Batch – odeslání do Sparku, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktivní dotaz (LLAP)|Interaktivní|<ul><li>[Beeline](#beeline)</li><li>[Zobrazení podregistru](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Všechny|Nainstalovat vlastní aplikaci|<ul><li>[Akce skriptů](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter není nainstalován nebo není podporován v Balíček zabezpečení podniku.

Použití standardních rozhraní API pomáhá z hlediska zabezpečení. Kromě toho získáte následující výhody:

- **Správa** – můžete spravovat kód a automatizovat úlohy pomocí standardních rozhraní API – LIVY, HS2 atd.
- **Audit** – pomocí SSH neexistuje žádný způsob, jak auditovat uživatele SSH ke clusteru. To by nepředstavovalo případ, kdy se úlohy vytvářejí pomocí standardních koncových bodů, protože by byly spuštěny v kontextu uživatele. 



### <a name="beeline"></a>Použití Beeline 
Nainstalujte do počítače Beeline a připojte se přes veřejný Internet. použijte následující parametry: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Pokud jste Beeline nainstalovali místně a připojujete se přes Virtual Network Azure, použijte následující parametry: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

K vyhledání plně kvalifikovaného názvu domény hlavnímu uzlu použijte informace v části Správa HDInsight pomocí dokumentu Ambari REST API.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Uživatelé clusterů HDInsight s protokolem ESP
Cluster HDInsight bez protokolu ESP má dva uživatelské účty, které se vytvoří během vytváření clusteru:

* **Správce Ambari**: Tento účet je také známý jako uživatel *Hadoop* nebo *uživatel http*. Tento účet se dá použít k přihlášení k Ambari na https://&lt;název_clusteru >. azurehdinsight. NET. Dá se také použít ke spouštění dotazů v zobrazeních Ambari, spouštění úloh prostřednictvím externích nástrojů (například PowerShell, Templeton, Visual Studio) a k ověření pomocí ovladače rozhraní ODBC pro podregistr a nástrojů BI (například Excel, Power BI nebo Tableau).

Cluster HDInsight s protokolem ESP má kromě správce Ambari tři nové uživatele.

* **Správce Ranger**: Tento účet je místní účet správce Apache Ranger. Nejedná se o uživatele domény služby Active Directory. Tento účet se dá použít k nastavení zásad a k provádění dalších správců uživatelů nebo delegovaných správců (aby mohli uživatelé spravovat zásady). Ve výchozím nastavení je uživatelské jméno *admin* a heslo je stejné jako heslo správce Ambari. Heslo lze aktualizovat ze stránky nastavení v Ranger.
* **Uživatel domény Správce clusteru**: Tento účet je uživatelem domény služby Active Directory, který je určený jako správce clusteru Hadoop, včetně Ambari a Ranger. Během vytváření clusteru je nutné zadat přihlašovací údaje tohoto uživatele. Tento uživatel má následující oprávnění:

  * Připojte počítače k doméně a umístěte je do organizační jednotky, kterou zadáte při vytváření clusteru.
  * Vytvořte instanční objekty v rámci organizační jednotky, kterou zadáte při vytváření clusteru.
  * Vytváření reverzních záznamů DNS

    Mějte na paměti, že ostatní uživatelé služby AD mají také tato oprávnění.

    V clusteru jsou některé koncové body (například Templeton), které nespravuje Ranger, a proto nejsou zabezpečené. Tyto koncové body jsou zamčené pro všechny uživatele s výjimkou uživatele domény Správce clusteru.
* **Regular**: během vytváření clusteru můžete zadat několik skupin služby Active Directory. Uživatelé v těchto skupinách jsou synchronizovaný na Ranger a Ambari. Tito uživatelé jsou uživatelé domény a mají přístup pouze k koncovým bodům spravovaným Ranger (například Hiveserver2). Pro tyto uživatele budou platit všechny zásady a auditování RBAC.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role clusterů HDInsight s ESP
HDInsight Balíček zabezpečení podniku má následující role:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Operátor služby
* Uživatel clusteru

**Chcete-li zobrazit oprávnění těchto rolí**

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo klikněte na **role**.
3. Kliknutím na modrou otazník zobrazíte oprávnění:

    ![Oprávnění role protokolu ESP pro HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otevřete uživatelské rozhraní pro správu Ambari.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete cluster HDInsight.
3. V horní nabídce klikněte na **řídicí panel** a otevřete Ambari.
4. Přihlaste se k Ambari pomocí uživatelského jména a hesla v doméně správce clusteru.
5. V pravém horním rohu klikněte na rozevírací nabídku **správce** a pak klikněte na **Spravovat Ambari**.

    ![ESP HDInsight spravovat Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Uživatelské rozhraní vypadá takto:

    ![ROZHRANÍ ESP pro správu protokolu HDInsight Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Seznam uživatelů domény synchronizovaných ze služby Active Directory
1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo klikněte na **Uživatelé**. Zobrazí se všichni uživatelé synchronizovaný ze služby Active Directory do clusteru HDInsight.

    ![Uživatelé seznamu v uživatelském rozhraní pro správu Ambari HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Seznam skupin domény synchronizovaných ze služby Active Directory
1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo klikněte na **skupiny**. Zobrazí se všechny skupiny synchronizované z Active Directory do clusteru HDInsight.

    ![Skupiny seznamů rozhraní ESP pro správu Ambari HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurace oprávnění k zobrazení podregistru
1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo klikněte na **zobrazení**.
3. Kliknutím na **podregistr** zobrazíte podrobnosti.

    ![Zobrazení registru ESP pro správu Ambari v prostředí HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Klikněte na odkaz **zobrazení podregistru** a nakonfigurujte zobrazení podregistru.
5. Posuňte se dolů k části **oprávnění** .

    ![Konfigurace zobrazení registru ESP Ambari pro správu v prostředí HDInsight konfigurace oprávnění](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Klikněte na **Přidat uživatele** nebo **Přidat skupinu**a pak zadejte uživatele nebo skupiny, které mohou používat zobrazení podregistru.

## <a name="configure-users-for-the-roles"></a>Konfigurace uživatelů pro role
 Pokud chcete zobrazit seznam rolí a jejich oprávnění, přečtěte si téma role clusterů HDInsight s protokolem ESP.

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo klikněte na **role**.
3. Pokud chcete přiřadit uživatele a skupiny k různým rolím, klikněte na **Přidat uživatele** nebo **Přidat skupinu** .

## <a name="next-steps"></a>Další kroky
* Informace o konfiguraci clusteru HDInsight s Balíček zabezpečení podniku najdete v tématu [konfigurace clusterů HDInsight s](apache-domain-joined-configure.md)protokolem ESP.
* Informace o konfiguraci zásad podregistru a spouštění dotazů na podregistr najdete v tématu [Konfigurace zásad Apache Hive pro clustery HDInsight s](apache-domain-joined-run-hive.md)protokolem ESP.
