---
title: Správa sad záznamů a záznamů DNS pomocí Azure DNS
description: Azure DNS poskytuje možnost spravovat sady záznamů a záznamy DNS při hostování vaší domény.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 111d94db1cbec658daabfb1a4c38c8160d6f50b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696827"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Správa záznamů a sad záznamů DNS pomocí Azure Portal

V tomto článku se dozvíte, jak spravovat sady záznamů a záznamy pro zónu DNS pomocí Azure Portal.

Je důležité pochopit rozdíl mezi sadami záznamů DNS a jednotlivými záznamy DNS. Sada záznamů je kolekce záznamů v zóně, které mají stejný název a jsou stejného typu. Další informace najdete v tématu [vytváření sad záznamů a záznamů DNS pomocí Azure Portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Vytvoření nové sady záznamů a záznamu

Pokud chcete vytvořit sadu záznamů v Azure Portal, přečtěte si téma [Vytvoření záznamů DNS pomocí Azure Portal](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Zobrazení sady záznamů

1. V Azure Portal otevřete okno **zóna DNS** .
2. Vyhledejte sadu záznamů a vyberte ji. Otevře se dialogové okno s vlastnostmi sady záznamů.

    ![Hledání sady záznamů](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Přidání nového záznamu do sady záznamů

Do libovolné sady záznamů můžete přidat až 20 záznamů. Sada záznamů nemůže obsahovat dva identické záznamy. Je možné vytvořit prázdné sady záznamů (s nulovými záznamy), ale nezobrazují se na Azure DNS názvových serverech. Sady záznamů typu CNAME můžou obsahovat jenom jeden záznam.

1. V okně **vlastností sady záznamů** pro vaši zónu DNS klikněte na sadu záznamů, do které chcete přidat záznam.

    ![Vybrat sadu záznamů](./media/dns-operations-recordsets-portal/selectset500.png)

2. Zadejte vlastnosti sady záznamů vyplněním polí.

    ![Přidání záznamu](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Uložte nastavení kliknutím na **Uložit** v horní části okna. Pak okno zavřete.
4. V rohu uvidíte, že se záznam ukládá.

    ![Ukládá se záznamová sada](./media/dns-operations-recordsets-portal/saving150.png)

Po uložení záznamu se hodnoty v okně **zóny DNS** projeví v novém záznamu.

## <a name="update-a-record"></a>Aktualizace záznamu

Když aktualizujete záznam v existující sadě záznamů, pole, která můžete aktualizovat, závisí na typu záznamu, se kterým pracujete.

1. V okně **vlastností sady záznamů** pro sadu záznamů vyhledejte záznam.
2. Upravte záznam. Když upravíte záznam, můžete změnit dostupná nastavení záznamu. V následujícím příkladu je vybráno pole **IP adresa** a IP adresa je právě upravována.

    ![Úprava záznamu](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Uložte nastavení kliknutím na **Uložit** v horní části okna. V pravém horním rohu uvidíte oznámení, že záznam byl uložen.

    ![Uložená sada záznamů](./media/dns-operations-recordsets-portal/saved150.png)

Po uložení záznamu se hodnoty pro sadu záznamů v okně **zóny DNS** projeví u aktualizovaného záznamu.

## <a name="remove-a-record-from-a-record-set"></a>Odebrat záznam ze sady záznamů

K odebrání záznamů ze sady záznamů můžete použít Azure Portal. Všimněte si, že při odebrání posledního záznamu ze sady záznamů nedojde k odstranění sady záznamů.

1. V okně **vlastností sady záznamů** pro sadu záznamů vyhledejte záznam.
2. Klikněte na záznam, který chcete odebrat. Pak vyberte **Odebrat**.

    ![Odebrat záznam](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Uložte nastavení kliknutím na **Uložit** v horní části okna.
4. Po odebrání záznamu se tyto hodnoty pro záznam v okně **zóny DNS** odeberou.

## <a name="delete-a-record-set"></a><a name="delete"></a>Odstranění sady záznamů

1. V okně **vlastností sady záznamů** pro sadu záznamů klikněte na **Odstranit**.

    ![Odstranění sady záznamů](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Zobrazí se zpráva s dotazem, zda chcete odstranit sadu záznamů.
3. Ověřte, zda název odpovídá sadě záznamů, kterou chcete odstranit, a potom klikněte na tlačítko **Ano**.
4. V okně **zóna DNS** ověřte, že sada záznamů již není viditelná.

## <a name="work-with-ns-and-soa-records"></a>Práce s záznamy NS a SOA

Záznamy NS a SOA, které se automaticky vytvoří, se spravují jinak než jiné typy záznamů.

### <a name="modify-soa-records"></a>Upravit záznamy SOA

Nemůžete přidávat ani odebírat záznamy z automaticky vytvořené sady záznamů SOA ve vrcholu zóny (název = " \@ "). Můžete však změnit libovolný parametr v rámci záznamu SOA (kromě "hostitel") a hodnoty TTL sady záznamů.

### <a name="modify-ns-records-at-the-zone-apex"></a>Úprava záznamů NS ve vrcholu zóny

Záznam NS nastavený na vrcholu zóny se automaticky vytvoří s každou zónou DNS. Obsahuje názvy Azure DNS názvových serverů přiřazených k zóně.

Do této sady záznamů NS můžete přidat další názvové servery, aby se podporovaly domény spoluhostování s více než jedním poskytovatelem DNS. Můžete také upravit hodnotu TTL a metadata této sady záznamů. Nemůžete ale odebrat ani změnit předem vyplněné Azure DNS názvové servery.

Všimněte si, že to platí jenom pro záznam NS, který je nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jako používané pro delegování podřízených zón) se dají upravovat bez omezení.

### <a name="delete-soa-or-ns-record-sets"></a>Odstranit sady záznamů SOA nebo NS

Sady záznamů SOA a NS nemůžete odstranit na vrcholu zóny (název = " \@ "), které se vytvoří automaticky při vytvoření zóny. Odstraní se automaticky při odstranění zóny.

## <a name="next-steps"></a>Další kroky

* Další informace o Azure DNS najdete v [přehledu Azure DNS](dns-overview.md).
* Další informace o automatizaci DNS najdete v tématu [vytváření zón a sad záznamů DNS pomocí sady .NET SDK](dns-sdk.md).
* Další informace o reverzních záznamech DNS najdete v tématu [Přehled reverzních DNS a podpory v Azure](dns-reverse-dns-overview.md).
* Další informace o Azure DNS záznamů aliasů najdete v tématu [Azure DNS záznamů aliasů](dns-alias.md).
