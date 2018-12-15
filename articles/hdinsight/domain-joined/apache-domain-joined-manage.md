---
title: Správa clusterů HDInsight s Enterprise Security Enterprise – Azure
description: Další informace o správě clusterů HDInsight s balíčkem Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 4df96d0c3d1ec9cb64f2394fb97d46178906d916
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434813"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Správa clusterů HDInsight s balíčkem Enterprise Security Package
Další uživatelé a role v HDInsight Enterprise Security Package (ESP) a jak spravovat clustery ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí VSCode

Můžete propojit normální cluster s použitím Apache Ambari, spravovat uživatelské jméno, také propojit cluster Apache Hadoop zabezpečení s využitím doména uživatelské jméno (například: user1@contoso.com).
1. Výběrem otevřete paletu příkazů **CTRL + SHIFT + P**a pak zadejte **HDInsight: Propojení clusteru**.

   ![příkaz Link clusteru](./media/apache-domain-joined-manage/link-cluster-command.png)

2. HDInsight zadejte adresu URL clusteru -> vstup -> uživatelské jméno zadejte heslo -> vyberte typ clusteru -> se informace zobrazí úspěch předán ověření.
   
   ![Dialogové okno clusteru odkaz](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Propojené uživatelské jméno a heslo se použijí, pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru. 
   
3. Propojené clusteru můžete zobrazit pomocí příkazu **clusteru seznamu**. Nyní můžete odeslat skript do tohoto clusteru propojené.

   ![propojené clusteru](./media/apache-domain-joined-manage/linked-cluster.png)

4. Také můžete zrušit propojení clusteru podle vložení **HDInsight: Zrušit propojení clusteru** z palety příkazů.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí IntelliJ

Můžete propojit normální cluster pomocí Ambari, spravovat uživatelské jméno, také propojit zabezpečení clusteru hadoop s použitím doména uživatelské jméno (například: user1@contoso.com). 
1. Klikněte na tlačítko **propojení clusteru** z **Průzkumníka služby Azure**.

   ![propojení clusteru kontextové nabídky](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**. Je potřeba zkontrolovat uživatelské jméno a heslo, pokud máte ověřování se nezdařilo. Volitelně můžete přidat účet úložiště, klíč úložiště, pak vyberte kontejner z kontejneru úložiště. Informace o úložiště je pro Průzkumníka služby storage v levém stromu
   
   ![Dialogové okno clusteru odkaz](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru používáme klíč propojené úložiště, uživatelské jméno a heslo.
   > ![Průzkumník služby Storage v IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Můžete zobrazit propojené clusteru v **HDInsight** uzel Pokud vstupní informace jsou správné. Nyní můžete odeslat aplikace do této propojené clusteru.

   ![propojené clusteru](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Také můžete odpojit clusteru ze **Průzkumníka služby Azure**.
   
   ![Odpojit clusteru](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí Eclipse

Můžete propojit normální cluster pomocí Ambari, spravovat uživatelské jméno, také propojit zabezpečení clusteru hadoop s použitím doména uživatelské jméno (například: user1@contoso.com).
1. Klikněte na tlačítko **propojení clusteru** z **Průzkumníka služby Azure**.

   ![propojení clusteru kontextové nabídky](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**, pak klikněte na tlačítko OK, chcete-li propojit clusteru. Volitelně můžete zadat účet úložiště, klíč úložiště a pak vyberte kontejner úložiště pro Průzkumníka služby storage pracovat v zobrazení stromu vlevo
   
   ![Dialogové okno clusteru odkaz](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru používáme klíč propojené úložiště, uživatelské jméno a heslo.
   > ![Průzkumník služby Storage v Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Můžete zobrazit propojené clusteru v **HDInsight** uzlu po kliknutí na tlačítko OK, pokud vstupní informace jsou správné. Nyní můžete odeslat aplikace do této propojené clusteru.

   ![propojené clusteru](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Také můžete odpojit clusteru ze **Průzkumníka služby Azure**.
   
   ![Odpojit clusteru](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Přístup k clustery s balíčkem Enterprise Security Package.

Enterprise Security Package (dříve označovanou jako HDInsight Premium) uživatelům poskytuje přístup ke clusteru, kde se provádí ověřování pomocí služby Active Directory a povolení Apache Ranger a úložiště seznamů ACL (seznamy ACL ADLS). Povolení poskytuje zabezpečené hranice mezi více uživatelů a umožňuje pouze uživatelé s oprávněním k přístupu k datům na základě zásad autorizace.

Zabezpečení a uživatelských izolace jsou důležité pro cluster HDInsight s balíčkem Enterprise Security Package. Budou odpovídat vašim požadavkům, blokovaný přístup přes SSH ke clusteru s balíčkem Enterprise Security Package. V následující tabulce jsou uvedeny doporučené přístupové metody pro každý typ clusteru:

|Úloha|Scénář|Metoda přístupu|
|--------|--------|-------------|
|Apache Hadoop|Hive – interaktivní úlohy a dotazy  |<ul><li>[Beeline](#beeline)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[Rozhraní ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Nástroje sady Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktivní úlohy a dotazy, interaktivní PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin s Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[Rozhraní ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Nástroje sady Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scénáře služby batch – Spark odeslat PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktivní dotazy (LLAP)|Interaktivní|<ul><li>[Beeline](#beeline)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[Rozhraní ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Nástroje sady Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Všechny|Instalace vlastních aplikací|<ul><li>[Akce skriptů](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter není nainstalován nebo podporované v Enterprise Security Package.

Použití standardních rozhraní API pomáhá z hlediska zabezpečení. Kromě toho získáte následující výhody:

1.  **Správa** – můžete kód můžete spravovat a automatizovat úlohy pomocí standardních rozhraní API – Livy, HS2 atd.
2.  **Auditovat** – pomocí protokolu SSH, neexistuje žádný způsob, jak auditovat, kteří uživatelé SSH došlo ke clusteru. To nemusí být případ, kdy úlohy jsou vytvořeny pomocí standardních koncových bodů, jako by byl proveden v kontextu uživatele. 



### <a name="beeline"></a>Použití Beeline 
Instalace Beeline na svém počítači a připojit přes veřejný internet, použijte následující parametry: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Pokud máte nainstalovaný místně Beeline a připojení přes virtuální síť Azure, použijte následující parametry: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Najít plně kvalifikovaný název domény hlavního uzlu, použijte informace v Správa HDInsight pomocí rozhraní Ambari REST API dokumentu.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Uživatelé clusterů HDInsight s ESP
Cluster HDInsight ESP má dva uživatelské účty, které jsou vytvořeny během vytváření clusteru:

* **Správce Ambari**: Tento účet se taky říká *uživatele Hadoopu* nebo *uživatele HTTP*. Tento účet slouží pro přihlášení k Ambari na https://&lt;Název_clusteru >. azurehdinsight.net. To také umožňuje spouštět dotazy na zobrazení Ambari, spouštění úloh prostřednictvím externích nástrojů (například prostředí PowerShell, Templeton, Visual Studio) a ověřování pomocí ovladače Hive ODBC a nástrojů BI (například Excel, Power BI nebo Tableau).

Cluster HDInsight s ESP má tři nové uživatele kromě správce Ambari.

* **Správce ranger**:  Tento účet je účet místního správce Apache Ranger. Není uživatele domény služby active directory. Tento účet slouží k nastavení zásad a nastavte další uživatele správci nebo delegovaní správci (tak, aby tito uživatelé mohou spravovat zásady). Ve výchozím nastavení, uživatelské jméno je *správce* a heslo je stejná jako heslo správce Ambari. Heslo je aktualizovat ze stránky nastavení v Ranger.
* **Uživatel domény s rolí Správce clusteru**: Tento účet je určen jako správce clusteru Hadoop, včetně Ambari a Ranger uživatele domény služby active directory. Při vytváření clusteru je nutné zadat přihlašovací údaje tohoto uživatele. Tento uživatel má následující oprávnění:

  * Připojení počítače k doméně a umístit je v rámci organizační jednotky, které zadáte během vytváření clusteru.
  * Vytvoření instančních objektů v rámci organizační jednotky, které zadáte během vytváření clusteru.
  * Vytvořte reverzní položky DNS.

    Všimněte si, že pro ostatní uživatele AD také mít tato oprávnění.

    Existují některé koncové body v rámci clusteru (například Templeton), které nejsou spravovány nástrojem Ranger a proto nejsou zabezpečené. Tyto koncové body jsou uzamčené pro všechny uživatele kromě domény uživatele s rolí Správce clusteru.
* **Pravidelné**: Při vytváření clusteru můžete zadat více skupin služby active directory. Uživatelé z těchto skupin se synchronizují do Ranger a Ambari. Tito uživatelé jsou uživatelé domény a mají přístup pouze spravovaná Ranger koncových bodů (například Hiveserver2). Všechny zásady RBAC a auditování se vztahuje na tyto uživatele.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role clusterů HDInsight s ESP
HDInsight Enterprise Security Package má následující role:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Služby – operátor
* Uživatele clusteru

**Pokud chcete zobrazit oprávnění pro tyto role**

1. Otevřete uživatelské rozhraní pro správu Ambari.  Zobrazit [otevřete uživatelské rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V levé nabídce klikněte na tlačítko **role**.
3. Klikněte na modrý otazník zobrazíte oprávnění:

    ![Oprávnění role ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otevřete uživatelské rozhraní pro správu Ambari

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete váš cluster HDInsight. Zobrazit [výpisu a zobrazení clusterů](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Klikněte na tlačítko **řídicí panel** z hlavní nabídky pro otevření Ambari.
4. Přihlaste se na používání clusteru správce domény uživatelské jméno a heslo Ambari.
5. Klikněte na tlačítko **správce** rozevírací nabídky v pravém horním rohu klikněte pravým tlačítkem myši a klikněte na **spravovat Ambari**.

    ![ESP HDInsight spravovat Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Uživatelské rozhraní bude vypadat jako:

    ![Uživatelské rozhraní ESP HDInsight Ambari pro správu](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Zobrazit seznam uživatelů domény synchronizované z Active Directory
1. Otevřete uživatelské rozhraní pro správu Ambari.  Zobrazit [otevřete uživatelské rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V levé nabídce klikněte na tlačítko **uživatelé**. Zobrazí všechny uživatele, které jsou synchronizované z Active Directory do clusteru HDInsight.

    ![ESP HDInsight Ambari správu uživatelské rozhraní zobrazit seznam uživatelů](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Seznam skupiny domény, které jsou synchronizované z Active Directory
1. Otevřete uživatelské rozhraní pro správu Ambari.  Zobrazit [otevřete uživatelské rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V levé nabídce klikněte na tlačítko **skupiny**. Zobrazí všechny skupiny synchronizované z Active Directory do clusteru HDInsight.

    ![ESP HDInsight Ambari správu uživatelského rozhraní seznamu skupin](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurace oprávnění zobrazení Hive
1. Otevřete uživatelské rozhraní pro správu Ambari.  Zobrazit [otevřete uživatelské rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V levé nabídce klikněte na tlačítko **zobrazení**.
3. Klikněte na tlačítko **HIVE** zobrazte podrobnosti.

    ![Správa ESP HDInsight Ambari Hive zobrazení uživatelského rozhraní](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Klikněte na tlačítko **zobrazení Hive** odkaz pro konfiguraci zobrazení Hive.
5. Přejděte dolů k položce **oprávnění** oddílu.

    ![Zobrazení Hive uživatelského rozhraní ESP HDInsight Ambari správy nakonfigurovat oprávnění](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Klikněte na tlačítko **přidat uživatele** nebo **přidat skupinu**a pak zadejte uživatele nebo skupiny, které můžete použít zobrazení Hive.

## <a name="configure-users-for-the-roles"></a>Konfigurace pro role uživatelů
 Seznam rolí a jejich oprávnění najdete v tématu [role HDInsight clustery s ESP](#roles-of-domain---joined-hdinsight-clusters).

1. Otevřete uživatelské rozhraní pro správu Ambari.  Zobrazit [otevřete uživatelské rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V levé nabídce klikněte na tlačítko **role**.
3. Klikněte na tlačítko **přidat uživatele** nebo **přidat skupinu** přiřazení uživatelů a skupin pro různé role.

## <a name="next-steps"></a>Další postup
* Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package, naleznete v tématu [konfigurace HDInsight clustery s ESP](apache-domain-joined-configure.md).
* Konfigurovat zásady Hivu a spouštět dotazy Hivu, naleznete v tématu [zásad konfigurace Apache Hivu pro HDInsight clustery s ESP](apache-domain-joined-run-hive.md).
