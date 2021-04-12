---
title: Konfigurace koncového bodu služby Azure front-end Standard/Premium pomocí Správce koncových bodů
description: V tomto článku se dozvíte, jak nakonfigurovat koncový bod pomocí Správce koncových bodů.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098987"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Konfigurace koncového bodu služby Azure front-end Standard/Premium (Preview) pomocí Správce koncových bodů

> [!NOTE]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Prohlédněte si **[dokumentaci ke službě Azure front dveří](../front-door-overview.md)**.

V tomto článku se dozvíte, jak vytvořit koncový bod pro existující profil služby Azure front-Standard/Premium pomocí Správce koncových bodů.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit koncový bod služby Azure front-end Standard/Premium pomocí Správce koncových bodů, musíte vytvořit aspoň jeden profil front-dveří Azure. Profil musí mít alespoň jeden nebo více koncových bodů služby Azure front-Premium Standard/Premium. K organizování koncových bodů služby Azure front-end Standard/Premium pomocí internetové domény, webové aplikace nebo jiných kritérií můžete použít více profilů. 

Pokud chcete vytvořit profil front-dveří Azure, přečtěte si téma [Vytvoření nového profilu služby Azure front-dveří Standard/Premium](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Vytvořit nový koncový bod služby Azure front-end Standard/Premium

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k profilu Standard/Premium pro Azure front.

1. Vyberte **správce koncových bodů**. Pak vyberte **přidat koncový bod** a vytvořte nový koncový bod.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Snímek obrazovky s přidáním koncového bodu přes správce koncových bodů":::

1. Na stránce **přidat koncový bod** zadejte a vyberte následující nastavení.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Snímek obrazovky s přidáním stránky koncového bodu":::

    | Nastavení | Hodnota |
    | -------- | ----- |
    | Název | Zadejte jedinečný název pro nový koncový bod služby Azure front-end Standard/Premium. Tento název se používá pro přístup k prostředkům v mezipaměti v doméně. `<endpointname>.az01.azurefd.net` |
    | Časový limit odpovědi na zdroj (s) | Zadejte hodnotu časového limitu v sekundách, po kterou budou služby Azure na front-end čekat, než se zobrazí časový limit pro připojení s počátkem. |
    | Status | Zaškrtněte políčko pro povolení tohoto koncového bodu. |

## <a name="add-domains-origin-group-routes-and-security"></a>Přidání domén, skupiny původních, tras a zabezpečení

1. Na koncovém bodu vyberte **Upravit koncový bod** a nakonfigurujte trasu.

1. Na stránce **Upravit koncový bod** vyberte v části domény možnost **+ Přidat** .
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Snímek obrazovky výběru domény na stránce pro úpravu koncového bodu":::

### <a name="add-domain"></a>Přidat doménu

1. Na stránce **Přidat doménu** vyberte možnost přidružit doménu *ze svého profilu front-dveří Azure* nebo *Přidat novou doménu*. Informace o tom, jak vytvořit značku nové domény, najdete v tématu [Vytvoření nové vlastní domény Azure front-dveří Standard/Premium](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Snímek obrazovky s přidáním stránky domény":::

1. Vyberte **Přidat** a přidejte doménu do aktuálního koncového bodu. Vybraná doména by se měla zobrazit v panelu doména.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Snímek obrazovky domény v zobrazení doména":::

### <a name="add-origin-group"></a>Přidat skupinu původu

1. V zobrazení skupiny původních zdrojů vyberte **Přidat** . Zobrazí se stránka **přidat zdrojovou skupinu** . 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Snímek obrazovky s přidáním stránky zdrojové skupiny":::

1. Do pole **název** zadejte jedinečný název nové skupiny původních souborů.

1. Vyberte **Přidat počátek** a přidejte nový počátek do aktuální skupiny.
 
#### <a name="health-probes"></a>Sondy stavu
Přední dveře odesílají pravidelné zkušební požadavky HTTP/HTTPS každému zdroji. Požadavky na testování určují vzdálenost a stav jednotlivých zdrojů k vyrovnávání zatížení vašich požadavků koncových uživatelů. Nastavení sond stavu pro skupinu původu definují, jak se dotazuje na stav původu aplikace. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

> [!WARNING]
> Vzhledem k tomu, že přední dvířka mají globálně mnoho hraničních prostředí, objem sondy stavu pro váš zdroj může být poměrně vysoký od 25 požadavků každou minutu až po 1200 požadavků za minutu, v závislosti na konfiguraci frekvence sond stavu. S výchozí frekvencí sondy 30 sekund by měl mít svazek testu na začátku přibližně 200 požadavků za minutu.

* **Stav**: Určete, jestli se má zapnout zjišťování stavu. Pokud máte ve skupině původu jeden počátek, můžete zakázat sondy stavu, které snižují zatížení back-endu aplikace. I v případě, že ve skupině máte více původních zdrojů, ale pouze jeden z nich je v povoleném stavu, můžete vypnout sondy stavu.
   
* **Cesta**: adresa URL, která se používá k testování požadavků pro všechny zdroje v této skupině původu. Například pokud je jeden z vašich zdrojů contoso-westus.azurewebsites.net a cesta je nastavená na/PROBE/test.aspx, pak na front-prostředí, za předpokladu, že je protokol nastavený na HTTP, bude odesílat požadavky sond stavu na `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protokol**: Určuje, jestli se mají odesílat požadavky sondy stavu z předních dveří do svého původu pomocí protokolu HTTP nebo HTTPS.
   
* **Metoda sondy**: metoda HTTP, která se má použít k odesílání sond stavu. Mezi možnosti patří GET nebo HEAD (výchozí).

    > [!NOTE]
    > Pro nižší zatížení a náklady na váš původ přední dveře doporučuje použití HLAVních požadavků pro sondy stavu.

* **Interval (v sekundách)**: definuje četnost sond stavu pro váš původ nebo intervaly, ve kterých každé z těchto prostředí front-dveří odesílá test.
   
    >[!NOTE]
    >V případě rychlejšího převzetí služeb při selhání nastavte interval na nižší hodnotu. Čím nižší hodnota, tím vyšší je objem sondy stavu, který váš zdroj obdrží. Pokud je například interval nastaven na 30 sekund s vyslovením, 100 přední dvířka (pop) se zobrazí globálně, každý back-end bude dostávat asi 200 zkušebních požadavků za minutu.

#### <a name="load-balancing"></a>Vyrovnávání zatížení
Nastavení vyrovnávání zatížení pro skupinu původu definuje, jak vyhodnocujeme sondy stavu. Tato nastavení určují, jestli je back-end v pořádku nebo není v pořádku. Také kontrolují, jak vyrovnávat zatížení provozu mezi různými zdroji v původní skupině. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

- **Velikost vzorku**. Určuje, kolik ukázek sond stavu musíme zvážit pro vyhodnocování stavu původu.

- **Velikost vzorku je úspěšná**. Definuje velikost vzorku, jak je uvedeno výše, počet úspěšných vzorků potřebných k volání zdroje v pořádku. Předpokládejme například, že interval sondy stavů front je 30 sekund, velikost vzorku je 5 a úspěšná velikost vzorku je 3. Pokaždé, když vyhodnocujeme sondy stavu pro váš původ, podíváme se na posledních pět vzorků za více než 150 sekund (5 × 30). K deklaraci back-endu jako v pořádku se vyžadují aspoň tři úspěšné sondy.

- **Citlivost na latenci (mimořádná latence)**. Definuje, jestli chcete, aby přední dvířka odesílala požadavek na počátek v rozsahu citlivosti měření latence nebo předaly požadavek do nejbližšího back-endu.

Výběrem **Přidat** přidejte skupinu původ do aktuálního koncového bodu. Skupina původ by se měla zobrazit na panelu skupiny zdroje.

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Snímek obrazovky se zdroji v původní skupině":::

### <a name="add-route"></a>Přidat trasu

V zobrazení trasy vyberte **Přidat** a zobrazí se stránka **Přidat trasu** . Informace o tom, jak přidružit skupinu doména a původ, najdete v tématu [Vytvoření nové trasy front-dveří Azure](how-to-configure-route.md) .

### <a name="add-security"></a>Přidat zabezpečení

1. V zobrazení zabezpečení vyberte **Přidat** a zobrazí se stránka **Přidat zásady WAF** .
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Snímek obrazovky s přidáním stránky zásad WAF":::

1. **Zásady WAF**: vyberte zásadu WAF, kterou chcete použít pro vybranou doménu v rámci tohoto koncového bodu. 
  
   Vyberte **vytvořit novou** a vytvořte zcela novou zásadu WAF.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Snímek obrazovky s vytvořením nové zásady WAF":::
   
    **Název**: Zadejte jedinečný název pro nové zásady WAF. Tuto zásadu můžete upravit s větší konfigurací ze stránky firewallu webových aplikací.

    **Domény**: vyberte doménu, pro kterou chcete zásady WAF použít.

1. Vyberte tlačítko **Přidat** . Zásada WAF by se měla zobrazit v panelu zabezpečení

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Snímek obrazovky zásad WAF v zobrazení zabezpečení":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete koncový bod odstranit, když už ho nepotřebujete, vyberte **Odstranit koncový bod** na konci řádku koncového bodu. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Snímek obrazovky s postupem odstranění koncového bodu":::

## <a name="next-steps"></a>Další kroky

Další informace o vlastních doménách získáte tak, že budete pokračovat [v přidávání vlastní domény](how-to-add-custom-domain.md).
