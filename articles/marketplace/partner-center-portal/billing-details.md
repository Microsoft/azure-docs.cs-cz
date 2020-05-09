---
title: Faktura na komerční web Marketplace v Azure Marketplace
description: Seznamte se s možnostmi publikování na webu Marketplace a modely fakturace v jazyce Transact pro komerční tržiště.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: deb4f3f1bb17bff0b09b2e4f79ceb967d4d7ff59
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744952"
---
# <a name="commercial-marketplace-billing-with-transact-billing-models"></a>Fakturace komerčních tržišť pomocí modelů fakturace v jazyce Transact

Tento článek popisuje témata týkající se obchodu na komerčním webu Marketplace:

- [Možnosti publikování na Marketplace](#marketplace-publishing-options)
- [Základní přehled jazyka Transact](#transact-general-overview)
- [Modely fakturace v jazyce Transact](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Možnosti publikování na Marketplace

Obchodní Marketplace nabízí několik možností publikování pro vydavatele.

### <a name="list-and-trial-publishing-options"></a>Možnosti publikování seznamu a zkušební verze

Vydavatelé mohou využít možnosti publikování seznam, zkušební verze a přineste si vlastní licenci (BYOL) pro účely propagačního a nabývání uživatelů. S těmito možnostmi se společnost Microsoft neúčastní přímo v transakcích licencí na software vydavatele a není k dispozici žádný související transakční poplatek. Vydavatelé zodpovídají za podporu všech aspektů transakce softwarových licencí, mimo jiné: objednávka, plnění, měření, fakturace, fakturace, platby a shromažďování. V možnostech publikování seznamu a vyzkoušení můžou vydavatelé uchovávat 100% licenčních poplatků za software vydavatele shromážděných od zákazníka.

### <a name="transact-publishing-option"></a>Možnost publikování v režimu Transact

Kromě možností pro publikování seznamu a zkušební verze je možnost publikování v režimu Transact dostupná pro vydavatele. Tato možnost využívá výhod celosvětově dostupných možností obchodování společnosti Microsoft a umožňuje Microsoftu hostovat transakce cloudového tržiště jménem vydavatele.

## <a name="transact-general-overview"></a>Základní přehled jazyka Transact

Při použití možnosti publikování v jazyce Transact společnost Microsoft umožňuje prodej softwaru třetích stran a nasazení některých typů nabídek do předplatného Azure zákazníka. Vydavatel musí zvážit fakturaci poplatků za infrastrukturu a vlastní licenční poplatky za software vydavatele při výběru modelu fakturace a typu nabídky.

Možnost publikování v režimu Transact se teď podporuje pro následující typy nabídek: virtuální počítače, aplikace Azure a SaaS aplikace.

![Transact in Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Náklady na infrastrukturu fakturace

#### <a name="for-virtual-machines-and-azure-applications"></a>Pro virtuální počítače a aplikace Azure

U virtuálních počítačů a aplikací Azure se poplatky za využití infrastruktury Azure účtují na základě předplatného Azure zákazníka. Poplatky za využití infrastruktury se účtují a zobrazují se odděleně od licenčních poplatků poskytovatele softwaru na faktuře zákazníka.

#### <a name="for-saas-apps"></a>Pro aplikace SaaS

V případě aplikací SaaS musí vydavatel považovat za poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu položku nákladů. Je reprezentována jako paušální poplatek zákazníkovi. Využití infrastruktury Azure se spravuje a účtuje přímo partnerovi. Zákazníky nevidí skutečné poplatky za využití infrastruktury. Vydavatelé obvykle můžou využít poplatky za využití infrastruktury Azure na ceny za licence na software. Licenční poplatky za software nejsou měřené nebo vycházející z spotřeby.

## <a name="transact-billing-models"></a>Modely fakturace v jazyce Transact

V závislosti na použité možnosti transakce lze licenční poplatky za software vydavatele předložit následujícím způsobem:

- *Zdarma*: licence na software se neúčtují.
- *Přineste si vlastní licenci (BYOL)*: veškeré relevantní poplatky za licence na software se spravují přímo mezi vydavatelem a zákazníkem. Microsoft předává jenom poplatky za využití infrastruktury Azure. (Jenom Virtual Machines a aplikace Azure.)
- Průběžné *platby*: licenční poplatky za software se zobrazují jako ceny za každou hodinu, podle počtu jader (vCPU) na základě používané infrastruktury Azure. Tento model se vztahuje jenom na virtuální počítače a aplikace Azure.
- *Ceny předplatného*: licenční poplatky za software se zobrazují jako měsíční nebo roční poplatek, který se účtuje jako paušální sazba nebo na pracovní stanici. Tento model platí jenom pro aplikace SaaS a aplikace spravované aplikací Azure.
- *Bezplatná zkušební verze softwaru*: žádné poplatky za licence na software po dobu 30 dnů nebo 90 dnů.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ceny zdarma a s využitím vlastních licencí (BYOL)

Když publikujete nabídku bezplatné licence nebo přinesete vlastní licenci, Microsoft nehraje roli v usnadnění prodejní transakce za licenční poplatky za software. Podobně jako seznam a možnosti publikování zkušební verze uchovává Vydavatel 100% licenčních poplatků za software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Ceny za průběžné platby a předplatné (založené na webu)

Když publikujete nabídku transakcí s průběžnými platbami nebo předplatnými, společnost Microsoft poskytuje technologie a služby pro zpracování nákupů licencí softwaru, vrácení a vrácení se změnami. V tomto scénáři Vydavatel udělí Microsoftu, aby pro tyto účely jednal jako agent. Vydavatel umožňuje Microsoftu usnadňovat vydávání licenčních transakcí softwaru a přitom zachovává své označení jako prodávající, poskytovatel, distributor a poskytovatel licence.

Microsoft umožňuje zákazníkům objednat, licencovat a používat software vydavatele v souvislosti s podmínkami a ujednáními z komerčního tržiště společnosti Microsoft i s licenční smlouvou pro koncového uživatele vydavatele. Vydavatelé musí poskytnout licenční smlouvu s koncovým uživatelem nebo vybrat [standardní smlouvu](https://docs.microsoft.com/azure/marketplace/standard-contract) při vytváření nabídky.

### <a name="free-software-trials"></a>Bezplatné zkušební verze softwaru

V případě scénářů publikování v jazyce Transact může vydavatel licence na software zpřístupnit zdarma po dobu 30 dní nebo 90 dnů. Tato funkce diskontování nezahrnuje náklady na využití infrastruktury Azure, které je založené na používání partnerského řešení.

### <a name="private-offers"></a>Soukromé nabídky

Kromě používání typů nabídek a modelů fakturace k monetizovatí nabídky můžou vydavatelé využít soukromou nabídku, která se dokončí s vyjednáním a s konkrétními cenami nebo vlastními konfiguracemi. Soukromé nabídky jsou podporovány všemi 3 možnostmi publikování v transakcích.

Tato možnost umožňuje vyšší nebo nižší ceny než veřejně dostupná nabídka. Soukromé nabídky lze použít pro zlevnění nebo přidání prémie do nabídky. Soukromé nabídky je možné zpřístupnit jednomu nebo více zákazníkům na základě bílého seznamu předplatného Azure na úrovni nabídky.

### <a name="examples"></a>Příklady

#### <a name="pay-as-you-go"></a>Průběžné platby

* Pokud povolíte možnost platby s průběžnými platbami, budete mít následující nákladovou strukturu.

|Náklady na licenci  | $1,00 za hodinu  |
|---------|---------|
|Náklady na využití Azure (D1/1 – jádro)    |   $0,14 za hodinu     |
|*Zákazník se účtuje Microsoftem.*    |  *$1,14 za hodinu*       |

* V tomto scénáři vám Microsoft účtuje $1,14 za hodinu pro použití publikované image virtuálního počítače.

|Účty Microsoft  | $1,14 za hodinu  |
|---------|---------|
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů.|   $0,80 za hodinu     |
|Společnost Microsoft udržuje 20% vašich licenčních nákladů.  |  $0,20 za hodinu       |
|Microsoft udržuje 100% nákladů na využití Azure. | $0,14 za hodinu |

### <a name="bring-your-own-license-byol"></a>Přineste si vlastní licenci (BYOL)

* Pokud povolíte možnost BYOL, budete mít následující nákladovou strukturu.

|Náklady na licenci  | Licenční poplatek se vyjednává a účtuje vám  |
|---------|---------|
|Náklady na využití Azure (D1/1 – jádro)    |   $0,14 za hodinu     |
|*Zákazník se účtuje Microsoftem.*    |  *$0,14 za hodinu*       |

* V tomto scénáři vám Microsoft účtuje $0,14 za hodinu pro použití publikované image virtuálního počítače.

|Účty Microsoft  | $0,14 za hodinu  |
|---------|---------|
|Microsoft udržuje náklady na využití Azure    |   $0,14 za hodinu     |
|Společnost Microsoft udržuje 0% vašich licenčních nákladů.   |  $0,00 za hodinu       |

### <a name="saas-app-subscription"></a>Předplatné aplikace SaaS

Tato možnost musí být nakonfigurovaná tak, aby se dala prodávat přes Microsoft, a může se vám účtovat paušálně nebo měsíčně na jednotlivých uživatelích.

• Pokud pro nabídku SaaS povolíte možnost prodávat prostřednictvím Microsoftu, pak máte následující nákladovou strukturu.

|Náklady na licenci       | $100,00 za měsíc  |
|--------------|---------|
|Náklady na využití Azure (D1/1 – jádro)    | Účtuje se přímo vydavateli, nikoli zákazník. |
|*Zákazník se účtuje Microsoftem.*    |  *$100,00 za měsíc (Poznámka: Vydavatel musí v licenčním poplatku brát v úvahu náklady na infrastrukturu, které vznikly nebo předávaly)*  |

* V tomto scénáři Microsoft účtuje $100,00 pro vaši softwarovou licenci a vyplatí $80,00 vydavateli.
* Partneři, kteří mají nárok na snížený poplatek za službu Marketplace, uvidí za snížený poplatek za transakce SaaS z května 2019 do června 2020. V tomto scénáři Microsoft účtuje $100,00 pro vaši softwarovou licenci a vyplatí $90,00 vydavateli.

|Účty Microsoft  | $100,00 za měsíc  |
|---------|---------|
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. <br> \*Společnost Microsoft vám pro všechny kvalifikované aplikace SaaS zaplatí 90% nákladů na licenci.   |   $80,00 za měsíc <br> \*$90,00 za měsíc    |
|Společnost Microsoft udržuje 20% vašich licenčních nákladů. <br> \*Pro všechny kvalifikované aplikace SaaS udržuje společnost Microsoft 10% nákladů na licenci.  |  $20,00 za měsíc <br> \*$10,00     |

**Nižší poplatek za službu Marketplace:** U některých produktů SaaS, které publikujete na našem komerčním webu Marketplace, Microsoft sníží poplatek za službu Marketplace z 20% (jak je popsáno v tématu smlouva Microsoft Publisher Agreement) na 10%.  Aby produkt mohl být kvalifikován, musí být alespoň jeden z vašich produktů označený Microsoftem jako předprodejný nebo přizpůsobený protokol IP. Pro obdržení tohoto omezeného poplatku za služby Marketplace na tento měsíc musí být nárok splněn nejméně pět (5) pracovních dnů před koncem předchozího kalendářního měsíce. Nižší poplatek za službu Marketplace se nebude vztahovat na virtuální počítače, spravované aplikace ani žádné jiné produkty, které jsou dostupné prostřednictvím našeho komerčního tržiště.  Tento snížený poplatek za službu Marketplace bude dostupný pro kvalifikované nabídky s licenčními poplatky shromážděnými společností Microsoft mezi 1. května 2019 a 30. června 2020.  Po uplynutí této doby se poplatek za službu Marketplace vrátí do normálního množství.
