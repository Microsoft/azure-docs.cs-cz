---
title: Autorizace uživatelů pro zobrazení Ambari – Azure HDInsight
description: Jak spravovat oprávnění uživatelů a skupin Ambari pro clustery HDInsight s povoleným ESP.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: bcc29902628f4e7051d6a838d2e9ac145df9e45e
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916944"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizace uživatelů pro zobrazení Apache Ambari

[Clustery HDInsight s povolenou funkcí balíček zabezpečení podniku (ESP)](./domain-joined/hdinsight-security-overview.md) poskytují funkce na podnikové úrovni, včetně ověřování pomocí Azure Active Directory. Můžete [synchronizovat nové uživatele](hdinsight-sync-aad-users-to-cluster.md) přidané do skupin Azure AD, kterým byl poskytnut přístup ke clusteru, a umožnit tak konkrétním uživatelům provádět určité akce. Práce s uživateli, skupinami a oprávněními v [Apache Ambari](https://ambari.apache.org/) se podporuje pro clustery ESP HDInsight i pro standardní clustery HDInsight.

Uživatelé služby Active Directory se můžou přihlásit k uzlům clusteru pomocí svých přihlašovacích údajů do domény. Můžou taky pomocí svých přihlašovacích údajů do domény ověřovat interakce clusteru s jinými schválenými koncovými body, jako je [odstín](https://gethue.com/), zobrazení AMBARI, ODBC, JDBC, PowerShell a rozhraní REST API.

> [!WARNING]  
> Neměňte heslo sledovacího zařízení Ambari (hdinsightwatchdog) v clusteru HDInsight se systémem Linux. Změna hesla zruší schopnost používat akce skriptu nebo provádět operace škálování s vaším clusterem.

Pokud jste to ještě neudělali, postupujte podle [těchto pokynů](./domain-joined/apache-domain-joined-configure.md) a zřiďte nový cluster ESP.

## <a name="access-the-ambari-management-page"></a>Přístup ke stránce správy Ambari

Pokud se chcete dostat na **stránku správy Ambari** ve [webovém uživatelském rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md), přejděte **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** na adresu. Zadejte uživatelské jméno a heslo správce clusteru, které jste definovali při vytváření clusteru. Pak z řídicího panelu Ambari vyberte **Spravovat Ambari** pod nabídkou **správce** :

![Správa Ambari](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Udělení oprávnění Apache Hive zobrazení

Ambari obsahuje instance zobrazení pro [Apache Hive](https://hive.apache.org/) a [Apache tez](https://tez.apache.org/), mimo jiné. Chcete-li udělit přístup k jedné nebo více instancím zobrazení podregistru, přejděte na **stránku Správa Ambari**.

1. Na stránce Správa vyberte odkaz **zobrazení** v záhlaví nabídky **zobrazení** na levé straně.

    ![Odkaz zobrazení](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na stránce zobrazení rozbalte řádek **podregistr** . K dispozici je jedno výchozí zobrazení podregistru, které se vytvoří, když se do clusteru přidá služba podregistr. Podle potřeby můžete také vytvořit další instance zobrazení podregistru. Vyberte zobrazení podregistru:

    ![Zobrazení – zobrazení podregistru](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Posuňte se k dolnímu okraji stránky zobrazení. V části *oprávnění* máte dvě možnosti, jak udělit uživatelům domény oprávnění k zobrazení:

**Udělení oprávnění těmto uživatelům** ![Udělení oprávnění těmto uživatelům](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Udělit oprávnění těmto skupinám** ![Udělit oprávnění těmto skupinám](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Chcete-li přidat uživatele, vyberte tlačítko **Přidat uživatele** .

   * Začněte psát uživatelské jméno a zobrazí se rozevírací seznam dříve definovaných názvů.

     ![Uživatelské AutoComplete](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Vyberte nebo dokončete zadávání uživatelského jména. Chcete-li přidat toto uživatelské jméno jako nový uživatel, vyberte tlačítko **Nový** .

   * Změny uložíte tak, že vyberete **modré zaškrtávací políčko**.

     ![Zadáno uživatelem](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Chcete-li přidat skupinu, vyberte tlačítko **Přidat skupinu** .

   * Začněte psát název skupiny. Proces výběru existujícího názvu skupiny nebo přidání nové skupiny je stejný jako při přidávání uživatelů.
   * Změny uložíte tak, že vyberete **modré zaškrtávací políčko**.

     ![Zadaná skupina](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Přidávání uživatelů přímo do zobrazení je užitečné, pokud chcete uživateli přiřadit oprávnění k použití tohoto zobrazení, ale nepřejte by být členem skupiny, která má další oprávnění. Pro snížení objemu administrativních režijních nákladů může být jednodušší přiřadit oprávnění skupinám.

## <a name="grant-permissions-to-apache-tez-views"></a>Udělení oprávnění pro zobrazení Apache TEZ

Instance zobrazení [Apache tez](https://tez.apache.org/) umožňují uživatelům monitorovat a ladit všechny úlohy tez odeslané pomocí [Apache Hive](https://hive.apache.org/) dotazů a skriptů [Apache vepřového prasete](https://pig.apache.org/) . Existuje jedna výchozí instance zobrazení tez, která se vytvoří při zřízení clusteru.

Chcete-li přiřadit uživatele a skupiny k tez zobrazení instance, rozbalte řádek **tez** na stránce zobrazení, jak je popsáno výše.

![Zobrazení – zobrazení tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Chcete-li přidat uživatele nebo skupiny, opakujte kroky 3-5 v předchozí části.

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím

Pro uživatele a skupiny existuje pět rolí zabezpečení, které jsou uvedené v pořadí snížení přístupových oprávnění:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Operátor služby
* Uživatel clusteru

Pokud chcete spravovat role, přejděte na **stránku Správa Ambari**a pak vyberte odkaz **role** v rámci skupiny nabídky *clustery* na levé straně.

![Odkaz na nabídku rolí](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Pokud chcete zobrazit seznam oprávnění udělených jednotlivým rolím, klikněte na modré otazník vedle záhlaví tabulky **role** na stránce role.

![Oprávnění pro odkazy v nabídce role](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Oprávnění pro odkazy v nabídce role")

Na této stránce jsou k dispozici dvě různá zobrazení, která můžete použít ke správě rolí pro uživatele a skupiny: Zablokování a výpis.

### <a name="block-view"></a>Zobrazení blokování

Zobrazení blokování zobrazuje jednotlivé role ve vlastním řádku a poskytuje **těmto uživatelům role přiřazení** a **přiřazuje role k těmto skupinám** , jak je popsáno výše.

![Zobrazení blokování rolí](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Zobrazení seznamu

Zobrazení seznamu poskytuje možnosti rychlého úprav ve dvou kategoriích: Uživatelé a skupiny.

* Kategorie uživatelé v zobrazení seznamu zobrazuje seznam všech uživatelů, což vám umožní vybrat roli pro každého uživatele v rozevíracím seznamu.

    ![Zobrazení seznamu rolí – uživatelé](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  V kategorii skupiny v zobrazení seznamu se zobrazí všechny skupiny a role přiřazená jednotlivým skupinám. V našem příkladu je seznam skupin synchronizovaný ze skupin Azure AD, které jsou zadané ve vlastnosti **skupiny uživatelů Access** v doméně nastavení domény. Přečtěte si téma [Vytvoření clusteru HDInsight s povoleným](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)protokolem ESP.

    ![Zobrazení seznamu rolí – skupiny](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na obrázku výše je skupině "hiveusers" přiřazena role *uživatele clusteru* . Tato role je jen pro čtení, která umožňuje uživatelům této skupiny zobrazovat ale měnit konfigurace služby a metriky clusterů.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Přihlášení k Ambari jako uživatel jen pro čtení

Přiřadili jsme našemu uživateli domény Azure AD oprávnění "hiveuser1" pro zobrazení podregistru a TEZ. Když spustíme webové uživatelské rozhraní Ambari a zadáte přihlašovací údaje domény tohoto uživatele (uživatelské jméno Azure AD ve formátu e-mailu a heslo), uživatel se přesměruje na stránku zobrazení Ambari. Odsud může uživatel vybrat libovolné dostupné zobrazení. Uživatel nemůže navštívit žádnou jinou část webu, včetně řídicího panelu, služeb, hostitelů, výstrah nebo stránek správce.

![Jenom uživatel s zobrazeními](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Přihlášení k Ambari jako uživatel clusteru

K roli *uživatele clusteru* jsme přiřadili uživatele domény služby Azure AD "hiveuser2". Tato role může mít přístup k řídicímu panelu a všem položkám nabídky. Uživatel clusteru má méně povolených možností než správce. Hiveuser2 může například zobrazit konfigurace pro každou ze služeb, ale nemůže je upravovat.

![Uživatel s rolí uživatele clusteru](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Další postup

* [Konfigurace zásad Apache Hive ve službě HDInsight pomocí protokolu ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Správa clusterů s protokolem ESP HDInsight](./domain-joined/apache-domain-joined-manage.md)
* [Použití zobrazení Apache Hive s Apache Hadoop ve službě HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizace uživatelů Azure AD s clusterem](hdinsight-sync-aad-users-to-cluster.md)
