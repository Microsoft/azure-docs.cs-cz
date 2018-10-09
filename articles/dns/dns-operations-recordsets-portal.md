---
title: Spravovat sady záznamů DNS a záznamy s využitím Azure DNS
description: Azure DNS umožňuje spravovat záznamy a sadami záznamů DNS, při hostování domény.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48853704"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Ke správě DNS záznamů a sad záznamů pomocí webu Azure portal

V tomto článku se dozvíte, jak spravovat sady záznamů a záznamů zóny DNS pomocí webu Azure portal.

Je důležité porozumět rozdílu mezi jednotlivé záznamy DNS a sad záznamů DNS. Sady záznamů je kolekcí záznamů v zóně, které mají stejný název a jsou stejného typu. Další informace najdete v tématu [DNS vytvořit sad záznamů a záznamů pomocí webu Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Vytvořit novou sadu záznamů a záznamu

Vytvoření záznamu nastavit na webu Azure Portal najdete v tématu [vytvářet DNS záznamy pomocí webu Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Zobrazení sady záznamů

1. Na webu Azure Portal, přejděte **zónu DNS** okno.
2. Vyhledání sady záznamů a vyberte ji. Tím se otevře vlastnosti sady záznamů.

    ![Hledání pro sadu záznamů](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Přidat nový záznam do sady záznamů

Můžete přidat až 20 záznamů do žádné sady záznamů. Sada záznamů nesmí obsahovat dva shodné záznamy. Prázdné sady záznamů (s nulovou záznamy) je možné vytvořit, ale nejsou zobrazeny na názvové servery Azure DNS. Sady záznamů CNAME typu může obsahovat nanejvýš jeden záznam.

1. Na **vlastnosti sady záznamů** okno pro vaši zónu DNS, klikněte na sadu záznamů, které chcete přidat záznam.

    ![Vyberte sadu záznamů](./media/dns-operations-recordsets-portal/selectset500.png)

2. Zadejte že vlastnosti sady záznamu vyplněním polí.

    ![Přidejte záznam](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klikněte na tlačítko **Uložit** v horní části okna uložte nastavení. Potom toto okno zavřete.
4. V pravém rohu zobrazí se, že je uložení záznamu.

    ![Ukládá se záznamová sada](./media/dns-operations-recordsets-portal/saving150.png)

Po záznam byl uložen, hodnoty **zónu DNS** okno bude odrážet nový záznam.

## <a name="update-a-record"></a>Aktualizovat záznam

Při aktualizaci záznamu v existující sady záznamů, které můžete aktualizovat pole závisí na typu záznamu pracujete s.

1. Na **vlastnosti sady záznamů** okno pro sadu záznamů, vyhledejte záznam.
2. Upravte záznamu. Pokud chcete záznam upravit, můžete změnit dostupná nastavení pro záznam. V následujícím příkladu **IP adresu** vybrané pole, a IP adresa je právě upravována.

    ![Pokud chcete záznam upravit](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klikněte na tlačítko **Uložit** v horní části okna uložte nastavení. V pravém horním rohu uvidíte oznámení, které se uložil záznam.

    ![Uložila se záznamová sada](./media/dns-operations-recordsets-portal/saved150.png)

Po uložení záznamu na nastavit hodnoty pro záznam **zónu DNS** okno bude odrážet aktualizovaný záznam.

## <a name="remove-a-record-from-a-record-set"></a>Odebrat záznam ze sady záznamů

Můžete na webu Azure portal odebrat záznamy ze sady záznamů. Všimněte si, že poslední záznam odebrání sady záznamů nedojde k odstranění sady záznamů.

1. Na **vlastnosti sady záznamů** okno pro sadu záznamů, vyhledejte záznam.
2. Klikněte na záznam, který chcete odebrat. Potom vyberte **odebrat**.

    ![Odebrání záznamu](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klikněte na tlačítko **Uložit** v horní části okna uložte nastavení.
4. Po odebrání záznamu hodnoty pro záznam na **zónu DNS** okno se odebrání projeví.

## <a name="delete"></a>Odstranit záznamovou sadu

1. Na **vlastnosti sady záznamů** okno pro sadu záznamů, klikněte na tlačítko **odstranit**.

    ![Odstranit záznamovou sadu](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Zobrazí se zpráva s dotazem, pokud chcete odstranit záznamovou sadu.
3. Zkontrolujte, zda odpovídá názvu sady záznamů, které chcete odstranit a potom klikněte na tlačítko **Ano**.
4. Na **zónu DNS** okno, ověřte, že sada záznamů již není viditelný.

## <a name="work-with-ns-and-soa-records"></a>Práce se záznamy NS a SOA

Záznamy NS a SOA, které se automaticky vytvoří spravují jinak než ostatní typy záznamů.

### <a name="modify-soa-records"></a>Úprava záznamů SOA

Nelze přidat ani odebrat záznamy automaticky vytvořený záznam SOA nastavit ve vrcholu zóny (název = "\@"). Však můžete upravit některé parametry v záznamu SOA (s výjimkou "hostitel") a hodnota TTL sady záznamu.

### <a name="modify-ns-records-at-the-zone-apex"></a>Upravte záznamy NS ve vrcholu zóny

S každou zónou DNS se automaticky vytvoří sadu ve vrcholu zóny záznamů NS. Obsahuje názvy názvových serverů Azure DNS přiřazených k zóně.

Můžete přidat další názvové servery pro tento záznam NS nastavit podporuje společné hosting domén s více než jednoho poskytovatele DNS. Můžete také upravit hodnotu TTL a metadata pro tuto sadu záznamů. Ale nejde odebrat ani změnit předem naplněných názvové servery Azure DNS.

Všimněte si, že to platí pouze pro záznam NS, nastavte ve vrcholu zóny. Bez omezení, lze upravit jinými sadami záznamů NS v pásmu (jak se používá k delegování podřízené zóny).

### <a name="delete-soa-or-ns-record-sets"></a>Odstranit sady záznamů SOA nebo NS

Nelze odstranit SOA a sady záznamů NS ve vrcholu zóny (název = "\@"), který se vytvoří automaticky při vytváření zóny. Odstraní se automaticky při odstranění zóny.

## <a name="next-steps"></a>Další postup

* Další informace o službě Azure DNS, najdete v článku [přehled Azure DNS](dns-overview.md).
* Další informace o automatizaci DNS najdete v tématu [vytváření DNS zón a sad záznamů pomocí sady .NET SDK](dns-sdk.md).
* Další informace o reverzních záznamů DNS najdete v tématu [přehled reverzní DNS a podporu v Azure](dns-reverse-dns-overview.md).
* Další informace o záznamů aliasů DNS Azure najdete v tématu [Azure DNS alias záznamy přehled](dns-alias.md).
