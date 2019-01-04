---
title: Autorizace uživatelů pro zobrazení Ambari – Azure HDInsight
description: Správa oprávnění uživatelů a skupin Ambari pro HDInsight clustery s ESP povolena.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 976bb44ae81a6c002d2503407a4d74d513a76042
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725265"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizace uživatelů pro zobrazení Apache Ambari

[Enterprise Security Package (ESP) povolena clustery HDInsight](./domain-joined/apache-domain-joined-introduction.md) poskytuje funkce na podnikové úrovni, včetně ověřování pomocí Azure Active Directory. Je možné [synchronizovat noví uživatelé](hdinsight-sync-aad-users-to-cluster.md) přidán do skupiny Azure AD, které byly zadány přístup ke clusteru, tyto konkrétní uživatelé můžou k provedení určité akce. Práce s uživatele, skupiny a oprávnění v [Apache Ambari](https://ambari.apache.org/) se podporuje pro clustery HDInsight ESP i clusterů HDInsight standard.

Uživatelé služby Active Directory může přihlásit k uzlům clusteru pomocí svých přihlašovacích údajů domény. Přihlašovacích údajů domény může také používat k ověření clusteru interakce s dalších schválených koncových bodech, jako je [Hue](https://gethue.com/), zobrazení Ambari, rozhraní ODBC, JDBC, PowerShell a rozhraní REST API.

> [!WARNING]  
> Neměňte heslo Ambari sledovacích (hdinsightwatchdog) ve vašem clusteru HDInsight se systémem Linux. Změna hesla dělí schopnost pomocí skriptových akcí nebo provádění operací škálování s vaším clusterem.

Pokud jste tak již neučinili, postupujte podle [tyto pokyny](./domain-joined/apache-domain-joined-configure.md) zřizovat ESP nového clusteru.

## <a name="access-the-ambari-management-page"></a>Přístup ke stránce správy Ambari

Zobrazíte **stránku Správa Ambari** na [webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md), přejděte do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Zadejte uživatelské jméno správce clusteru a heslo, které jste definovali při vytváření clusteru. V dalším kroku vyberte na řídicím panelu Ambari **spravovat Ambari** pod **správce** nabídky:

![Správa Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Udělení oprávnění k zobrazení Apache Hive

Ambari obsahuje zobrazení instance pro [Apache Hive](https://hive.apache.org/) a [Apache TEZ](https://tez.apache.org/), mimo jiné. Pokud chcete udělit přístup k jedné nebo více instancí zobrazení Hive, přejděte **stránku Správa Ambari**.

1. Ze stránky správy vyberte **zobrazení** odkaz pod **zobrazení** záhlaví nabídky na levé straně.

    ![Propojení zobrazení](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na stránce zobrazení rozbalte **HIVE** řádek. Existuje jeden výchozí zobrazení Hive, který je vytvořen při přidání služby Hive do clusteru. Můžete také vytvořit více instancí zobrazení Hive podle potřeby. Vyberte zobrazení Hive:

    ![Zobrazení – zobrazení Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Posun směrem k dolní části stránky zobrazení. V části *oprávnění* oddílu, máte dvě možnosti pro poskytování uživatelé domény jejich oprávnění k zobrazení:

**Udělení oprávnění pro tyto uživatele** ![udělení oprávnění pro tyto uživatele](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Udělení oprávnění pro tyto skupiny** ![udělení oprávnění pro tyto skupiny](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. Chcete-li přidat uživatele, **přidat uživatele** tlačítko.

    * Začněte psát uživatelské jméno a zobrazí rozevírací seznam dříve definované názvy.

    ![Automaticky dokončuje uživatele](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Vyberte nebo dokončit zadáním uživatelského jména. Chcete-li přidat toto uživatelské jméno nového uživatele, vyberte **nový** tlačítko.

    * Chcete-li uložit změny, vyberte **modré zaškrtávací políčko**.

    ![Zadaný uživatel](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Chcete-li přidat skupinu, vyberte **přidat skupinu** tlačítko.

    * Začněte psát název skupiny. Proces výběru stávající název skupiny nebo přidání nové skupiny, je stejná jako přidání uživatelů.
    * Chcete-li uložit změny, vyberte **modré zaškrtávací políčko**.

    ![Zadané skupiny](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Přidání uživatele přímo k zobrazení je užitečné, pokud chcete přiřadit oprávnění uživateli pomocí tohoto zobrazení, ale nechcete, aby mohly být členem skupiny, které obsahuje další oprávnění. Pro snížení správní režie, může být jednodušší přiřazení oprávnění skupinám.

## <a name="grant-permissions-to-apache-tez-views"></a>Udělení oprávnění k zobrazení Apache TEZ

[Apache TEZ](https://tez.apache.org/) zobrazit instance povolit uživatelům monitorovat a ladit všechny úlohy Tez odeslal [Apache Hive](https://hive.apache.org/) dotazy a [Apache Pig](https://pig.apache.org/) skripty. Existuje jedna výchozí instance zobrazení Tez, který je vytvořen při zřizování clusteru.

Přiřazení uživatelů a skupin na instanci zobrazení Tez, rozbalte **TEZ** řádek na stránce zobrazení, jak je popsáno výše.

![Zobrazení – zobrazení Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Chcete-li přidat uživatele nebo skupiny, opakujte kroky 3 až 5 v předchozí části.

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím

Existuje pět rolí zabezpečení pro uživatele a skupiny, které jsou uvedeny v pořadí podle snížení přístupová oprávnění:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Služby – operátor
* Uživatele clusteru

Ke správě rolí, přejděte na **stránku Správa Ambari**, vyberte **role** propojit v rámci *clustery* skupina nabídky na levé straně.

![Role nabídky odkazu](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Pokud chcete zobrazit seznam oprávnění udělená pro jednotlivé role, klikněte na modrý otazník vedle **role** záhlaví tabulky na stránce role.

![Role nabídky odkazu](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Na této stránce se dvěma různými zobrazeními, který můžete použít ke správě rolí pro uživatele a skupiny: Blok a seznam.

### <a name="block-view"></a>Zobrazení bloku

Zobrazení bloku zobrazuje každou roli v samostatném řádku a poskytuje **přiřazení rolí k těmto uživatelům** a **přiřadit role do těchto skupin** možnosti, jak je popsáno výše.

![Role blokovat zobrazení](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Zobrazení seznamu

Zobrazení seznamu poskytuje funkce pro rychlé úpravy do dvou kategorií: Uživatelé a skupiny.

* Kategorie uživatelů v zobrazení seznamu zobrazuje seznam všech uživatelů, což vám umožní vybrat roli pro každého uživatele v rozevíracím seznamu.

    ![Role zobrazení seznamu – Uživatelé](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  Skupiny kategorií v zobrazení seznamu zobrazuje všechny skupiny a role přiřazených ke každé skupině. V našem příkladu seznam skupin se synchronizují ze služby Azure AD skupiny zadané v **přístupová skupina uživatelů** vlastnost nastavení domény clusteru. Zobrazit [vytvořit HDInsight cluster s ESP povolené](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Role zobrazení seznamu – skupiny](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na obrázku výše je přiřazen skupině "hiveusers" *uživatele clusteru* role. Toto je role jen pro čtení, který umožňuje uživatelům zobrazovat, ale nezmění konfigurace služby a metriky clusteru této skupiny.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Přihlaste se k Ambari jako uživatel jen pro čtení

Jsme přiřadili naše uživatele Azure AD domain "uživatel hiveuser1" oprávnění k zobrazení Hive a Tez. Po spuštění webové uživatelské rozhraní Ambari a zadejte přihlašovací údaje tohoto uživatele domény (Azure AD uživatelské jméno ve formátu e-mailu a heslo), bude přesměrován na stránku pro zobrazení Ambari. Odtud se může uživatel vybrat libovolné dostupné zobrazení. Uživatel nemůžou navštívit jiné části webu, včetně stránky řídicího panelu, služby, hostitele, upozornění nebo správce.

![Uživatel s pouze zobrazení](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Přihlaste se k Ambari jako uživatele clusteru

Jsme naše uživatele domény Azure AD "hiveuser2" přiřadili *uživatele clusteru* role. Tato role je přístup k řídicím panelu a všechny položky nabídky. Uživatele clusteru má méně možností povolených než správce. Například hiveuser2 můžete zobrazit konfigurace pro každou ze služeb, ale nemůžete je upravovat.

![Uživatel s rolí uživatele clusteru](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Další postup

* [Nakonfigurovat zásady Apache Hive v HDInsight s ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Správa clusterů HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Použití zobrazení Hivu Apache s Apache Hadoop v HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizace uživatelů Azure AD do clusteru](hdinsight-sync-aad-users-to-cluster.md)
