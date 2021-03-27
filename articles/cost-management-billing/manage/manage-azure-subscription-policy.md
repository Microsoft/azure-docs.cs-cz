---
title: Správa zásad předplatného Azure
description: Naučte se spravovat zásady předplatného Azure a řídit přesun předplatných Azure z a do adresářů.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631849"
---
# <a name="manage-azure-subscription-policies"></a>Správa zásad předplatného Azure

>[!NOTE]
>Tato funkce je aktuálně ve verzi Preview a je stále prováděná, takže se na Azure Portal ještě nikdo nezobrazuje.

Tento článek vám pomůže nakonfigurovat zásady předplatného Azure pro operace předplatného a řídit přesun předplatných Azure z a do do adresářů.

## <a name="prerequisites"></a>Požadavky

- Zásady předplatného můžou upravovat jenom [globální správci](../../active-directory/roles/permissions-reference.md#global-administrator) adresáře. Před úpravou zásad předplatného musí globální správce [zvýšit přístup ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md). Pak můžou upravit zásady předplatného.
- Všichni ostatní uživatelé můžou číst jenom aktuální nastavení zásad.

## <a name="available-subscription-policy-settings"></a>Dostupná nastavení zásad předplatného

Pomocí následujících nastavení zásad můžete řídit přesun předplatných Azure z a do adresářů.

### <a name="subscriptions-leaving-aad-directory"></a>Předplatná opustí adresář AAD

Tato zásada umožňuje nebo brání uživatelům přesouvat odběry z aktuálního adresáře. [Vlastníci předplatného](../../role-based-access-control/built-in-roles.md#owner) můžou [změnit adresář předplatného Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) na jiný, kde se jedná o člena. Přináší problémy se zásadami správného řízení, takže globální správci můžou povolit nebo zakázat uživatelům adresáře měnit adresář.

### <a name="subscriptions-entering-aad-directory"></a>Odběry vstupující do adresáře AAD

Zásada povoluje nebo zastavuje uživatele z jiných adresářů, kteří mají přístup v aktuálním adresáři, k přesunutí předplatných do aktuálního adresáře. [Vlastníci předplatného](../../role-based-access-control/built-in-roles.md#owner) můžou [změnit adresář předplatného Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) na jiný, kde se jedná o člena. Přináší problémy se zásadami správného řízení, takže globální správci můžou povolit nebo zakázat uživatelům adresáře měnit adresář.

### <a name="exempted-users"></a>Vyloučení uživatelé

V případě zásad správného řízení můžou globální správci blokovat přesunutí všech adresářů předplatných do aktuálního adresáře. Můžou ale chtít povolit konkrétním uživatelům provádět operace. V obou případech můžou nakonfigurovat seznam vyloučených uživatelů, kteří uživatelům umožní obejít nastavení zásad, která platí pro všechny ostatní.

## <a name="setting-subscription-policy"></a>Nastavení zásad předplatného

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Přejděte k **předplatným**. **Správa zásad** se zobrazuje na panelu příkazů.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Snímek obrazovky s informacemi o správě zásad v předplatných" lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Výběrem **možnosti spravovat zásady** zobrazíte podrobnosti o aktuálních zásadách předplatného, které jsou pro tento adresář nastavené. Globální správce se [zvýšenými oprávněními](../../role-based-access-control/elevate-access-global-admin.md) může upravovat nastavení, včetně přidávání nebo odebírání vyloučených uživatelů.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Snímek obrazovky zobrazující konkrétní nastavení zásad a vyloučené uživatele" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. V dolní části vyberte **Uložit změny** a uložte změny. Změny se projeví okamžitě.

## <a name="read-subscription-policy"></a>Číst zásady předplatného

Neglobální správci můžou pořád přejít do oblasti zásad předplatného a zobrazit nastavení zásad adresáře. Nemůžou dělat žádné úpravy. Nemůžou zobrazit seznam vyloučených uživatelů z důvodu ochrany osobních údajů. Můžou si prohlížet své globální správce a odesílat žádosti o změny zásad, pokud je nastavení adresáře povoluje.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Snímek obrazovky znázorňující zásady správy v předplatných jako čtecí modul." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Další kroky

- Přečtěte si [dokumentaci cost management + fakturace](../index.yml) .