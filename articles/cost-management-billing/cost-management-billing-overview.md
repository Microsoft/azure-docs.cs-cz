---
title: Přehled služby Azure Cost Management a fakturace
description: Pomocí funkcí služby Azure Cost Management a fakturace můžete provádět úlohy správy fakturace a spravovat přístup k fakturám a nákladům. Pomůžou vám také sledovat a řídit útratu v Azure a optimalizovat využití prostředků Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contentperfq2
ms.openlocfilehash: 1dca2fa938c26ba45f162fae962aec0bfaf6cef7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353030"
---
# <a name="what-is-azure-cost-management--billing"></a>Co je Azure Cost Management a fakturace?

S využitím cloudu od Microsoftu můžete výrazně zvýšit technický výkon vašich obchodních úloh. Můžete také snížit náklady a režii spojenou se správou prostředků organizace. Tato obchodní příležitost s sebou však nese riziko možného plýtvání a nedostatečné efektivity v rámci cloudových nasazení. Azure Cost Management a fakturace je sada nástrojů od Microsoftu, které pomáhají analyzovat, spravovat a optimalizovat náklady na úlohy. Tato sada pomáhá zajistit využívání výhod cloudu ve vaší organizaci.

Úlohy Azure si můžete představit jako světla v domácnosti. Když odcházíte z domu, necháváte světla zapnutá? Mohli byste snížit svůj měsíční účet za energii používáním jiných, úspornějších žárovek? Nemáte v některé místnosti více světel, než je potřeba? S využitím služby Azure Cost Management a fakturace můžete podobný myšlenkový proces uplatnit i na úlohy používané ve vaší organizaci.

S produkty a službami Azure platíte jenom za to, co využijete. S tím, jak vytváříte a používáte prostředky Azure, se vám za ně účtují poplatky. Vzhledem k tomu, jak je nasazování nových prostředků snadné, můžou náklady na úlohy bez řádné analýzy a správného monitorování výrazně vzrůst. S využitím funkcí služby Azure Cost Management a fakturace můžete:

- Provádět úlohy správy fakturace, jako je úhrada faktury
- Spravovat přístup k fakturám a nákladům
- Stáhnout data o nákladech a využití použitá k vygenerování měsíční faktury
- Proaktivně provádět analýzu dat u nákladů
- Nastavit prahové hodnoty útraty
- Identifikovat příležitosti ke změnám úloh, které můžou optimalizovat útratu

Další informace o tom, jak organizace mohou využít správu nákladů, najdete v článku, který se věnuje [osvědčeným postupům pro Azure Cost Management](./costs/cost-mgt-best-practices.md).

## <a name="understand-azure-billing"></a>Principy fakturace v Azure

Funkce fakturace v Azure slouží ke kontrole fakturovaných nákladů a ke správě přístupu k fakturačním údajům. Ve velkých organizacích se o fakturační úlohy obvykle starají zásobovací a finanční týmy.

Když se zaregistrujete do Azure, vytvoří se vám fakturační účet. Ten slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Může to být třeba v situaci, kdy se zaregistrujete do Azure, abyste mohli pracovat na svých osobních projektech, takže budete mít individuální předplatné Azure s fakturačním účtem, ale současně máte přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý scénář byste měli samostatný fakturační účet.

### <a name="billing-accounts"></a>Fakturační účty

Web Azure Portal aktuálně podporuje následující typy fakturačních účtů:

- **Program MOSP (Microsoft Online Services Program):** Individuální fakturační účet pro program MOSP (Microsoft Online Services Program) se vytvoří, když se zaregistrujete do Azure prostřednictvím webu Azure. Když si například zaregistrujete bezplatný účet Azure, účet s průběžnými platbami nebo jste předplatitelem sady Visual Studio.

- **Smlouva Enterprise:** Fakturační účet pro smlouvu Enterprise se vytvoří, když vaše organizace uzavře smlouvu Enterprise (EA) na používání Azure.

- **Smlouva se zákazníkem Microsoftu:** Fakturační účet pro smlouvu se zákazníkem Microsoftu se vytvoří tehdy, když vaše organizace uzavře smlouvu se zákazníkem Microsoftu prostřednictvím zástupce Microsoftu. Fakturační účet pro smlouvu se zákazníkem Microsoftu můžou mít i někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují účet s průběžnými platbami nebo upgradují svůj bezplatný účet Azure.

### <a name="scopes-for-billing-accounts"></a>Rozsahy pro fakturační účty
Obor je uzel v rámci fakturačního účtu, pomocí kterého zobrazujete a spravujete fakturaci. Právě tady spravujete fakturační údaje, platby a faktury a provádíte všeobecnou správu účtu.

#### <a name="microsoft-online-services-program"></a>Program MOSP (Microsoft Online Services Program)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet     | Představuje jednoho vlastníka (správce účtu) pro jedno nebo více předplatných Azure. Správce účtu má oprávnění provádět různé úkony spojené s fakturací, jako je vytváření předplatných, zobrazení faktur nebo změna fakturace pro předplatná.  |
|Předplatné     |  Představuje seskupení prostředků Azure. Faktura se vygeneruje v oboru předplatného. Má vlastní způsoby platby, pomocí kterých se hradí příslušné faktury.|

#### <a name="enterprise-agreement"></a>Smlouva Enterprise

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet    | Představuje registraci smlouvy Enterprise. Faktura se vygeneruje v oboru fakturačního účtu. Je strukturovaná na základě oddělení a registračních účtů.  |
|Oddělení     |  Volitelné seskupení registračních účtů.      |
|Registrační účet     |  Představuje vlastníka jednoho účtu. Předplatná Azure se vytvářejí v rámci oboru registračního účtu.  |

#### <a name="microsoft-customer-agreement"></a>Smlouva se zákazníkem Microsoftu

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje zákaznickou smlouvu na více produktů a služeb Microsoftu. Fakturační účet je strukturovaný pomocí fakturačních profilů a oddílů faktury.   |
|Fakturační profil     |  Představuje fakturu a související způsoby platby. V tomto rozsahu se generují faktury. Fakturační profil může obsahovat více oddílů faktury.      |
|Oddíl faktury     |   Představuje skupinu nákladů na faktuře. K oboru oddílu faktury jsou přidružená předplatná a další nákupy.    |

## <a name="understand-azure-cost-management"></a>Principy správy nákladů v Azure

Přestože spolu souvisejí, fakturace se od správy nákladů liší. Fakturace je proces, při kterém vystavujete faktury za zboží a služby zákazníkům a spravujete komerční vztahy.

Pomocí pokročilých analýz služba Cost Management zobrazuje schémata nákladů a využití na úrovni organizace. Sestavy ve službě Cost Management ukazují náklady na základě využití pro služby Azure a nabídky třetích stran z Marketplace. Náklady jsou založené na vyjednaných cenách a zohledňují slevy za rezervace a Zvýhodněné hybridní využití Azure. Společně tyto sestavy zobrazují interní a externí náklady na využití a poplatky za Azure Marketplace. Ostatní poplatky, například za nákupy rezervací, podporu a daně, se zatím v sestavách nezobrazují. Tyto sestavy vám pomohou vyznat se v útratách a využití prostředků a zjistit neobvyklé výdaje. K dispozici máte také prediktivní analýzu. Služba Cost Management využívá skupiny pro správu, rozpočty a doporučení Azure, aby přehledně zobrazila, jak máte uspořádány výdaje a jak byste mohli snížit náklady.

K automatizaci exportu můžete využít web Azure Portal nebo různá rozhraní API, abyste mohli integrovat data nákladů s externími systémy a procesy. K dispozici máte také automatizovaný export dat fakturace a naplánované sestavy.

Rychlý přehled o tom, jak vám funkce správy nákladů v Azure můžou pomoct ušetřit v Azure peníze, najdete ve videu s přehledem správy nákladů v Azure. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Plánování a řízení nákladů

Mezi způsoby, jak vám služba Cost Management pomáhá plánovat a řídit náklady, patří: analýza nákladů, rozpočty, doporučení a exportování údajů pro správu nákladů.

Prostřednictvím analýzy nákladů můžete prozkoumat a analyzovat výdaje organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit souhrnné náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu.

Rozpočty pomáhají plánovat a plnit finanční odpovědnost v organizaci. Pomáhají vám, abyste nepřekročili mezní hodnoty nebo limity nákladů. Prostřednictvím rozpočtů také můžete informovat ostatní o jejich útratách a proaktivně tak spravovat náklady. Díky nim se také můžete podívat na to, jak se útraty během času vyvíjejí.

Doporučení znázorňují, jak můžete optimalizovat a zvýšit efektivitu pomocí identifikace nečinných a nedostatečně využitých prostředků. Mohou také zobrazit levnější možnosti prostředků. Pokud se budete řídit doporučeními, můžete změnit způsob, jakým využíváte prostředky, a ušetřit tak peníze. Pokud chcete postupovat podle doporučení, nejprve si zobrazte doporučení k optimalizaci nákladů a podívejte se na možné neefektivní využití prostředků. Dále změňte využití prostředku Azure na nákladově efektivnější možnost. Potom ověřte akci, abyste měli jistotu, že provedená změna je úspěšná.

Pokud pro přístup k datům správy nákladů nebo jejich kontrole používáte externí systémy, můžete z Azure data snadno exportovat. Můžete také naplánovat denní export ve formátu CSV a soubory dat ukládat v úložišti Azure. Potom budete mít přístup k datům v externím systému.

### <a name="cloudyn-deprecation"></a>Vyřazení služby Cloudyn z provozu

Cloudyn je služba Azure související s Cost Managementem, do konce roku 2020 vyřazuje z provozu. Stávající funkce Cloudynu se tam, kde je to možné, integrují přímo do webu Azure Portal. V tuto chvíli se neonboardují žádní noví zákazníci, ale podpora pro tento produkt zůstane zachovaná, dokud se úplně nevyřadí z provozu.
 
### <a name="additional-azure-tools"></a>Další nástroje Azure

Azure má další nástroje, které nejsou součástí sady funkcí služby Azure Cost Management a fakturace. Hrají ale důležitou roli v procesu správy nákladů. Další informace o těchto nástrojích si můžete přečíst po kliknutí na následující odkazy.

- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/) – tento nástroj slouží k odhadování počátečních nákladů na cloud.
- [Azure Migrate](../migrate/migrate-services-overview.md) – umožňuje zhodnotit aktuální úlohy datacentra a zjistit, co je třeba využít z náhradního řešení Azure.
- [Azure Advisor](../advisor/advisor-overview.md) – umožňuje identifikovat nevyužité virtuální počítače a získat doporučení k nákupům rezervovaných instancí Azure.
- [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) – umožňuje využít aktuální místní licence k Windows Serveru nebo SQL Serveru pro virtuální počítače v Azure a ušetřit tak peníze.

## <a name="next-steps"></a>Další kroky

Seznámili jste se se službou Cost Management a fakturací, takže dalším krokem je začít tuto službu využívat.

- Začněte s použitím služby Cost Management k [analýze nákladů](./costs/quick-acm-cost-analysis.md).
- Můžete si také přečíst další informace o [osvědčených postupech pro Azure Cost Management](./costs/cost-mgt-best-practices.md).