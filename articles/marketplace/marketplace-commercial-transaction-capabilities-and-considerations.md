---
title: Možnosti Transact-The Microsoft Commercial Marketplace
description: V tomto článku se dozvíte o cenách, fakturaci, fakturaci a výběrech v tématu možnosti Transact-The pro komerční web Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9a975ee863e81a9f542e717d734f12db850f749e
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94738318"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Možnosti Transact-The pro komerční web Marketplace

Tento článek popisuje cenové, fakturační, fakturační a výběrové požadavky na komerční tržiště Microsoftu.

## <a name="transactions-by-listing-option"></a>Transakce podle možnosti výpis

Vydavatel nebo Microsoft zodpovídá za správu transakcí softwarových licencí pro nabídky na komerčním tržišti. Možnost výpisu, kterou zvolíte pro vaši nabídku, určuje, kdo spravuje transakci. Dostupnost a vysvětlení jednotlivých možností publikování najdete v tématu [Úvod do možností výpisu](determine-your-listing-type.md) .

### <a name="contact-me-free-trial-and-byol-options"></a>Kontaktujte mě, bezplatné zkušební verze a BYOL možnosti

Vydavatelé si můžou zvolit _kontaktní mě_ a _bezplatnou zkušební verzi_, možnosti pro účely propagačního a nabývání uživatelů. U některých typů nabídek můžou vydavatelé zvolit možnost Přineste si vlastní licenci (BYOL), která zákazníkům umožní zakoupit si předplatné pro vaši nabídku pomocí licence, kterou si koupili přímo od vás. S těmito možnostmi se společnost Microsoft neúčastní přímo v transakcích licencí na software vydavatele a není k dispozici žádný související transakční poplatek.

Vydavatel zodpovídá za podporu všech aspektů transakce softwarové licence. To zahrnuje, ale není omezené na pořadí, splnění, měření, fakturaci, fakturaci, platby a shromažďování. Pomocí možnosti kontakt mi se seznam vydavatelů udržuje 100% licenčních poplatků za software vydavatele shromážděných od zákazníka.

### <a name="transact-publishing-option"></a>Možnost publikování v režimu Transact

Zvolíte-li možnost prodávat prostřednictvím Microsoftu, využijte možnosti Microsoft Commerce a získáte ucelené prostředí od zjišťování a vyhodnocení k nákupu a implementaci. Nabídka, která je transakční, je jedním z nich, kde Microsoft usnadňuje výměnu peněz za softwarovou licenci jménem vydavatele. Nabídky s možností provádění transakcí se účtují na základě stávajícího předplatného nebo platební karty společnosti Microsoft a umožňují společnosti Microsoft hostovat transakce cloudového tržiště jménem vydavatele.

Možnost Transact zvolíte při vytváření nové nabídky v partnerském centru. Tato možnost se zobrazí pouze v případě, že je k dispozici příkaz Transact pro váš typ nabídky.

## <a name="transact-overview"></a>Přehled Transact

Při použití možnosti jazyka Transact společnost Microsoft umožňuje prodej softwaru třetích stran a nasazování některých typů nabídek do předplatného Azure zákazníka. Vydavatel musí při výběru cenového modelu pro nabídku zvážit fakturaci poplatků za infrastrukturu a vlastní licenční poplatky za software.

Možnost publikování v režimu Transact je aktuálně podporována pro následující typy nabídek:

- Virtuální počítače
- Aplikace Azure
- Aplikace SaaS

### <a name="billing-infrastructure-costs"></a>Náklady na infrastrukturu fakturace

U **virtuálních počítačů** a **aplikací Azure** se poplatky za využití infrastruktury Azure účtují na základě předplatného Azure zákazníka. Poplatky za využití infrastruktury se účtují a zobrazují se odděleně od licenčních poplatků poskytovatele softwaru na faktuře zákazníka.

V případě **aplikací SaaS** musí vydavatel považovat za poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu položku nákladů.  Je reprezentována jako paušální poplatek zákazníkovi. Využití infrastruktury Azure se spravuje a účtuje přímo vydavateli. Zákazníky nevidí skutečné poplatky za využití infrastruktury. Vydavatelé obvykle můžou využít poplatky za využití infrastruktury Azure na ceny za licence na software. Licenční poplatky na software se neměří nebo využívají spotřebu uživatelů.

## <a name="pricing-models"></a>Cenové modely

V závislosti na použité možnosti transakce jsou poplatky za předplatné následující:

- **Získejte ho hned (zdarma)** – bez poplatků za licence na software. Zákazníkům se za použití bezplatné nabídky neúčtují poplatky za Azure Marketplace. Bezplatné nabídky se nedají převést na placené nabídky. Zákazníci musí objednat placené nabídky.
- **Přineste si vlastní licenci** (BYOL) – veškeré použitelné poplatky za licence na software se spravují přímo mezi vydavatelem a zákazníkem. Microsoft předává jenom poplatky za využití infrastruktury Azure. Pokud je nabídka uvedená na komerčním webu Marketplace, zákazníkům, kteří získají přístup nebo použití nabídky mimo komerční web Marketplace, se poplatky za komerční tržiště neúčtují.
- **Ceny předplatného** – poplatky za licence na software se zobrazují jako měsíční nebo roční poplatek za předplatné, který se účtuje jako paušální sazba nebo na pracovní stanici. U krátkodobých zrušení a nevyužitých služeb se neúčtují průběžné poplatky za předplatné. Pokud zákazník upgraduje nebo downgrade jejich předplatné uprostřed období předplatného, můžou se převažovat za průběžné hodnocení.
- **Ceny na základě využití** – pro nabídky virtuálních počítačů Azure se zákazníkům účtují podle rozsahu používání nabídky. U imagí virtuálních počítačů se zákazníkům účtuje hodinový Azure Marketplace poplatek, který nastaví vydavatelé, a to pro použití virtuálních počítačů nasazených z imagí virtuálních počítačů. Hodinová sazba může být jednotná nebo proměnlivá v rámci velikostí virtuálních počítačů. Za částečné hodiny se účtují po minutách. Plány se účtují měsíčně.
- **Měřené ceny** – pro nabídky aplikací Azure a nabídky SaaS můžou vydavatelé použít [službu měření na webu Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) k fakturaci za spotřebu na základě dimenzí měřičů, které si zvolí. Například šířka pásma, lístky nebo zpracované e-maily. Vydavatelé mohou pro každý plán definovat jednu nebo více dimenzí měřiče. Vydavatelé zodpovídají za sledování využití jednotlivých zákazníků a jednotlivých měření definovaných v nabídce. Události by měly být společnosti Microsoft hlášeny během hodiny. Společnost Microsoft účtuje zákazníky na základě informací o využití hlášených vydavateli za příslušné fakturační období.
- Bezplatná **zkušební verze** – bez poplatků za licence na software, které jsou v rozsahu od 30 dnů do šesti měsíců, v závislosti na typu nabídky. Pokud vydavatelé poskytují bezplatnou zkušební verzi v rámci stejné nabídky, zákazníci můžou přejít na bezplatnou zkušební verzi v jiném plánu, ale zkušební období se nerestartuje. U nabídek virtuálních počítačů se zákazníkům účtují náklady na infrastrukturu Azure za použití nabídky během zkušebního období. Po vypršení zkušebního období se zákazníkům automaticky účtují poplatky za poslední plán, který vyzkoušel na základě standardních sazeb, pokud se zruší před koncem zkušebního období.

> [!NOTE]
> Nabídky, které se účtují podle spotřeby po použití řešení, nejsou způsobilé k refundaci.

Vydavatelé, kteří chtějí změnit poplatky za použití spojené s nabídkou, by nejdřív měli z komerčního tržiště odebrat nabídku (nebo konkrétní plán v rámci této nabídky). Odebrání by se mělo provádět v souladu s požadavky [smlouvy Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Vydavatel pak může publikovat novou nabídku (nebo naplánovat v rámci nabídky), která zahrnuje nové poplatky za použití. Informace o odebrání nabídky nebo plánu najdete v tématu o tom, jak [zastavit prodej nabídky nebo plánu](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Zdarma, kontaktujte mě a uveďte ceny pro vlastní licenci (BYOL).

Když publikujete nabídku pomocí možnosti získat hned (zdarma), kontaktujte mě nebo BYOL, Microsoft nehraje roli v usnadnění prodejní transakce za licenční poplatky za software. Podobně jako seznam a bezplatné možnosti publikování zkušební verze uchovává Vydavatel 100% licenčních poplatků za software.

### <a name="usage-based-and-subscription-pricing"></a>Ceny na základě využití a předplatného

Když publikujete nabídku s uživatelem nebo předplatným, poskytuje společnost Microsoft technologii a služby pro zpracování nákupů licencí softwaru, jejich vrácení a vratek. V tomto scénáři Vydavatel udělí Microsoftu, aby pro tyto účely jednal jako agent. Vydavatel umožňuje Microsoftu usnadňovat vydávání licenčních transakcí softwaru a přitom zachovává své označení jako prodávající, poskytovatel, distributor a poskytovatel licence.

Microsoft umožňuje zákazníkům objednat, licencovat a používat váš software v souladu s podmínkami a ujednáními z komerčního tržiště společnosti Microsoft i s licenční smlouvou pro koncového uživatele. Při vytváření nabídky musíte zadat vlastní licenční smlouvu s koncovým uživatelem nebo vybrat [standardní kontrakt](./standard-contract.md) .

### <a name="free-software-trials"></a>Bezplatné zkušební verze softwaru

V případě scénářů publikování v jazyce Transact můžete zpřístupnit softwarovou licenci zdarma po dobu 30 až 120 dnů v závislosti na předplatném. Tato funkce diskontování nezahrnuje náklady na využití infrastruktury Azure, které se řídí použitím partnerského řešení.

### <a name="private-offers"></a>Soukromé nabídky

Kromě používání typů nabídek a modelů fakturace k monetizovatí nabídky můžete použít pro transakce soukromou nabídku, která se doplní s použitím vyjednané ceny, ceny specifické pro konkrétní obchodování nebo vlastní konfigurace. Soukromé nabídky jsou podporovány všemi třemi možnostmi publikování v transakčním režimu.

Tato možnost umožňuje vyšší nebo nižší ceny než veřejně dostupná nabídka. Soukromé nabídky můžete použít k zlevnění nebo přidání prémie do nabídky. K dispozici máte přístup k soukromým nabídkám pro jednoho nebo více zákazníků, a to bílým uvedením předplatného Azure na úrovni nabídky.

### <a name="commercial-marketplace-service-fees"></a>Poplatky za služby komerčního tržiště

Pokud si zákazníci koupí svou nabídku jazyka Transact z komerčního tržiště, účtujeme vám i 20% standardního poplatku za službu Store. Podrobnosti o tomto poplatku najdete v části 5c [smlouvy Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560).

U některých nabídek, které jsou publikovány na komerčním webu Marketplace, můžete získat nárok na omezený poplatek za službu úložiště o 10%. Aby byla nabídka oprávněná, musí ji Microsoft jmenovat jako spoluprodejní motivovaní Azure. Nárok na získání omezeného servisního poplatku za měsíc musí být splněn alespoň pět pracovních dnů před koncem každého kalendářního měsíce.

Snížený poplatek za službu Marketplace se vztahuje na Azure IP spoluprodejní motivovaní SaaS, virtuální počítače, spravované aplikace a všechna další kvalifikovaná IaaS řešení, která jsou dostupná prostřednictvím komerčního tržiště. Placené nabídky SaaS jsou spojené s jednou aplikací Microsoft Teams nebo aspoň dvěma Microsoft 365mi doplňky (Excel, PowerPoint, Word, Outlook a SharePoint) a publikované do Microsoft AppSource také dostanou slevu.

### <a name="examples"></a>Příklady

**Na základě využití** 

Ceny na základě využití mají následující strukturu nákladů:

|Náklady na licenci  | $1,00 za hodinu   |
|---------|---------|
|Náklady na využití Azure (D1/1 – jádro)    |   $0,14 za hodinu     |
|*Zákazník se účtuje Microsoftem.*    |  *$1,14 za hodinu*       |
||

V tomto scénáři vám Microsoft účtuje $1,14 za hodinu pro použití publikované image virtuálního počítače.

|Účty Microsoft  | $1,14 za hodinu  |
|---------|---------|
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů.|   $0,80 za hodinu     |
|Společnost Microsoft udržuje 20% vašich licenčních nákladů.  |  $0,20 za hodinu       |
|Microsoft udržuje 100% nákladů na využití Azure. | $0,14 za hodinu |
||

**Používání vlastní licence (BYOL)**

BYOL má následující nákladovou strukturu:

|Náklady na licenci  | Licenční poplatek se vyjednává a účtuje vám  |
|---------|---------|
|Náklady na využití Azure (D1/1 – jádro)    |   $0,14 za hodinu     |
|*Zákazník se účtuje Microsoftem.*    |  *$0,14 za hodinu*       |
||

V tomto scénáři vám Microsoft účtuje $0,14 za hodinu pro použití publikované image virtuálního počítače.

|Účty Microsoft  | $0,14 za hodinu  |
|---------|---------|
|Microsoft udržuje náklady na využití Azure    |   $0,14 za hodinu     |
|Společnost Microsoft udržuje 0% vašich licenčních nákladů.   |  $0,00 za hodinu       |
||

**Předplatné aplikace SaaS**

Tato možnost musí být nakonfigurovaná tak, aby se dala prodávat přes Microsoft, a může se vám účtovat paušálně nebo měsíčně na jednotlivých uživatelích. Pokud povolíte možnost **prodávat prostřednictvím Microsoftu** pro nabídku SaaS, máte následující nákladovou strukturu:

| Náklady na licenci       | $100,00 za měsíc  |
|--------------|---------|
| Náklady na využití Azure (D1/1 – jádro)    | Účtuje se přímo vydavateli, nikoli zákazník. |
| *Zákazník se účtuje Microsoftem.*    |  *$100,00 za měsíc (Vydavatel musí mít v licenčním poplatku vynaložené nebo předávací náklady na infrastrukturu)*  |
||

V tomto scénáři Microsoft účtuje $100,00 pro vaši softwarovou licenci a vyplatí $80,00 vydavateli.

V tomto scénáři Microsoft účtuje $100,00 pro softwarovou licenci a vyplatí $90,00 vydavateli:

|Účty Microsoft  | $100,00 za měsíc  |
|---------|---------|
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. <br> \* Společnost Microsoft vám pro všechny kvalifikované aplikace SaaS zaplatí 90% nákladů na licenci.   |   $80,00 za měsíc <br> \* $90,00 za měsíc    |
|Společnost Microsoft udržuje 20% vašich licenčních nákladů. <br> \* Pro všechny kvalifikované aplikace SaaS udržuje společnost Microsoft 10% nákladů na licenci.  |  $20,00 za měsíc <br> \* $10,00     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fakturace, platby, fakturace a kolekce zákazníků

**Fakturace a platba** – můžete použít preferovanou metodu fakturace zákazníka k doručování předplatných nebo PAYGO licenčních poplatků za software.

**Smlouva Enterprise** – Pokud je upřednostňovanou metodou fakturace zákazníka Microsoft smlouva Enterprise, poplatky za licence na software budou účtovány pomocí této metody fakturace jako účtované náklady, oddělené od jakýchkoli nákladů na používání specifické pro Azure.

**Kreditní karty a měsíční faktury** – zákazníci můžou také platit pomocí platební karty a faktury za měsíc. V takovém případě se vaše licenční poplatky za software účtují stejně jako smlouva Enterprise scénář, jako náklady na použití, oddělené od jakýchkoli nákladů na používání specifické pro Azure.

**Bezplatné kredity a peněžní závazky** – někteří zákazníci si můžou Předplaťte Azure s peněžním závazkem v smlouva Enterprise nebo jste získali bezplatné kredity pro použití s Azure. I když se tyto kredity dají použít k placení za využití Azure, nedají se za licenční poplatky za software vydavatele použít.

**Fakturace a kolekce** – fakturace na základě softwaru vydavatele se zobrazí při fakturaci vybrané zákazníkem a postupuje podle časové osy fakturace. Zákazníkům, kteří nemají smlouva Enterprise v místě, se účtují měsíčně pro licence na tržiště pro software Marketplace. Zákazníci s smlouva Enterprise se účtují měsíčně prostřednictvím faktury, která se prezentuje čtvrtletně.

Po výběru předplatného nebo cenového modelu s průběžnými platbami se společnost Microsoft chová jako agent vydavatele a zodpovídá za všechny aspekty fakturace, platby a kolekce.

### <a name="publisher-payout-and-reporting"></a>Výběr vydavatele a vytváření sestav

Jakékoli licenční poplatky za software shromážděné Microsoftem jako agent podléhají 20% za transakce, pokud není uvedeno jinak a jsou v době určení vydavatele odečteny.

Zákazníci se obvykle kupují pomocí smlouva Enterprise nebo smlouvy s povolenými průběžnými platbami podle kreditní karty. Typ smlouvy určuje načasování, fakturaci, shromažďování a časování výběr.

>[!NOTE]
>Všechny sestavy a přehledy pro možnost publikování v režimu Transact jsou k dispozici prostřednictvím části analýza v partnerském centru.

#### <a name="billing-questions-and-support"></a>Otázky a podpora fakturace

Další informace a právní zásady najdete v tématu [smlouva Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560) (k dispozici v partnerském centru).

Pokud chcete získat nápovědu k fakturaci, kontaktujte [podporu pro komerčního vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Požadavky Transact

V této části jsou popsány požadavky na transakční pro různé typy nabídek.

### <a name="requirements-for-all-offer-types"></a>Požadavky pro všechny typy nabídek

- Pro možnost publikování v režimu Transact se vyžadují účet Microsoft a finanční informace, bez ohledu na cenový model nabídky.
- Povinné finanční informace zahrnují účet výběr a daňový profil.
- Vydavatel musí být živý v [podporované zemi nebo oblasti](sell-from-countries.md).

Další informace o nastavení těchto účtů najdete v tématu [Správa účtu komerčního tržiště v partnerském centru](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Požadavky na konkrétní typy nabídek

Možnost publikování v režimu Transact je k dispozici pouze pro následující typy nabídek Marketplace:

- **Virtuální počítač Azure** – vyberte si z ceny Free, využijte vlastní licenci nebo cenové modely založené na využití a prezentovat jako plány definované na úrovni nabídky. V zákaznických fakturách od zákazníka společnost Microsoft prezentuje licenční poplatky za software vydavatele odděleně od poplatků za infrastrukturu Azure. Poplatky za infrastrukturu Azure jsou založené na používání softwaru vydavatele.

- **Aplikace Azure: Šablona řešení nebo spravovaná aplikace** – musí zřídit jeden nebo víc virtuálních počítačů a vyžádat si součet cen virtuálních počítačů. U spravovaných aplikací s jedním plánem je možné jako cenový model vybrat ceny za virtuální počítače paušální měsíční předplatné. V některých případech jsou poplatky za využití infrastruktury Azure předávány zákazníkovi odděleně od licenčních poplatků za software, ale na stejném výpisu fakturace. Pokud však nakonfigurujete nabídku spravované aplikace pro poplatky za infrastrukturu ISV, prostředky Azure se budou fakturovat vydavateli a zákazník obdrží paušální poplatek, který zahrnuje náklady na infrastrukturu, licence na software a služby správy.

- **SaaS aplikace** – musí se jednat o víceklientské řešení, použít [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pro ověřování a integrovat s [rozhraními API pro plnění SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). Využití infrastruktury Azure se spravuje a fakturuje přímo vám (partner), takže musíte mít poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu položku nákladů. Podrobné pokyny najdete v tématu [Vytvoření nové nabídky SaaS na komerčním webu Marketplace](./create-new-saas-offer.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete dokončit výběr a konfiguraci vaší nabídky, přečtěte si požadavky na způsobilost v části Možnosti publikování podle typu nabídky.
- Seznamte se se vzorem publikování podle online obchodu s příklady, jak vaše řešení mapuje typ a konfiguraci nabídky.
