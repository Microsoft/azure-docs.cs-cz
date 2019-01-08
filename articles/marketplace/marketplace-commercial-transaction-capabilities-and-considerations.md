---
title: Možnosti Marketplace obchodní transakce a důležité faktory | Azure
description: Tento článek popisuje Transact cenách, fakturaci, fakturace a výběr důležité informace pro typ nabídky.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: 867af6de05d1d9ab54cf8a4bf02221acc43a2b28
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075630"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Azure Marketplace obchodní transakce možnosti a důležité faktory

Možnosti publikování Azure Marketplace nabízí jedinečné způsoby připojení poskytovatelů softwaru a služeb v cloudu se zákazníky. Tento článek obsahuje následující témata související s obchodování na webu Azure Marketplace:

* Možnosti publikování na webu Marketplace
* Příkaz Transact obecný přehled
* Příkaz Transact modely fakturace
* Příkaz Transact požadavky

## <a name="marketplace-publishing-options"></a>Možnosti publikování na webu Marketplace

Následující možnosti publikování jsou k dispozici pro vydavatele Azure Marketplace.

### <a name="list--trial-publishing-options"></a>Seznam & možnostmi pořízení zkušební verze publikování

Na webu Azure Marketplace, můžete využít vydavatelé seznamu a publikování možnostmi pořízení zkušební verze pro propagační účely získání uživatele a. Seznam nebo publikování možnostmi pořízení zkušební verze Microsoft není součástí přímo vydavatele softwaru licence transakce a se neúčtují žádné poplatky přidružené transakce. Vydavatelé nesou odpovědnost za všechny aspekty transakce licence softwaru, včetně, ale nikoli výhradně podporu: pořadí, splnění, měření, fakturace, fakturaci, platby a kolekce. Seznam a publikování možnostmi pořízení zkušební verze vydavatel nechejte si 100 % vydavatele licenční poplatky za software shromážděných od zákazníka. 

### <a name="transact-publishing-option"></a>Příkaz Transact možnost publikování

Kromě seznamu a možnostmi pořízení zkušební verze publikování možnost publikování transact je k dispozici pro vydavatele Azure Marketplace.   Využívá možnosti globálně dostupné obchodování od Microsoftu. Tato možnost umožňuje společnosti Microsoft na hostitele cloudové Tržiště transakce jménem vydavatele.

## <a name="transact-general-overview"></a>Příkaz Transact obecný přehled

Při použití možnosti publikování transact díky microsoftu Prodej a nasazení softwaru třetích stran s předplatným Azure zákazníka. Vydavatel nutné vzít v úvahu tak účtování poplatků za infrastrukturu Azure a vydavatele vlastní licenční poplatky za software, při výběru fakturace modelu a typ v Azure Marketplace nabídky. 

Možnost Transact publikování na webu Azure Marketplace aktuálně podporuje pro následující typy nabídek: Virtuální počítače, aplikace Azure nebo aplikace SaaS.

![[Objednávkám organizace se zabývá na webu Azure Marketplace.]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Fakturační náklady na infrastrukturu

**Pro virtuální počítače a aplikace Azure**

Pro virtuální počítače a aplikace Azure se účtují poplatky za využití infrastruktury Azure s předplatným Azure zákazníka.  Poplatky za využití infrastruktury jsou ceny a předkládán samostatně z licenční poplatky za software poskytovatele na faktuře zákazníka.

**Pro aplikace SaaS**

Pro aplikace SaaS vydavatel musí odpovídat poplatky za využití infrastruktury Azure a licenční poplatky za software jako náklady na jednu položku.  Je datum vyjádřeno jako fixní měsíční poplatek, abyste zákazníka. Využití infrastruktury Azure je spravovaná a účtuje se na partnera přímo.  Poplatky za využití infrastruktury skutečný nevidí zákazníka.  Vydavatelé se obvykle optimalizované poplatky za využití infrastruktury Azure začlenit do svých ceny licencí softwaru.  Nejsou měřené licenční poplatky za software nebo na základě spotřeby.

## <a name="transact-billing-models"></a>Příkaz Transact modely fakturace

V závislosti na možnosti transakce, používají lze takto zobrazit licenční poplatky za software vydavatele:  

* Zadarmo: Žádné poplatky za licence k softwaru. 

* Používání vlastní licence (BYOL): Všechny příslušné poplatky za licence na software se spravují přímo mezi vydavatele a odběratele. Microsoft prochází pouze poplatky za využití infrastruktury Azure. (Virtuální počítače a jenom aplikace Azure.)

* Průběžné platby: Licenční poplatky za software se zobrazují jako za každou hodinu, na jádro (vCPU) ceny sazba založená na infrastruktuře Azure používat. To platí jenom pro virtuální počítače a aplikace Azure.

* Předplatné ceny (Správa): Licenční poplatky za software se zobrazují jako měsíční, opakované poplatek.  Platí jen pro aplikace SaaS a aplikací Azure – spravované aplikace.

* Bezplatná zkušební verze softwaru: Žádné poplatky za licence na software pro 30 dnů nebo 90 dnů.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Zdarma a přenést svůj – používání vlastní licence (BYOL) ceny

Při publikování nabídky transakcí zdarma nebo přeneste svůj – používání vlastní licence, Microsoft nehraje roli v rámci usnadnění prodejní transakce pro váš licenční poplatky za software. Jako seznam a publikování možnostmi pořízení zkušební verze vydavatel udržuje 100 % licenční poplatky za software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>S průběžnými platbami a (Správa) ceník předplatného

Při publikování na transakci nabídku s průběžnými platbami nebo předplatného, společnost Microsoft poskytuje technologií a služeb na proces softwarových licencí nákupů, refundací a vratek. V tomto scénáři vydavatele opravňuje společnost Microsoft tak, aby fungoval jako agent pro tyto účely. Vydavatel umožňuje společnosti Microsoft pro usnadnění transakci, při zachování jejich označení jako prodejce, poskytovatele, distributor a vystavování licencí pro licencování softwaru.

Microsoft umožňuje zákazníkům objednávky, licence a používat vydavatele softwaru, aby podmínky a ujednání Azure Marketplace a tohoto vydavatele licenční smlouva s koncovým (viz portál partnerů cloudu). Vydavatelé musí poskytnout jejich licenční smlouva s koncovým uživatelem v nabídce marketplace.

Objednávky zpracovává prostřednictvím webu marketplace účtují s předplatným Azure zákazníka v faktury, fakturace stejným způsobem jako náklady na infrastrukturu Azure zákazníka. Zákazníci mohou využívat upřednostňované fakturace metoda a platební nástroj používaného pro fakturaci své předplatné Azure.

### <a name="free-software-trials"></a>Zkušební verze softwaru

Pro transact scénáře publikování, vydavatele můžete provádět softwarové licence k dispozici zdarma v po dobu 30 dnů nebo 90 dnů. Tato funkce užívána nezahrnuje náklady na infrastrukturu Azure využití, který řídíte pomocí partnerského řešení.

### <a name="private-offers"></a>Soukromé nabídky

Kromě použití nabízí typy a modelem fakturace vázaným na finanční zhodnocení nabídku, vydavatele dokáže provádět transakční privátní verzi nabídky řešení s cenami vyjednávaný, konkrétní zakázky a vlastní konfigurace s použitím vlastní image. Soukromé nabídky podporují všechny možnosti publikování transact 3.

Tento cenový tarif může být vyšší nebo nižší než veřejně zobrazené ceny.  Soukromé nabídky je možné slevy nebo přidat na úrovni premium pro nabídky. Soukromé nabídky může být zpřístupní jeden nebo více zákazníkům prázdné zobrazení svého předplatného Azure na úrovni nabídky.

### <a name="examples"></a>Příklady

**Průběžné platby** 

* Pokud povolíte možnost s průběžnými platbami, mají následující strukturu nákladů.

|Vaše náklady na licence  | 1,00 $ za hodinu  |
|---------|---------|
|Náklady na využití Azure (D1 / 1jader)    |   0.14 za hodinu     |
|*Microsoft účtuje*    |  *1.14 $ za hodinu*       |

* V tomto scénáři účtuje Microsoft 1.14 $ za hodinu za použití publikované image virtuálního počítače.

|Microsoft účtuje  | 1.14 $ za hodinu  |
|---------|---------|
|Společnost Microsoft platí, 80 % vaše náklady na licence|   0,80 $ za hodinu     |
|Microsoft si ponechá 20 % vaše náklady na licence  |  0.20 za hodinu       |
|Microsoft si ponechá 100 % nákladů na využití Azure | 0.14 za hodinu |

**Používání vlastní licence (BYOL)**

* Pokud povolíte možnost BYOL, mají následující strukturu nákladů.

|Vaše náklady na licence  | Licenční poplatek za vyjednávat a účtuje se podle vás  |
|---------|---------|
|Náklady na využití Azure (D1 / 1jader)    |   0.14 za hodinu     |
|*Microsoft účtuje*    |  *0.14 za hodinu*       |

* V tomto scénáři účtuje Microsoft 0.14 $ za hodinu za použití publikované image virtuálního počítače.

|Microsoft účtuje  | 0.14 za hodinu  |
|---------|---------|
|Microsoft si ponechá náklady na využití Azure    |   0.14 za hodinu     |
|Microsoft udržuje vaše náklady na licence % 0   |  0,00 USD za hodinu       |

**Aplikace SaaS předplatného (zákazník prostřednictvím Azure)**

Tato možnost musí být nakonfigurovaný na jejím prostřednictvím – Microsoft a můžete stanoví pomocí jednoho nebo více paušální měsíční plány definované na úrovni nabídky.

* Pokud povolíte zákazník pomocí Azure, máte následující strukturu nákladů.

|Vaše náklady na licence       | 100,00 USD za měsíc  |
|--------------|---------|
|Náklady na využití Azure (D1 / 1jader)    | Účtuje se přímo k vydavateli, nejste zákazník |
|*Microsoft účtuje*    |  *100,00 USD za měsíc (Poznámka: vydavatel musí odpovídat žádné náklady na infrastrukturu vzniklých nebo průchozí v licenční poplatek za)*  |

* V tomto scénáři Microsoft účtuje 100,00 USD pro licence na software a zaplatí si $80,00 k vydavateli.

|Microsoft účtuje  | 100,00 USD za měsíc  |
|---------|---------|
|Společnost Microsoft platí, 80 % vaše náklady na licence    |   80,00 $ za měsíc     |
|Microsoft si ponechá 20 % vaše náklady na licence   |  20,00 $ za měsíc       |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Zákazník fakturaci, platby a fakturaci a kolekce

**Fakturace a platby**

Vydavatele můžete použít zákazníka upřednostňovaný způsob fakturace k doručování předplatné nebo licenční poplatky za software PAYGO.

**Smlouvy Enterprise** 

Pokud upřednostňovaný způsob fakturace zákazníka je Microsoft Enterprise Agreement, váš licenční poplatky za software bude vám účtována to způsob fakturace jako rozpis nákladů, nezávisle na případné poplatky za využití týkající se Azure.

**Přijímání kreditních karet a měsíční faktuře** 

Zákazníci mohou také platit pomocí platební karty a měsíční faktuře. Váš licenční poplatky za software v takovém případě se bude účtovat stejně jako scénář smlouvy Enterprise jako rozpis nákladů, nezávisle na případné poplatky za využití týkající se Azure.

Například, pokud zákazník koupí prostřednictvím platební karty:

|Popis    |    Datum  |
|----------|----------|
|Pořadí období   | 15 srpna 2018 – 30. srpna 2018 |
|Výraz ukončení (měsíc)   | 30. srpna 2018 |
|Datum fakturace | 1. září 2018 |
|Datum platby zákazníků | 1. září 2018 |
|Úschovu používala služba období (kreditní karty, 30 dnů) | 1 září 2018 – 30. září 2018 |
|Kolekce doby spuštění | 1. září 2018 |
|Konec období kolekce (maximální počet, 30 dní) | 30. září 2018 |
|Datum výpočtu výběr (každý měsíc na 15. dne) | 1. října 2018 |
|Výběr data | 15. října 2018 |

Pokud zákazník koupí pomocí smlouvy Enterprise:

| Popis |    Datum  |
|----------|----------|
|Pořadí období | 15 srpna 2018 – 30. srpna 2018 |
|Výraz ukončení (čtvrtletí) | 30. září 2018 |
|Datum fakturace | 15. října 2018 |
|Úschovu používala služba období (kreditní karty, 30 dnů) | neuvedeno |
|Kolekce doby spuštění | 15. října 2018 |
|Konec období kolekce (maximální počet, 90 dní) | 15. ledna 2018 |
|Datum platby zákazníků | 30. prosince 2018 |
|Datum výpočtu výběr (každý měsíc na 15. dne) | 15. ledna 2018 |
|Výběr data | 15. února 2019 |

**Bezplatné kredity a peněžní závazek** 

Zákazníci, kteří se je rozhodnete předem Azure s peněžního závazku smlouvy Enterprise nebo byly zadány bezplatné kredity pro použití s Azure. I když tyto kredity můžete použít pro platby za využití Azure, nelze použít k úhradě licenční poplatky za software vydavatele.

**Fakturace a kolekce** 

Vydavatele softwaru licence fakturace se zobrazí pomocí metody zákazníka vybraného fakturace a řídí fakturace časové osy. Zákazníci bez smlouvy Enterprise na místě fakturují se měsíčně pro marketplace softwarových licencí. Zákazníci se smlouvou Enterprise fakturují se měsíčně prostřednictvím faktury, který se zobrazí zpětně.

Když jsou vybrané předplatné nebo s průběžnými platbami cenové modely, Microsoft funguje jako agent vydavatele a zodpovídá za všechny aspekty fakturaci, platby a kolekce.

### <a name="publisher-payout-and-reporting"></a>Výběr vydavatele a vytváření sestav

* Žádné licenční poplatky za software shromažďovaná společností Microsoft jako agenta podléhají 20 % transakčními poplatky, pokud není uvedeno jinak a se strhávají v době výběr vydavatele.

* Zákazníci si obvykle koupit pomocí smlouvy Enterprise nebo platební karty povolen s průběžnými platbami smlouvy. Typ smlouvy určuje fakturace, fakturace, kolekce a výběr časování.

>[!NOTE] 
>Vytváření sestav a přehledů umožňuje transact možnosti publikování jsou k dispozici prostřednictvím části Insights portál partnerů cloudu.

#### <a name="billing-questions-and-support"></a>Dotazy k fakturaci a podporu

Další informace a právní zásady, najdete v článku [smlouvě s vydavatelem](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (k dispozici na portálu partnerů cloudu).

Nápovědu k fakturaci, [vytvořit incident podpory](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) a zvolte virtuální počítače nebo webových aplikací (označuje se také jako aplikace SaaS) v závislosti na typu nabídky použít.

## <a name="transact-requirements"></a>Příkaz Transact požadavky

Transact požadavky na jinou nabídku typy jsou popsané v této části.

### <a name="requirements-for-all-offer-types"></a>Nabízí požadavky pro všechny typy

**Účtu dev Center a Microsoft** 

* U transact publikování možnost, bez ohledu na to nabídky cenových modelů je vyžadováno Dev Center a účet Microsoft.
* Účet vývojáře pro obsahuje všechny relevantní finanční informace požadované pro společnost Microsoft shromažďovat poplatky od zákazníka jménem vydavatele a platit na vydavatele.
* I když můžete použít stejné organizace nebo společnosti Microsoft přihlašovací podrobnosti prostřednictvím obou účtů, Dev Center je samostatný účet z portálu pro vydavatele cloudu účtu. Použít možnost publikování transact vydavatel musí Dev Center účet registraci dokončit, kromě registrace přístup na portál partnerů cloudu.

*Další informace o nastavení těchto účtů najdete v tématu [stát vydavatelem cloudové Tržiště](https://docs.microsoft.com/azure/marketplace/become-publisher).*

### <a name="requirements-for-specific-offer-types"></a>Požadavky pro určité typy nabídek

Možnost publikování transact dostupná jenom pro použití s těmito typy nabídky marketplace: 

**Virtuální počítač** 

Vyberte z bezplatné, přeneste svůj – používání vlastní licence nebo průběžnými platbami-as-you-go-pricing modelů a prezentovat jako skladové položky definované na úrovni nabídky. Na zákazníka vyúčtování služeb Azure Microsoft uvede vydavatele licenční poplatky za software odděleně od základní poplatky za infrastrukturu Azure. Poplatky za infrastrukturu Azure se řídí užívání softwaru vydavatele.

**Aplikace Azure: Šablona řešení nebo spravovaná aplikace** 

Musíte zřídit jednu nebo více virtuálních počítačů a si prostřednictvím součet ceny virtuálního počítače. Paušální měsíční předplatné u spravovaných aplikací na jeden plán, je možné vybrat jako ceny místo toho modelu, ceny virtuálních počítačů. V obou případech platí poplatky za využití infrastruktury Azure jsou předány do zákazníka odděleně od licenční poplatky za software, ale na stejný fakturu.

## <a name="next-steps"></a>Další postup

* Přečtěte si požadavky zakládající nárok v možnosti publikování nabídky typu oddílu k dokončení pro výběr a konfiguraci nabídky.
* Zkontrolujte publikování vzory jejich příklady jak řešení mapuje na typ nabídky a konfigurace z prodejních míst.
* Označuje jako vydavatele na webu Marketplace a přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com) k vytvoření a konfiguraci nabídky.
