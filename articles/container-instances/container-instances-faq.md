---
title: Nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se služby Azure Container Instances
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261614"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Nejčastější dotazy týkající se instancí kontejnerů Azure

Tento článek se zabývá nejčastějšími dotazy týkajícími se instancí kontejnerů Azure.

## <a name="deployment"></a>Nasazení

### <a name="how-large-can-my-container-image-be"></a>Jak velký může být můj obrázek kontejneru?

Maximální velikost nasaditelné image kontejneru v instanci kontejneru Azure je 15 GB. Můžete být schopni nasadit větší bitové kopie v závislosti na přesné dostupnosti v okamžiku nasazení, ale to není zaručeno.

Velikost image kontejneru má vliv na to, jak dlouho trvá nasazení, takže obecně chcete, aby vaše image kontejneru byly co nejmenší.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Jak mohu urychlit nasazení kontejneru?

Vzhledem k tomu, že jedním z hlavních determinantů doby nasazení je velikost bitové kopie, vyhledejte způsoby, jak velikost zmenšit. Odstraňte vrstvy, které nepotřebujete, nebo zmenšete velikost vrstev v obraze (výběrem světlejšího základního obrazu operačního systému). Pokud například používáte kontejnery Linux, zvažte použití Alpine jako základního obrazu spíše než úplného serveru Ubuntu. Podobně pro kontejnery systému Windows použijte základní bitovou kopii nano serveru, pokud je to možné. 

Měli byste také zkontrolovat seznam ibi před uloženými v mezipaměti v ibi kontejnerů Azure, které jsou k dispozici prostřednictvím rozhraní [API pro image v mezipaměti seznamu.](/rest/api/container-instances/listcachedimages) Je možné, že budete moci přepnout vrstvu obrazu pro jeden z obrazů před uložením do mezipaměti. 

Podívejte se na [podrobnější pokyny ke](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) zkrácení doby spuštění kontejneru.

### <a name="what-windows-base-os-images-are-supported"></a>Jaké bitové kopie základního operačního systému Windows jsou podporovány?

#### <a name="windows-server-2016-base-images"></a>Základní bitové kopie Systému Windows Server 2016

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `10.0.14393.x`: ,`sac2016`
* [Jádro systému Windows Server](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,`10.0.14393.x`

> [!NOTE]
> Bitové kopie systému Windows založené na verzi 1709 nebo 1803 s pololetním kanálem nejsou podporovány.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Bitové kopie Windows Serveru 2019 a klientské základny (náhled)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : , nebo starší
* [Jádro systému Windows Server](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`nebo `10.0.17763.914` starší
* [Windows](https://hub.docker.com/_/microsoft-windows) `1809`: `10.0.17763.914` nebo starší

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Co .NET nebo .NET Core vrstva obrazu mám použít v mém kontejneru? 

Použijte nejmenší obrázek, který splňuje vaše požadavky. Pro Linux můžete použít bitovou kopii *runtime-alpine* .NET Core, která byla podporována od vydání rozhraní .NET Core 2.1. Pokud v systému Windows používáte úplnou architekturu .NET Framework, je třeba použít bitovou kopii jádra systému Windows Server (bitová kopie pouze za běhu, například *4.7.2-windowsservercore-ltsc2016*). Bitové kopie pouze za běhu jsou menší, ale nepodporují úlohy, které vyžadují .NET SDK.

## <a name="availability-and-quotas"></a>Dostupnost a kvóty

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Kolik jader a paměti mám přidělit pro své kontejnery nebo skupinu kontejnerů?

To opravdu závisí na vaší pracovní zátěži. Začněte s malými a otestujte výkon, abyste viděli, jak si vaše kontejnery pomohou. [Monitorujte využití prostředků procesoru a paměti](container-instances-monitor.md)a pak přidejte jádra nebo paměť na základě druhu procesů, které nasadíte v kontejneru. 

Zkontrolujte také [dostupnost prostředků](container-instances-region-availability.md#availability---general) pro oblast, ve které nasazujete pro horní hranice na jádrech procesoru a paměti, které jsou k dispozici pro skupinu kontejnerů. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Na jaké základní infrastruktuře běží ACI?

Azure Container Instances si klade za cíl být bez serveru kontejnerů na vyžádání služby, takže chceme, abyste se zaměřili na vývoj kontejnerů, a ne starat o infrastrukturu! Pro ty, kteří jsou zvědaví nebo chtějí provést porovnání výkonu, ACI běží na sadách virtuálních počítačích Azure různých skum, především z řady F a D. Očekáváme, že se to v budoucnu změní, protože budeme pokračovat ve vývoji a optimalizaci služby. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Chci nasadit tisíce jader na ACI - mohu získat kvótu?
 
Ano (někdy). Viz [kvóty a omezení](container-instances-quotas.md) článku pro aktuální kvóty a omezení lze zvýšit na požádání.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Mohu nasadit s více než 4 jádry a 16 GB paměti RAM?

Zatím ne. V současné době se jedná o maxima pro skupinu kontejnerů. Obraťte se na podporu Azure s konkrétnípožadavky nebo požadavky. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Kdy bude ACI v určité oblasti?

Aktuální dostupnost oblasti je publikována [zde](container-instances-region-availability.md#availability---general). Pokud máte požadavek na konkrétní oblast, obraťte se na podporu Azure.

## <a name="features-and-scenarios"></a>Funkce a scénáře

### <a name="how-do-i-scale-a-container-group"></a>Jak lze škálovat skupinu kontejnerů?

V současné době škálování není k dispozici pro kontejnery nebo skupiny kontejnerů. Pokud potřebujete spustit více instancí, použijte naše rozhraní API k automatizaci a vytvoření dalších požadavků na vytvoření skupiny kontejnerů pro službu. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Jaké funkce jsou dostupné pro instance spuštěné ve vlastní virtuální síti?

Skupiny kontejnerů můžete [nasadit ve virtuální síti Azure](container-instances-vnet.md) podle vašeho výběru a delegovat privátní IP adresy na skupiny kontejnerů pro směrování provozu v rámci virtuální sítě napříč prostředky Azure. Nasazení skupiny kontejnerů do virtuální sítě je momentálně dostupné pro produkční úlohy v podmnožině oblastí Azure.

## <a name="pricing"></a>Ceny

### <a name="when-does-the-meter-start-running"></a>Kdy začne běžet měřič?

Trvání skupiny kontejnerů se počítá od okamžiku, kdy začneme vytahovat image prvního kontejneru (pro nové nasazení) nebo se vaše skupina kontejnerů restartuje (pokud je již nasazená), dokud se skupina kontejnerů nezastaví. Podrobnosti najdete v [informacích o cenách instancí kontejneru](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Přestat se nabíjet, když jsou moje kontejnery zastaveny?

Měřiče přestanou běžet, jakmile je zastavena celá skupina kontejnerů. Dokud je spuštěn kontejner ve vaší skupině kontejnerů, podržíme prostředky pro případ, že byste je chtěli znovu spustit. 

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace](container-instances-overview.md) o instancích kontejnerů Azure.
* [Řešení běžných problémů](container-instances-troubleshooting.md) v instanci kontejnerů Azure.