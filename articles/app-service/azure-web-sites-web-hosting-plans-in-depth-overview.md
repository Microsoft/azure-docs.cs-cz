---
title: Plánu služby App Service – přehled – Azure | Dokumentace Microsoftu
description: Zjistěte, jak plány služby App Service pro práci na službě Azure App Service a jaké jsou výhody prostředí pro správu.
keywords: služby App service, služby azure app service, škálování, škálovatelné, škálovatelnost, plán služby app service, náklady služby app service
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9cab3a8d9fd85d19cc2cc11874085d41d2441a07
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253931"
---
# <a name="azure-app-service-plan-overview"></a>Přehled plánu služby Azure App Service

Ve službě App Service se aplikace spouští v rámci _plánu služby App Service_. Plán služby App Service definuje sadu výpočetních prostředků pro provozování webové aplikace. Tyto výpočetní prostředky, které se podobá [ _serverovou farmu_ ](https://wikipedia.org/wiki/Server_farm) v konvenční webhosting. Jeden nebo více aplikace je možné nakonfigurovat pro spuštění ve stejné výpočetní prostředky (nebo v rámci stejného plánu služby App Service).

Když vytvoříte plán služby App Service v určité oblasti (například, západní Evropa), sada výpočetní prostředky se vytvoří pro tento plán v dané oblasti. Stačí vložit do tohoto plánu služby App Service, spusťte na tyto aplikace výpočetní prostředky podle vašeho plánu služby App Service. Každý plán služby App Service definuje:

- Oblast (USA – Západ, USA – východ, atd.)
- Počet instancí virtuálních počítačů
- Velikosti instancí virtuálních počítačů (malá, střední, velká)
- Cenová úroveň (Free, Shared, Basic, Standard, Premium, PremiumV2, izolované, využití)

_Cenovou úroveň_ App Service plán Určuje, jaké funkce služby App Service můžete získat a kolik platíte za plán. Existuje několik kategorií cenové úrovně:

- **Sdílené výpočetní**: **Bezplatné** a **Shared**, dva základní úrovně, spustí aplikaci pro stejný virtuální počítač Azure jako ostatní aplikace služby App Service, včetně aplikací od ostatních zákazníků. Tyto úrovně přidělení procesoru kvóty pro každou aplikaci, která se spouští na sdílených prostředcích a prostředky nelze škálovat na více systémů.
- **Vyhrazené výpočetní**: **Základní**, **standardní**, **Premium**, a **PremiumV2** úrovně spouštění aplikací ve vyhrazených virtuálních počítačích Azure. Jenom aplikace v rámci stejného plánu služby App Service sdílejí stejnou výpočetní prostředky. Čím vyšší úrovně, dalších instancí virtuálních počítačů jsou k dispozici pro horizontální navýšení kapacity.
- **Izolované**: Tato úroveň běží na vyhrazených virtuálních sítí Azure, která poskytuje izolaci sítě nad rámec izolace výpočetní prostředky pro vaše aplikace vyhrazených virtuálních počítačích Azure. Poskytuje maximální možnostmi škálování.
- **Spotřeba**: Tato úroveň je dostupná jenom pro [aplikace function](../azure-functions/functions-overview.md). Škáluje se funkce dynamicky v závislosti na úloze. Další informace najdete v tématu [porovnání plánů hostování služby Azure Functions](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Každá úroveň také poskytuje určitou podmnožinu funkcí služby App Service. Tyto funkce patří vlastní domény a certifikáty SSL, automatické škálování, sloty nasazení, zálohování, integrace Traffic Manageru a informace. Čím vyšší úrovně, víc funkcí jsou k dispozici. Které funkce jsou podporovány v jednotlivých cenových úrovní najdete v tématu [podrobnosti plánu služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Nové **PremiumV2** cenová úroveň poskytuje [virtuální počítače řady Dv2-series](../virtual-machines/windows/sizes-general.md#dv2-series) s rychlejšími procesory, SSD úložištěm a double poměr paměti k jádrům ve srovnání s **standardní** vrstvy. **PremiumV2** také podporuje škálování ve větším měřítku prostřednictvím vyššího počtu instancí současně stále poskytuje veškeré pokročilé možnosti plánu Standard. Všechny funkce dostupné v existujícím **Premium** vrstvy jsou součástí **PremiumV2**.
>
> Podobně jako u jiných vyhrazené vrstev, tři velikosti virtuálních počítačů jsou k dispozici pro tuto úroveň:
>
> - Malý (jedno Procesorové jádro, 3,5 GB paměti) 
> - Střední (dvě procesoru jádra, 7 GB paměti) 
> - Velké (čtyři procesoru jádra, 14 GB paměti)  
>
> Pro **PremiumV2** informace o cenách najdete v článku [App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/).
>
> Abyste mohli začít s novým **PremiumV2** cenovou úroveň, naleznete v tématu [úrovně PremiumV2 konfigurace pro službu App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak probíhá Moje aplikace spustit a škálovat?

V **Free** a **Shared** úrovně, aplikace obdrží CPU minut na sdílené instance virtuálního počítače a nelze škálovat na více systémů. V jiných vrstev aplikace běží a následujícím způsobem se škáluje.

Při vytváření aplikace ve službě App Service, přejde do plánu služby App Service. Při spuštění aplikace poběží v instancích virtuálních počítačů nakonfigurované v plánu služby App Service. Pokud více aplikacemi v rámci stejného plánu služby App Service, všichni sdílejí stejné instance virtuálních počítačů. Pokud máte několik nasazovacích slotů pro aplikaci pro všechny sloty nasazení spustit také na stejných instancích virtuálního počítače. Pokud se povolení diagnostických protokolů, zálohování nebo spustit WebJobs, používají cyklů procesoru a paměti na těchto instancí virtuálních počítačů.

Tímto způsobem plán služby App Service je jednotka škálování aplikací služby App Service. Pokud je plán konfigurován pro běh pěti instancemi virtuálních počítačů, všechny aplikace v plánu spustit na všech pět instancí. Pokud tento plán je nakonfigurovaná pro automatické škálování, všechny aplikace v plánu jsou společně škálovat podle nastavení automatického škálování.

Informace o horizontálním navýšení kapacity aplikace naleznete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Kolik stojí Můj plán služby App Service?

Tato část popisuje, jak se účtují aplikací služby App Service. Oblast, podrobné informace o cenách najdete v části [App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/).

S výjimkou **Free** vrstvy, plán služby App Service představuje hodinovou sazbu na výpočetní prostředky, které používá.

- V **Shared** vrstvy, každé aplikaci, která přijímá kvótu minut využití procesoru, proto _každé aplikaci, která_ se účtuje po hodinách pro kvótu využití procesoru.
- Ve vyhrazené výpočetní úrovně (**základní**, **standardní**, **Premium**, **PremiumV2**), plán služby App Service definuje počet virtuálních počítačů instance aplikace se upraví tak, aby _jednotlivých instancí virtuálních počítačů_ ve službě App Service má plán hodinovou sazbu. Tyto instance virtuálních počítačů se účtují stejný bez ohledu na tom, kolik aplikace běží na nich. Chcete-li se vyhnout neočekávaným poplatkům, naleznete v tématu [vyčištění plán služby App Service](app-service-plan-manage.md#delete).
- V **izolované** vrstvy, služba App Service Environment definuje počet izolované pracovní procesy, které běží vaše aplikace a _každému pracovnímu procesu_ se účtuje po hodinách. Kromě toho je základní hodinové sazby pro spouštění služby App Service Environment samotný. 
- (Pouze azure Functions) **Spotřeby** úroveň dynamicky přiděluje instance virtuálních počítačů jsou úlohy funkce app service a účtuje se dynamicky za sekundu v Azure. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Není vám účtují poplatky pro používání funkce App Service, které jsou k dispozici (Konfigurace vlastní domény, certifikáty protokolu SSL, sloty nasazení, zálohování atd.). Výjimky jsou:

- Domény služby App Service – platíte při nákupu v Azure a kdy ho obnovíte každý rok.
- Certifikáty App Service – platíte při nákupu v Azure a kdy ho obnovíte každý rok.
- Připojení SSL na základě IP adresy – tady je hodinovou sazbu pro každé připojení SSL na základě IP adresy, ale některé **standardní** vrstvy nebo vyšší poskytuje jedno připojení založené na protokolu IP SSL zdarma. Připojení SSL na základě SNI je zdarma.

> [!NOTE]
> Pokud integrujete s jinou službou Azure App Service, budete muset vzít v úvahu účtování poplatků za tyto služby. Například pokud používáte Azure Traffic Manager umožní škálování vaší aplikace geograficky, Azure Traffic Manager také poplatky za vás na základě využití. Pro odhad nákladů napříč služby v Azure, najdete v článku [cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co když Moje aplikace potřebuje další možnosti nebo funkce?

Plán služby App Service je možné škálovat nahoru a dolů v každém okamžiku. Je stejně snadné jako změna cenové úrovně plánu. Můžete zvolit nižší cenovou úroveň na první a později navýšit Pokud potřebujete další funkce služby App Service.

Například můžete spustit testování webové aplikace **Free** služby App Service plánování a nic neplatíte. Pokud chcete přidat vaše [vlastní název DNS](app-service-web-tutorial-custom-domain.md) do webové aplikace stačí škálování plánu až **Shared** vrstvy. Později, když chcete přidat [vlastního certifikátu SSL](app-service-web-tutorial-custom-ssl.md), škálování plánu až **základní** vrstvy. Pokud chcete mít [přípravná prostředí](web-sites-staged-publishing.md), škálovat až **standardní** vrstvy. Když budete potřebovat více jader, paměť nebo úložiště, vertikálně navýšit kapacitu na větší velikost virtuálního počítače v rámci stejné úrovně.

Stejné funguje v opačném pořadí. Pokud máte pocit, že jste už budete potřebovat možnosti nebo funkce vyšší úrovně, můžete vertikálně snížit kapacitu na nižší úroveň, které vám ušetří peníze.

Informace o vertikálním navýšení kapacity plánu služby App Service najdete v tématu [vertikální navýšení kapacity aplikace v Azure](web-sites-scale.md).

Pokud je vaše aplikace v rámci stejného plánu služby App Service s jinými aplikacemi, můžete zlepšit výkon vaší aplikace tak, že izoluje výpočetní prostředky. Můžete to udělat příkazem přesunu aplikace do samostatného plánu služby App Service. Další informace najdete v tématu [přesun aplikace do jiného plánu služby App Service](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Mám umístit aplikace v plánu nové nebo existující plán?

Protože platíte za výpočetní prostředky plánu služby App Service přiděluje (naleznete v tématu [jaká je Moje náklady na plán služby App Service?](#cost)), můžete potenciálně ušetřit peníze vložením více aplikací do jednoho plánu služby App Service. Můžete pokračovat, dokud plán nemá dostatek prostředků pro zpracování zátěže přidání aplikací do existující plán. Nicméně mějte na paměti, že aplikace v rámci stejného plánu služby App Service, že všechny sdílet stejný výpočetní prostředky. Pokud chcete zjistit, jestli tato nová aplikace má potřebné prostředky, musíte pochopit existující plán služby App Service a očekávané zatížení kapacitu pro nové aplikace. Přetížení plán služby App Service může potenciálně způsobit výpadek pro nové i stávající aplikace.

Izolace při plánování vaší aplikace do nové služby App Service:

- Aplikace je náročné.
- Chcete škálovat aplikace odděleně od ostatních aplikací existující plán.
- Aplikace musí prostředku v jiné geografické oblasti.

Tímto způsobem můžete přidělit novou sadu prostředků pro vaši aplikaci a získat větší kontrolu nad svými aplikacemi.

## <a name="manage-an-app-service-plan"></a>Správa plánu služby App Service

> [!div class="nextstepaction"]
> [Správa plánu služby App Service](app-service-plan-manage.md)
