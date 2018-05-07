---
title: Spravovat sady záznamů DNS a záznamy s Azure DNS | Microsoft Docs
description: Azure DNS poskytuje možnost spravovat sady záznamů DNS a záznamy při hostování vaší domény.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: da7d2118a0fb6bc5004856d994a01a932094ca45
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Záznamy DNS spravovat a sady záznamů pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Tento článek ukazuje, jak spravovat sady záznamů a záznamů zóny DNS pomocí portálu Azure.

Je důležité si uvědomit rozdíl mezi sady záznamů DNS a jednotlivé záznamy DNS. Sada záznamů je kolekce záznamů v zóně, které mají stejný název a jsou stejného typu. Další informace najdete v tématu [DNS vytvoření sady záznamů a záznamy pomocí portálu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Vytvoření nové sady záznamů a záznamů

Vytvoření záznamů na portálu Azure, naleznete v části [záznamy DNS vytvořit pomocí portálu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Zobrazení sady záznamů

1. V portálu Azure přejděte do **zónu DNS** okno.
2. Hledání pro sadu záznamů a vyberte jej. Otevře vlastnosti sady záznamů.

    ![Vyhledejte sady záznamů](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Přidejte nový záznam do sady záznamů

Můžete přidat až 20 záznamů pro všechny sady záznamů. Sada záznamů nesmí obsahovat dva identické záznamy. Prázdný sady záznamů (s nulový počet záznamů) mohou být vytvořeny, ale nejsou zobrazeny na názvových serverů Azure DNS. Sady záznamů typu CNAME může obsahovat maximálně jeden záznam.

1. Na **záznam nastavit vlastnosti** pro zónu DNS, klikněte na sadu záznamů, které chcete přidat záznam do.

    ![Vyberte sady záznamů](./media/dns-operations-recordsets-portal/selectset500.png)

2. Zadejte, že záznam nastaven vlastnosti vyplnění polí.

    ![Přidání záznamu](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klikněte na tlačítko **Uložit** v horní části okna nastavení uložte. Zavřete okno.
4. V horním uvidíte, zda je ukládání záznamu.

    ![Ukládání sady záznamů](./media/dns-operations-recordsets-portal/saving150.png)

Záznam po uložení, hodnoty na **zóny DNS** okně se projeví nový záznam.

## <a name="update-a-record"></a>Aktualizace záznamu

Při aktualizaci záznamu v existující sady záznamů pole, která můžete aktualizovat závisí na vybraném typu záznamu, kterými pracujete.

1. Na **vlastnosti sady záznamů** okna pro vaše sadu záznamů, vyhledejte záznam.
2. Upravte záznamu. Když upravíte záznam, můžete změnit nastavení k dispozici pro tento záznam. V následujícím příkladu **IP adresu** pole je vybrána, a IP adresu právě upravována.

    ![Upravte záznam](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klikněte na tlačítko **Uložit** v horní části okna nastavení uložte. V pravém horním rohu uvidíte oznámení, které byly uloženy v záznamu.

    ![Uložit záznamovou sadu](./media/dns-operations-recordsets-portal/saved150.png)

Po uložení záznamu, nastavte hodnoty pro záznam na **zónu DNS** okně se projeví aktualizovaný záznam.

## <a name="remove-a-record-from-a-record-set"></a>Odebrat záznam ze sady záznamů

Na portálu Azure můžete odebrat záznamy ze sady záznamů. Všimněte si, že odstraněním poslední záznam ze záznamů sady nedojde k odstranění sady záznamů.

1. Na **vlastnosti sady záznamů** okna pro vaše sadu záznamů, vyhledejte záznam.
2. Klikněte na záznam, který chcete odebrat. Potom vyberte **odebrat**.

    ![Odebrat záznam](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klikněte na tlačítko **Uložit** v horní části okna nastavení uložte.
4. Po odebrání záznamu, hodnoty pro záznam na **zónu DNS** okně se projeví odebrání.

## <a name="delete"></a>Odstranit sadu záznamů

1. Na **vlastnosti sady záznamů** pro vaše sadu záznamů, klikněte na **odstranit**.

    ![Odstranit sadu záznamů](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Zobrazí se zpráva s dotazem, pokud chcete odstranit záznamovou sadu.
3. Zkontrolujte, zda název odpovídá sady záznamů, který chcete odstranit a potom klikněte na **Ano**.
4. Na **zóny DNS** okno, ověřte, zda sada záznamů je již nebudou viditelné.

## <a name="work-with-ns-and-soa-records"></a>Práce se záznamy NS a SOA

Záznamy NS a SOA, které se automaticky vytvoří spravuje odlišně od ostatních typů záznamu.

### <a name="modify-soa-records"></a>Upravit záznamy SOA

Nelze přidat nebo odebrat záznamy z automaticky vytvořený záznam SOA nastavit ve vrcholu zóny (název = "@"). Ale můžete upravit některý z parametrů v rámci záznamu SOA (s výjimkou "hostitel") a hodnota TTL sady záznamu.

### <a name="modify-ns-records-at-the-zone-apex"></a>Upravit NS záznamů ve vrcholu zóny

S každou zónou DNS se automaticky vytvoří záznam NS nastavit ve vrcholu zóny. Obsahuje názvy názvových serverů Azure DNS přiřadit do zóny.

Můžete přidat další název nastavit servery na tento záznam NS, podporuje společné hosting domén s více než jednoho poskytovatele DNS. Můžete také upravit TTL a metadat pro tuto sadu záznamů. Však nelze odebrat ani změnit předem vyplněná názvových serverů Azure DNS.

Všimněte si, že vztahuje se pouze na vrcholu zóny sady záznamů NS. Jiné sady záznamů NS v pásmu (jak je používá delegovat podřízených zónách) můžete změnit bez omezení.

### <a name="delete-soa-or-ns-record-sets"></a>Odstranění sady záznamů SOA nebo NS

Nelze odstranit, SOA a sady záznamů NS ve vrcholu zóny (název = "@"), jsou vytvořeny automaticky při vytváření zóny. Jsou automaticky odstraněny při odstranění zóny.

## <a name="next-steps"></a>Další postup

* Další informace o službě Azure DNS najdete v tématu [přehled Azure DNS](dns-overview.md).
* Další informace o automatizaci DNS najdete v tématu [vytvoření DNS zóny a sad záznamů pomocí sady .NET SDK](dns-sdk.md).
* Další informace o zpětné záznamy DNS, najdete v části [přehled zpětné DNS a podpory v Azure](dns-reverse-dns-overview.md).
