---
title: Správa clusterů Balíček zabezpečení podniku – Azure HDInsight
description: Naučte se spravovat clustery Azure HDInsight pomocí Balíček zabezpečení podniku.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: b0f8ba4adfa4b08c23d3f69fa4e2b01f4580bb19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742207"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Správa clusterů HDInsight pomocí Balíček zabezpečení podniku

Seznamte se s uživateli a rolemi v HDInsight Balíček zabezpečení podniku (ESP) a Správa clusterů ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí VSCode

Běžný cluster můžete propojit pomocí spravovaného uživatelského jména v Apache Ambari, taky propojit cluster Apache Hadoop zabezpečení pomocí uživatelského jména domény (například: `user1@contoso.com` ).

1. Otevřete [Visual Studio Code](https://code.visualstudio.com/). Ujistěte se, že je nainstalované rozšíření [nástrojů pro podregistr Spark &](../hdinsight-for-vscode.md) .

1. Postupujte podle kroků v části [propojení clusteru](../hdinsight-for-vscode.md#link-a-cluster) pro Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí IntelliJ

Běžný cluster můžete propojit pomocí spravovaného uživatelského jména v Ambari, taky propojit cluster systému Hadoop Security pomocí uživatelského jména domény (například: `user1@contoso.com` ).

1. Otevřete IntelliJ IDEA. Ujistěte se, že jsou splněné všechny [požadavky](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) .

1. Postupujte podle kroků v části [propojení clusteru](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) pro IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Propojení s clusterem připojeným k doméně pomocí Eclipse

Běžný cluster můžete propojit pomocí spravovaného uživatelského jména v Ambari, taky propojit cluster systému Hadoop Security pomocí uživatelského jména domény (například: `user1@contoso.com` ).

1. Otevřete Eclipse. Ujistěte se, že jsou splněné všechny [požadavky](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) .

1. Postupujte podle kroků v části [propojení clusteru](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) pro zatmění.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Přístup ke clusterům pomocí Balíček zabezpečení podniku

Balíček zabezpečení podniku (dříve označované jako HDInsight Premium) poskytují více uživatelům přístup ke clusteru, ve kterém je ověřování prováděno pomocí služby Active Directory a ověřování pomocí seznamů ACL pro Apache Ranger a Storage (seznamy ACL pro ADLS). Autorizace zajišťuje zabezpečené hranice mezi více uživateli a umožňuje přístup k datům na základě zásad autorizace jenom privilegovaným uživatelům.

Zabezpečení a izolace uživatelů jsou důležité pro cluster HDInsight s Balíček zabezpečení podniku. Aby bylo možné splnit tyto požadavky, je podporován přístup SSH ke clusteru s Balíček zabezpečení podniku pro místního uživatele, který byl vybrán při vytváření clusteru, i uživatelům, kteří jsou k dispozici v AAD-DS (tj. Kerberos). V následující tabulce jsou uvedeny doporučené metody přístupu pro jednotlivé typy clusterů:

|Úloha|Scenario|Metoda přístupu|
|--------|--------|-------------|
|Apache Hadoop|Podregistr – interaktivní úlohy a dotazy    |<ul><li>[Beeline](#beeline)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktivní úlohy/dotazy, PySpark Interactive|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin s Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scénáře Batch – odeslání do Sparku, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktivní dotaz (LLAP)|Interaktivní|<ul><li>[Beeline](#beeline)</li><li>[Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Všechny|Nainstalovat vlastní aplikaci|<ul><li>[Akce skriptů](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter není nainstalován nebo není podporován v Balíček zabezpečení podniku.

Použití standardních rozhraní API pomáhá z hlediska zabezpečení. Získáte také následující výhody:

- **Správa** – můžete spravovat kód a automatizovat úlohy pomocí standardních rozhraní API – LIVY, HS2 atd.
- **Audit** – pomocí SSH neexistuje žádný způsob, jak auditovat uživatele SSH ke clusteru. To by nepředstavovalo případ, kdy se úlohy vytvářejí pomocí standardních koncových bodů, protože by byly spuštěny v kontextu uživatele.

### <a name="use-beeline"></a><a name="beeline"></a>Použití Beeline

Nainstalujte do počítače Beeline a připojte se přes veřejný Internet. použijte následující parametry:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Pokud jste Beeline nainstalovali místně a připojujete se přes Virtual Network Azure, použijte následující parametry:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

K vyhledání plně kvalifikovaného názvu domény hlavnímu uzlu použijte informace v části Správa HDInsight pomocí dokumentu Ambari REST API.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Uživatelé clusterů HDInsight s protokolem ESP

Cluster HDInsight bez protokolu ESP má dva uživatelské účty, které se vytvoří během vytváření clusteru:

- **Správce Ambari**: Tento účet je také známý jako uživatel *Hadoop* nebo *uživatel http*. Tento účet se dá použít k přihlášení k Ambari na `https://CLUSTERNAME.azurehdinsight.net` . Dá se také použít ke spouštění dotazů v zobrazeních Ambari, spouštění úloh prostřednictvím externích nástrojů (například PowerShell, Templeton, Visual Studio) a k ověření pomocí ovladače rozhraní ODBC pro podregistr a nástrojů BI (například Excel, Power BI nebo Tableau).

Cluster HDInsight s protokolem ESP má kromě správce Ambari tři nové uživatele.

- **Správce Ranger**: Tento účet je místní účet správce Apache Ranger. Nejedná se o uživatele domény služby Active Directory. Tento účet se dá použít k nastavení zásad a k provádění dalších správců uživatelů nebo delegovaných správců (aby mohli uživatelé spravovat zásady). Ve výchozím nastavení je uživatelské jméno *admin* a heslo je stejné jako heslo správce Ambari. Heslo lze aktualizovat ze stránky nastavení v Ranger.

- **Uživatel domény Správce clusteru**: Tento účet je uživatelem domény služby Active Directory, který je určený jako správce clusteru Hadoop, včetně Ambari a Ranger. Během vytváření clusteru je nutné zadat přihlašovací údaje tohoto uživatele. Tento uživatel má následující oprávnění:
    - Připojte počítače k doméně a umístěte je do organizační jednotky, kterou zadáte při vytváření clusteru.
    - Vytvořte instanční objekty v rámci organizační jednotky, kterou zadáte při vytváření clusteru.
    - Vytváření reverzních záznamů DNS

    Mějte na paměti, že ostatní uživatelé služby AD mají také tato oprávnění.

    V clusteru jsou některé koncové body (například Templeton), které nespravuje Ranger, a proto nejsou zabezpečené. Tyto koncové body jsou zamčené pro všechny uživatele s výjimkou uživatele domény Správce clusteru.

- **Regular**: během vytváření clusteru můžete zadat několik skupin služby Active Directory. Uživatelé v těchto skupinách jsou synchronizovaný na Ranger a Ambari. Tito uživatelé jsou uživatelé domény a mají přístup pouze k koncovým bodům spravovaným Ranger (například Hiveserver2). Pro tyto uživatele budou platit všechny zásady a auditování RBAC.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role clusterů HDInsight s ESP

HDInsight Balíček zabezpečení podniku má následující role:

- Správce clusteru
- Operátor clusteru
- Správce služeb
- Operátor služby
- Uživatel clusteru

**Chcete-li zobrazit oprávnění těchto rolí**

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo vyberte **role**.
3. Vyberte modrou otazník, abyste viděli oprávnění:

    ![Oprávnění role protokolu ESP pro HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otevřete uživatelské rozhraní pro správu Ambari.

1. Přejděte do `https://CLUSTERNAME.azurehdinsight.net/` složky název_clusteru, kde je název vašeho clusteru.
1. Přihlaste se k Ambari pomocí uživatelského jména a hesla v doméně správce clusteru.
1. V pravém horním rohu vyberte rozevírací nabídku **správce** a pak vyberte **Spravovat Ambari**.

    ![ESP HDInsight spravovat Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Uživatelské rozhraní vypadá takto:

    ![ROZHRANÍ ESP pro správu protokolu HDInsight Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Seznam uživatelů domény synchronizovaných ze služby Active Directory

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo vyberte **Uživatelé**. Zobrazí se všichni uživatelé synchronizovaný ze služby Active Directory do clusteru HDInsight.

    ![Uživatelé seznamu v uživatelském rozhraní pro správu Ambari HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Seznam skupin domény synchronizovaných ze služby Active Directory

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo vyberte **skupiny**. Zobrazí se všechny skupiny synchronizované z Active Directory do clusteru HDInsight.

    ![Skupiny seznamů rozhraní ESP pro správu Ambari HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurace oprávnění k zobrazení podregistru

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo vyberte **zobrazení**.
3. Pokud chcete zobrazit podrobnosti, vyberte **podregistr** .

    ![Zobrazení registru ESP pro správu Ambari v prostředí HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Vyberte odkaz **zobrazení podregistru** a nakonfigurujte zobrazení podregistru.
5. Posuňte se dolů k části **oprávnění** .

    ![Konfigurace zobrazení registru ESP Ambari pro správu v prostředí HDInsight konfigurace oprávnění](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Vyberte **Přidat uživatele** nebo **Přidat skupinu** a pak zadejte uživatele nebo skupiny, které mohou používat zobrazení podregistru.

## <a name="configure-users-for-the-roles"></a>Konfigurace uživatelů pro role

 Pokud chcete zobrazit seznam rolí a jejich oprávnění, přečtěte si téma role clusterů HDInsight s protokolem ESP.

1. Otevřete uživatelské rozhraní pro správu Ambari.  Podívejte [se na téma otevření uživatelského rozhraní pro správu Ambari](#open-the-ambari-management-ui).
2. V nabídce vlevo vyberte **role**.
3. Pokud chcete přiřadit uživatele a skupiny k různým rolím, vyberte **Přidat uživatele** nebo **Přidat skupinu** .

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci clusteru HDInsight s Balíček zabezpečení podniku najdete v tématu [konfigurace clusterů HDInsight s](./apache-domain-joined-configure-using-azure-adds.md)protokolem ESP.
- Informace o konfiguraci zásad podregistru a spouštění dotazů na podregistr najdete v tématu [Konfigurace zásad Apache Hive pro clustery HDInsight s](apache-domain-joined-run-hive.md)protokolem ESP.