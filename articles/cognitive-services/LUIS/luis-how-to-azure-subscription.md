---
title: Spravovat předplatné koncového bodu Azure | Microsoft Docs
description: V tomto článku vytvořit koncový bod měření podle objemu klíč pro váš účet LEOŠ zajistit neomezená provoz na váš koncový bod, následující platebního plánu.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 3526871f126ac975f323fe84b14883b361b684ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343826"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Správa klíčů předplatné koncového bodu Azure

Pro účely testování a prototypu pouze pomocí úroveň free (F0). Pro produkční systémy, používat [placené](https://aka.ms/luis-price-tier) vrstvy. 

> [!NOTE]
> Nepoužívejte [vytváření klíč](luis-concept-keys.md#authoring-key) pro koncový bod dotazy v produkčním prostředí.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Vytvořte klíč LEOŠ koncového bodu

1. Přihlaste se k  **[Microsoft Azure](https://ms.portal.azure.com/)** 
2. Klikněte na tlačítko se zeleným **+** přihlásit v levém horním panelu a vyhledejte "LEOŠ" na webu Marketplace, a potom klikněte na **znalosti jazyka** a postupujte podle pokynů **vytvořit prostředí**  k vytvoření účtu LEOŠ předplatného. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Nakonfigurujte odběr nastavení název účtu, včetně cenové úrovně atd. 

    ![Volba Azure API](./media/luis-azure-subscription/azure-api-choice.png) 

4. Jakmile vytvoříte službu LEOŠ, můžete zobrazit přístupové klíče generované v **Správa prostředků -> klíče**.  

    ![Azure klíče](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > * Přihlaste se k vaší oblasti [LEOŠ](luis-reference-regions.md) webu a [přidejte nový klíč koncového bodu LEOŠ](Manage-Keys.md#assign-endpoint-key). 
    > * Je potřeba pamatovat název služby Azure jste vytvořili, aby bylo možné, vyberte ho v této oblasti [LEOŠ](luis-reference-regions.md) stránky publikování.  

## <a name="change-luis-pricing-tier"></a>Změna LEOŠ cenovou úroveň

1.  V [Azure](https://portal.azure.com), najít předplatné LEOŠ. Klikněte na předplatné LEOŠ.
    ![Najít předplatné LEOŠ](./media/luis-usage-tiers/find.png)
2.  Klikněte na tlačítko **cenová úroveň** Chcete-li zobrazit dostupné cenové úrovně. 
    ![Zobrazení cenové úrovně](./media/luis-usage-tiers/subscription.png)
3.  Klikněte na cenovou úroveň a klikněte na tlačítko **vyberte** uložte změny. 
    ![Vaše úroveň platebních LEOŠ změnit.](./media/luis-usage-tiers/plans.png)
4.  Po dokončení změny cenové ověřuje automaticky otevírané okno nového cenovou úroveň. 
    ![Ověřte vaší LEOŠ platebních vrstvy](./media/luis-usage-tiers/updated.png)
5. Nezapomeňte [přiřadit tento koncový bod klíč](manage-keys.md#assign-endpoint-key) na **publikovat** stránky a použít ho v všechny dotazy na koncový bod. 

## <a name="exceed-pricing-tier-usage"></a>Překročit cenovou úroveň využití
Každá úroveň umožňuje, aby váš účet LEOŠ požadavky koncového bodu v určitých. Pokud je rychlost požadavků na vyšší než povolený počet účtu měřené za minutu nebo měsíčně, požadavky obdrží chybu HTTP "429: příliš mnoho požadavků."

Každá úroveň umožňuje kumulativní požadavky za měsíc. Pokud jsou vyšší než počet povolených celkový počet požadavků, požadavky obdrží chybu HTTP "403: zakázáno".  

## <a name="viewing-summary-usage"></a>Zobrazení souhrnu využití
Informace o využití LEOŠ můžete zobrazit v Azure. **Přehled** stránky se zobrazí poslední souhrnné informace, včetně volání a chyb. Pokud provedete LEOŠ požadavku koncového bodu, pak okamžitě podívejte se **stránku přehled**, povolit až pět minut využití objeví.

![Zobrazení souhrnu využití](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Přizpůsobení použití grafů
Metriky poskytuje podrobnější pohled na data.

![Výchozích metrik](./media/luis-usage-tiers/metrics-default.png)

Vaše grafy metriky můžete nakonfigurovat pro časové období a metriky typu. 

![Vlastní metriky](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Celkový počet transakcí prahová hodnota výstrahy
Pokud chcete vědět, kdy bylo dosaženo určité transakce prahovou hodnotu, například 10 000 transakcí, můžete vytvořit výstrahu. 

![Výchozí výstrahy](./media/luis-usage-tiers/alert-default.png)

Přidání metriky upozornění pro **celkový počet volání** metriky pro určité časové období. Přidání e-mailové adresy všech osob, které by měly dostávat upozornění. Přidejte webhooky pro všechny systémy, které by měly dostávat upozornění. Aplikace logiky můžete také spustit, když je výstraha. 

## <a name="next-steps"></a>Další postup

Další informace o použití [verze](luis-how-to-manage-versions.md) pro správu změn LEOŠ aplikace.