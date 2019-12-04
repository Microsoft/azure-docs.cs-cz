---
title: Možnosti komerční transakce Marketplace a požadavky | Azure
description: Tento článek popisuje cenové, fakturační, fakturační a výběrové požadavky pro typ nabídky.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: 5219ff94b29d4fd02edb8567ae59884ced07b37e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791351"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Možnosti a požadavky transakce komerčního tržiště

Tento článek se zabývá následujícími tématy týkajícími se obchodování na komerčním webu Marketplace.

* Možnosti publikování na Marketplace
* Základní přehled jazyka Transact
* Modely fakturace v jazyce Transact
* Požadavky Transact

## <a name="marketplace-publishing-options"></a>Možnosti publikování na Marketplace

Pro komerční vydavatele na webu Marketplace jsou k dispozici následující možnosti publikování.

### <a name="list--trial-publishing-options"></a>Seznam možností publikování & zkušební verze

Vydavatelé mohou využít možnosti publikování seznamu, zkušební verze a BYOL pro účely propagačního a nabývání uživatelů. S těmito možnostmi se společnost Microsoft neúčastní přímo v transakcích licencí na software vydavatele a není k dispozici žádný související transakční poplatek. Vydavatelé zodpovídají za podporu všech aspektů transakce softwarových licencí, mimo jiné: objednávka, plnění, měření, fakturace, fakturace, platby a shromažďování. V možnostech publikování seznamu a vyzkoušení můžou vydavatelé uchovávat 100% licenčních poplatků za software vydavatele shromážděných od zákazníka. 

### <a name="transact-publishing-option"></a>Možnost publikování v režimu Transact

Kromě možností pro publikování seznamu a zkušební verze je možnost publikování v režimu Transact dostupná pro vydavatele. To využívá výhod celosvětově dostupných možností obchodování společnosti Microsoft a umožňuje Microsoftu hostovat transakce cloudového tržiště jménem vydavatele.

## <a name="transact-general-overview"></a>Základní přehled jazyka Transact

Při použití možnosti publikování v jazyce Transact společnost Microsoft umožňuje prodej softwaru třetích stran a nasazení některých typů nabídek do předplatného Azure zákazníka. Vydavatel musí zvážit fakturaci poplatků za infrastrukturu a vlastní licenční poplatky za software vydavatele při výběru modelu fakturace a typu nabídky.

Možnost publikování v režimu Transact se teď podporuje pro následující typy nabídek: Virtual Machines, aplikace Azure a aplikace SaaS.


![[Transakce v podniku v Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Náklady na infrastrukturu fakturace

**Pro Virtual Machines a aplikace Azure**

V případě aplikací Virtual Machines a Azure se poplatky za využití infrastruktury Azure účtují na základě předplatného Azure zákazníka.  Poplatky za využití infrastruktury se účtují a zobrazují se odděleně od licenčních poplatků poskytovatele softwaru na faktuře zákazníka.

**Pro aplikace SaaS**

V případě aplikací SaaS musí vydavatel považovat za poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu položku nákladů.  Je reprezentována jako paušální poplatek zákazníkovi. Využití infrastruktury Azure se spravuje a účtuje přímo partnerovi.  Zákazníky nevidí skutečné poplatky za využití infrastruktury.  Vydavatelé obvykle můžou využít poplatky za využití infrastruktury Azure na ceny za licence na software.  Licenční poplatky za software nejsou měřené nebo vycházející z spotřeby.

## <a name="transact-billing-models"></a>Modely fakturace v jazyce Transact

V závislosti na použité možnosti transakce lze licenční poplatky za software vydavatele předložit následujícím způsobem:  

* Zdarma: licence na software se neúčtují. 

* Přineste si vlastní licenci (BYOL): veškeré relevantní poplatky za licence na software se spravují přímo mezi vydavatelem a zákazníkem. Microsoft předává jenom poplatky za využití infrastruktury Azure. (Jenom Virtual Machines a aplikace Azure.)

* Průběžné platby: licenční poplatky za software se zobrazují jako ceny za každou hodinu, podle počtu jader (vCPU) na základě používané infrastruktury Azure. To platí jenom pro Virtual Machines a aplikace Azure.

* Ceny předplatného: licenční poplatky za software se zobrazují jako měsíční nebo roční poplatek, který se účtuje jako paušální sazba nebo na pracovní stanici. To platí jenom pro aplikace SaaS a aplikace spravované aplikací Azure.

* Bezplatná zkušební verze softwaru: žádné poplatky za licence na software po dobu 30 dnů nebo 90 dnů.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ceny zdarma a s využitím vlastních licencí (BYOL)

Když publikujete nabídku bezplatné licence nebo přinesete vlastní licenci, Microsoft nehraje roli v usnadnění prodejní transakce za licenční poplatky za software. Podobně jako seznam a možnosti publikování zkušební verze uchovává Vydavatel 100% licenčních poplatků za software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Ceny za průběžné platby a předplatné (založené na webu)

Při publikování nabídky s průběžnými platbami nebo předplatnými poskytuje společnost Microsoft technologii a služby pro zpracování nákupů licencí softwaru, vrácení a vratek. V tomto scénáři Vydavatel udělí Microsoftu, aby pro tyto účely jednal jako agent. Vydavatel umožňuje Microsoftu usnadňovat vydávání licenčních transakcí softwaru a přitom zachovává své označení jako prodávající, poskytovatel, distributor a poskytovatel licence.

Microsoft umožňuje zákazníkům objednat, licencovat a používat software vydavatele v souvislosti s podmínkami a ujednáními z komerčního tržiště společnosti Microsoft i s licenční smlouvou pro koncového uživatele vydavatele. Vydavatelé musí poskytnout licenční smlouvu s koncovým uživatelem nebo vybrat [standardní kontrakt](https://docs.microsoft.com/azure/marketplace/standard-contract) při vytváření nabídky.


### <a name="free-software-trials"></a>Bezplatné zkušební verze softwaru

V případě scénářů publikování v jazyce Transact může vydavatel licence na software zpřístupnit zdarma po dobu 30 dní nebo 90 dnů. Tato funkce diskontování nezahrnuje náklady na využití infrastruktury Azure, které je založené na používání partnerského řešení.

### <a name="private-offers"></a>Soukromé nabídky

Kromě používání typů nabídek a modelů fakturace pro monetizovatí nabídky můžou vydavatelé využít soukromou nabídku, která se dokončí s vyjednáním, cenami specifickými pro konkrétní účely a vlastními konfiguracemi. Soukromé nabídky jsou podporovány všemi 3 možnostmi publikování v transakcích.

Tato možnost umožňuje vyšší nebo nižší ceny než veřejně dostupná nabídka. Soukromé nabídky lze použít k zlevnění nebo přidání prémie do nabídky. Soukromé nabídky je možné zpřístupnit jednomu nebo více zákazníkům na základě bílého seznamu předplatného Azure na úrovni nabídky.


### <a name="examples"></a>Příklady

**Průběžné platby** 

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

**Přineste si vlastní licenci (BYOL)**

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

**Předplatné aplikace SaaS**

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
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. <br> \* Microsoft vám pro všechny kvalifikované aplikace SaaS zaplatí 90% nákladů na licenci.   |   $80,00 za měsíc <br> \* $90,00 za měsíc    |
|Společnost Microsoft udržuje 20% vašich licenčních nákladů. <br> \* Microsoftu udržuje 10% nákladů na licenci pro všechny kvalifikované aplikace SaaS.  |  $20,00 za měsíc <br> \* $10,00     |

* **Nižší poplatek za službu Marketplace:** U některých produktů SaaS, které publikujete na našem komerčním webu Marketplace, Microsoft sníží poplatek za službu Marketplace z 20% (jak je popsáno v tématu smlouva Microsoft Publisher Agreement) na 10%.  Aby produkt mohl být kvalifikován, musí být alespoň jeden z vašich produktů označený Microsoftem jako předprodejný nebo přizpůsobený protokol IP. Pro obdržení tohoto omezeného poplatku za služby Marketplace na tento měsíc musí být nárok splněn nejméně pět (5) pracovních dnů před koncem předchozího kalendářního měsíce. Nižší poplatek za službu Marketplace se nebude vztahovat na virtuální počítače, spravované aplikace ani žádné jiné produkty, které jsou dostupné prostřednictvím našeho komerčního tržiště.  Tento snížený poplatek za službu Marketplace bude dostupný pro kvalifikované nabídky s licenčními poplatky shromážděnými společností Microsoft mezi 1. května 2019 a 30. června 2020.  Po uplynutí této doby se poplatek za službu Marketplace vrátí do normálního množství.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fakturace, platby, fakturace a kolekce zákazníků

**Fakturace a platba**

Publisher může použít upřednostňovanou způsob fakturace zákazníka k doručování licenčních poplatků za předplatné nebo PAYGO softwaru.

**Smlouva Enterprise** 

Pokud je upřednostňovanou fakturační metodou zákazníka smlouva Enterprise, poplatky za licence na software budou účtovány pomocí této metody fakturace jako účtované náklady, oddělené od jakýchkoli nákladů na používání specifické pro Azure.

**Kreditní karty a měsíční faktury** 

Zákazníci můžou také platit pomocí platební karty a faktury za měsíc. V takovém případě se vaše licenční poplatky za software účtují stejně jako smlouva Enterprise scénář, jako náklady na použití, oddělené od jakýchkoli nákladů na používání specifické pro Azure.

Například pokud zákazník koupí pomocí platební karty:

|Popis    |    Datum  |
|----------|----------|
|Období objednávky   | 15. srpna 2018 – srpna 30, 2018 |
|Termín ukončení (měsíc)   | 30. srpna 2018 |
|Datum fakturace | Září 1, 2018 |
|Datum platby zákazníka | Září 1, 2018 |
|V úschově období (jenom kreditní karty, 30 dní) | Září 1, 2018 – září 30, 2018 |
|Začátek období shromažďování | Září 1, 2018 |
|Konec období shromažďování (maximum, 30 dní) | Září 30, 2018 |
|Výběr data výpočtu (měsíčně na 15.) | Říjen 1, 2018 |
|Datum vyvýběru | 15. října 2018 |

Pokud se zákazník koupí pomocí smlouva Enterprise:

| Popis |    Datum  |
|----------|----------|
|Období objednávky | 15. srpna 2018 – srpna 30, 2018 |
|Termín ukončení (čtvrtletí) | Září 30, 2018 |
|Datum fakturace | 15. října 2018 |
|V úschově období (jenom kreditní karty, 30 dní) | – |
|Začátek období shromažďování | 15. října 2018 |
|Konec období shromažďování (maximum, 90 dní) | 15. ledna 2019 |
|Datum platby zákazníka | 30. prosince 2018 |
|Výběr data výpočtu (měsíčně na 15.) | 15. ledna 2019 |
|Datum vyvýběru | 15. února 2019 |

**Bezplatné kredity a peněžní závazky** 

Někteří zákazníci si chtějí zaplatit za Azure za použití peněžního závazku v smlouva Enterprise nebo získali bezplatné kredity pro použití s Azure. I když se tyto kredity dají použít k placení za využití Azure, nedají se za licenční poplatky za software vydavatele použít.

**Fakturace a kolekce** 

Faktura za licence na software vydavatele je k dispozici s použitím metody fakturace vybrané zákazníkem a postupovat podle časové osy fakturace. Zákazníkům, kteří nemají smlouva Enterprise v místě, se účtují měsíčně pro licence na tržiště pro software Marketplace. Zákazníci s smlouva Enterprise se účtují měsíčně prostřednictvím faktury, která se prezentuje čtvrtletně.

Po výběru předplatného nebo cenového modelu s průběžnými platbami se společnost Microsoft chová jako agent vydavatele a zodpovídá za všechny aspekty fakturace, platby a kolekce.

### <a name="publisher-payout-and-reporting"></a>Výběr vydavatele a vytváření sestav

* Jakékoli licenční poplatky za software shromážděné Microsoftem jako agent podléhají 20% za transakce, pokud není uvedeno jinak a jsou v době určení vydavatele odečteny.

* Zákazníci se obvykle kupují pomocí smlouva Enterprise nebo smlouvy s povolenými průběžnými platbami podle kreditní karty. Typ smlouvy určuje načasování, fakturaci, shromažďování a časování výběr.

>[!NOTE] 
>Všechny sestavy a přehledy pro možnost publikování v režimu Transact jsou k dispozici prostřednictvím oddílu Insights v části portál partnerů cloudu nebo analýza v partnerském centru.

#### <a name="billing-questions-and-support"></a>Otázky a podpora fakturace

Další informace a právní zásady najdete v tématu [smlouva vydavatele](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (k dispozici v portál partnerů cloudu).

Pokud potřebujete pomoc s fakturací na otázky, kontaktujte prosím [podporu komerčního vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Požadavky Transact

V této části jsou pokryty požadavky Transact pro různé typy nabídek.

### <a name="requirements-for-all-offer-types"></a>Požadavky pro všechny typy nabídek

- Pro možnost publikování v režimu Transact se vyžadují účet Microsoft a finanční informace, bez ohledu na cenový model nabídky.
- Povinné finanční informace zahrnují účet výběr a daňový profil.

Další informace o nastavení těchto účtů najdete v tématu [Správa účtu partnerského centra](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Požadavky na konkrétní typy nabídek

Možnost publikování v režimu Transact je k dispozici pouze pro následující typy nabídek Marketplace: 

**Virtuální počítač** 

Vyberte si z nabídky bezplatné, vlastní licence nebo průběžné platby a předveďte jako skladové jednotky definované na úrovni nabídky. V zákaznických fakturách od zákazníka společnost Microsoft prezentuje licenční poplatky za software vydavatele odděleně od poplatků za infrastrukturu Azure. Poplatky za infrastrukturu Azure jsou založené na používání softwaru vydavatele.

**Aplikace Azure: Šablona řešení nebo spravovaná aplikace** 

Musí zřídit jeden nebo víc virtuálních počítačů a vyžádat si součet cen virtuálních počítačů. U spravovaných aplikací s jedním plánem je možné jako cenový model vybrat ceny za virtuální počítače paušální měsíční předplatné. V některých případech jsou poplatky za využití infrastruktury Azure předávány zákazníkovi odděleně od licenčních poplatků za software, ale na stejném výpisu fakturace. Pokud však nakonfigurujete nabídku spravované aplikace pro poplatky za infrastrukturu ISV, prostředky Azure se budou fakturovat vydavateli a zákazník obdrží paušální poplatek, který zahrnuje náklady na infrastrukturu, licence na software a služby správy.

## <a name="next-steps"></a>Další kroky

* Pokud chcete dokončit výběr a konfiguraci vaší nabídky, přečtěte si požadavky na způsobilost v části Možnosti publikování podle typu nabídky.
* Prohlédněte si vzory publikování podle prezentace, kde najdete příklady, jak vaše řešení mapuje typ a konfiguraci nabídky.
