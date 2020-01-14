---
title: Fakturace komerčního webu Marketplace | Azure Marketplace
description: Tento článek popisuje témata týkající se obchodu na komerčním webu Marketplace.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 0fda04bbfa632f429539ddc0ad90941cb645226b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934637"
---
# <a name="commercial-marketplace-billing"></a>Fakturace komerčního webu Marketplace 

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
- *Přineste si vlastní licenci (BYOL)* : veškeré relevantní poplatky za licence na software se spravují přímo mezi vydavatelem a zákazníkem. Microsoft předává jenom poplatky za využití infrastruktury Azure. (Jenom Virtual Machines a aplikace Azure.)
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

This option allows higher or lower pricing than the publicly available offering. Private offers can be used to discount or add a premium for an offer. Private offers can be made available to one or more customers by white listing their Azure subscription at the offer level.

### <a name="examples"></a>Příklady

#### <a name="pay-as-you-go"></a>Plaťte jenom za to, co využijete

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### <a name="bring-your-own-license-byol"></a>Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### <a name="saas-app-subscription"></a>SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.

•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.
