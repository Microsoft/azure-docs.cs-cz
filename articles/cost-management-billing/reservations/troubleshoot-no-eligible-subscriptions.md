---
title: Řešení potíží se situací, kdy na webu Azure Portal nejsou žádná oprávněná předplatná
description: Tento článek vám pomůže při řešení potíží s chybovou zprávou typu Žádné opravňující předplatné na webu Azure Portal při pokusu o zakoupení rezervace.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.openlocfilehash: 1b36577c3c0940687f98394f8ea4faae83f371be
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887191"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Řešení potíží s chybou typu Žádné opravňující předplatné

Tento článek vám pomůže při řešení potíží s chybovou zprávou typu *Žádné opravňující předplatné* na webu Azure Portal při pokusu o zakoupení rezervace.

## <a name="symptoms"></a>Příznaky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a přejděte k položce **Rezervace**.
1. Vyberte **Přidat** a pak vyberte požadovanou službu.
1. Zobrazí se následující chybová zpráva:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. V sekci **Vybrat produkt, který chcete koupit** rozbalte seznam **Předplatné pro fakturaci** a projděte si důvody, proč pro konkrétní předplatné není nárok na zakoupení rezervované instance. Následující obrázek ukazuje příklady, proč rezervaci nejde koupit.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Příklady ukazující, proč rezervaci nejde koupit" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Příčina

Pokud si chcete koupit rezervovanou instanci Azure, musíte mít alespoň jedno předplatné, které splňuje následující požadavky:

- Předplatné musí být podporovaný typ nabídky. Podporované typy nabídek: Průběžné platby, Cloud Solution Provider (CSP), Microsoft Azure Enterprise nebo Smlouva se zákazníkem Microsoftu.
- Musíte být vlastníkem nebo nákupčím rezervací pro toto předplatné.

Pokud nemáte předplatné, které by splňovalo požadavky, zobrazí se chyba `No eligible subscriptions`.

### <a name="cause-1"></a>Příčina 1

Předplatné musí být podporovaný typ nabídky. Podporované typy nabídek: Průběžné platby, CSP, Microsoft Azure Enterprise nebo Smlouva se zákazníkem Microsoftu. Váš typ předplatného nepatří mezi podporované. Po výběru předplatného s typem nabídky, který nepodporuje rezervace, zobrazí se následující chyba.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Příklad ukazující chybovou zprávu Předplatné nemá nárok na nákup" :::

### <a name="cause-2"></a>Příčina 2

Musíte být vlastníkem nebo nákupčím rezervací pro toto předplatné. Pokud nemáte dostatečná oprávnění, zobrazí se následující chyba.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Řešení

- Pokud vaše aktuální nabídka nepodporuje rezervace, je potřeba vytvořit nové předplatné Azure.
- Pokud nemáte přístup k existující rezervaci, můžete k ní získat přístup od aktuálního vlastníka.

### <a name="solution-1"></a>Řešení 1

Pokud si chcete koupit rezervaci, musíte vytvořit nové předplatné Azure, které podporuje rezervace.

- Pokud máte bezplatnou zkušební verzi Azure, můžete [upgradovat vaše předplatné](../manage/upgrade-azure-subscription.md).
- Můžete si vytvořit nové předplatné Azure s [průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Zaregistrujte se pro [Smlouvu se zákazníkem Microsoftu](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) a vytvořte si nové předplatné.
- Kupte si nové předplatné s [CSP](https://www.microsoft.com/solution-providers/home) a vytvořte nové předplatné.

### <a name="solution-2"></a>Řešení 2

Pokud chcete pro rezervaci získat přístup vlastníka, musíte získat přístup k jedné z těchto možností:

- K objednávce rezervací, přes kterou byla rezervace zakoupena
- K samotné rezervaci

Aktuální vlastník objednávky rezervace nebo vlastník rezervace vám může delegovat přístup pomocí následujících kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Výběrem **Všechny služby** > **Rezervace** zobrazte seznam rezervací, ke kterým máte přístup.
1. Vyberte rezervaci, ke které chcete delegovat přístup jiným uživatelům.
1. Vyberte **Řízení přístupu (IAM)** .
1. Vyberte **Přidat přiřazení role** > **Role** > **Vlastník**. Pokud chcete udělit omezený přístup, vyberte jinou roli.
1. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
1. Vyberte uživatele a pak vyberte **Uložit**.

Další informace najdete v tématu [Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="next-steps"></a>Další kroky

- Pokud potřebujete, aby vám vlastník rezervace udělil přístup, projděte si téma [Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
