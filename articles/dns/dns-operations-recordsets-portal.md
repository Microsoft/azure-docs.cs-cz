---
title: Správa sad záznamů DNS a záznamů pomocí Azure DNS
description: Azure DNS poskytuje možnost spravovat sady záznamů DNS a záznamy při hostování vaší domény.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 4012b32eb2684126b8dc64b9e86bf35a016e9fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936838"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Správa záznamů DNS a sad záznamů pomocí portálu Azure

Tento článek ukazuje, jak spravovat sady záznamů a záznamy pro vaši zónu DNS pomocí portálu Azure.

Je důležité porozumět rozdílu mezi sadami záznamů DNS a jednotlivými záznamy DNS. Sada záznamů je kolekce záznamů v zóně, které mají stejný název a jsou stejného typu. Další informace najdete [v tématu Vytváření sad záznamů DNS a záznamů pomocí portálu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Vytvoření nové sady a záznamu záznamů

Pokud chcete vytvořit sadu záznamů na webu Azure Portal, přečtěte [si, že nawebu Vytvoření záznamů DNS pomocí portálu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Zobrazení sady záznamů

1. Na webu Azure Portal přejděte do okna **zóny DNS.**
2. Vyhledejte sadu záznamů a vyberte ji. Tím se otevřou vlastnosti sady záznamů.

    ![Hledání sady záznamů](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Přidání nového záznamu do sady záznamů

Do libovolné sady záznamů můžete přidat až 20 záznamů. Sada záznamů nemůže obsahovat dva identické záznamy. Prázdné sady záznamů (s nulovými záznamy) lze vytvořit, ale nezobrazují se na názvových serverech Azure DNS. Sady záznamů typu CNAME mohou obsahovat maximálně jeden záznam.

1. V okně **Vlastnosti sady záznamů** pro zónu DNS klikněte na sadu záznamů, do které chcete přidat záznam.

    ![Výběr sady záznamů](./media/dns-operations-recordsets-portal/selectset500.png)

2. Zadejte vlastnosti sady záznamů vyplněním polí.

    ![Přidání záznamu](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kliknutím na **Uložit** v horní části okna uložte nastavení. Pak nůž zavřete.
4. V rohu uvidíte, že záznam se ukládá.

    ![Uložení sady záznamů](./media/dns-operations-recordsets-portal/saving150.png)

Po uložení záznamu budou hodnoty v okně **zóny DNS** odrážet nový záznam.

## <a name="update-a-record"></a>Aktualizace záznamu

Při aktualizaci záznamu v existující sadě záznamů závisí pole, která můžete aktualizovat, na typu záznamu, se kterým pracujete.

1. V okně **Vlastnosti sady záznamů** pro sadu záznamů vyhledejte záznam.
2. Upravte záznam. Při úpravě záznamu můžete změnit dostupná nastavení záznamu. V následujícím příkladu je vybráno pole **IP adresy** a adresa IP je právě upravována.

    ![Úprava záznamu](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kliknutím na **Uložit** v horní části okna uložte nastavení. V pravém horním rohu se zobrazí oznámení, že záznam byl uložen.

    ![Uložená sada záznamů](./media/dns-operations-recordsets-portal/saved150.png)

Po uložení záznamu budou hodnoty záznamu nastavené ho v okně **zóny DNS** odrážet aktualizovaný záznam.

## <a name="remove-a-record-from-a-record-set"></a>Odebrání záznamu ze sady záznamů

Portál Azure můžete použít k odebrání záznamů ze sady záznamů. Všimněte si, že odebrání posledního záznamu ze sady záznamů neodstraní sadu záznamů.

1. V okně **Vlastnosti sady záznamů** pro sadu záznamů vyhledejte záznam.
2. Klikněte na záznam, který chcete odebrat. Pak vyberte **Odebrat**.

    ![Odebrání záznamu](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kliknutím na **Uložit** v horní části okna uložte nastavení.
4. Po odebrání záznamu budou hodnoty záznamu v okně **zóny DNS** odrážet odebrání.

## <a name="delete-a-record-set"></a><a name="delete"></a>Odstranění sady záznamů

1. V okně **Vlastnosti sady záznamů** pro sadu záznamů klepněte na **tlačítko Odstranit**.

    ![Odstranění sady záznamů](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Zobrazí se zpráva s dotazem, zda chcete sadu záznamů odstranit.
3. Ověřte, zda název odpovídá sadě záznamů, kterou chcete odstranit, a klepněte na tlačítko **Ano**.
4. V okně **zóny DNS** ověřte, zda sada záznamů již není viditelná.

## <a name="work-with-ns-and-soa-records"></a>Práce se záznamy NS a SOA

Záznamy NS a SOA, které jsou vytvořeny automaticky, jsou spravovány odlišně od jiných typů záznamů.

### <a name="modify-soa-records"></a>Upravit záznamy SOA

Záznamy nelze přidávat ani odebírat z automaticky vytvořeného záznamu SOA\@nastaveného na vrcholu zóny (název = " "). Můžete však upravit libovolný parametr v záznamu SOA (s výjimkou "Host") a sady záznamů TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Změna záznamů NS na vrcholu zóny

Záznam NS nastavený na vrcholu zóny je automaticky vytvořen s každou zónou DNS. Obsahuje názvy názvových serverů Azure DNS přiřazených k zóně.

Do této sady záznamů NS můžete přidat další názvové servery, které podporují co-hostingové domény s více než jedním poskytovatelem DNS. Můžete také upravit TTL a metadata pro tuto sadu záznamů. Předem vyplněné názvové servery Azure DNS však nelze odebrat ani upravit.

Všimněte si, že to platí pouze pro záznam NS nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jako slouží k delegování podřízených zón) lze změnit bez omezení.

### <a name="delete-soa-or-ns-record-sets"></a>Odstranit sady záznamů SOA nebo NS

Nelze odstranit sady záznamů SOA a NS na vrcholu\@zóny (název = " "), které jsou vytvořeny automaticky při vytvoření zóny. Jsou odstraněny automaticky při odstranění zóny.

## <a name="next-steps"></a>Další kroky

* Další informace o Azure DNS najdete v přehledu [Azure DNS](dns-overview.md).
* Další informace o automatizaci služby DNS naleznete [v tématu Vytváření zón DNS a sad záznamů pomocí sady .NET SDK](dns-sdk.md).
* Další informace o reverzních záznamech DNS najdete [v tématu Přehled reverzního DNS a podpory v Azure](dns-reverse-dns-overview.md).
* Další informace o záznamech aliasů Azure DNS najdete v tématu [Přehled záznamů aliasů Azure DNS](dns-alias.md).
