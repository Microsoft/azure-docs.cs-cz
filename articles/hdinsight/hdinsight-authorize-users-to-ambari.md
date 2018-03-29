---
title: Autorizace uživatelů pro zobrazení Ambari - Azure HDInsight | Microsoft Docs
description: Jak spravovat Ambari oprávnění uživatelů a skupin pro clustery služby HDInsight připojený k doméně.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: f4dfb51cf344d1ec51363ef3a9f74c1ca59119a6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="authorize-users-for-ambari-views"></a>Autorizace uživatelů pro zobrazení Ambari

[Clustery HDInsight připojený k doméně](./domain-joined/apache-domain-joined-introduction.md) poskytují funkce na podnikové úrovni, včetně ověřování založené na Azure Active Directory. Můžete [synchronizovat nové uživatele](hdinsight-sync-aad-users-to-cluster.md) přidán do skupiny Azure AD, které byly zadány přístup ke clusteru, kteří konkrétní mohou provádět určité akce. Práce se uživatelé, skupiny a oprávnění v Ambari je podporována pro připojené k doméně cluster HDInsight a standardní clusteru HDInsight.

Uživatelé služby Active Directory může přihlásit k uzlům clusteru pomocí svých přihlašovacích údajů domény. Přihlašovací údaje domény může také používat k ověření clusteru interakce s ostatní schválené koncové body, jako je Hue, zobrazení Ambari, rozhraní ODBC, JDBC, prostředí PowerShell a rozhraní REST API.

> [!WARNING]
> Neměňte heslo Ambari sledovací zařízení (hdinsightwatchdog) v clusteru HDInsight se systémem Linux. Změna hesla se dělí možnost pomocí skriptových akcí nebo provádět operace škálování k vašemu clusteru.

Pokud jste tak již neučinili, postupujte podle [tyto pokyny](./domain-joined/apache-domain-joined-configure.md) ke zřízení nového clusteru, který je připojený k doméně.

## <a name="access-the-ambari-management-page"></a>Přístup na stránce Správa Ambari

Abyste se dostali na **stránce management Ambari** na [webové uživatelské rozhraní Ambari](hdinsight-hadoop-manage-ambari.md), přejděte do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Zadejte uživatelské jméno správce clusteru a heslo, které jste definovali při vytvoření clusteru. Potom vyberte z řídicího panelu Ambari **spravovat Ambari** pod **správce** nabídky:

![Manage Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-hive-views"></a>Udělení oprávnění k zobrazení Hive

Ambari se dodává s instancí zobrazení Hive a Tez, mimo jiné. Pokud chcete udělit přístup k jedné nebo více instancí zobrazení Hive, přejděte na **stránce management Ambari**.

1. Na stránce Správa, vyberte **zobrazení** v části **zobrazení** záhlaví nabídky na levé straně.

    ![Propojení zobrazení](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na stránce zobrazení, rozbalte **HIVE** řádek. Neexistuje jeden výchozí zobrazení Hive, která se vytvoří při službu Hive je přidat do clusteru. Můžete také vytvořit více instancí zobrazení Hive podle potřeby. Vyberte zobrazení Hive:

    ![Zobrazení – zobrazení Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Posuňte se ve spodní části stránky zobrazení. V části *oprávnění* části, máte dvě možnosti pro poskytování uživatele domény jejich oprávnění k zobrazení:

**Udělení oprávnění pro tyto uživatele** ![udělit oprávnění pro tyto uživatele](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Udělení oprávnění k těmto skupinám** ![udělit oprávnění k těmto skupinám](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. Chcete-li přidat uživatele, vyberte **přidat uživatele** tlačítko.

    * Začněte psát uživatelské jméno a zobrazí rozevírací seznam dříve definovaném názvů.

    ![Autocompletes uživatele](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Vyberte nebo ukončení zápisu, uživatelské jméno. Pokud chcete přidat jako nový uživatel toto uživatelské jméno, vyberte **nový** tlačítko.

    * Chcete-li uložit změny, vyberte **blue políčko**.

    ![Uživatelem](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Chcete-li přidat skupinu, vyberte **přidat skupinu** tlačítko.

    * Začněte psát název skupiny. Proces výběru stávající název skupiny nebo přidání nové skupiny, je stejné jako pro přidávání uživatelů.
    * Chcete-li uložit změny, vyberte **blue políčko**.

    ![Zadané skupině](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Přidání uživatelů přímo k zobrazení je užitečné, když chcete přiřadit oprávnění k uživateli pomocí tohoto zobrazení, ale nechcete, aby byl uživatel členem skupiny, která má další oprávnění. Snížíte nároky na správu, může být jednodušší přiřadit oprávnění ke skupinám.

## <a name="grant-permissions-to-tez-views"></a>Udělení oprávnění k zobrazení Tez

Zobrazit instance Tez umožňují uživatelům monitorovat a ladit všechny úlohy Tez odeslaný dotazů Hive a Pig skripty. Existuje jedna výchozí Tez zobrazení instance, která se vytvoří při zřízení clusteru.

Pokud chcete přiřadit k instanci zobrazení Tez uživatelé a skupiny, rozbalte **TEZ** řádek na stránce zobrazení, jak je popsáno výše.

![Zobrazení – zobrazení Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Chcete-li přidat uživatele nebo skupiny, opakujte kroky 3 až 5 v předchozí části.

## <a name="assign-users-to-roles"></a>Přiřadit uživatele k rolím

Existují pět role zabezpečení pro uživatele a skupiny, které jsou uvedené v pořadí podle snížení oprávnění k přístupu:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Operátor služby
* Uživatel clusteru

Ke správě rolí, přejděte na **stránce management Ambari**, vyberte **role** odkaz v rámci *clustery* skupiny nabídky na levé straně.

![Odkaz nabídky role](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Pokud chcete zobrazit seznam oprávnění, které jsou zadány pro každou roli, klikněte na modré otazník vedle **role** záhlaví tabulky na stránce role.

![Odkaz nabídky role](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Na této stránce jsou dvě různá zobrazení můžete použít ke správě role pro uživatele a skupiny: blokování a seznamu.

### <a name="block-view"></a>Zobrazení bloku

Zobrazení bloku zobrazí každou roli v svůj vlastní řádek a poskytuje **přiřadit role pro tyto uživatele** a **přiřadit role do těchto skupin** možnosti, jak je popsáno výše.

![Role blokovat zobrazení](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Zobrazení seznamu

Zobrazení seznamu umožňuje rychle upravovat ve dvou kategoriích: uživatelé a skupiny.

* Kategorie uživatelů v zobrazení seznamu zobrazí seznam všech uživatelů, což vám umožní vybrat roli pro každého uživatele v rozevíracím seznamu.

    ![Role zobrazení seznamu – Uživatelé](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* Kategorii zobrazení seznamu skupin zobrazí všechny skupiny a role přiřazené ke každé skupině. V našem příkladu seznam skupin synchronizovaných z zadaných v skupin Azure AD **skupiny uživatelů přístup** vlastnost nastavení domény clusteru. V tématu [vytvoření clusteru HDInsight se připojený k doméně](/domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster).

    ![Role zobrazení seznamu – skupin](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na obrázku výše je přiřazen skupině "hiveusers" *clusteru uživatele* role. Toto je jen pro čtení role, která umožňuje uživatelům zobrazit, ale nemění konfigurace služby a metriky clusteru této skupiny.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Přihlaste se k Ambari jako uživatel jen pro čtení

Jsme přiřadili naše uživatele Azure AD domény oprávnění "hiveuser1" zobrazení Hive a Tez. Když jsme spuštění webové uživatelské rozhraní Ambari a zadejte přihlašovací údaje domény uživatele (Azure AD uživatelské jméno ve formátu e-mailu a heslo), je uživatel přesměrován na stránku zobrazení Ambari. Tady můžete vybrat uživatele všechna zobrazení, přístupné. Uživatele nelze najdete na jiných součástí webu, včetně stránky řídicího panelu, služby, hostitele, výstrahy nebo správce.

![Uživatel s pouze zobrazení](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Přihlaste se k Ambari jako uživatel clusteru

Jsme naše uživatele Azure AD domain "hiveuser2" přiřadili *clusteru uživatele* role. Tato role je mít přístup k řídicím panelu a všechny položky nabídky. Uživatel clusteru má méně možností povolených než správce. Například hiveuser2 můžete zobrazit konfigurace pro jednotlivé služby, ale nelze je upravit.

![Uživatel s rolí uživatele clusteru](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Další postup

* [Nakonfigurovat zásady Hive v HDInsight připojený k doméně](./domain-joined/apache-domain-joined-run-hive.md)
* [Správa clusterů HDInsight připojený k doméně](./domain-joined/apache-domain-joined-manage.md)
* [Použití zobrazení Hive se systémem Hadoop v HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizace Azure AD uživatelům clusteru](hdinsight-sync-aad-users-to-cluster.md)
