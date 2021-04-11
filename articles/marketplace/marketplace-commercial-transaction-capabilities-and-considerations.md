---
title: Možnosti Transact-The Microsoft Commercial Marketplace
description: V tomto článku se dozvíte o cenách, fakturaci, fakturaci a výběrech v tématu možnosti Transact-The pro komerční web Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9797401fd96bf052fdc9de0c2c2ceaf88b3d46b6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504205"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Možnosti Transact-The pro komerční web Marketplace

Tento článek popisuje cenové, fakturační, fakturační a výběrové požadavky na komerční tržiště Microsoftu.

## <a name="transactions-by-listing-option"></a>Transakce podle možnosti výpis

Vydavatel nebo Microsoft zodpovídá za správu transakcí softwarových licencí pro nabídky na komerčním tržišti. Možnost výpisu, kterou zvolíte pro vaši nabídku, určuje, kdo spravuje transakci. Dostupnost a vysvětlení jednotlivých možností publikování najdete v tématu [Úvod do možností výpisu](determine-your-listing-type.md) .

### <a name="contact-me-free-trial-and-byol-options"></a>Kontaktujte mě, bezplatné zkušební verze a BYOL možnosti

Vydavatelé si můžou zvolit _kontaktní mě_ a _bezplatnou zkušební verzi_, možnosti pro účely propagačního a nabývání uživatelů. U některých typů nabídek můžou vydavatelé zvolit možnost _Přineste si vlastní licenci_ (BYOL), která zákazníkům umožní zakoupit si předplatné pro vaši nabídku pomocí licence, kterou si koupili přímo od vás. S těmito možnostmi se společnost Microsoft neúčastní přímo v transakcích licencí na software vydavatele a neexistuje žádný přidružený transakční poplatek, takže vydavatelé tyto tržby udržují.

S těmito možnostmi jsou vydavatelé odpovědni za podporu všech aspektů transakce softwarové licence. To zahrnuje, ale není omezené na pořadí, splnění, měření, fakturaci, fakturaci, platby a shromažďování. Pomocí možnosti kontakt mi se seznam vydavatelů udržuje 100% licenčních poplatků za software vydavatele shromážděných od zákazníka.

### <a name="transact-publishing-option"></a>Možnost publikování v režimu Transact

Zvolíte-li možnost prodávat prostřednictvím Microsoftu, využijte možnosti Microsoft Commerce a získáte ucelené prostředí od zjišťování a vyhodnocení k nákupu a implementaci. Nabídka s _podporou transakcí_ je ta, ve které Microsoft usnadňuje výměnu peněz za softwarovou licenci jménem vydavatele. Nabídky Transact se účtují na základě stávajícího předplatného nebo platební karty společnosti Microsoft a umožňují společnosti Microsoft hostovat transakce cloudového tržiště jménem vydavatele.

Možnost Transact zvolíte při vytváření nové nabídky v partnerském centru. Tato možnost se zobrazí pouze v případě, že je k dispozici příkaz Transact pro váš typ nabídky.

## <a name="transact-overview"></a>Přehled Transact

Při použití možnosti jazyka Transact společnost Microsoft umožňuje prodej softwaru třetích stran a nasazování některých typů nabídek do předplatného Azure zákazníka. Vydavatel musí při výběru cenového modelu nabídky vzít v úvahu fakturaci za poplatky za infrastrukturu a licenční poplatky za software.

Možnost publikování v režimu Transact je aktuálně podporována pro následující typy nabídek:

| Typ nabídky | Fakturační tempo | Měřené fakturace | Cenový model |
| ------------ | ------------- | ------------- | ------------- |
| Azure Application<br>(Spravovaná aplikace) | Měsíčně | Yes | Na základě využití |
| Virtuální počítač Azure | Nadpis | No | Založené na používání, BYOL |
| Software jako služba (SaaS) | Měsíčně a ročně | Yes | Paušální sazba, na uživatele a na základě využití. |
|||||

`*` Virtuální počítač Azure nabízí podporu fakturačních plánů na základě využití. Tyto plány se účtují měsíčně za hodinové použití předplatného založeného na jednotlivých jádrech, na základě počtu jader nebo na trh a na využití jádra velikosti.

### <a name="metered-billing"></a>Měřené fakturace

_Služba měření na webu Marketplace_ umožňuje zadat poplatky za průběžné platby (na základě spotřeby) Kromě měsíčních nebo ročních poplatků zahrnutých do smlouvy (nárok). Můžete účtovat náklady na využití pro dimenze služby Marketplace, které zadáte, jako je šířka pásma, lístky nebo zpracované e-maily. Další informace o účtovaných fakturách pro nabídky SaaS najdete v tématu [účtované vyúčtování pro SaaS pomocí komerčního měření služby Marketplace](./partner-center-portal/saas-metered-billing.md). Další informace o účtovaných fakturách pro nabídky aplikací Azure najdete v tématu [Správa účtované podle objemu dat](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Náklady na infrastrukturu fakturace

U **virtuálních počítačů** a **aplikací Azure** se poplatky za využití infrastruktury Azure účtují na základě předplatného Azure zákazníka. Na faktuře zákazníka jsou poplatky za využití infrastruktury uvedené odděleně od licenčních poplatků poskytovatele softwaru.

Pro **aplikace SaaS** musí vydavatel považovat za poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu položku nákladů. Je reprezentována jako paušální poplatek zákazníkovi. Využití infrastruktury Azure se spravuje a účtuje přímo vydavateli. Zákazníky nevidí skutečné poplatky za využití infrastruktury. Vydavatelé obvykle můžou využít poplatky za využití infrastruktury Azure na ceny za licence na software. Licenční poplatky na software se neměří nebo využívají spotřebu uživatelů.

## <a name="pricing-models"></a>Cenové modely

V závislosti na použité možnosti transakce jsou poplatky za předplatné následující:

- **Získejte hned (zdarma)**: licence na software se neúčtují. Bezplatné nabídky se nedají převést na placené nabídky. Zákazníci musí objednat placené nabídky.
- **Přineste si vlastní licenci** (BYOL): Pokud je nabídka uvedená na komerčním webu Marketplace, veškeré příslušné poplatky za licence na software se spravují přímo mezi vydavatelem a zákazníkem. Společnost Microsoft účtuje jenom poplatky za využití infrastruktury Azure na účet předplatného Azure zákazníka.
- **Ceny předplatného**: licenční poplatky za software se zobrazují jako měsíční nebo roční poplatek za opakovaný odběr, který se účtuje jako paušální sazba nebo na pracovní stanici. U krátkodobých zrušení a nevyužitých služeb se neúčtují průběžné poplatky za předplatné. Pokud zákazník upgraduje nebo downgrade jejich předplatné uprostřed období předplatného, můžou se převažovat za průběžné hodnocení.
- **Ceny na základě využití**: u nabídek virtuálních počítačů Azure se zákazníkům účtují podle rozsahu jejich používání. U imagí virtuálních počítačů se zákazníkům účtuje hodinový Azure Marketplace poplatek, který nastaví Vydavatel, a to pro použití virtuálních počítačů nasazených z imagí virtuálních počítačů. Hodinová sazba může být jednotná nebo proměnlivá v rámci velikostí virtuálních počítačů. Za částečné hodiny se účtují po minutách. Plány se účtují měsíčně.
- **Účtované ceny**: pro nabídky aplikací Azure a nabídky SaaS můžou vydavatelé použít [službu měření na webu Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) k fakturaci za spotřebu na základě vlastních dimenzí měřičů, které konfigurují. Tyto změny se vztahují i na měsíční nebo roční poplatky zahrnuté do smlouvy (nárok). Příklady dimenzí vlastního měřiče jsou šířky pásma, lístky nebo zpracované e-maily. Vydavatelé mohou definovat jednu nebo více měřených dimenzí pro každý plán, ale maximálně 30 na jednu nabídku. Vydavatelé zodpovídají za sledování využití jednotlivých zákazníků a jednotlivých měření definovaných v nabídce. Události by měly být společnosti Microsoft hlášeny během hodiny. Společnost Microsoft účtuje zákazníky na základě informací o využití hlášených vydavateli za příslušné fakturační období.
- **Bezplatná zkušební verze**: žádné poplatky za licence na software, které jsou v rozsahu od 30 dnů do šesti měsíců, v závislosti na typu nabídky. Pokud vydavatelé poskytují bezplatnou zkušební verzi na více plánech v rámci stejné nabídky, zákazníci můžou přejít na bezplatnou zkušební verzi jiného plánu, ale zkušební období se nerestartuje. U nabídek virtuálních počítačů se zákazníkům účtují náklady na infrastrukturu Azure za použití nabídky během zkušebního období. Po vypršení zkušebního období se zákazníkům automaticky účtují poplatky za poslední plán, který vyzkoušel na základě standardních sazeb, pokud se zruší před koncem zkušebního období.

> [!NOTE]
> Nabídky, které se účtují podle spotřeby po použití řešení, nejsou způsobilé k refundaci.

Vydavatelé, kteří chtějí změnit poplatky za použití spojené s nabídkou, by nejdřív měli z komerčního tržiště odebrat nabídku (nebo konkrétní plán v rámci této nabídky). Odebrání by se mělo provádět v souladu s požadavky [smlouvy Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Vydavatel pak může publikovat novou nabídku (nebo naplánovat v rámci nabídky), která zahrnuje nové poplatky za použití. Informace o odebrání nabídky nebo plánu najdete v tématu o tom, jak [zastavit prodej nabídky nebo plánu](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Zdarma, kontaktujte mě a uveďte ceny pro vlastní licenci (BYOL).

Když publikujete nabídku pomocí možnosti získat hned (zdarma), kontaktujte mě nebo BYOL, Microsoft nehraje roli v usnadnění prodejní transakce za licenční poplatky za software. Vydavatel udržuje 100% licenčních poplatků za software.

### <a name="usage-based-and-subscription-pricing"></a>Ceny na základě využití a předplatného

Při publikování nabídky jako transakce na základě využití nebo předplatného poskytuje společnost Microsoft technologii a služby pro zpracování nákupů licencí softwaru, jejich vrácení a vrácení se změnami. V tomto scénáři Vydavatel udělí Microsoftu, aby pro tyto účely jednal jako agent. Vydavatel umožní společnosti Microsoft, aby usnadnila transakci licencování softwaru. Vydavatel, zachovejte své označení jako prodejce, poskytovatele, distributora a poskytovatele licence.

Microsoft umožňuje zákazníkům objednat, licencovat a používat váš software v souladu s podmínkami a ujednáními z komerčního tržiště společnosti Microsoft i s licenční smlouvou pro koncového uživatele. Musíte buď zadat vlastní licenční smlouvu s koncovým uživatelem, nebo při vytváření nabídky vybrat [standardní kontrakt](./standard-contract.md) .

### <a name="free-software-trials"></a>Bezplatné zkušební verze softwaru

V případě scénářů publikování v jazyce Transact můžete zpřístupnit softwarovou licenci zdarma po dobu 30 až 120 dnů v závislosti na předplatném. Zákazníci se budou měnit pro příslušné využití infrastruktury Azure.

### <a name="examples-of-pricing-and-store-fees"></a>Příklady cen a poplatků za Store

**Na základě využití**

Ceny na základě využití mají následující strukturu nákladů:

| **Náklady na licenci** | **$1,00 za hodinu** |
|---------|---------|
| Náklady na využití Azure (D1/1 – jádro) | $0,14 za hodinu |
| _Zákazník se účtuje Microsoftem._ | _$1,14 za hodinu_ |
||

V tomto scénáři vám Microsoft účtuje $1,14 za hodinu pro použití publikované image virtuálního počítače.

| **Účty Microsoft** | **$1,14 za hodinu**  |
|---------|---------|
| Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. | $0,80 za hodinu |
| Společnost Microsoft udržuje 20% vašich licenčních nákladů.  |  $0,20 za hodinu |
| Microsoft udržuje 100% nákladů na využití Azure. | $0,14 za hodinu |
||

**Přineste si vlastní licenci (BYOL)**

BYOL má následující nákladovou strukturu:

| **Náklady na licenci** | **Licenční poplatek se vyjednává a účtuje vám** |
|---------|---------|
|Náklady na využití Azure (D1/1 – jádro)    |   $0,14 za hodinu     |
| _Zákazník se účtuje Microsoftem._ | _$0,14 za hodinu_ |
||

V tomto scénáři vám Microsoft účtuje $0,14 za hodinu pro použití publikované image virtuálního počítače.

| **Účty Microsoft** | **$0,14 za hodinu** |
|---------|---------|
| Microsoft udržuje náklady na využití Azure | $0,14 za hodinu |
| Společnost Microsoft udržuje 0% vašich licenčních nákladů. | $0,00 za hodinu |
||

**Předplatné aplikace SaaS**

SaaS předplatných se dá účtovat paušální sazbou nebo podle počtu uživatelů na měsíc nebo roční úrovni. Pokud povolíte možnost  **prodávat prostřednictvím Microsoftu** pro nabídku SaaS, máte následující nákladovou strukturu:

| **Náklady na licenci** | **$100,00 za měsíc** |
|--------------|---------|
| Náklady na využití Azure (D1/1 – jádro) | Účtuje se přímo vydavateli, nikoli zákazník. |
| _Zákazník se účtuje Microsoftem._ | _$100,00 za měsíc (Vydavatel musí mít v licenčním poplatku vynaložené nebo předávací náklady na infrastrukturu)_ |
||

V tomto scénáři Microsoft účtuje $100,00 pro softwarovou licenci a za vás vyplatí $80,00 nebo $90,00 v závislosti na tom, jestli se tato nabídka pro omezený poplatek za službu Store omezuje.

| **Účty Microsoft** | **$100,00 za měsíc** |
|---------|---------|
| Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. <br> \* Společnost Microsoft vám pro všechny kvalifikované aplikace SaaS zaplatí 90% nákladů na licenci. | $80,00 za měsíc <br> \* $90,00 za měsíc |
| Společnost Microsoft udržuje 20% vašich licenčních nákladů. <br> \* Pro všechny kvalifikované aplikace SaaS udržuje společnost Microsoft 10% nákladů na licenci. | $20,00 za měsíc <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Poplatky za služby komerčního tržiště

Pokud si zákazníci koupí svou nabídku jazyka Transact z komerčního tržiště, účtujeme vám i 20% standardního poplatku za službu Store. Podrobnosti o tomto poplatku najdete v části 5c [smlouvy Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560).

U některých nabídek, které publikujete na komerční tržiště, můžete nárokovat na snížený poplatek za službu úložiště o 10%. Aby byla nabídka oprávněná, musí ji Microsoft jmenovat jako _spoluprodejní motivovaní Azure_. Nárok na získání omezeného poplatku za služby Marketplace vyžaduje splnění alespoň pěti pracovních dnů před koncem každého kalendářního měsíce. Po splnění nároku na vyřízení je snížený poplatek za službu udělen všem transakcím platným první den v následujícím měsíci, dokud nebude _motivovaní stav společného prodeje Azure IP_ . Podrobnosti o způsobilosti spoluprodejních IP adres najdete v tématu [požadavky na stav společného prodeje](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

Snížený poplatek za službu Marketplace se vztahuje na Azure IP spoluprodejní motivovaní SaaS, virtuální počítače, spravované aplikace a všechna další kvalifikovaná IaaS řešení, která jsou dostupná prostřednictvím komerčního tržiště. Placené nabídky SaaS spojené s jednou aplikací Microsoft Teams nebo aspoň dvěma Microsoft 365 doplňky (Excel, PowerPoint, Word, Outlook a SharePoint) a publikované do Microsoft AppSource můžou být také kvalifikovány pro tuto slevu.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fakturace, platby, fakturace a kolekce zákazníků

**Fakturace a platby**: můžete použít upřednostňovanou metodu fakturace zákazníka k doručování licenčních poplatků za předplatné nebo [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) softwaru.

**Smlouva Enterprise**: Pokud je upřednostňovanou metodou fakturace zákazníka Microsoft smlouva Enterprise, poplatky za licence na software budou účtovány pomocí této metody fakturace jako účtované náklady, oddělené od jakýchkoli nákladů na používání specifické pro Azure.

**Kreditní karty a měsíční faktury**: zákazníci můžou platit pomocí platební karty a faktury za měsíc. V takovém případě se vaše licenční poplatky za software účtují stejně jako smlouva Enterprise scénář, jako náklady na použití, oddělené od jakýchkoli nákladů na používání specifické pro Azure.

**Bezplatné kredity a peněžní závazky**: někteří zákazníci si vyberou za závazek zaplatit za Azure s peněžním závazkem v smlouva Enterprise nebo byly poskytnuty bezplatné kredity, které se použijí k využití Azure. I když se tyto kredity dají použít k placení za využití Azure, nedají se za licenční poplatky za software vydavatele použít.

**Fakturace a kolekce**: faktura na software vydavatele se účtuje pomocí zákaznické metody vybrané k fakturaci a řídí se časovou osou fakturace. Zákazníkům, kteří nemají smlouva Enterprise v místě, se účtují měsíčně pro licence na tržiště pro software Marketplace. Zákazníci s smlouva Enterprise se účtují měsíčně prostřednictvím faktury, která se prezentuje čtvrtletně.

Když je vybraná možnost předplatné nebo průběžné platby (označované taky jako na základě využití), Microsoft se chová jako agent vydavatele a zodpovídá za všechny aspekty fakturace, platby a kolekce.

### <a name="publisher-payout-and-reporting"></a>Výběr vydavatele a vytváření sestav

Jakékoli licenční poplatky za software shromážděné Microsoftem jako agent podléhají 20% za transakce, pokud není uvedeno jinak a jsou v době určení vydavatele odečteny.

Zákazníci se obvykle kupují pomocí smlouva Enterprise nebo smlouvy s povolenými průběžnými platbami podle kreditní karty. Typ smlouvy určuje načasování, fakturaci, shromažďování a časování výběr.

>[!NOTE]
>Všechny sestavy a přehledy pro možnost publikování v režimu Transact jsou k dispozici prostřednictvím části analýza v partnerském centru.

#### <a name="billing-questions-and-support"></a>Otázky a podpora fakturace

Další informace a právní zásady najdete v tématu [smlouva Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Pro pomoc s dotazy ohledně fakturace kontaktujte [komerční podporu vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Požadavky Transact

V této části jsou popsány požadavky na transakční pro různé typy nabídek.

### <a name="requirements-for-all-offer-types"></a>Požadavky pro všechny typy nabídek

- Pro možnost publikování v režimu Transact se vyžadují účet Microsoft a finanční informace, bez ohledu na cenový model nabídky.
- Povinné finanční informace zahrnují účet výběr a daňový profil.

Další informace o nastavení těchto účtů najdete v tématu [Správa účtu komerčního tržiště v partnerském centru](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Požadavky na konkrétní typy nabídek

Schopnost vyhledávat prostřednictvím Microsoftu je dostupná jenom pro následující typy nabídek na komerčním webu Marketplace. V tomto seznamu najdete požadavky na to, aby tyto typy nabídek byly v komerčním tržišti transakční.

- **Aplikace Azure (šablona řešení a plány spravované aplikace**: v některých případech jsou poplatky za využití infrastruktury Azure předávány zákazníkovi odděleně od licenčních poplatků za software, ale na stejném výpisu fakturace. Pokud ale nakonfigurujete plán spravované aplikace pro poplatky za infrastrukturu ISV, prostředky Azure se budou fakturovat vydavateli a zákazník obdrží paušální poplatek, který zahrnuje náklady na infrastrukturu, licence na software a služby správy.

- **Virtuální počítač Azure**: Vyberte si z cenových modelů založených na bezplatném, BYOL nebo využití. V zákaznických fakturách od zákazníka společnost Microsoft prezentuje licenční poplatky za software vydavatele odděleně od poplatků za infrastrukturu Azure. Poplatky za infrastrukturu Azure se řídí použitím softwaru vydavatele.

- **SaaS aplikace**: musí se jednat o víceklientské řešení, použít [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pro ověřování a integrovat s [rozhraními API pro plnění SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). Využití infrastruktury Azure se spravuje a fakturuje přímo vám (vydavatel), takže musíte mít poplatky za využití infrastruktury Azure a licenční poplatky za software jako jednu položku nákladů. Podrobné pokyny najdete v tématu [plánování nabídky SaaS pro komerční tržiště](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Soukromé plány

Pro nabídku můžete vytvořit soukromý plán, který se dokončí vyjednáním, cenami specifickými pro konkrétní práci nebo vlastními konfiguracemi.

Soukromé plány umožňují poskytovat vyšší nebo nižší ceny konkrétním zákazníkům než k veřejně dostupné nabídce. Soukromé plány lze použít k zlevnění nebo přidání prémií do nabídky. Soukromé plány je možné zpřístupnit jednomu nebo více zákazníkům uvedením předplatného Azure na úrovni plánu.

## <a name="next-steps"></a>Další kroky

- Seznamte se se vzorem publikování podle online obchodu s příklady, jak vaše řešení mapuje typ a konfiguraci nabídky.
- [Průvodce publikováním podle typu nabídky](publisher-guide-by-offer-type.md)
