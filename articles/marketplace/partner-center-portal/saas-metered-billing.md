---
title: Měřená fakturace pro nabídky SaaS s využitím služby Microsoft komerčního měření softwaru
description: Přečtěte si o flexibilních modelech fakturace pro nabídky SaaS pomocí komerční služby měření na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892749"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Měřená fakturace pro SaaS s využitím komerční služby měření na webu Marketplace

Pomocí komerční služby měření na webu Marketplace můžete vytvořit nabídky software jako služba (SaaS), které se účtují podle nestandardních jednotek. Před publikováním nabídky SaaS na komerčním webu Marketplace definujete fakturační rozměry, jako je šířka pásma, lístky nebo zpracované e-maily.  Zákazníci pak můžou platit podle jejich spotřeby, a to s využitím vašeho systému Microsoftu prostřednictvím rozhraní API služby měření softwaru pro Fakturovatelné události, když k nim dojde.  

## <a name="prerequisites-for-metered-billing"></a>Předpoklady pro účtované fakturované ceny

Aby SaaS nabídka používala účtované podle objemu dat, musí nejdřív:

- Seznamte se se všemi požadavky nabídky pro [Prodej prostřednictvím nabídky Microsoft](../plan-saas-offer.md#listing-options) , jak je uvedeno v [části Vytvoření nabídky SaaS na komerčním webu Marketplace](../create-new-saas-offer.md).
- Integrujte s [rozhraními API pro plnění SaaS](./pc-saas-fulfillment-api-v2.md) , aby si zákazníci mohli zřídit a připojit se k vaší nabídce.  
- Dá se nakonfigurovat pro cenový model s **paušální sazbou** při zpoplatnění zákazníků pro vaši službu.  Dimenze jsou volitelné rozšíření cenového modelu paušálních sazeb. 

Nabídka SaaS se pak může integrovat s [rozhraními API služby měření komerčního tržiště](./marketplace-metering-service-apis.md) a informovat společnost Microsoft o fakturovatelných událostech.

>[!Note]
>Služba měření na Marketplace je dostupná jenom pro model fakturace paušálních sazeb a nevztahuje se na model fakturace na uživatele.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak se vyúčtování účtované podle objemu dokončí s cenami

Porozumění hierarchii nabídky je důležité, pokud je k dispozici pro definování nabídky společně s jejich cenovými modely.

- Každá nabídka SaaS je nakonfigurována k prodeji buď prostřednictvím Microsoft, nebo ne.  Po publikování nabídky nejde tuto možnost změnit.
- Každý SaaS nabídka, která je nakonfigurovaná pro prodej prostřednictvím Microsoftu, může mít jeden nebo víc plánů.  Uživatel se přihlásí k odběru nabídky SaaS, ale v rámci plánu je zakoupený prostřednictvím Microsoftu.
- K jednotlivým plánům je přiřazený cenový model: **paušální sazba** nebo **na uživatele**. Všechny plány v nabídce musí být přidruženy ke stejnému cenovému modelu. Nemůžete například mít nabídku s plány pro cenový model s paušální sazbou a další model cenového modelu podle uživatele.
- V rámci každého plánu nakonfigurovaného pro model fakturace paušálních sazeb se zahrnuje aspoň jeden opakovaný poplatek (který může být $0):
    - Opakovaný **měsíční** poplatek: paušální měsíční poplatek, který se předem hradí při měsíčním opakování, když uživatel zakoupí plán.
    - Periodický **roční** poplatek: paušální roční poplatek, který je předem zaplacen na roční opakování, když uživatel zakoupí plán.
- Kromě opakovaných poplatků může plán paušálních sazeb také zahrnovat volitelné vlastní dimenze, které se používají k tomu, aby se zákazníkům poplatky za nadlimitní využití, který není zahrnutý v paušální sazbě, používali.  Každá dimenze představuje Fakturovatelné jednotky, které vaše služba bude komunikovat s Microsoftem pomocí [rozhraní API služby měření komerčního webu Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Ukázka nabídky

Například contoso je vydavatel se službou SaaS s názvem contoso Notification Services (CNS). PROPOJENÉ sítě umožňují svým zákazníkům odesílat oznámení prostřednictvím e-mailu nebo textu. Společnost Contoso je registrovaná jako vydavatel v partnerském centru, aby mohl komerční web Marketplace publikovat nabídky SaaS pro zákazníky Azure.  K propojeným sítím jsou k dispozici dva plány:

- Plán úrovně Basic
    - Poslat 10000 e-mailů a 1000 texty pro $0/měsíc (paušální měsíční poplatek)
    - Za 10000 e-mailů platíte $1 pro každých 100 e-mailů.
    - Kromě textů 1000, platíte $0,02 za každý text

    [![Ceny plánu na úrovni Basic](./media/saas-basic-pricing.png "Kliknutím zobrazíte Zvětšené zobrazení.")](./media/saas-basic-pricing.png)

- Plán Premium
    - Posílat 50000 e-mailů a 10000 texty pro $350/měsíc nebo 5 min e-maily a 1M texty pro $3500 za rok
    - Za 50000 e-mailů platíte $0,5 pro každých 100 e-mailů.
    - Kromě textů 10000, platíte $0,01 za každý text

    [![Ceny plánu Premium](./media/saas-premium-pricing.png "Kliknutím zobrazíte Zvětšené zobrazení.")](./media/saas-premium-pricing.png)

- Plán organizace
    - Poslat neomezený počet e-mailů a 50000 textů pro $400 za měsíc
    - Za texty přesahující 50000 text platíte $0,005 pro každý txt

    [![Ceny plánu Enterprise](./media/saas-enterprise-pricing.png "Kliknutím zobrazíte Zvětšené zobrazení.")](./media/saas-enterprise-pricing.png)

V závislosti na vybraném plánu bude moct zákaznická předplatná Azure pro cloudovou nabídku SaaS odeslat zahrnuté množství textu a e-mailů na jedno předplatné (měsíc nebo rok, jak se zobrazí v podrobnostech o předplatném – startDate a endDate).  Společnost Contoso počítá využití až do zahrnutého množství v základu bez odeslání událostí využití do Microsoftu. Když zákazníci spotřebovávají více než zahrnuté množství, nemusí měnit plány ani provádět jiné akce.  Společnost Contoso bude měřit překročení množství nad rámec zahrnutého množství a začít vysílat události využití do Microsoftu za účelem využívání nadlimitního využití pomocí [rozhraní API služby měření v komerčním tržišti](./marketplace-metering-service-apis.md).  Společnost Microsoft pak bude zákazníkovi účtovat za nadlimitní využití určené vydavatelem ve vlastních dimenzích. Faktura za nadlimitní využití se provádí v dalším fakturačním cyklu (měsíčně, ale můžou být pro některé zákazníky čtvrtletní nebo rané).  U měsíčního plánu paušálních sazeb se fakturace za nadlimitní využití provede za každý měsíc, kde došlo k překročení limitu.  Pro plán ročních paušálních sazeb se po vyčerpání množství zahrnutého v základním měření bude veškeré další využití vygenerované vlastním měřičem účtovat jako překročení v průběhu každého fakturačního cyklu (měsíčně) až do konce roku předplatného.

## <a name="billing-dimensions"></a>Fakturační dimenze

Každá fakturační dimenze definuje vlastní jednotku, kterou ISV může generovat události využití.  Fakturační dimenze se také používají ke komunikaci se zákazníkem podle toho, jak se budou fakturovat za použití softwaru.  Jsou definovány takto:

- **ID**: neproměnlivý identifikátor dimenze, na který se odkazuje při generování událostí použití.
- **Zobrazovaný název**: zobrazovaný název přidružený k dimenzi, například "odeslané textové zprávy".
- Měrná **jednotka**: Popis fakturační jednotky, například zpráva "na textovou zprávu" nebo "za 100 e-mailů".
- **Cena za jednotku v USD**: cena za jednu jednotku dimenze.  Může být 0. 
- **Měsíční množství zahrnuté v základu**: Počet zahrnutých dimenzí za měsíc pro zákazníky, kteří platí pro opakovaný měsíční poplatek, musí být celé číslo. Může být 0 nebo neomezený.
- **Roční množství zahrnuté v základu**: množství dimenze zahrnuté v každém roce pro zákazníky, kteří platíte do periodického ročního poplatku, musí být celé číslo. Může být 0 nebo neomezeno.

Fakturační dimenze se sdílejí napříč všemi plány nabídky.  Některé atributy platí pro dimenzi napříč všemi plány a další atributy jsou specifické pro plán.

Atributy, které definují vlastní dimenzi, jsou sdíleny ve všech plánech nabídky.  Před publikováním této nabídky ovlivní změna těchto atributů z kontextu libovolného plánu definici dimenze ve všech plánech.  Po publikování této nabídky již nebude možné tyto atributy upravovat.  Jsou to tyto atributy:

- ID
- Zobrazovaný název
- Jednotka měření

Ostatní atributy dimenze jsou specifické pro každý plán a můžou mít různé hodnoty od plánu k plánování.  Před publikováním plánu můžete tyto hodnoty upravit a bude to mít vliv pouze na tento plán.  Po publikování plánu už tyto atributy nebude možné upravovat.  Jsou to tyto atributy:

- Cena za jednotku v USD
- Měsíční množství zahrnuté v základu  
- Roční množství zahrnuté v baseIncluded  

Dimenze mají také dvě speciální pojmy "povolené" a "nekonečné":

- **Povoleno** znamená, že se tento plán účastní v této dimenzi.  Pokud vytváříte nový plán, který neodesílá události využití na základě této dimenze, můžete tuto možnost ponechat nezaškrtnutou.  Také všechny nové dimenze přidané po prvním publikování plánu budou v již publikovaném plánu zobrazovat stav "Nepovoleno".  Zakázaná dimenze se nezobrazí v žádných seznamech dimenzí pro plán, který si zákazníci uvidí.
- **Nekonečné** reprezentované symbolem nekonečno "∞", označuje, že tento plán se účastní této dimenze, ale negeneruje na základě této dimenze využití.  Pokud chcete zákazníkům označovat, že funkce reprezentované touto dimenzí jsou zahrnuté v plánu, ale bez omezení využití.  Dimenze s nekonečným využitím se zobrazí v seznamech dimenzí pro plán, který uvidí zákazníci, s označením, že se za tento plán nikdy neúčtuje poplatek.

>[!Note] 
>Následující scénáře jsou explicitně podporovány: <br> – Novou dimenzi můžete přidat do nového plánu.  Nová dimenze nebude povolena pro žádné z již publikovaných plánů. <br> – Plán s **paušálními sazbami** můžete publikovat bez dimenzí a pak přidat nový plán a nakonfigurovat novou dimenzi pro tento plán. Nová dimenze nebude povolena pro již publikované plány.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Nastavení ceny dimenze na jednotku na podporovaný trh

Podobně jako ceny paušálních sazeb je možné nastavit ceny fakturačních dimenzí podle podporované země nebo oblasti. V partnerském centru musíte použít funkci pro import a export dat s cenami, a to následujícím způsobem.

1. Definujte požadované rozměry a označte, které trhy se podporují. 
1. Exportujte tato data do souboru.
1. Přidejte správné ceny na zemi nebo oblast a importujte soubor v partnerském centru.

Uživatelské rozhraní měřiče se změní tak, aby odráželo, že ceny dimenze lze zobrazit pouze v souboru.

[![dimenze služby měření komerčního tržiště](media/metering-service-dimensions.png "Kliknutím zobrazíte Zvětšené zobrazení.")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Soukromý plán

Podobně jako u plánů paušálních sazeb se plán s rozměry dá nastavit jako soukromý plán, který je přístupný jenom pro vymezenou cílovou skupinu daného plánu.

## <a name="constraints"></a>Omezení

### <a name="trial-behavior"></a>Chování při zkušební verzi

Měřené fakturace pomocí komerčního měření služby Marketplace není kompatibilní s nabídkou bezplatné zkušební verze.  Plán pro použití účtované podle objemu dat a bezplatné zkušební verze není možné konfigurovat.

### <a name="locking-behavior"></a>Chování při zamykání

Vzhledem k tomu, že dimenze použitá ve službě měření komerčního obchodu na webu Marketplace představuje porozumění způsobu, jakým bude zákazník platit za službu, po publikování nebudou všechny podrobnosti pro dimenzi upravovat.  Před publikováním je důležité, abyste měli dimenze plně definované pro plán.

Po publikování nabídky s dimenzí již nelze změnit podrobnosti na úrovni nabídky pro tuto dimenzi:

- ID
- Zobrazovaný název
- Jednotka měření

Po publikování plánu se podrobnosti na úrovni plánu už nedají změnit:

- Cena za jednotku v USD
- Měsíční množství zahrnuté v základu
- Roční množství zahrnuté v základu
- Zda je dimenze pro daný plán povolena nebo ne

### <a name="upper-limits"></a>Horní meze

Maximální počet dimenzí, které lze konfigurovat pro jednu nabídku, je 30 jedinečných dimenzí.

## <a name="get-support"></a>Získání podpory

Pokud máte některý z následujících problémů, můžete otevřít lístek podpory.

- Technické problémy s rozhraním API služby pro měření na webu Marketplace
- Problém, který je potřeba eskalovat z důvodu chyby nebo chyby na vaší straně (např. nesprávná událost použití)
- Jakékoli další problémy související s měřením podle objemu.

Pokud chcete porozumět možnostem podpory vydavatelů a otevřít lístek podpory s Microsoftem, postupujte podle pokynů v tématu [Podpora programu komerčního tržiště v partnerském centru](../support.md).

## <a name="next-steps"></a>Další kroky

- [Rozhraní API služeb měření na marketplace](./marketplace-metering-service-apis.md)
