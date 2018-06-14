---
title: Přehled služby Azure plán služby App Service | Microsoft Docs
description: Zjistěte, jak plánů služby App Service pro pracovní Azure App Service a jak budou využívat vaše prostředí pro správu.
keywords: služby App service, služby azure app service, měřítko, škálovatelná, škálovatelnost, plán služby app service, náklady na aplikaci služby
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
ms.openlocfilehash: 268844eae8dc06937529e79d52515cad2f6da3f4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862355"
---
# <a name="azure-app-service-plan-overview"></a>Přehled služby Azure plán služby App Service

Ve službě App Service spouští aplikace _plán služby App Service_. Plán služby App Service definuje sadu výpočetní prostředky pro spuštění webové aplikace. Tyto výpočetní prostředky jsou obdobou [ _serverové farmy_ ](https://wikipedia.org/wiki/Server_farm) v hostování konvenční webů. Jeden nebo více aplikací může být nakonfigurována pro spuštění na stejné výpočetní prostředky (nebo stejný plán služby App Service).

Když vytvoříte plán služby App Service v určité oblasti (například západní Evropa), je pro tento plán v této oblasti vytvořit sadu výpočetní prostředky. Ať aplikace vložíte do tento plán služby App Service, spusťte na tyto výpočetní prostředky podle definice plán služby App Service. Definuje každý plán služby App Service:

- Oblast (západní USA, Východ USA, atd.)
- Počet instancí virtuálního počítače
- Velikost instance virtuálních počítačů (malé, střední, velké)
- Cenová úroveň (Free, sdílené, Basic, Standard, Premium, PremiumV2, izolovaná, spotřeba)

_Cenová úroveň_ služby App Service určuje plán, jaké funkce služby App Service můžete získat a kolik platíte plánu. Existuje několik kategorií cenové úrovně:

- **Sdílené výpočetní**: **volné** a **sdílené**, dva základní úrovně, spustí na aplikaci na stejný virtuální počítač Azure jako ostatní aplikace služby App Service, včetně aplikace od ostatních zákazníků. Tyto úrovně přidělení procesoru kvóty pro každou aplikaci, která běží na sdílené prostředky a prostředky nelze škálovat.
- **Vyhrazený výpočetní**: **základní**, **standardní**, **Premium**, a **PremiumV2** vrstev spouštět aplikace na vyhrazené Azure Virtuální počítače. Jenom aplikace ve stejném plán služby App Service sdílet stejnou výpočetní prostředky. Čím vyšší vrstvě, další instance virtuálních počítačů jsou dostupné pro Škálováním na více systémů.
- **Izolované**: Tato úroveň běží vyhrazených virtuálních počítačích Azure na vyhrazených virtuálních sítí Azure, která poskytuje izolace sítě nad výpočetní izolace pro vaše aplikace. Poskytuje maximální možnosti škálování.
- **Spotřeba**: Tato úroveň je k dispozici pouze [funkce aplikace](../azure-functions/functions-overview.md). Se škáluje funkce dynamicky v závislosti na zatížení. Další informace najdete v tématu [hostování na Azure Functions plány porovnání](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Každá úroveň také poskytuje určitou podmnožinu funkcí služby App Service. Tyto funkce patří vlastní domény a certifikáty protokolu SSL, automatické škálování, nasazovací sloty, zálohování, integrace Traffic Manager a další. Čím vyšší vrstvě, jsou k dispozici další funkce. Funkce, které jsou podporovány v jednotlivých cenových úrovní najdete v tématu [podrobnosti plánu služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Nové **PremiumV2** cenová úroveň nabízí [virtuální počítače Dv2-series](../virtual-machines/windows/sizes-general.md#dv2-series) s rychlejších procesorů, úložiště SSD a dvojité poměr paměti jádra ve srovnání s **standardní** vrstvy. **PremiumV2** také podporuje škálování na větší rozsah prostřednictvím počet vyšší instancí současně stále zajišťuje rozšířené možnosti v standardní plán nalezen. Všechny funkce, které jsou k dispozici v existující **Premium** jsou součástí vrstvy **PremiumV2**.
>
> Podobně jako u jiných vyhrazené vrstev, tři velikosti virtuálních počítačů jsou k dispozici pro tuto vrstvu:
>
> - Malá (jádrech procesoru, paměti 3.5 GiB) 
> - Střední (dva jader procesoru, paměti 7 GiB) 
> - Velké (čtyři jader procesoru, paměti 14 GiB)  
>
> Pro **PremiumV2** informace o cenách najdete v části [App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/).
>
> Začněte s novým **PremiumV2** cenovou úroveň, najdete v části [konfigurace PremiumV2 úroveň služby App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak aplikace my spouštění a škálování?

V **volné** a **sdílené** vrstvy, aplikace přijímá minut využití procesoru na instanci sdíleného virtuálního počítače a nelze škálovat. Na jiných úrovních aplikace běží a škáluje následujícím způsobem.

Když vytvoříte aplikaci v App Service, je uvést do plán služby App Service. Při spuštění aplikace běží na všechny instance virtuálních počítačů nakonfigurované v plánu služby App Service. Pokud se více aplikací jsou ve stejné plán služby App Service, všechny sdílejí stejné instance virtuálních počítačů. Pokud máte více nasazovací sloty pro aplikaci, všechny nasazovací sloty spustit také na stejné instance virtuálních počítačů. Pokud povolení diagnostických protokolů, zálohování nebo spuštění webové úlohy, také používají cyklů procesoru a paměti na těchto instancích virtuálních počítačů.

Tímto způsobem je plán služby App Service má jednotka škálování aplikace služby App Service. Pokud plán je nakonfigurována pro spuštění pět instancí virtuálních počítačů, spusťte na všech instancích pět všechny aplikace v plánu. Pokud plán je nakonfigurován pro automatické škálování, pak všechny aplikace v plánu jsou společně škálovat podle nastavení automatického škálování.

Informace o horizontálním navýšení kapacity aplikace naleznete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Kolik Moje plán služby App Service stojí?

Tato část popisuje, jak se účtují aplikací App Service. Oblast, podrobné informace o cenách, najdete v části [App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/).

S výjimkou **volné** vrstvy, plán služby App Service představuje hodinové poplatků na výpočetní prostředky používá.

- V **sdílené** vrstvy, každá aplikace obdrží kvótu procesoru minut, takže _každou aplikaci_ je účtovat každou hodinu kvóty pro procesor.
- V vyhrazenou výpočetní vrstvy (**základní**, **standardní**, **Premium**, **PremiumV2**), plán služby App Service definuje počet virtuálních počítačů instance aplikace jsou škálovat, tak _každá instance virtuálních počítačů_ ve službě App Service má plán hodinové poplatků. Tyto instance virtuálního počítače budou účtovat na nich běží stejný bez ohledu na to jak velký počet aplikací. Abyste se vyhnuli neočekávané poplatky, najdete v části [vyčištění plán služby App Service](app-service-plan-manage.md#delete).
- V **izolovaná** vrstvy, App Service Environment definuje počet izolované pracovních procesů, které spouští vaše aplikace a _každý pracovní_ je účtován každou hodinu. Kromě toho je hodinové základní poplatek pro spuštění služby App Service Environment sám sebe. 
- (Pouze azure funkce) **Spotřeba** vrstvy dynamicky přiděluje instance virtuálních počítačů do aplikaci funkce zatížení služby a je dynamicky poplatky za sekundu z Azure. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Nemáte získat účtovat poplatek za použití funkce služby App Service, které jsou k dispozici (Konfigurace vlastní domény, certifikátů SSL, nasazovací sloty, záloh atd.). Výjimky jsou:

- Aplikace služby domény – platí při nákupu v Azure a při obnovení se každý rok.
- Certifikáty App Service – platí při nákupu v Azure a při obnovení se každý rok.
- Na základě IP připojení SSL - zde je poplatek za hodinové pro každé připojení založená na protokolu IP, ale některé **standardní** úroveň, nebo vyšší poskytuje jedno připojení založená na IP zdarma. Na základě SNI připojení SSL jsou volné.

> [!NOTE]
> Pokud integrujete se jiná služba Azure App Service, musíte vzít v úvahu poplatky z těchto jiných služeb. Například pokud používáte Azure Traffic Manager geograficky, škálovat aplikace Azure Traffic Manager také poplatky vám na základě vašeho využití. Odhad vaše náklady cross-services v Azure, najdete v tématu [cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co když aplikace potřebuje další možnosti nebo funkce?

Plán služby App Service můžete kdykoli škálovat nahoru a dolů. Je stejně jednoduché jako při změně cenové úrovně plánu. Můžete vybrat nižší cenovou úroveň na první a škálování později Pokud potřebujete další funkce služby App Service.

Například můžete spustit testování vaší webové aplikace v **volné** služby App Service plánování a nic platit. Pokud chcete přidat vaše [vlastní název DNS](app-service-web-tutorial-custom-domain.md) do webové aplikace právě škálovat plán až **sdílené** vrstvy. Když chcete přidat později [vlastní certifikát SSL](app-service-web-tutorial-custom-ssl.md), škálovat plán až **základní** vrstvy. Pokud chcete mít [přípravná prostředí](web-sites-staged-publishing.md), škálovat až **standardní** vrstvy. Pokud potřebujete více jader, pamětí nebo úložištěm, škálování na větší velikost virtuálního počítače ve stejné vrstvě.

Stejné funguje v opačném pořadí. Pokud si myslíte, že už potřebovat možnosti nebo funkce vyšší úroveň, je možné škálovat na nižší úroveň, která jste úsporu nákladů.

Informace o vertikálním navýšení kapacity plán služby App Service najdete v tématu [škálování aplikace v Azure](web-sites-scale.md).

Pokud je vaše aplikace ve stejném plán služby App Service s jinými aplikacemi, můžete zlepšit výkon aplikace tak, že izoluje výpočetní prostředky. Můžete provést přesunutím aplikace do samostatné plán služby App Service. Další informace najdete v tématu [přesuňte aplikace na jiný plán aplikační služby](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Mají vloženy aplikace v plánu nové nebo existující plán?

Vzhledem k tomu, že platíte za výpočetní prostředky plán služby App Service přiděluje (najdete v části [kolik nemá Moje náklady na plán služby App Service?](#cost)), může potenciálně ušetřit peníze umístěním více aplikací do jednoho plánu služby App Service. Můžete přidat do existujícího plánu aplikace, tak dlouho, dokud plán nemá dostatek prostředků pro zpracování zátěže. Ale mějte na paměti, že aplikace ve stejném plán služby App Service, že všechny sdílet stejný výpočetní prostředky. Chcete-li zjistit, že jestli nové aplikace má potřebné prostředky, je potřeba pochopit kapacitu existující plán služby App Service a očekávané zatížení pro novou aplikaci. Přetížení plán služby App Service může potenciálně způsobit výpadky v nových nebo stávajících aplikací.

Isolate vaší aplikace do nové služby App Service při plánování:

- Tato aplikace je náročná.
- Chcete škálovat aplikaci nezávisle z jiných aplikací existující plán.
- Aplikace musí prostředků v jiné zeměpisné oblasti.

Tímto způsobem můžete přidělit novou sadu prostředků pro vaši aplikaci a získáte větší kontrolu nad vaší aplikace.

## <a name="manage-an-app-service-plan"></a>Spravovat plán služby App Service

> [!div class="nextstepaction"]
> [Spravovat plán služby App Service](app-service-plan-manage.md)
