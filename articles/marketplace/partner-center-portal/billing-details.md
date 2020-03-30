---
title: Fakturace na komerčním trhu | Azure Marketplace
description: Tento článek se zabývá tématy souvisejícími s obchodem pro komerční trh.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279738"
---
# <a name="commercial-marketplace-billing"></a>Fakturace na komerčním trhu

Tento článek se zabývá tématy souvisejícími s obchodem pro komerční trh:

- [Možnosti publikování na Marketplace](#marketplace-publishing-options)
- [Transact obecný přehled](#transact-general-overview)
- [Transact fakturační modely](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Možnosti publikování na Marketplace

Komerční tržiště nabízí vydavatelům několik možností publikování.

### <a name="list-and-trial-publishing-options"></a>Možnosti publikování seznamu a zkušební verze

Vydavatelé mohou využít seznam, zkušební verzi a přinést vlastní licence (BYOL) možnosti publikování pro propagační a uživatelské účely akvizice. S těmito možnostmi se společnost Microsoft přímo neúčastní transakcí s licencemi na software vydavatele a neexistuje žádný související transakční poplatek. Vydavatelé jsou zodpovědní za podporu všech aspektů transakce s licencí na software, mimo jiné včetně: objednávky, plnění, měření, fakturace, fakturace, platby a inkasa. Díky možnostem seznamu a zkušebního publikování si vydavatelé ponechají 100 % licenčních poplatků za software vydavatelů vybraných od zákazníka.

### <a name="transact-publishing-option"></a>Možnost publikování transact

Kromě možností seznamu a zkušebního publikování je možnost publikování transact k dispozici vydavatelům. Tato možnost využívá globálně dostupné možnosti obchodování společnosti Microsoft a umožňuje společnosti Microsoft hostovat transakce na trhu cloudu jménem vydavatele.

## <a name="transact-general-overview"></a>Transact obecný přehled

Při použití možnosti publikování transact, Microsoft umožňuje prodej softwaru třetích stran a nasazení některých typů nabídek pro zákazníka předplatného Azure. Při výběru fakturačního modelu a typu nabídky musí vydavatel zvážit účtování poplatků za infrastrukturu a vlastní licenční poplatky za software.

Možnost publikování Transact je aktuálně podporovaná pro následující typy nabídek: virtuální počítače, aplikace Azure a aplikace SaaS.

![Transakt na Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Náklady na fakturační infrastrukturu

#### <a name="for-virtual-machines-and-azure-applications"></a>Pro virtuální počítače a aplikace Azure

U virtuálních počítačů a aplikací Azure se poplatky za využití infrastruktury Azure účtují podle předplatného Azure zákazníka. Poplatky za využití infrastruktury jsou oceněny a prezentovány odděleně od licenčních poplatků poskytovatele softwaru na faktuře zákazníka.

#### <a name="for-saas-apps"></a>Pro aplikace SaaS

U aplikací SaaS musí vydavatel účtovat poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu nákladovou položku. Je pro zákazníka zastoupena jako paušální poplatek. Využití infrastruktury Azure se spravuje a účtuje přímo partnerovi. Skutečné poplatky za využití infrastruktury nejsou zákazníkem vidět. Vydavatelé se obvykle rozhodnou složit poplatky za využití infrastruktury Azure do cen softwarových licencí. Licenční poplatky za software nejsou měřeny nebo na základě spotřeby.

## <a name="transact-billing-models"></a>Transact fakturační modely

V závislosti na použité možnosti transakce mohou být licenční poplatky vydavatele na základě softwarových licencí prezentovány takto:

- *Zdarma*: Bez poplatku za softwarové licence.
- *Přineste si vlastní licenci (BYOL):* Veškeré příslušné poplatky za softwarové licence jsou spravovány přímo mezi vydavatelem a zákazníkem. Microsoft projíždí jenom poplatky za využití infrastruktury Azure. (Pouze virtuální počítače a aplikace Azure.)
- *Platby za každou nařadu*: Licenční poplatky za software se zobrazí jako cenová sazba za hodinu a za jádro (vCPU) na základě použité infrastruktury Azure. Tento model platí jenom pro virtuální počítače a aplikace Azure.
- *Ceny předplatného*: Licenční poplatky za software jsou prezentovány jako měsíční nebo roční opakující se poplatek účtovaný jako paušální sazba nebo za sedadlo. Tento model se vztahuje jenom pro aplikace SaaS a aplikace Azure – spravované aplikace.
- *Zkušební verze bezplatného softwaru*: Za softwarové licence se neúčtuje poplatek po dobu 30 dnů nebo 90 dnů.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ceny zdarma a přineste si vlastní licenci (BYOL)

Při publikování nabídky transakcí zdarma nebo přineste vlastní licenci, společnost Microsoft nehraje roli při usnadňování prodejní transakce pro licenční poplatky za software. Stejně jako seznam a možnosti publikování zkušební verze, vydavatel udržuje 100% licenčních poplatků za software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Ceny průběžných plateb a předplatného (na webu)

Při publikování nabídky transakcí s průběžnými platbami nebo předplatného poskytuje společnost Microsoft technologii a služby pro zpracování nákupů softwarových licencí, vrácení a vrácení poplatků. V tomto scénáři vydavatel opravňuje společnost Microsoft jednat jako agent pro tyto účely. Vydavatel umožňuje společnosti Microsoft usnadnit transakci licencování softwaru a zároveň zachovat jejich označení jako prodejce, poskytovatele, distributora a poskytovatele licence.

Společnost Microsoft umožňuje zákazníkům objednávat, licencovat a používat software vydavatele v souladu s podmínkami komerčního webu Marketplace společnosti Microsoft i licenční smlouvy s koncovým uživatelem vydavatele. Vydavatelé musí při vytváření nabídky poskytnout licenční smlouvu s koncovým uživatelem nebo vybrat [standardní smlouvu.](https://docs.microsoft.com/azure/marketplace/standard-contract)

### <a name="free-software-trials"></a>Bezplatné softwarové zkušební verze

Pro scénáře publikování na transakt může vydavatel zpřístupnit softwarovou licenci zdarma po dobu 30 dnů nebo 90 dnů. Tato možnost slevy nezahrnuje náklady na využití infrastruktury Azure, která je řízena pomocí partnerského řešení.

### <a name="private-offers"></a>Soukromé nabídky

Kromě použití typů nabídek a fakturačních modelů ke zpeněžení nabídky mohou majitelé stránek provést soukromou nabídku, doplněnou o vyjednávané ceny a ceny specifické pro dohodu nebo vlastní konfigurace. Soukromé nabídky jsou podporovány všemi 3 možnostmi publikování transact.

Tato možnost umožňuje vyšší nebo nižší ceny než veřejně dostupná nabídka. Soukromé nabídky mohou být použity ke slevě nebo přidání prémie pro nabídku. Soukromé nabídky lze zpřístupnit jednomu nebo více zákazníkům pomocí bílého výpisu jejich předplatného Azure na úrovni nabídky.

### <a name="examples"></a>Příklady

#### <a name="pay-as-you-go"></a>Průběžné platby

* Pokud povolíte možnost průběžných plateb, máte následující strukturu nákladů.

|Náklady na licenci  | $1.00 za hodinu  |
|---------|---------|
|Náklady na využití Azure (D1/1-Core)    |   $0.14 za hodinu     |
|*Odběrateli se účtuje společnost Microsoft.*    |  *$1.14 za hodinu*       |

* V tomto scénáři Microsoft účtuje $1.14 za hodinu pro použití publikované image virtuálního počítače.

|Účty společnosti Microsoft  | $1.14 za hodinu  |
|---------|---------|
|Microsoft vám zaplatí 80 % nákladů na licenci|   $0.80 za hodinu     |
|Společnost Microsoft udržuje 20 % nákladů na licenci  |  $0.20 za hodinu       |
|Microsoft si ponechá 100 % nákladů na využití Azure | $0.14 za hodinu |

### <a name="bring-your-own-license-byol"></a>Přineste si vlastní licenci (BYOL)

* Pokud povolíte možnost BYOL, pak máte následující strukturu nákladů.

|Náklady na licenci  | Licenční poplatek, který jste vyjednali a naúčtovali  |
|---------|---------|
|Náklady na využití Azure (D1/1-Core)    |   $0.14 za hodinu     |
|*Odběrateli se účtuje společnost Microsoft.*    |  *$0.14 za hodinu*       |

* V tomto scénáři Microsoft účtuje $0.14 za hodinu pro použití publikované image virtuálního počítače.

|Účty společnosti Microsoft  | $0.14 za hodinu  |
|---------|---------|
|Microsoft udržuje náklady na využití Azure    |   $0.14 za hodinu     |
|Společnost Microsoft si ponechá 0 % nákladů na licenci   |  $0.00 za hodinu       |

### <a name="saas-app-subscription"></a>Předplatné aplikace SaaS

Tato možnost musí být nakonfigurována tak, aby se prodávala prostřednictvím společnosti Microsoft, a může být oceněna paušální sazbou nebo na uživatele měsíčně nebo ročně.

• Pokud pro nabídku SaaS povolíte možnost Prodat prostřednictvím microsoftu, máte následující strukturu nákladů.

|Náklady na licenci       | $100.00 za měsíc  |
|--------------|---------|
|Náklady na využití Azure (D1/1-Core)    | Fakturováno přímo vydavateli, nikoli zákazníkovi |
|*Odběrateli se účtuje společnost Microsoft.*    |  *100,00 USD měsíčně (poznámka: Vydavatel musí v licenčním poplatku uvodit veškeré vzniklé nebo předávací náklady na infrastrukturu).*  |

* V tomto scénáři společnost Microsoft účtuje 100,00 USD za licenci softwaru a vyplatí vydavateli 80,00 USD.
* Partneři, kteří se kvalifikovali pro sníženou službu Marketplace, uvidí snížený transakční poplatek u nabídek SaaS od května 2019 do června 2020. V tomto scénáři společnost Microsoft účtuje 100,00 USD za licenci softwaru a vyplatí vydavateli 90,00 USD.

|Účty společnosti Microsoft  | $100.00 za měsíc  |
|---------|---------|
|Microsoft vám zaplatí 80 % nákladů na licenci <br> \*Společnost Microsoft vám zaplatí 90 % nákladů na licenci za všechny kvalifikované aplikace SaaS   |   $80.00 za měsíc <br> \*$90.00 za měsíc    |
|Společnost Microsoft udržuje 20 % nákladů na licenci <br> \*Společnost Microsoft uchovává 10 % nákladů na licenci pro všechny kvalifikované aplikace SaaS.  |  $20.00 za měsíc <br> \*10,00 dolarů     |

**Snížený poplatek za službu Marketplace:** U některých produktů SaaS, které publikujete na našem komerčním tržišti, sníží společnost Microsoft poplatek za službu Marketplace z 20 % (jak je popsáno ve smlouvě s vydavatelem společnosti Microsoft) na 10 %.  Aby se váš produkt kvalifikoval, musí být alespoň jeden z vašich produktů společností Microsoft označen jako připravený pro co-sell IP nebo s prioritou prodávat ip adresy. Chcete-li získat tento snížený poplatek za službu Marketplace za měsíc, musí být způsobilost splněna nejméně pět (5) pracovních dnů před koncem předchozího kalendářního měsíce. Snížený poplatek za službu Marketplace se nebude vztahovat na virtuální společnosti, spravované aplikace ani na jiné produkty zpřístupněné prostřednictvím našeho komerčního tržiště.  Tento snížený poplatek za službu Marketplace bude k dispozici kvalifikovaným nabídkám s licenčními poplatky vybranými společností Microsoft v období od 1.  Po uplynutí této doby se poplatek za službu Marketplace vrátí na svou obvyklou částku.
