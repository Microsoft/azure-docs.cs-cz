---
title: Správa clusterů balíčků podnikového zabezpečení – Azure HDInsight
description: Přečtěte si, jak spravovat clustery Azure HDInsight pomocí balíčku enterprise security package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435879"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Správa clusterů HDInsight pomocí balíčku enterprise security package

Seznamte se s uživateli a rolemi v balíčku HDInsight Enterprise Security Package (ESP) a na způsob správy clusterů ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí VSCode

Normální cluster můžete propojit pomocí uživatelského jména spravovaného Apache Ambari, můžete také propojit bezpečnostní cluster `user1@contoso.com`Apache Hadoop pomocí uživatelského jména domény (například: ).

1. Otevřete [kód sady Visual Studio](https://code.visualstudio.com/). Ujistěte se, že je nainstalováno rozšíření [Spark & Hive Tools.](../hdinsight-for-vscode.md)

1. Postupujte podle kroků z [propojení clusteru](../hdinsight-for-vscode.md#link-a-cluster) pro kód sady Visual Studio.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí IntelliJ

Normální cluster můžete propojit pomocí uživatelského jména spravovaného ambari, můžete také propojit cluster hadoop zabezpečení pomocí uživatelského jména domény (například: `user1@contoso.com`).

1. Otevřete IntelliJ IDEA. Ujistěte [se, že](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) jsou splněny všechny požadavky.

1. Postupujte podle kroků z [propojení clusteru](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) pro IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí Eclipse

Normální cluster můžete propojit pomocí uživatelského jména spravovaného ambari, můžete také propojit cluster hadoop zabezpečení pomocí uživatelského jména domény (například: `user1@contoso.com`).

1. Otevřete Eclipse. Ujistěte [se, že](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) jsou splněny všechny požadavky.

1. Postupujte podle kroků z [Propojení clusteru](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) pro Eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Přístup ke clusterům pomocí balíčku enterprise security package

Balíček enterprise security (dříve známý jako HDInsight Premium) poskytuje víceuživatelský přístup ke clusteru, kde ověřování provádí služba Active Directory a autorizace seznamů ACL Apache Ranger a storage (ADLS ACLs). Autorizace poskytuje zabezpečené hranice mezi více uživateli a umožňuje pouze privilegovaným uživatelům přístup k datům na základě zásad autorizace.

Zabezpečení a izolace uživatelů jsou důležité pro cluster HDInsight s balíčkem podnikového zabezpečení. Chcete-li splnit tyto požadavky, Je blokován přístup SSH ke clusteru s balíčkem enterprise zabezpečení. V následující tabulce jsou uvedeny doporučené metody přístupu pro každý typ clusteru:

|Úloha|Scénář|Přístupová metoda|
|--------|--------|-------------|
|Apache Hadoop|Hive – interaktivní úlohy/dotazy  |<ul><li>[Beeline](#beeline)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Nástroje visual studia](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktivní úlohy/dotazy, Interaktivní PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin s Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Nástroje visual studia](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Dávkové scénáře – Spark submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktivní dotaz (LLAP)|Interaktivní|<ul><li>[Beeline](#beeline)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Nástroje visual studia](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Všechny|Instalace vlastní aplikace|<ul><li>[Akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter není nainstalován/podporován v balíčku Enterprise Security Package.

Použití standardních api pomáhá z hlediska zabezpečení. Získáte také následující výhody:

- **Správa** – můžete spravovat svůj kód a automatizovat úlohy pomocí standardních API – Livy, HS2 atd.
- **Audit** – S SSH neexistuje žádný způsob, jak auditovat, které uživatelé SSH'd do clusteru. To by nebyl případ, kdy jsou vytvořeny úlohy prostřednictvím standardních koncových bodů, protože by byly provedeny v kontextu uživatele.

### <a name="use-beeline"></a><a name="beeline"></a>Použití Beeline

Nainstalujte Beeline na svém počítači a připojte se přes veřejný internet, použijte následující parametry:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Pokud máte Beeline nainstalovanou místně a připojujete se přes virtuální síť Azure, použijte následující parametry:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Chcete-li najít plně kvalifikovaný název domény headnode, použijte informace v spravovat HDInsight pomocí dokumentu Rozhraní API Ambari REST.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Uživatelé clusterů HDInsight s ESP

Cluster HDInsight, který není esp, má dva uživatelské účty, které jsou vytvořeny během vytváření clusteru:

- **Ambari admin**: Tento účet je také známý jako *uživatel Hadoop* nebo *HTTP .* Tento účet lze použít k přihlášení do `https://CLUSTERNAME.azurehdinsight.net`Ambari na adrese . Lze jej také použít ke spouštění dotazů na zobrazení Ambari, provádění úloh pomocí externích nástrojů (například PowerShell, Templeton, Visual Studio) a ověřování pomocí ovladače Hive ODBC a nástrojů BI (například Excel, Power BI nebo Tableau).

Cluster HDInsight s ESP má kromě Ambari Admin tři nové uživatele.

- **Ranger admin**: Tento účet je místní Apache Ranger admin účet. Nejedná se o uživatele domény služby Active Directory. Tento účet lze použít k nastavení zásad a k tomu, aby ostatní uživatelé byli správci nebo delegovaní správci (aby tito uživatelé mohli spravovat zásady). Ve výchozím nastavení je uživatelské jméno *admin* a heslo je stejné jako heslo správce Ambari. Heslo lze aktualizovat na stránce Nastavení v rangeru.

- **Uživatel domény správce clusteru**: Tento účet je uživatelem domény služby Active Directory, který je určen jako správce clusteru Hadoop, včetně ambari a ranger. Během vytváření clusteru je nutné zadat pověření tohoto uživatele. Tento uživatel má následující oprávnění:
    - Připojte počítače k doméně a umístěte je do hlavní pracovní průběhu, kterou zadáte při vytváření clusteru.
    - Vytvořte instanční objekty v rámci ou, které zadáte při vytváření clusteru.
    - Vytvořte reverzní položky DNS.

    Všimněte si, že ostatní uživatelé služby AD mají také tato oprávnění.

    V clusteru jsou některé koncové body (například Templeton), které nejsou spravovány rangerem, a proto nejsou bezpečné. Tyto koncové body jsou uzamčeny pro všechny uživatele kromě uživatele domény správce clusteru.

- **Pravidelné**: Během vytváření clusteru můžete poskytnout více skupin active directory. Uživatelé v těchto skupinách jsou synchronizovány s Ranger a Ambari. Tito uživatelé jsou uživatelé domény a mají přístup pouze k koncovým bodům spravovaným uživatelem Ranger (například Hiveserver2). Všechny zásady RBAC a auditování budou pro tyto uživatele použitelné.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role clusterů HDInsight s ESP

HdInsight Enterprise Security Package má následující role:

- Správce clusteru
- Operátor clusteru
- Správce služeb
- Provozovatel služby
- Uživatel clusteru

**Zobrazení oprávnění těchto rolí**

1. Otevřete uj.  Viz [Otevření uj.](#open-the-ambari-management-ui)
2. V levé nabídce vyberte **Role**.
3. Vyberte modrý otazník, chcete-li zobrazit oprávnění:

    ![Oprávnění rolí ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otevření hlavního panelu správy Ambari

1. Přejděte `https://CLUSTERNAME.azurehdinsight.net/` na místo, kde clusterNAME je název clusteru.
1. Přihlaste se k Ambari pomocí uživatelského jména a hesla domény správce clusteru.
1. V pravém horním rohu vyberte rozevírací nabídku **správce** a pak vyberte **Spravovat Ambari**.

    ![ESP HDInsight spravovat Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI vypadá takto:

    ![EP HDInsight Apache Ambari řízení ui](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Seznam domén, které uživatelé synchronizovali ze služby Active Directory

1. Otevřete uj.  Viz [Otevření uj.](#open-the-ambari-management-ui)
2. V levé nabídce vyberte **možnost Uživatelé**. Zobrazí se všechny uživatele synchronizované ze služby Active Directory do clusteru HDInsight.

    ![Esp HDInsight Ambari seznam uživatelů](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Seznam skupin domén synchronizovaných ze služby Active Directory

1. Otevřete uj.  Viz [Otevření uj.](#open-the-ambari-management-ui)
2. V levé nabídce vyberte **Skupiny**. Zobrazí se všechny skupiny synchronizované ze služby Active Directory do clusteru HDInsight.

    ![Skupiny seznamů ui správu ESP HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurace oprávnění zobrazení hive

1. Otevřete uj.  Viz [Otevření uj.](#open-the-ambari-management-ui)
2. V levé nabídce vyberte **Zobrazení**.
3. Chcete-li zobrazit podrobnosti, vyberte **možnost HIVE.**

    ![Zobrazení hive pro správu ESP HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Chcete-li konfigurovat zobrazení Hive, vyberte odkaz **Zobrazení úlu.**
5. Posuňte se dolů do části **Oprávnění.**

    ![Zobrazení hive pro správu služby ESP HDInsight Ambari nakonfigurují oprávnění](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Vyberte **Přidat uživatele** nebo **Přidat skupinu**a zadejte uživatele nebo skupiny, které můžou používat zobrazení Hive.

## <a name="configure-users-for-the-roles"></a>Konfigurace uživatelů pro role

 Seznam rolí a jejich oprávnění najdete v tématu Role clusterů HDInsight s ESP.

1. Otevřete uj.  Viz [Otevření uj.](#open-the-ambari-management-ui)
2. V levé nabídce vyberte **Role**.
3. Chcete-li přiřadit uživatele a skupiny k různým rolím, vyberte **Přidat uživatele** nebo **Přidat skupinu.**

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci clusteru HDInsight pomocí balíčku Enterprise Security Package [najdete v tématu Konfigurace clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-configure.md).
- Informace o konfiguraci zásad Hive a spuštění dotazů Hive naleznete [v tématu Konfigurace zásad Apache Hive pro clustery HDInsight pomocí protokolu ESP](apache-domain-joined-run-hive.md).
