---
title: Plány služby App Service
description: Přečtěte si, jak App Service plány pracují v Azure App Service, jak se účtují zákazníkovi a jak se dají škálovat podle svých potřeb.
keywords: App Service, Azure App Service, škálování, škálovatelná, škálovatelnost, plán služby App Service, náklady na službu App Service
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 4c3003a5cbb55464f3a089c3045ac28f3786cb6b
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742969"
---
# <a name="azure-app-service-plan-overview"></a>Přehled plánu služby App Service

V App Service (Web Apps, API Apps nebo Mobile Apps) se aplikace vždy spustí v _plánu App Service_. Kromě toho [Azure Functions](../azure-functions/functions-scale.md#app-service-plan) také možnost spouštět v _plánu App Service_. Plán služby App Service definuje sadu výpočetních prostředků pro provozování webové aplikace. Tyto výpočetní prostředky jsou obdobné jako [_Serverová farma_](https://wikipedia.org/wiki/Server_farm) v části konvenční webové hostování. Jednu nebo více aplikací je možné nakonfigurovat tak, aby běžely na stejných výpočetních prostředcích (nebo ve stejném plánu App Service).

Při vytváření plánu App Service v určité oblasti (například Západní Evropa) se pro tento plán v této oblasti vytvoří sada výpočetních prostředků. Všechny aplikace, které zadáte do tohoto plánu App Service, běží na těchto výpočetních prostředcích, jak jsou definované vaším plánem App Service. Každý plán služby App Service definuje:

- Oblast (USA – západ, USA – východ atd.)
- Počet instancí virtuálních počítačů
- Velikost instancí virtuálních počítačů (malá, střední, velká)
- Cenová úroveň (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, izolovaný režim)

_Cenová úroveň_ plánu App Service určuje, jaké App Service funkce získáte a kolik platíte za plán. Kategorií cenových úrovní existuje několik:

- **Shared COMPUTE**: **Free** and **Shared**, dvě základní úrovně, spustí aplikaci na stejném virtuálním počítači Azure jako jiné aplikace App Service, včetně aplikací jiných zákazníků. Tyto úrovně přidělují kvóty procesoru každé aplikaci, která na sdílených prostředcích běží, a u těchto prostředků není možné škálovat na více instancí.
- **Vyhrazené výpočetní**prostředky: úrovně **Basic**, **Standard**, **Premium**, **PremiumV2**a **PremiumV3** spouštějí aplikace na vyhrazených virtuálních počítačích Azure. Stejné výpočetní prostředky sdílejí jen aplikace ve stejném plánu služby App Service. Čím vyšší cenová úroveň, tím více instancí virtuálních počítačů můžete škálovat.
- **Izolované**: Tato úroveň spouští vyhrazené virtuální počítače Azure ve vyhrazených virtuálních sítích Azure. Poskytuje izolaci sítě nad výpočetní izolací pro vaše aplikace. Tato úroveň nabízí maximální škálování na více instancí.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Každá úroveň také poskytuje určitou podmnožinu funkcí App Service. Mezi tyto funkce patří vlastní domény a certifikáty TLS/SSL, automatické škálování, sloty nasazení, zálohování, Traffic Manager integrace a další. Čím vyšší je úroveň, tím více funkcí je k dispozici. Chcete-li zjistit, které funkce jsou podporovány v každé cenové úrovni, přečtěte si téma [informace o App Service plánu](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> Nová cenová úroveň **PremiumV3** garantuje [virtuální počítače řady Dv3-Series](../virtual-machines/dv3-dsv3-series.md) s rychlejšími procesory, SSD úložištěm a čtyřnásobným poměrem paměti k jádrům ve srovnání s úrovní **Standard** . **PremiumV3** také podporuje vyšší škálování prostřednictvím zvýšeného počtu instancí a zároveň zajišťuje všechny rozšířené možnosti, které se nacházejí na úrovni **Standard** . Všechny funkce, které jsou k dispozici ve stávající úrovni **PremiumV2** , jsou součástí **PremiumV3**.
>
> Podobně jako jiné vyhrazené úrovně jsou pro tuto vrstvu k dispozici tři velikosti virtuálních počítačů:
>
> - Malé (2 jádra procesoru, 8 GiB paměti) 
> - Střední (4 jádra procesoru, 16 GiB paměti) 
> - Velký (8 jader procesoru, 32 GiB paměti)  
>
> Informace o cenách **PremiumV3** najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/).
>
> Pokud chcete začít používat novou cenovou úroveň **PremiumV3** , přečtěte si téma [Konfigurace úrovně PremiumV3 pro App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak aplikace běží a mění?

V úrovních **Free** a **Shared** aplikace obdrží minuty procesoru na sdílené instanci virtuálního počítače a nedá se škálovat. Na jiných úrovních aplikace běží a škáluje následujícím způsobem.

Když vytváříte aplikaci v App Service, je umístěna do plánu App Service. Po spuštění aplikace se spustí na všech instancích virtuálních počítačů nakonfigurovaných v plánu App Service. Pokud jsou ve stejném App Service plánu víc aplikací, všechny sdílejí stejné instance virtuálních počítačů. Pokud máte pro aplikaci více slotů pro nasazení, všechny nasazovací sloty se spouštějí také ve stejných instancích virtuálních počítačů. Pokud povolíte diagnostické protokoly, provádíte zálohování nebo spouštíte webové úlohy, využívají cykly procesoru a paměť na těchto instancích virtuálních počítačů.

Tímto způsobem je plán App Service jednotka škálování App Servicech aplikací. Pokud je plán nakonfigurovaný tak, aby spouštěl pět instancí virtuálních počítačů, pak všechny aplikace v plánu běží na všech pěti instancích. Pokud je plán nakonfigurovaný pro automatické škálování, pak se všechny aplikace v plánu škálují společně na základě nastavení automatického škálování.

Informace o škálování aplikace najdete v tématu [Ruční nebo automatické škálování počtu instancí](../azure-monitor/platform/autoscale-get-started.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Kolik stojí App Service plán?

Tato část popisuje, jak se účtují App Serviceé aplikace. Podrobné informace o cenách pro konkrétní oblast najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/).

S výjimkou **bezplatné** úrovně přináší plán App Service poplatky za výpočetní prostředky, které používá.

- Na **sdílené** úrovni každá aplikace obdrží kvótu pro procesorové minuty, takže se pro _každou aplikaci_ účtuje kvóta procesoru.
- V rámci vyhrazených výpočetních úrovní (**Basic**, **Standard**, **Premium**, **PremiumV2**, **PremiumV3**) plán App Service definuje počet instancí virtuálních počítačů, na které se aplikace škálují, aby se _všechny instance virtuálních počítačů_ v plánu App Service vyrovnaly. Tyto instance virtuálních počítačů se účtují stejně bez ohledu na to, kolik aplikací je v nich spuštěné. Pokud se chcete vyhnout neočekávaným poplatkům, přečtěte si téma [vyčištění App Serviceho plánu](app-service-plan-manage.md#delete).
- V **izolované** úrovni App Service Environment definuje počet izolovaných pracovníků, na kterých běží vaše aplikace, a _každý pracovní proces_ se účtuje. Kromě toho existuje poplatek za paušální razítko pro spuštění samotného App Service Environment.

Neúčtují se vám žádné poplatky za použití funkcí App Service, které jsou k dispozici (Konfigurace vlastních domén, certifikátů TLS/SSL, slotů nasazení, zálohování atd.). Výjimky jsou:

- App Service domény – platíte při nákupu v Azure a při obnovování každého roku.
- App Service certifikátů – platíte při nákupu v Azure a při obnovování každého roku.
- Připojení TLS založená na protokolu IP – pro každé připojení TLS založené na protokolu IP je účtována hodinová sazba, ale u některé úrovně **Standard** nebo vyšší je k dispozici jedno připojení TLS založené na protokolu IP. Připojení TLS založená na SNI jsou zadarmo.

> [!NOTE]
> Pokud integraci App Service s jinou službou Azure, možná budete muset zvážit poplatky z těchto dalších služeb. Pokud například používáte Azure Traffic Manager k geograficky škálované aplikace, Azure Traffic Manager taky účtujeme podle svého využití. Pokud chcete odhadnout náklady na různé služby v Azure, podívejte se do [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/). 

Chcete optimalizovat a uložit své útraty do cloudu?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co když moje aplikace potřebuje více funkcí nebo funkcí?

Kapacitu plánu služby App Service je možné kdykoli vertikálně navýšit nebo snížit. Je to jednoduché jako změna cenové úrovně plánu. Nejprve si můžete zvolit nižší cenovou úroveň a později kapacitu vertikálně navýšit, když budete potřebovat další funkce služby App Service.

Můžete například zahájit testování webové aplikace v **bezplatném** App Service plánu a platíte nic. Pokud chcete do webové aplikace přidat [vlastní název DNS](app-service-web-tutorial-custom-domain.md) , stačí škálovat plán až na **sdílenou** úroveň. Později, pokud chcete [vytvořit vazbu TLS](configure-ssl-bindings.md), Škálujte svůj plán až na úroveň **Basic** . Pokud chcete mít [pracovní prostředí](deploy-staging-slots.md), Škálujte až na úroveň **Standard** . Pokud potřebujete více jader, paměti nebo úložiště, Škálujte až větší velikost virtuálního počítače ve stejné vrstvě.

Stejné funguje v obráceném pořadí. Pokud se domníváte, že už nepotřebujete možnosti nebo funkce vyšší úrovně, můžete horizontální navýšení kapacity snížit na nižší úroveň, která vám ušetří peníze.

Informace o škálování plánu App Service najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md).

Pokud je vaše aplikace ve stejném App Service plánu s jinými aplikacemi, možná budete chtít zlepšit výkon aplikace tím, že izolujete výpočetní prostředky. Můžete to udělat přesunutím aplikace do samostatného plánu App Service. Další informace najdete v tématu [Přesunutí aplikace do jiného App Serviceho plánu](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Mám aplikaci umístit do nového plánu nebo existujícího plánu?

Vzhledem k tomu, že platíte za výpočetní prostředky, které váš App Service plán přiděluje (podívejte se, kolik [stojí App Service plánu?](#cost)), můžete potenciálně ušetřit peníze tím, že do jednoho App Service plánu zadáte několik aplikací. Můžete dál přidávat aplikace do existujícího plánu, pokud má plán dostatek prostředků na zpracování zatížení. Mějte ale na paměti, že aplikace ve stejném App Service naplánují všechny sdílené výpočetní prostředky. Pokud chcete zjistit, jestli bude mít nová aplikace potřebné prostředky, musíte znát kapacitu existujícího plánu služby App Service a očekávanou zátěž nové aplikace. Přetížení plánu služby App Service může způsobit výpadek nejen nové aplikace, ale i stávajících aplikací.

Aplikaci je vhodné do nového plánu služby App Service izolovat v těchto případech:

- Aplikace je náročná na prostředky.
- Chcete aplikaci škálovat nezávisle na ostatních aplikacích v existujícím plánu.
- Aplikace potřebuje prostředek v jiné geografické oblasti.

Tímto způsobem můžete přidělit novou sadu prostředků pro vaši aplikaci a získat větší kontrolu nad vašimi aplikacemi.

## <a name="manage-an-app-service-plan"></a>Správa plánu App Service

> [!div class="nextstepaction"]
> [Správa plánu App Service](app-service-plan-manage.md)