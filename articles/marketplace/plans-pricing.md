---
title: Plány a ceny pro komerční nabídky na webu Marketplace
description: Seznamte se s plány pro nabídky Microsoft Commercial Marketplace v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858168"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Plány a ceny pro komerční nabídky na webu Marketplace

Plán definuje rozsah a omezení nabídky a související ceny, pokud jsou k dispozici. Například v závislosti na typu nabídky můžete vybrat regionální trhy a vybrat, jestli je plán viditelný pro veřejnost, nebo jenom pro soukromou cílovou skupinu. Některé typy nabídek podporují opakující se odběry, některé podporují ceny založené na používání a některé umožňují zákazníkovi zakoupit nabídku s licencí, kterou si koupili přímo od vydavatele. Díky tomu získáte flexibilitu v případě potřeby pro zákazníky s různými technickými a cenovými možnostmi.

Pro každou nabídku, která podporuje plány, můžete vytvořit až 100 plánů a až 45 těchto plánů může být [privátní](#plan-visibility). Placené plány můžete vytvářet jenom pro nabídky Azure Virtual Machine, nabídky aplikací Azure (spravované aplikace) a nabídky software jako služba (SaaS). Pokud se rozhodnete prodávat některou z těchto nabídek prostřednictvím Microsoft (nabídky s podporou transakcí), je nutné vytvořit alespoň jeden plán. Můžete vytvořit plány pro některé z ostatních typů nabídek, ale plány pro tyto typy nabídek nezahrnují cenové možnosti.

> [!TIP]
> Nabídka s podporou transakcí je ta, ve které Microsoft usnadňuje výměnu peněz za softwarovou licenci jménem vydavatele.

## <a name="plans-by-offer-type"></a>Plány podle typu nabídky

V následující tabulce jsou uvedeny možnosti plánu pro každý typ nabídky. V následujících částech se dozvíte víc o těchto možnostech. 

| Typ nabídky | Plány s cenovými možnostmi | Plány bez cenových možností | Možnost privátních cílových skupin |
| ------------ | ------------- | ------------- | ------------- |
| Spravovaná aplikace Azure | &#10004; | | &#10004; |
| Šablona řešení Azure | | &#10004; | &#10004; |
| Kontejner Azure | | &#10004; (BYOL) | |
| Modul IoT Edge |  | &#10004; |  |
| Spravovaná služba |  | &#10004; (BYOL) | &#10004; |
| Software jako služba | &#10004; |  | &#10004; |
| Virtuální počítač Azure | &#10004; |  | &#10004; |
|||||

Plány nejsou podporovány pro následující typy nabídek:

- Konzultační služba
- Dynamics 365 Business Central
- Dynamics 365 Customer Engagement & PowerApps
- Dynamics 365 for Operations
- Aplikace Power BI

## <a name="plan-information"></a>Informace o plánu

Každý typ nabídky při vytváření nového plánu vyžaduje jiné informace. Odkazy na články konkrétní nabídky najdete v [příručce pro publikování podle typu nabídky](publisher-guide-by-offer-type.md). Po vytvoření nového plánu na stránce **Přehled plánu** uvidíte různé karty, jako je například **Výpis plánu** nebo **ceny a dostupnost** , pro konfiguraci různých podrobností pro váš plán. Při práci s těmito poli se na každé kartě zobrazí stav nedokončeno nebo dokončeno.

![Znázorňuje stránku seznam plánů v partnerském centru. Zvýrazní se pole název plánu, souhrn plánu a popis plánu.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Pro nový plán je potřeba provést několik běžných podrobností:

- **ID plánu**: Vytvořte jedinečné ID pro každý plán v této nabídce. Použijte maximálně 50 znaků: jenom malá písmena, alfanumerické znaky, pomlčky a podtržítka. Toto ID se bude zobrazovat zákazníkům v adrese URL produktu a šablony Azure Resource Manager (pokud jsou k dispozici). Po publikování této nabídky nelze toto ID změnit.
- **Název plánu**: (popisek 1 na obrázku výše) Pro každý plán v této nabídce Vytvořte jedinečný název. Použijte maximálně 50 znaků. Název plánu slouží k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (například název nabídky: Plán Standard a plán Enterprise). Zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky.
- **Souhrn plánu**: (popisek 2 na obrázku výše) Tento souhrn se zobrazí ve výsledcích hledání Azure Marketplace a může obsahovat až 100 znaků.
   > [!NOTE]
   > Toto pole se nevztahuje na nabídky SaaS.
- **Popis plánu**: (popisek 3 na obrázku výše) Přidejte popis plánu, který vysvětluje, co dělá tento plán jedinečný z jiných plánů vaší nabídky. Použijte maximálně 500 znaků. Tento obsah se uživatelům zobrazí na stránkách seznamu nabídky při procházení a vyberte plán pro vaši nabídku.

Název a popis plánu se zobrazí na stránce seznam nabídek v online obchodech s komerčním Marketplace. Následující snímek obrazovky ukazuje tři plány pro seznam SaaS nabídek v Azure Marketplace.

![Ukazuje stránku se seznamem nabídek v partnerském centru. Zobrazují se tři plány.](./media/commercial-marketplace-plans/offer-listing-page.png)

Po vytvoření plánů se na stránce **Přehled plánu** zobrazí seznam názvů vašich plánů, ID, další podrobnosti, aktuální stav publikování a všechny dostupné akce. Dostupné akce budou záviset na stavu vašeho plánu a můžou zahrnovat:

- Pokud je stav plánu **koncept** – odstranit koncept.
- Pokud je stav plánu **Live** – zastavte prodej plánu nebo synchronizujte soukromou cílovou skupinu.

Následující snímek obrazovky ukazuje dvě koncepty nabídek.

![Popisuje stránku s přehledem plánu v partnerském centru. Zobrazí se dva plány.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Komerční tržiště funguje na modelu agentury, na základě kterého vydavatelé nastavili ceny, zákazníky s Microsoftem a Microsoft platí za to, že budou mít poplatky za srážku. Na kartě **ceny a dostupnost** **nebo dostupnost můžete** definovat ceny, viditelnost a ceny vaší nabídky (Pokud je k dispozici).

- **Trhy**: každý plán musí být k dispozici aspoň na jednom trhu.  Máte možnost vybrat jenom země "daňové prominuté", ve kterých Microsoft za vás vybírá prodej a používání daně.
- **Ceny**: cenové modely se vztahují jenom na plány pro nabídky Azure Managed Application, SaaS a Azure pro virtuální počítače. Všechny plány pro stejnou nabídku musí používat stejný cenový model.  
- **Plánování viditelnosti**: v závislosti na typu nabídky můžete definovat privátní cílovou skupinu nebo skrýt nabídku nebo plán z Azure Marketplace. Tato informace je podrobněji vysvětlena v části [plánování viditelnosti](#plan-visibility) dále v tomto článku.

> [!TIP]
> Doporučujeme vytvořit plány, které jsou nejvhodnější pro vzorce používání cílové zákaznické základny. Tím se snižuje počet uživatelů, kteří budou často přepínat plány na základě jejich změn v používání. Příklad nabídky SaaS se třemi měřenými fakturačními plány najdete v tématu [Ukázka nabídky](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Cenové modely

Pro následující typy nabídek musíte přidružit cenový model k jednotlivým plánům. Každý z těchto typů nabídek má různé dostupné cenové modely:

- **Spravovaná aplikace Azure**: paušální sazba (měsíčně) a ceny na základě využití (dimenze služby měření).
- **Software jako služba**: paušální sazba (měsíční nebo roční), na uživatele a ceny na základě využití (dimenze služby měření). 
- **Virtuální počítač Azure**: Přineste si vlastní licenci (BYOL) a ceny na základě využití. V případě cenového modelu založeného na využití můžete účtovat za jádro, na velikost jádra nebo na trh a základní velikost. Model licence BYOL neumožňuje další poplatky založené na využití.   (Nabídky virtuálních počítačů s BYOL nevyžadují cenový model.)

Všechny plány pro stejnou nabídku musí používat stejný cenový model. Například nabídka SaaS nemůže mít jeden plán, který je paušální sazbou a druhý plán na uživatele. Podrobné informace najdete v dokumentaci ke konkrétní nabídce.

Pokud jste už pro svůj plán nastavili ceny v USA dolary (USD) a přidáte další místo na trhu, cena za nový trh se vypočítá podle aktuálních směnných kurzů. Po uložení změn se zobrazí odkaz **exportovat ceny (XLSX)** , který můžete použít ke kontrole a změně ceny pro každý trh před publikováním.

> [!IMPORTANT]
> Po publikování vaší nabídky nelze změnit výběr cenového modelu.

Nabídky SaaS s paušálními sazbami a spravované aplikace nabízí podporu účtované podle objemu pomocí služby měření na webu Marketplace. Toto je model fakturace založený na využití, který vám umožní definovat nestandardní jednotky, jako je například šířka pásma nebo e-maily, které budou platit pro vaše zákazníky na základě spotřeby. Další informace o účtovaných fakturách pro [spravované aplikace](./partner-center-portal/azure-app-metered-billing.md) a [aplikace SaaS](./partner-center-portal/saas-metered-billing.md)najdete v související dokumentaci.

## <a name="custom-prices"></a>Vlastní ceny

Chcete-li nastavit vlastní ceny v individuálním trhu, exportujte, upravte a pak Importujte tabulku s cenami. Zodpovídáte za ověřování těchto cen a vlastnictví těchto nastavení.

Před publikováním pečlivě zkontrolujte své ceny, protože existují určitá omezení, co se může po publikování plánu změnit:

- Po publikování plánu se cenový model nedá změnit.
- Po zveřejnění fakturačního období pro určitý plán ho nebude možné odebrat později.
- Po zveřejnění ceny za uvedení na trh ve vašem plánu ji nelze později změnit.

Ceny nastavené v USA dolarů (USD) se při uložení převedou na místní měnu všech vybraných trhů. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a provedete kontrolu ceny na všech vybraných trzích.

1. Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen. U dolního okraje karty **ceny a dostupnost** vyberte **Uložit koncept**.
1. V části **ceny**vyberte odkaz **exportovat data cen** .
1. Otevřete soubor exportedPrice.xlsx v aplikaci Microsoft Excel.
1. V tabulce proveďte požadované aktualizace informací o cenách a pak uložte. Soubor CSV.
    > [!NOTE]
    > Než budete moct soubor aktualizovat, možná budete muset povolit úpravy v Excelu.
1. Na kartě **ceny a dostupnost** v části **ceny**vyberte odkaz **importovat data ceny** .
1. V dialogovém okně, které se zobrazí, vyberte **Ano**.
1. Vyberte soubor exportedPrice.xlsx, který jste aktualizovali, a pak vyberte **otevřít**.

## <a name="plan-visibility"></a>Zobrazení plánu

Můžete vytvořit plány pro některé typy nabídek veřejně dostupné nebo k dispozici pouze konkrétní (soukromé) cílové skupině. Nabídky s privátními plány budou publikovány na Azure Portal. Další informace o privátních plánech najdete v [části soukromé nabídky na komerčním webu Microsoft Marketplace](private-offers.md).

V závislosti na typu nabídky můžete definovat privátní cílovou skupinu pomocí ID klientů Azure nebo ID předplatných Azure. Můžete zadat až 10 ID ručně nebo můžete importovat až 10, 00 ID předplatných nebo 20 000 ID klientů (podle potřeby) se souborem. csv. Můžete také definovat soukromé cílové skupiny pro konzultační služby a nabídky Dynamics 365, které nemají plány.

Po publikování vaší nabídky s privátním plánem můžete cílovou skupinu aktualizovat nebo si vybrat, že plán bude dostupný všem. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny a nemůže být nakonfigurován jako soukromý plán znovu.

> [!NOTE]
> Privátní skupina se liší od cílové skupiny Preview. Na stránce **dostupnost** u některých typů nabídek můžete definovat cílovou skupinu, která může zobrazit náhled vaší nabídky před tím, než se nabídka publikuje živě na komerčním webu Marketplace. I když se označení soukromé cílové skupiny vztahuje pouze na konkrétní plán, cílová skupina verze Preview může zobrazit všechny plány (soukromě nebo ne), ale pouze v době, kdy se plán testuje a ověřuje, až po dobu omezené verze Preview.

Můžete také zvolit možnost zcela skrýt plán z Azure Marketplace, pokud se vaše nabídka nasadí jako součást spravované aplikace nebo šablony řešení.

## <a name="free-trials"></a>Bezplatné zkušební verze

Můžete povolit bezplatnou zkušební verzi plánů pro virtuální počítač Azure s podporou transakcí a SaaS nabídek.

> [!NOTE]
> Tato část vysvětluje bezplatné zkušební verze na placené plány pro nabídky, které se prodávají prostřednictvím Microsoftu. To je jiné než bezplatné zkušební výpisy od partnerů, kteří se rozhodnou zpracovávat své transakce nezávisle. Seznam bezplatné zkušební verze (který není spojen s plánem) můžete vytvořit pro následující typy nabídek:
> - Virtuální počítač Azure 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 pro Customer Engagement & PowerApps
> - Dynamics 365 for Operations
>
> Další informace o možnostech výpisu najdete v tématu [Určení možnosti publikování](determine-your-listing-type.md).

Bezplatné zkušební verze se podporují pro všechny modely fakturace s výjimkou měřených plánů. Plány SaaS umožňují bezplatné zkušební verze na 1 měsíc. Plány virtuálních počítačů Azure umožňují bezplatné zkušební verze na 1, 3 nebo 6 měsíců.

Když zákazník vybere bezplatnou zkušební verzi, shromáždíme si informace o fakturaci, ale zákazníky nepůjde účtovat, dokud se zkušební verze nepřevede na placené předplatné. Bezplatné zkušební verze se na konci zkušebního období automaticky převedou na placené předplatné, pokud zákazník předplatné zruší před skončením zkušebního období.

Během zkušebního období můžou zákazníci vyhodnotit některý z podporovaných plánů v rámci stejné nabídky, která má povolenou bezplatnou zkušební verzi. Pokud se v rámci stejné nabídky přepne na jinou zkušební verzi, zkušební období se nerestartuje. Pokud třeba zákazník používá bezplatnou zkušební verzi na 15 dní a pak přepne na jinou bezplatnou zkušební verzi pro stejnou nabídku, nové zkušební období bude mít za měsíc použitý 15 dnů. Bezplatná zkušební verze, kterou zákazník vyhodnotil po skončení zkušebního období, je ten, který se automaticky převede na placené předplatné.

Když zákazník vybere bezplatnou zkušební verzi plánu, nepůjde převést na placené předplatné tohoto plánu, dokud zkušební období neskončí. Pokud se zákazník rozhodne převést na jiný plán v rámci nabídky, která nemá bezplatnou zkušební verzi, dojde k převodu, ale bezplatná zkušební verze skončí okamžitě a veškerá data budou ztracena.

> [!NOTE]
> Po zahájení platby pro určitý plán zákazníkem nemůžou znovu získat bezplatnou zkušební verzi stejné nabídky, a to i v případě, že přepnou na jiný plán, který podporuje bezplatné zkušební verze.

Pokud chcete získat informace o zákaznických předplatných, která se aktuálně účastní bezplatné zkušební verze, použijte novou vlastnost rozhraní API `isFreeTrial` , která bude označená jako true nebo false. Další informace najdete v tématu [rozhraní API pro SaaS získat předplatné](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak přidat nebo aktualizovat plány pro existující nabídku, najdete v tématu [aktualizace stávající nabídky na komerčním webu Marketplace](./partner-center-portal/update-existing-offer.md).
- Další informace o možnostech transakcí a přidružených cenových modelech najdete v tématu možnosti Transact-The pro [prodej na webu Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).
