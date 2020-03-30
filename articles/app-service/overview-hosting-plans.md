---
title: Plány služby App Service
description: Zjistěte, jak plány služby App Service fungují ve službě Azure App Service, jak se účtují zákazníkovi a jak je škálovat podle vašich potřeb.
keywords: služby aplikace, azure app service, škálovatelnost, škálovatelná, škálovatelnost, plán služeb aplikace, náklady na služby aplikace
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: f1012f8c00de4b19bbf6206408ec1a806e09e54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482339"
---
# <a name="azure-app-service-plan-overview"></a>Přehled plánu služby App Service

Ve službě App Service se aplikace spouští v rámci _plánu služby App Service_. Plán služby App Service definuje sadu výpočetních prostředků pro provozování webové aplikace. Tyto výpočetní prostředky jsou obdobou [_serverové farmy_](https://wikipedia.org/wiki/Server_farm) v konvenčním webhostingu. Jednu nebo více aplikací lze nakonfigurovat tak, aby se spouštěla na stejných výpočetních prostředcích (nebo ve stejném plánu služby App Service).

Když vytvoříte plán služby App Service v určité oblasti (například v západní Evropě), vytvoří se pro tento plán v této oblasti sada výpočetních prostředků. Ať už aplikace, které vložíte do tohoto plánu služby App Service, běží na těchto výpočetních prostředcích definovaných v plánu služby App Service. Každý plán služby App Service definuje:

- Oblast (USA – západ, USA – východ atd.)
- Počet instancí virtuálních počítačů
- Velikost instancí virtuálních počítačů (malá, střední, velká)
- Cenová úroveň (zdarma, sdílená, základní, standardní, prémiová, prémiováV2, izolovaná)

_Cenová úroveň_ plánu služby App Service určuje, jaké funkce služby App Service získáte a kolik za plán zaplatíte. Kategorií cenových úrovní existuje několik:

- **Sdílené výpočetní prostředky**: **Free** and **Shared**, dvě základní vrstvy, spouští aplikaci na stejném virtuálním počítači Azure jako ostatní aplikace služby App Service, včetně aplikací jiných zákazníků. Tyto úrovně přidělují kvóty procesoru každé aplikaci, která na sdílených prostředcích běží, a u těchto prostředků není možné škálovat na více instancí.
- **Vyhrazené výpočetní prostředky:** Úrovně **Basic**, **Standard**, **Premium**a **PremiumV2** spouštějí aplikace na vyhrazených virtuálních počítačích Azure. Stejné výpočetní prostředky sdílí pouze aplikace v rámci stejného plánu služby App Service. Čím vyšší cenová úroveň, tím více instancí virtuálních počítačů můžete škálovat.
- **Izolované**: Tato úroveň spouští vyhrazené virtuální počítače Azure ve vyhrazených virtuálních sítích Azure. Poskytuje izolaci sítě nad výpočetní izolací vašich aplikací. Nabízí nejvyšší úroveň škálování na více instancí.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Každá úroveň také poskytuje konkrétní podmnožinu funkcí služby App Service. Mezi tyto funkce patří vlastní domény a certifikáty SSL, automatické škálování, sloty pro nasazení, zálohy, integrace Traffic Manageru a další. Čím vyšší je úroveň, tím více funkcí je k dispozici. Chcete-li zjistit, které funkce jsou podporovány v každé cenové úrovni, najdete v [tématu Podrobnosti o plánu služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Nová cenová úroveň **PremiumV2** poskytuje [virtuálním mům řady Dv2](../virtual-machines/dv2-dsv2-series.md) rychlejší procesory, úložiště SSD a dvojitý poměr paměti k jádru ve srovnání s úrovní **Standard.** **PremiumV2** také podporuje vyšší škálování prostřednictvím zvýšeného počtu instancí a zároveň poskytuje všechny pokročilé funkce, které se nacházejí v plánu Standard. Všechny funkce dostupné ve stávající úrovni **Premium** jsou zahrnuty v **PremiumV2**.
>
> Podobně jako u jiných úrovní vyhrazené jsou pro tuto úroveň k dispozici tři velikosti virtuálních stránek:
>
> - Malé (jedno jádro CPU, 3,5 Gb paměti) 
> - Střední (dvě jádra CPU, 7 GiB paměti) 
> - Velké (čtyři procesorová jádra, 14 Gb paměti)  
>
> Informace o cenách **PremiumV2** najdete v tématu [Ceny služby App .](https://azure.microsoft.com/pricing/details/app-service/)
>
> Pokud chcete začít s novou cenovou vrstvou **PremiumV2,** [přečtěte si téma Konfigurace úrovně PremiumV2 pro službu App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak moje aplikace běží a škáluje?

Na **volných** a **sdílených** úrovních aplikace přijímá minuty procesoru na sdílené instanci virtuálního počítače a nemůže horizontální navýšení kapacity. V jiných úrovních se aplikace spouští a škáluje následujícím způsobem.

Když vytvoříte aplikaci ve službě App Service, vloží se do plánu služby App Service. Když se aplikace spustí, běží na všech instancích virtuálních počítače nakonfigurovaných v plánu služby App Service. Pokud je ve stejném plánu služby App Service více aplikací, všechny sdílejí stejné instance virtuálních aplikací. Pokud máte více slotů nasazení pro aplikaci, všechny sloty nasazení také spustit na stejné instance virtuálních aplikací. Pokud povolíte diagnostické protokoly, provedete zálohování nebo spustíte webové úlohy, budou na těchto instancích virtuálních aplikací používat také cykly procesoru a paměť.

Tímto způsobem je plán služby App Service škálovací jednotkou aplikací služby App Service. Pokud je plán nakonfigurovaný tak, aby spouštěl pět instancí virtuálních počítače, všechny aplikace v plánu běží na všech pěti instancích. Pokud je plán nakonfigurován pro automatické škálování, pak všechny aplikace v plánu jsou škálovány společně na základě nastavení automatického škálování.

Informace o škálování aplikace najdete v tématu [Škálování počtu instancí ručně nebo automaticky](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Kolik stojí můj plán služby App Service?

Tato část popisuje, jak se aplikace služby App Service účtují. Podrobné informace o cenách specifických pro danou oblast najdete v [tématu Ceny služby App Service](https://azure.microsoft.com/pricing/details/app-service/).

S výjimkou úrovně **Free,** plán služby App Service nese hodinové poplatky na výpočetní prostředky, které používá.

- Ve **sdílené** vrstvě obdrží každá aplikace kvótu minut procesoru, takže _každá aplikace_ se účtuje každou hodinu za kvótu procesoru.
- Ve vyhrazených výpočetních úrovních **(Basic**, **Standard**, **Premium**, **PremiumV2**) definuje plán služby App Service počet instancí virtuálních aplikací, na které se aplikace přizpůsobují, takže _každá instance virtuálního počítače_ v plánu služby App Service má hodinový poplatek. Tyto instance virtuálních aplikací se účtují stejně bez ohledu na to, kolik aplikací na nich běží. Chcete-li se vyhnout [neočekávaným poplatkům,](app-service-plan-manage.md#delete)přečtěte si informace o vyčištění plánu služby App Service .
- V **izolované** vrstvě prostředí app service definuje počet izolovaných pracovníků, kteří spouštějí vaše aplikace a _každý pracovník_ se účtuje každou hodinu. Kromě toho je hodinový základní poplatek za spuštění samotného prostředí služby App Service.

Za používání funkcí služby App Service, které máte k dispozici (konfigurace vlastních domén, certifikátů SSL, slotů pro nasazení, záloh atd.), se vám neúčtují poplatky. Výjimky jsou:

- Domény služby App Service – platíte při nákupu v Azure a při jeho obnově každý rok.
- Certifikáty služby App Service – platíte při nákupu v Azure a při jeho obnově každý rok.
- Připojení SSL založená na protokolu IP – za každé připojení SSL založené na protokolu IP se účtuje hodinový poplatek, ale některé úrovně **Standard** nebo vyšší poskytují jedno připojení SSL založené na protokolu IP zdarma. Připojení SSL založené na SNI jsou zdarma.

> [!NOTE]
> Pokud integrujete službu App Service s jinou službou Azure, možná budete muset zvážit poplatky z těchto dalších služeb. Pokud například pomocí Azure Traffic Manageru geograficky škálujete aplikaci, Azure Traffic Manager vám také účtuje poplatky na základě vašeho využití. Informace o odhadu nákladů na křížové služby v Azure najdete v [tématu Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co když moje aplikace potřebuje víc funkcí nebo funkcí?

Kapacitu plánu služby App Service je možné kdykoli vertikálně navýšit nebo snížit. Je to stejně jednoduché jako změna cenové úrovně plánu. Nejprve si můžete zvolit nižší cenovou úroveň a později kapacitu vertikálně navýšit, když budete potřebovat další funkce služby App Service.

Můžete například začít testovat webovou aplikaci v plánu **bezplatné** služby App Service a nic neplatit. Pokud chcete do webové aplikace přidat [vlastní název DNS,](app-service-web-tutorial-custom-domain.md) stačí škálovat plán na **sdílenou** úroveň. Později, když chcete [vytvořit vazbu SSL](configure-ssl-bindings.md), škálovat plán až na úroveň **Basic.** Pokud chcete mít [pracovní prostředí](deploy-staging-slots.md), škálovat na úroveň **Standard.** Když potřebujete více jader, paměti nebo úložiště, navažte na větší velikost virtuálního počítače ve stejné vrstvě.

Totéž funguje v opačném směru. Když máte pocit, že už nepotřebujete možnosti nebo funkce vyšší úrovně, můžete se škálovat na nižší úroveň, což vám ušetří peníze.

Informace o škálování plánu služby App Service najdete [v tématu Vertika navýšit kapacitu aplikace v Azure](manage-scale-up.md).

Pokud je vaše aplikace ve stejném plánu služby App Service s jinými aplikacemi, můžete zlepšit výkon aplikace izolováním výpočetních prostředků. Můžete to udělat přesunutím aplikace do samostatného plánu služby App Service. Další informace najdete [v tématu Přesunutí aplikace do jiného plánu služby App Service](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Mám dát aplikaci do nového nebo existujícího plánu?

Vzhledem k tomu, že platíte za výpočetní prostředky, které váš plán služby App Service přiděluje (viz [Kolik stojí můj plán služby App Service?](#cost)), můžete potenciálně ušetřit peníze vložením více aplikací do jednoho plánu služby App Service. Můžete pokračovat v přidávání aplikací do existujícího plánu tak dlouho, dokud plán má dostatek prostředků pro zpracování zatížení. Mějte však na paměti, že aplikace ve stejném plánu služby App Service sdílejí stejné výpočetní prostředky. Pokud chcete zjistit, jestli bude mít nová aplikace potřebné prostředky, musíte znát kapacitu existujícího plánu služby App Service a očekávanou zátěž nové aplikace. Přetížení plánu služby App Service může způsobit výpadek nejen nové aplikace, ale i stávajících aplikací.

Aplikaci je vhodné do nového plánu služby App Service izolovat v těchto případech:

- Aplikace je náročná na prostředky.
- Chcete škálovat aplikaci nezávisle na ostatních aplikacích v existujícím plánu.
- Aplikace potřebuje prostředky v jiné zeměpisné oblasti.

Tímto způsobem můžete přidělit novou sadu prostředků pro vaši aplikaci a získat větší kontrolu nad vašimi aplikacemi.

## <a name="manage-an-app-service-plan"></a>Správa plánu služby App Service

> [!div class="nextstepaction"]
> [Správa plánu služby App Service](app-service-plan-manage.md)
