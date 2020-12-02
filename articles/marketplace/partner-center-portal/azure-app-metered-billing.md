---
title: Měřená fakturace pro spravované aplikace pomocí služby měření na webu Marketplace | Azure Marketplace
description: Tato dokumentace je průvodcem pro nezávislé výrobce softwaru pro publikování aplikací Azure s flexibilními fakturačními modely.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436347"
---
# <a name="managed-application-metered-billing"></a>Spravovaná fakturace podle objemu spravované aplikace 

Pomocí služby měření na webu Marketplace můžete vytvořit plány spravované aplikace pro nabídky aplikací Azure, které se účtují podle nestandardních jednotek. Před publikováním této nabídky definujete fakturační rozměry, jako je šířka pásma, lístky nebo zpracované e-maily. Zákazníci pak platí podle jejich spotřeby.  Systém bude informovat společnost Microsoft prostřednictvím rozhraní API služby měření na webu Marketplace fakturovaných událostí při jejich výskytu.

## <a name="prerequisites-for-metered-billing"></a>Předpoklady pro účtované fakturované ceny

Aby plán spravované aplikace mohl použít účtované podle objemu dat, musí:

* Seznamte se se všemi požadavky na nabídku, jak je uvedeno v části [Vytvoření nabídky aplikací Azure](../create-new-azure-apps-offer.md).
* Nakonfigurujte **ceny** pro zpoplatnění zákazníků za měsíc za službu. Cena může být nulová, pokud nechcete účtovat fixní poplatek a místo toho se spoléhá výhradně na účtované poplatky.
* Nastavte **fakturační dimenze** pro události měření, za které se zákazník za paušální sazbou platíte.
* Integrujte s [rozhraními API služby pro měření na webu Marketplace](./marketplace-metering-service-apis.md) a informujte společnost Microsoft o fakturovatelných událostech.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak se vyúčtování účtované podle objemu dokončí s cenami

Když přichází k definování nabídky společně s jejich cenovými modely, je důležité pochopit hierarchii nabídky.

* Každá nabídka aplikace Azure může mít šablonu řešení nebo plány spravované aplikace.
* Účtované měřené platby se implementují jenom s plány spravovaných aplikací.
* K jednotlivým plánům spravované aplikace je přiřazený cenový model. 
* Cenový model má měsíční opakovaný poplatek, který je možné nastavit na $0.
* Kromě opakovaného poplatku může plán také zahrnovat volitelné dimenze, které se použijí k účtování zákazníků na využití, která nejsou zahrnutá do paušální sazby. Každá dimenze představuje Fakturovatelné jednotky, které vaše služba bude komunikovat s Microsoftem pomocí [rozhraní API služby měření na webu Marketplace](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Ukázka nabídky

Například contoso je vydavatel se spravovanou aplikační službou nazvanou contoso Analytics (CoA). Certifikát CoA umožňuje zákazníkům analyzovat velké objemy dat pro generování sestav a datových skladů. Společnost Contoso je registrovaná jako vydavatel v partnerském centru, aby mohl program komerčního tržiště publikovat nabídky pro zákazníky Azure. Existují dva plány přidružené k certifikátu CoA, který je popsaný níže:

* Základní plán
    * Analyzujte 100 GB a generujte 100 sestav pro $0/měsíc.
    * Nad rámec 100 GB, platíte $10 za každých 1 GB
    * Kromě sestav 100, platíte $1 pro každou sestavu
* Plán Premium
    * Analyzovat 1000 GB a generovat 1000 sestav pro $350 za měsíc
    * Nad rámec 1000 GB, platíte $100 pro každých 1 TB
    * Kromě sestav 1000, platíte $0,5 pro každou sestavu

Zákazník Azure, který se přihlásí k odběru služby CoA, může analyzovat a generovat sestavy za měsíc na základě vybraného plánu. Společnost Contoso měří využití až do zahrnutého množství bez odesílání událostí využití společnosti Microsoft. Když zákazníci spotřebovávají více než zahrnuté množství, nemusí měnit plány ani provádět jiné akce. Společnost Contoso bude měřit překročení množství nad rámec zahrnutého množství a začít vysílat události využití do Microsoftu za účelem dalšího využití pomocí [rozhraní API služby měření na webu Marketplace](./marketplace-metering-service-apis.md). Společnost Microsoft pak bude zákazníkovi účtovat další využití, jak je určeno vydavatelem.

## <a name="billing-dimensions"></a>Fakturační dimenze

Fakturační dimenze se používají ke komunikaci se zákazníkem podle toho, jak se budou fakturovat za použití softwaru.  Tyto dimenze se také používají ke komunikaci mezi událostmi využití společnosti Microsoft. Jsou definovány takto:

* **Identifikátor dimenze**: neproměnlivý identifikátor, na který se odkazuje při generování událostí využití.
* **Název dimenze**: zobrazovaný název přidružený k dimenzi, například "odeslané textové zprávy".
* Měrná **jednotka**: Popis fakturační jednotky, například zpráva "na textovou zprávu" nebo "za 100 e-mailů".
* **Cena za jednotku**: cena za jednu jednotku dimenze.
* **Zahrnuté množství pro měsíční podmínky**: Počet zahrnutých dimenzí za měsíc pro zákazníky, kteří platí pro opakovaný měsíční poplatek, musí být celé číslo.

Fakturační dimenze se sdílejí napříč všemi plány nabídky. Některé atributy platí pro dimenzi napříč všemi plány a další atributy jsou specifické pro plán.

Atributy, které definují vlastní dimenzi, jsou sdíleny ve všech plánech nabídky. Před publikováním této nabídky ovlivní změna těchto atributů z kontextu libovolného plánu definici dimenze ve všech plánech. Po publikování této nabídky již nebude možné tyto atributy upravovat. Atributy jsou:

* Identifikátor
* Name
* Jednotka měření

Ostatní atributy dimenze jsou specifické pro každý plán a můžou mít různé hodnoty od plánu k plánování.  Před publikováním plánu můžete tyto hodnoty upravit a bude to mít vliv pouze na tento plán. Po publikování plánu už tyto atributy nebude možné upravovat. Atributy jsou:

* Cena za jednotku
* Zahrnuté množství pro měsíční zákazníky 
* Zahrnuté množství pro roční zákazníky 

Dimenze mají také dvě speciální pojmy "povolené" a "nekonečné":

* **Povoleno** znamená, že se tento plán účastní v této dimenzi.  Tuto možnost můžete nechat beze změny zrušit, pokud vytváříte nový plán, který neposílá události využití založené na této dimenzi. Také všechny nové dimenze přidané po prvním publikování plánu budou v již publikovaném plánu zobrazovat stav "Nepovoleno".  Zakázaná dimenze se nezobrazí v žádných seznamech dimenzí pro plán, který si zákazníci uvidí.
* **Nekonečno**, reprezentované symbolem nekonečno "∞", označuje, že se tento plán účastní v této dimenzi, bez měření využití na tuto dimenzi. Pokud chcete zákazníkům označovat, že funkce reprezentované touto dimenzí jsou zahrnuté v plánu, ale bez omezení využití.  Dimenze s nekonečným využitím se zobrazí v seznamech dimenzí pro plán, který si zákazníci uvidí.  Tento plán se nikdy nebude účtovat.

>[!Note] 
>Následující scénáře jsou explicitně podporovány:  <br> – Novou dimenzi můžete přidat do nového plánu.  Nová dimenze nebude povolena pro žádné z již publikovaných plánů. <br> – Plán můžete publikovat s pevným měsíčním poplatkem a bez dimenzí a pak přidat nový plán a nakonfigurovat novou dimenzi pro tento plán. Nová dimenze nebude povolena pro již publikované plány.

## <a name="constraints"></a>Omezení

### <a name="locking-behavior"></a>Chování při zamykání

Dimenze použitá v rámci služby měření na webu Marketplace představuje porozumění způsobu, jakým zákazník bude platit za službu.  Po publikování nabídky už nejsou žádné podrobnosti o dimenzi editovatelné.  Před publikováním vaší nabídky je důležité, abyste měli dimenze plně definované.

Po publikování nabídky s dimenzí již nelze změnit podrobnosti na úrovni nabídky pro tuto dimenzi:

* Identifikátor
* Name
* Jednotka měření

Po publikování plánu se podrobnosti na úrovni plánu už nedají změnit:

* Cena za jednotku
* Zahrnuté množství pro měsíční podmínky
* Zda je dimenze pro plán povolena

>[!Note]
>Měřené vyúčtování pomocí služby měření na webu Marketplace zatím není v cloudu Azure Government podporováno.

### <a name="upper-limits"></a>Horní meze

Maximální počet dimenzí, které lze konfigurovat pro jednu nabídku, je 30 jedinečných dimenzí.

## <a name="get-support"></a>Získání podpory

Pokud máte některý z následujících problémů, můžete otevřít lístek podpory.

* Technické problémy s rozhraním API služby pro měření na webu Marketplace
* Problém, který je potřeba eskalovat z důvodu chyby nebo chyby na vaší straně (např. nesprávná událost použití)
* Jakékoli další problémy související s měřením podle objemu.

Postupujte podle pokynů v [části Podpora programu komerčního tržiště v partnerském centru](../support.md) , abyste pochopili možnosti podpory vydavatele a otevřeli lístek podpory s Microsoftem.

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [rozhraní API služby měření softwaru Marketplace](./marketplace-metering-service-apis.md) .
