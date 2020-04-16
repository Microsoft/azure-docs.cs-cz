---
title: Možnosti obchodního trhu společnosti Microsoft
description: Tento článek popisuje ceny, fakturaci, fakturaci a výplaty pro možnost obchodního trhu společnosti Microsoft.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 768fa9ca2080fc9a58fb321e62d8d61a608f9564
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415263"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Možnosti provází obchodní trh

Tento článek popisuje následující obchodní témata pro obchodní trh společnosti Microsoft:

* Odpovědnost za transakce za různé možnosti publikování
* Transact obecný přehled
* Transact fakturační modely
* Požadavky na provádění

## <a name="transactions-by-publishing-option"></a>Transakce pomocí možnosti publikování

Vydavatel nebo společnost Microsoft je zodpovědná za správu transakcí softwarových licencí pro nabídky na komerčním trhu. Možnost publikování, kterou zvolíte pro vaši nabídku, určí, kdo transakci spravuje. Dostupnost a vysvětlení jednotlivých možností publikování najdete v tématu [Určení možnosti publikování.](./determine-your-listing-type.md#choose-a-publishing-option)

### <a name="list-trial-and-byol-publishing-options"></a>Možnosti publikování seznamu, zkušební verze a byol

Majitelé stránek s existujícími možnostmi obchodování si mohou vybrat možnosti publikování seznamu, zkušební verze a vlastní licence (BYOL) pro účely propagace a získávání uživatelů. S těmito možnostmi se společnost Microsoft přímo neúčastní transakcí s licencemi na software vydavatele a neexistuje žádný související transakční poplatek. Vydavatelé jsou zodpovědní za podporu všech aspektů transakce s licencí na software, mimo jiné včetně: objednávky, plnění, měření, fakturace, fakturace, platby a inkasa. Díky možnostem seznamu a zkušebního publikování si vydavatelé ponechají 100 % licenčních poplatků za software vydavatelů vybraných od zákazníka.

### <a name="transact-publishing-option"></a>Možnost publikování transact

Možnost publikování transact využívá možnosti obchodování microsoftu a poskytuje komplexní prostředí od zjišťování a hodnocení až po nákup a implementaci. Nabídky transactu se účtují proti stávajícímu předplatnému společnosti Microsoft nebo platební kartě, což společnosti Microsoft umožňuje hostovat transakce na trhu cloudu jménem vydavatele.

Možnost transaktu zvolíte při vytváření nové nabídky v Partnerském centru. Na stránce **Nastavení nabídky** v části **Podrobnosti o instalaci**vyberte možnost Ano, chtěl bych prodávat prostřednictvím společnosti Microsoft a mít transakce hostitele společnosti Microsoft mým jménem. Tato možnost se zobrazí pouze v případě, že je pro typ nabídky k dispozici transakt.

## <a name="transact-general-overview"></a>Transact obecný přehled

Při použití možnosti publikování transact, Microsoft umožňuje prodej softwaru třetích stran a nasazení některých typů nabídek pro zákazníka předplatného Azure. Při výběru fakturačního modelu a typu nabídky musí vydavatel zvážit účtování poplatků za infrastrukturu a vlastní licenční poplatky za software.

Možnost publikování Transact je aktuálně podporovaná pro následující typy nabídek: Virtuální počítače, Aplikace Azure a Aplikace SaaS.

### <a name="billing-infrastructure-costs"></a>Náklady na fakturační infrastrukturu

**Pro virtuální počítače a aplikace Azure**

U virtuálních počítačů a aplikací Azure se poplatky za využití infrastruktury Azure účtují podle předplatného Azure zákazníka. Poplatky za využití infrastruktury jsou oceněny a prezentovány odděleně od licenčních poplatků poskytovatele softwaru na faktuře zákazníka.

**Pro aplikace SaaS**

U aplikací SaaS musí vydavatel účtovat poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu nákladovou položku.  Je pro zákazníka zastoupena jako paušální poplatek. Využití infrastruktury Azure se spravuje a účtuje přímo partnerovi. Skutečné poplatky za využití infrastruktury nejsou zákazníkem vidět. Vydavatelé se obvykle rozhodnou složit poplatky za využití infrastruktury Azure do cen softwarových licencí. Licenční poplatky za software nejsou měřeny nebo na základě spotřeby.

## <a name="transact-billing-models"></a>Transact fakturační modely

V závislosti na použité možnosti transakce mohou být licenční poplatky vydavatele na základě softwarových licencí prezentovány takto:  

* Zdarma: Za softwarové licence se neplatí.

* Přineste si vlastní licenci (BYOL): Veškeré příslušné poplatky za softwarové licence jsou spravovány přímo mezi vydavatelem a zákazníkem. Microsoft projíždí jenom poplatky za využití infrastruktury Azure. To platí pouze pro virtuální počítače a aplikace Azure.

* Průběžně: Licenční poplatky za software se prezentují jako cena za hodinu a za jádro (vCPU) na základě použité infrastruktury Azure. To platí pouze pro virtuální počítače a aplikace Azure.

* Ceny předplatného: Licenční poplatky za software jsou prezentovány jako měsíční nebo roční opakující se poplatek účtovaný jako paušální sazba nebo za sedadlo. To platí pro aplikace SaaS a aplikace Azure – jenom spravované aplikace.

* Zkušební verze bezplatného softwaru: Za softwarové licence se neúčtuje po dobu 30 dnů nebo 90 dní.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ceny zdarma a přineste si vlastní licenci (BYOL)

Při publikování nabídky transakcí zdarma nebo přineste vlastní licenci, společnost Microsoft nehraje roli při usnadňování prodejní transakce pro licenční poplatky za software. Stejně jako seznam a možnosti publikování zkušební verze, vydavatel udržuje 100% licenčních poplatků za software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Ceny průběžných plateb a předplatného (na webu)

Při publikování nabídky transakcí s průběžnými platbami nebo předplatného poskytuje společnost Microsoft technologii a služby pro zpracování nákupů softwarových licencí, vrácení a vrácení poplatků. V tomto scénáři vydavatel opravňuje společnost Microsoft jednat jako agent pro tyto účely. Vydavatel umožňuje společnosti Microsoft usnadnit transakci licencování softwaru a zároveň zachovat jejich označení jako prodejce, poskytovatele, distributora a poskytovatele licence.

Společnost Microsoft umožňuje zákazníkům objednávat, licencovat a používat software vydavatele v souladu s podmínkami komerčního webu Marketplace společnosti Microsoft i licenční smlouvy s koncovým uživatelem vydavatele. Vydavatelé musí při vytváření nabídky poskytnout licenční smlouvu s koncovým uživatelem nebo vybrat [standardní smlouvu.](./standard-contract.md)

### <a name="free-software-trials"></a>Bezplatné softwarové zkušební verze

Pro scénáře publikování na transakt může vydavatel zpřístupnit softwarovou licenci zdarma po dobu 30 dnů nebo 90 dnů. Tato možnost slevy nezahrnuje náklady na využití infrastruktury Azure, která je řízena pomocí partnerského řešení.

### <a name="private-offers"></a>Soukromé nabídky

Kromě použití typů nabídek a fakturačních modelů ke zpeněžení nabídky mohou majitelé stránek provést soukromou nabídku s vyjednanými cenami, cenami specifickými pro dohodu nebo vlastními konfiguracemi. Soukromé nabídky jsou podporovány všemi 3 možnostmi publikování transact.

Tato možnost umožňuje vyšší nebo nižší ceny než veřejně dostupná nabídka. Soukromé nabídky mohou být použity ke slevě, nebo přidat prémii za nabídku. Soukromé nabídky lze zpřístupnit jednomu nebo více zákazníkům pomocí bílého výpisu jejich předplatného Azure na úrovni nabídky.


### <a name="examples"></a>Příklady

**Průběžné platby** 

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

**Přineste si vlastní licenci (BYOL)**

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

**Předplatné aplikace SaaS**

Tato možnost musí být nakonfigurována tak, aby se prodávala prostřednictvím společnosti Microsoft, a může být oceněna paušální sazbou nebo na uživatele měsíčně nebo ročně.
*    Pokud povolíte možnost Prodat prostřednictvím microsoftu pro nabídku SaaS, pak máte následující strukturu nákladů.

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

* **Snížený poplatek za službu Marketplace:** U některých produktů SaaS, které publikujete na našem komerčním tržišti, sníží společnost Microsoft poplatek za službu Marketplace z 20 % (jak je popsáno ve smlouvě s vydavatelem společnosti Microsoft) na 10 %.  Aby se váš produkt kvalifikoval, musí být alespoň jeden z vašich produktů společností Microsoft označen jako připravený pro co-sell IP nebo s prioritou prodávat ip adresy. Chcete-li získat tento snížený poplatek za službu Marketplace za měsíc, musí být způsobilost splněna nejméně pět (5) pracovních dnů před koncem předchozího kalendářního měsíce. Snížený poplatek za službu Marketplace se nebude vztahovat na virtuální společnosti, spravované aplikace ani na jiné produkty zpřístupněné prostřednictvím našeho komerčního tržiště.  Tento snížený poplatek za službu Marketplace bude k dispozici kvalifikovaným nabídkám s licenčními poplatky vybranými společností Microsoft v období od 1.  Po uplynutí této doby se poplatek za službu Marketplace vrátí na svou obvyklou částku.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fakturace, platba, fakturace a inkasa odběratele

**Fakturace a platba**

Aplikace Publisher může použít metodu preferované fakturace zákazníka k doručení předplatného nebo licenčních poplatků za software PAYGO.

**Podniková smlouva** 

Pokud zákazník upřednostňuje způsob fakturace je Microsoft Enterprise Agreement, vaše licenční poplatky za software se budou účtovat pomocí této metody fakturace jako rozčleněné náklady, odděleně od všech nákladů na využití specifických pro Azure.

**Kreditní karty a měsíční faktura** 

Zákazníci mohou také platit kreditní kartou a měsíční fakturou. V takovém případě se licenční poplatky za software budou účtovat stejně jako scénář smlouvy Enterprise, jako položková cena, která je oddělená od veškerých nákladů na využití specifických pro Azure.

Pokud například zákazník nakupuje pomocí platební karty:

|Popis    |    Datum  |
|----------|----------|
|Období objednávky   | Srp 15, 2018 - Srp 30, 2018 |
|Konec termínu (měsíc)   | 30. srpna 2018 |
|Datum fakturace | 1. září 2018 |
|Datum platby odběratele | 1. září 2018 |
|Doba úschovy (pouze kreditní karty, 30 dní) | 1. září 2018 – 30. |
|Začátek období vyzvednutí | 1. září 2018 |
|Konec období vyzvednutí (maximálně 30 dní) | 30. září 2018 |
|Datum výpočtu výplaty (měsíčně 15.) | 1. října 2018 |
|Datum výplaty | 15. října 2018 |

Pokud zákazník nakupuje pomocí smlouvy Enterprise:

| Popis |    Datum  |
|----------|----------|
|Období objednávky | Srp 15, 2018 - Srp 30, 2018 |
|Konec termínu (čtvrtletí) | 30. září 2018 |
|Datum fakturace | 15. října 2018 |
|Doba úschovy (pouze kreditní karty, 30 dní) | neuvedeno |
|Začátek období vyzvednutí | 15. října 2018 |
|Konec období vyzvednutí (maximálně, 90 dní) | 15. ledna 2019 |
|Datum platby odběratele | 30. prosince 2018 |
|Datum výpočtu výplaty (měsíčně 15.) | 15. ledna 2019 |
|Datum výplaty | 15. února 2019 Úno 15, 2019 |

**Volné úvěry a peněžní závazky** 

Někteří zákazníci se rozhodnou předplatit Azure s peněžním závazkem ve smlouvě Enterprise nebo jim byly poskytnuty bezplatné kredity pro použití s Azure. I když tyto kredity lze použít k platbě za využití Azure, nelze je použít k úhradě licenčních poplatků vydavatele softwaru.

**Fakturace a kolekce** 

Fakturace softwarové licence aplikace Publisher je prezentována pomocí metody fakturace vybrané zákazníkem a sleduje časovou osu fakturace. Zákazníkům bez smlouvy Enterprise se měsíčně účtují licence na software marketplace. Zákazníkům se smlouvou Enterprise se fakturuje měsíčně prostřednictvím faktury, která je prezentována čtvrtletně.

Pokud jsou vybrány cenové modely s průběžným platbou nebo s průběžným platbou, společnost Microsoft funguje jako zástupce vydavatele a je zodpovědná za všechny aspekty fakturace, plateb a inkasa.

### <a name="publisher-payout-and-reporting"></a>Výplata a reportování vydavatele

* Veškeré licenční poplatky za software vybrané společností Microsoft jako agentpodléhají transakčnímu poplatku ve výši 20 %, pokud není uvedeno jinak, a jsou odečteny v době výplaty majitele stránek.

* Zákazníci obvykle nakupují pomocí smlouvy Enterprise nebo smlouvy s povolenými platbami s průběžnou platbou s povolenou kreditní kartou. Typ smlouvy určuje čas fakturace, fakturace, inkaso a výplaty.

>[!NOTE]
>Všechny přehledy a přehledy pro možnost publikování transgrafu jsou k dispozici prostřednictvím části Přehledy v části Portál pro partnery cloudnebo analytics v Centru partnerů.

#### <a name="billing-questions-and-support"></a>Otázky k fakturaci a podpora

Další informace a právní zásady najdete ve [smlouvě s vydavatelem](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (k dispozici na portálu partnerů cloudu).

Chcete-li získat pomoc s dotazy týkajícími se fakturace, obraťte se na [podporu vydavatele komerčního trhu](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Požadavky na provádění

V této části jsou uvedeny požadavky na transakt pro různé typy nabídek.

### <a name="requirements-for-all-offer-types"></a>Požadavky na všechny typy nabídek

* Pro možnost publikování převodu jsou vyžadovány informace o účtu Microsoft a finanční informace bez ohledu na cenový model nabídky.
* Povinné finanční údaje zahrnují výplatní účet a daňový profil.

Další informace o nastavení těchto účtů naleznete v [tématu Správa účtu komerčního tržiště v Centru partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)).

### <a name="requirements-for-specific-offer-types"></a>Požadavky na konkrétní typy nabídek

Možnost publikování transact je k dispozici pouze pro použití s následujícími typy nabídek marketplace:

**Virtuální počítač** 

Vyberte si z bezplatných modelů s vlastní licencí nebo průběžných cen a prezentujte je jako skutážní prodejny definované na úrovni nabídky. Na účet Azure zákazníka microsoft prezentuje licenční poplatky vydavatele softwaru odděleně od základních poplatků za infrastrukturu Azure. Poplatky za infrastrukturu Azure se řídí používáním softwaru vydavatele.

**Aplikace Azure: Šablona řešení nebo spravovaná aplikace** 

Musí zřídit jeden nebo více virtuálních počítačů a vytáhne součet cen virtuálního počítače. U spravovaných aplikací v jednom plánu lze jako cenový model místo ceny virtuálního počítače vybrat paušální měsíční předplatné. V některých případech se poplatky za využití infrastruktury Azure předávají zákazníkovi odděleně od licenčních poplatků za software, ale na stejném fakturačním výpisu. Pokud však nakonfigurujete nabídku spravované aplikace pro poplatky za infrastrukturu isv, prostředky Azure se účtují vydavateli a zákazník obdrží paušální poplatek, který zahrnuje náklady na infrastrukturu, softwarové licence a služby správy.

## <a name="next-steps"></a>Další kroky

* Zkontrolujte požadavky na způsobilost v části Možnosti publikování podle typu nabídky a dokončte výběr a konfiguraci nabídky.
* Projděte si vzory publikování podle výkladní skříně, kde například vypadá, jak se vaše řešení mapuje na typ nabídky a konfiguraci.
