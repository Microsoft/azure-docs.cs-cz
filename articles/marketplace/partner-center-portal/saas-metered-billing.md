---
title: Měřená fakturace pomocí služby měření na webu Marketplace | Azure Marketplace
description: Tato dokumentace je průvodcem pro publikování nabídek SaaS nezávislým výrobcům softwaru pomocí flexibilních modelů fakturace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a244e4f7ac406fecd4c053b39a5e9a9cb6ecab2c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105499"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Měřená fakturace pomocí služby měření na webu Marketplace

Pomocí služby měření na webu Marketplace můžete v programu komerčního tržiště vytvářet nabídky typu software jako služba (SaaS), které se účtují podle nestandardních jednotek.  Před publikováním této nabídky definujete fakturační rozměry, jako je šířka pásma, lístky nebo zpracované e-maily.  Zákazníci pak můžou platit podle jejich spotřeby, a to s vaším systémem informovat Microsoft prostřednictvím rozhraní API služby měření softwaru, které umožňuje Fakturovatelné události, když k nim dojde.  

## <a name="prerequisites-for-metered-billing"></a>Předpoklady pro účtované fakturované ceny

Aby SaaS nabídka používala účtované podle objemu dat, musí:

* Doplněním všech požadavků na nabídku pro [Prodej prostřednictvím nabídky Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) , jak je uvedeno v části [Vytvoření nabídky SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integrujte s [rozhraními API pro plnění SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) , aby si zákazníci mohli zřídit a připojit se k vaší nabídce.  
* Nakonfigurujeme pro cenový model **paušálních sazeb** pro zpoplatnění zákazníků vaší služby.  Dimenze jsou volitelné rozšíření cenového modelu paušálních sazeb. 
* Integrujte s [rozhraními API služby pro měření na webu Marketplace](./marketplace-metering-service-apis.md) a informujte společnost Microsoft o fakturovatelných událostech.

>[!Note]
>Služba měření na Marketplace je dostupná jenom pro model fakturace paušálních sazeb a nevztahuje se na model fakturace na uživatele.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak se vyúčtování účtované podle objemu dokončí s cenami

Když přichází k definování nabídky společně s jejich cenovými modely, je důležité pochopit hierarchii nabídky.

* Každá nabídka SaaS je nakonfigurována k prodeji buď prostřednictvím Microsoft, nebo ne.  Po publikování nabídky nelze toto nastavení změnit.
* Každý SaaS nabídka, která je nakonfigurovaná pro prodej prostřednictvím Microsoftu, může mít jeden nebo víc plánů. Uživatel se přihlásí k odběru nabídky SaaS, ale v rámci plánu je zakoupený prostřednictvím Microsoftu.
* K jednotlivým plánům je přiřazený cenový model: **paušální sazba** nebo **na uživatele**. Všechny plány v nabídce musí být přidruženy ke stejnému cenovému modelu. Například nemůže existovat nabídka, kde jeden z jeho plánů je cenový model s paušální sazbou a další je cenový model pro uživatele.
* V rámci každého plánu nakonfigurovaného pro model fakturace paušálních sazeb se zahrnuje aspoň jeden opakovaný poplatek (který může být $0):
    * Opakovaný **měsíční** poplatek: paušální měsíční poplatek, který se předem hradí při měsíčním opakování při nákupu plánu uživatelem.
    * Periodický **roční** poplatek: paušální roční poplatek, který je předem zaplacen na roční opakování při nákupu plánu uživatelem.
* Kromě opakujících se poplatků může plán také zahrnovat volitelné dimenze, které se použijí k účtování zákazníků na využití, která nejsou zahrnutá do paušální sazby.   Každá dimenze představuje Fakturovatelné jednotky, které vaše služba bude komunikovat s Microsoftem pomocí [rozhraní API služby měření na webu Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Ukázka nabídky

Například contoso je vydavatel se službou SaaS s názvem contoso Notification Services (CNS). CNS můžou zákazníkům posílat oznámení e-mailem nebo textem. Společnost Contoso je registrovaná jako vydavatel v partnerském centru, aby mohl program komerčního tržiště publikovat nabídky pro zákazníky Azure.  K propojeným sítím jsou k dispozici dva plány:

* Základní plán
    * Odeslat 10000 e-mailů a 1000 texty pro $0/měsíc
    * Za 10000 e-mailů platíte $1 pro každých 100 e-mailů.
    * Kromě textů 1000, platíte $0,02 za každý text
* Plán Premium
    * Odeslat 50000 e-mailů a 10000 textů pro $350/měsíc
    * Za 50000 e-mailů platíte $0,5 pro každých 100 e-mailů.
    * Kromě textů 10000, platíte $0,01 za každý text

Zákazník Azure přihlašuje se k odběru služby CNS, který bude na základě vybraného plánu odesílat zahrnuté množství textu a e-mailů za měsíc.  Společnost Contoso měří využití až do zahrnutého množství bez odesílání událostí využití společnosti Microsoft.  Když zákazníci spotřebovávají více než zahrnuté množství, nemusí měnit plány ani provádět jiné akce.  Společnost Contoso bude měřit překročení množství nad rámec zahrnutého množství a začít vysílat události využití do Microsoftu za účelem dalšího využití pomocí [rozhraní API služby měření na webu Marketplace](./marketplace-metering-service-apis.md).  Společnost Microsoft pak bude zákazníkovi účtovat další využití, jak je určeno vydavatelem.

## <a name="billing-dimensions"></a>Fakturační dimenze

Fakturační dimenze se používají ke komunikaci se zákazníkem na tom, jak se budou fakturovat za použití softwaru, a také k sdělování událostí využití společnosti Microsoft. Jsou definovány takto:

* **Identifikátor dimenze**: neproměnlivý identifikátor, na který se odkazuje při generování událostí využití.
* **Název dimenze**: zobrazovaný název přidružený k dimenzi, např. "odeslané textové zprávy".
* Měrná **jednotka**: Popis fakturační jednotky, např. "na textovou zprávu" nebo "za 100 e-mailů".
* **Cena za jednotku**: cena za jednu jednotku dimenze.  
* **Zahrnuté množství pro měsíční podmínky**: Počet zahrnutých dimenzí za měsíc pro zákazníky, kteří platí pro opakovaný měsíční poplatek, musí být celé číslo.
* **Zahrnuté množství pro roční období**: Počet zahrnutých dimenzí za měsíc pro zákazníky, kteří platíte do pravidelného ročního poplatku, musí být celé číslo.

Fakturační dimenze se sdílejí napříč všemi plány nabídky.  Některé atributy platí pro dimenzi napříč všemi plány a další atributy jsou specifické pro plán.

Atributy, které definují vlastní dimenzi, jsou sdíleny ve všech plánech nabídky.  Před publikováním této nabídky ovlivní změna těchto atributů z kontextu libovolného plánu definici dimenze ve všech plánech.  Po publikování této nabídky již nebude možné tyto atributy upravovat.  Tyto atributy jsou:

* identifikátor
* Name
* Měrná jednotka

Ostatní atributy dimenze jsou specifické pro každý plán a můžou mít různé hodnoty od plánu k plánování.  Před publikováním plánu můžete tyto hodnoty upravit a bude to mít vliv pouze na tento plán.  Po publikování plánu už tyto atributy nebude možné upravovat.  Tyto atributy jsou:

* Cena za jednotku
* Zahrnuté množství pro měsíční zákazníky 
* Zahrnuté množství pro roční zákazníky 

Dimenze mají také dvě speciální pojmy "povolené" a "nekonečné":

* **Povoleno** znamená, že se tento plán účastní v této dimenzi.  Pokud vytváříte nový plán, který neodesílá události využití na základě této dimenze, je vhodné ponechat toto zrušení zaškrtnutí.  Také všechny nové dimenze přidané po prvním publikování plánu budou v již publikovaném plánu zobrazovat stav "Nepovoleno".  Zakázaná dimenze se nezobrazí v žádných seznamech dimenzí pro plán, který si zákazníci uvidí.
* Nekonečno, reprezentované symbolem nekonečno "∞", označuje, že se tento plán účastní v této dimenzi, ale nepoužívá měření na základě této dimenze.  Pokud chcete zákazníkům označovat, že funkce reprezentované touto dimenzí jsou zahrnuté v plánu, ale bez omezení využití.  Dimenze s nekonečným využitím se zobrazí v seznamech dimenzí pro plán, který uvidí zákazníci, s označením, že se za tento plán nikdy neúčtuje poplatek.

>[!Note] 
>Následující scénáře jsou explicitně podporovány: <br> – Novou dimenzi můžete přidat do nového plánu.  Nová dimenze nebude povolena pro žádné z již publikovaných plánů. <br> – Plán s paušálními **sazbami** můžete publikovat bez dimenzí a pak přidat nový plán a nakonfigurovat novou dimenzi pro tento plán. Nová dimenze nebude povolena pro již publikované plány.

## <a name="constraints"></a>Omezení

### <a name="trial-behavior"></a>Chování při zkušební verzi

Měřené fakturace pomocí služby měření na webu Marketplace není kompatibilní s nabídkou bezplatné zkušební verze.  Plán pro použití účtované podle objemu dat a bezplatné zkušební verze není možné konfigurovat.

### <a name="locking-behavior"></a>Chování při zamykání

Vzhledem k tomu, že dimenze použitá v rámci služby měření na webu Marketplace představuje porozumění způsobu, jakým bude zákazník platit za službu, po jeho publikování už nebudete moct všechny podrobnosti o dimenzi upravovat.  Před publikováním je důležité, abyste měli dimenze plně definované pro plán.
  
Po publikování nabídky s dimenzí již nelze změnit podrobnosti na úrovni nabídky pro tuto dimenzi:

* identifikátor
* Name
* Měrná jednotka

Po publikování plánu se podrobnosti na úrovni plánu už nedají změnit:

* Cena za jednotku
* Zahrnuté množství pro měsíční podmínky
* Zahrnuté množství pro roční období
* Zda je dimenze pro plán povolena

### <a name="upper-limits"></a>Horní meze

Maximální počet dimenzí, které lze konfigurovat pro jednu nabídku, je 18 jedinečných dimenzí.

## <a name="get-support"></a>Získat podporu

Pokud máte jednu z následujících možností, můžete otevřít lístek podpory.

* Technické problémy s rozhraním API služby pro měření na webu Marketplace
* Problém, který je potřeba eskalovat z důvodu chyby nebo chyby na vaší straně (např. nesprávná událost použití)
* Jakékoli další problémy související s měřením podle objemu. 

Použijte následující postup, chcete-li odeslat lístek podpory:

1. Přejít na [stránku podpory](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Prvních několik rozevíracích nabídek je pro vás automaticky vyplněné. V případě podpory na webu Marketplace Identifikujte produktovou rodinu jako **cloudové a online služby**, produkt jako **Vydavatel webu Marketplace**.  Neměňte výběry předem vyplněných rozevíracích nabídek.
2. V části vybrat verzi produktu vyberte **živá nabídka Správa**.
3. V části vyberte kategorii, která nejlépe popisuje problém, zvolte **aplikace SaaS**.
4. V části vyberte problém, který nejlépe popisuje problém, vyberte **účtované podle objemu**.
5. Když vyberete tlačítko **Další** , budete přesměrováni na stránku **Podrobnosti o problému** , kde můžete zadat další podrobnosti o problému.

Další možnosti podpory pro vydavatele najdete v tématu [Podpora programu komerčního tržiště v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) .

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [rozhraní API služby měření softwaru Marketplace](./marketplace-metering-service-apis.md) .
