---
title: Přehled Azure Cost Management a fakturace | Microsoft Docs
description: Pomocí funkcí Azure Cost Management a fakturace můžete provádět úlohy správy fakturace a spravovat přístup k fakturaci na náklady. Také můžete využít funkci pro monitorování a řízení útraty Azure a optimalizaci využití prostředků Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/20/2019
ms.topic: overview
ms.service: cost-management-billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: cadff1d83a8b47a540efe9b74ffaf6de171138b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987513"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Co je Azure Cost Management a fakturace?

S produkty a službami Azure platíte jenom za to, co využijete. Při vytváření a používání prostředků Azure se vám budou účtovat prostředky. Pomocí funkcí Azure Cost Management a fakturace můžete provádět úlohy správy fakturace a spravovat přístup k fakturaci na náklady. I jeho funkce pro monitorování a řízení útraty Azure a optimalizaci využití prostředků Azure.

## <a name="understand-azure-billing"></a>Vysvětlení fakturace Azure

Funkce fakturace Azure slouží ke kontrole fakturovaných nákladů a ke správě přístupu k fakturačním údajům. Ve větších organizacích týmy pro zadávání a financování obvykle provádějí fakturační úkoly.

Fakturační účet se vytváří při registraci k Azure. Ten slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Může to být třeba v situaci, kdy se zaregistrujete do Azure, abyste mohli pracovat na svých osobních projektech, Takže můžete mít individuální předplatné Azure s fakturačním účtem. ale současně máte přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý scénář byste měli mít samostatný fakturační účet.

### <a name="billing-accounts"></a>Fakturační účty

Azure Portal aktuálně podporuje následující typy fakturačních účtů:

- **Program Microsoft Online Services**: při registraci do Azure na webu Azure se vytvoří samostatný fakturační účet pro program Microsoft Online Services. Může to být třeba tehdy, když si zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo účet pro [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Smlouva Enterprise**: fakturační účet pro smlouva Enterprise se vytvoří, když vaše organizace podepíše [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) , aby používala Azure.

- **Smlouva o zákaznících Microsoftu**: fakturační účet pro smlouvu o zákaznících Microsoftu se vytvoří, když vaše organizace spolupracuje s zástupcem Microsoftu k podepsání smlouvy o zákaznících Microsoftu. Fakturační účet pro smlouvu se zákazníkem Microsoftu můžou mít i někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo upgradují svůj [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/). Další informace najdete v tématu [Začínáme s fakturačními účty pro smlouvu se zákazníkem Microsoftu](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Rozsahy pro fakturační účty
Obor je uzel ve fakturačním účtu, který používáte k zobrazení a správě fakturace. Je tam, kde spravujete fakturační údaje, platby, faktury a provedete obecnou správu účtů.

#### <a name="microsoft-online-services-program"></a>Program MOSP (Microsoft Online Services Program)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet     | Představuje jednoho vlastníka (správce účtu) pro jedno nebo více předplatných Azure. Správce účtu má oprávnění provádět různé úkony spojené s fakturací, jako je vytváření předplatných, zobrazení faktur nebo změna fakturace pro předplatná.  |
|Předplatné     |  Představuje seskupení prostředků Azure. Faktura se vygeneruje v oboru předplatného. Má vlastní způsoby platby, pomocí kterých se hradí příslušné faktury.|


#### <a name="enterprise-agreement"></a>Smlouva Enterprise

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet    | Představuje registraci smlouvy Enterprise. Faktura se vygeneruje v oboru fakturačního účtu. Je strukturované pomocí oddělení a registračních účtů.  |
|Oddělení     |  Volitelné seskupení registračních účtů.      |
|Registrační účet     |  Představuje vlastníka jednoho účtu. Předplatná Azure se vytvářejí v oboru účtu pro zápis.  |


#### <a name="microsoft-customer-agreement"></a>Smlouva se zákazníkem Microsoftu

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje zákaznickou smlouvu na více produktů a služeb Microsoftu. Fakturační účet je strukturovaný pomocí profilů fakturace a oddílů fakturace.   |
|Fakturační profil     |  Představuje fakturu a související způsoby platby. V tomto rozsahu se generují faktury. Fakturační profil může mít více oddílů faktury.      |
|Oddíl faktury     |   Představuje skupinu nákladů na faktuře. Předplatná a další nákupy jsou přidruženy k oboru oddílu faktury.    |


## <a name="understand-azure-cost-management"></a>Pochopení Azure Cost Management
Správa nákladů je proces, při kterém efektivně plánujete a řídíte náklady své firmy. Úlohy správy nákladů obvykle provádějí finanční týmy, týmy správy účtů a týmy aplikací. Azure Cost Management + faktura pomáhá organizacím plánovat s ohledem na náklady. Pomůže vám také efektivně analyzovat náklady a provádět opatření k optimalizaci útraty cloudu. Další informace o tom, jak organizace mohou využít správu nákladů, najdete v článku, který se věnuje [osvědčeným postupům pro Azure Cost Management](./costs/cost-mgt-best-practices.md).

Podívejte se na [video s přehledem Azure cost management](https://www.youtube.com/watch?v=el4yN5cHsJ0) , kde najdete rychlý přehled toho, jak vám Azure cost management můžou ušetřit peníze v Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Přestože spolu souvisejí, fakturace se od správy nákladů liší. Fakturace je proces, při kterém vystavujete faktury za zboží a služby zákazníkům a spravujete komerční vztahy.

Pomocí pokročilých analýz služba Cost Management zobrazuje schémata nákladů a využití na úrovni organizace. Sestavy v Cost Management zobrazují náklady založené na využívání využívané službami Azure a nabídkami na webu Marketplace třetích stran. Náklady jsou založené na sjednaných cenách a faktorech v rezervacích a Zvýhodněné hybridní využití Azure slevách. Společně tyto sestavy zobrazují interní a externí náklady na využití a poplatky za Azure Marketplace. Ostatní poplatky, například za nákupy rezervací, podporu a daně, se zatím v sestavách nezobrazují. Tyto sestavy vám pomohou vyznat se v útratách a využití prostředků a zjistit neobvyklé výdaje. K dispozici máte také prediktivní analýzu. Služba Cost Management využívá skupiny pro správu, rozpočty a doporučení Azure, aby přehledně zobrazila, jak máte uspořádány výdaje a jak byste mohli snížit náklady.

K automatizaci exportu můžete využít web Azure Portal nebo různá rozhraní API, abyste mohli integrovat data nákladů s externími systémy a procesy. K dispozici máte také automatizovaný export dat fakturace a naplánované sestavy.

### <a name="plan-and-control-expenses"></a>Plánování a řízení nákladů

Mezi způsoby, které Cost Management vám pomůžou plánovat a kontrolovat náklady, patří: analýza nákladů, rozpočty, doporučení a export dat správy nákladů.

Prostřednictvím analýzy nákladů můžete prozkoumat a analyzovat výdaje organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit souhrnné náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu.

Rozpočty pomáhají plánovat a plnit finanční odpovědnost v organizaci. Pomáhají vám, abyste nepřekročili mezní hodnoty nebo limity nákladů. Prostřednictvím rozpočtů také můžete informovat ostatní o jejich útratách a proaktivně tak spravovat náklady. Díky nim se také můžete podívat na to, jak se útraty během času vyvíjejí.

Doporučení znázorňují, jak můžete optimalizovat a zvýšit efektivitu pomocí identifikace nečinných a nedostatečně využitých prostředků. Mohou také zobrazit levnější možnosti prostředků. Pokud se budete řídit doporučeními, můžete změnit způsob, jakým využíváte prostředky, a ušetřit tak peníze. Pokud chcete postupovat podle doporučení, nejprve si zobrazte doporučení k optimalizaci nákladů a podívejte se na možné neefektivní využití prostředků. Dále změňte využití prostředku Azure na nákladově efektivnější možnost. Potom ověřte akci, abyste měli jistotu, že provedená změna je úspěšná.

Pokud pro přístup k datům správy nákladů nebo jejich kontrole používáte externí systémy, můžete z Azure data snadno exportovat. Můžete také naplánovat denní export ve formátu CSV a soubory dat ukládat v úložišti Azure. Potom budete mít přístup k datům v externím systému.

### <a name="consider-cloudyn"></a>Zvážení využití služby Cloudyn

[Cloudyn](./cloudyn/overview.md) je služba Azure, která souvisí se službou Cost Management. Pomocí služby Cloudyn můžete sledovat využití cloudu a výdaje na prostředky Azure. Podporuje také další poskytovatele cloudu, včetně AWS a Googlu. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. V současné době služba Cost Management nepodporuje metody showback a chargeback ani jiné poskytovatele cloudových služeb. Cloudyn je však možnost, která je _podporuje_. V současné době Cost Management nepodporuje účty poskytovatele služeb Microsoft Cloud (CSP), ale Cloudyn. Pokud máte účty CSP, nebo pokud chcete používat showback/vrácení peněz, můžete k usnadnění správy nákladů využít Cloudyn.

Podívejte se na [video Azure cost management a Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) , kde můžete zobrazit doporučení v závislosti na vašich obchodních potřebách, pokud používáte buď Azure cost management nebo Cloudyn.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>Další nástroje Azure

Azure obsahuje další nástroje, které nejsou součástí Azure Cost Management a funkce pro fakturaci. Hrají ale důležitou roli v procesu správy nákladů. Další informace o těchto nástrojích si můžete přečíst po kliknutí na následující odkazy.

- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/) – tento nástroj slouží k odhadování počátečních nákladů na cloud.
- [Azure Migrate](../migrate/migrate-overview.md) – umožňuje zhodnotit aktuální úlohy datacentra a zjistit, co je třeba využít z náhradního řešení Azure.
- [Azure Advisor](../advisor/advisor-overview.md) – umožňuje identifikovat nevyužité virtuální počítače a získat doporučení k nákupům rezervovaných instancí Azure.
- [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) – umožňuje využít aktuální místní licence k Windows Serveru nebo SQL Serveru pro virtuální počítače v Azure a ušetřit tak peníze.


## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s Cost Management a fakturací, je dalším krokem začít službu používat.

- Začněte s použitím služby Cost Management k [analýze nákladů](./costs/quick-acm-cost-analysis.md).
- Můžete si také přečíst další informace o [osvědčených postupech pro Azure Cost Management](./costs/cost-mgt-best-practices.md).
