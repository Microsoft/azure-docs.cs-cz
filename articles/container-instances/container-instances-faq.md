---
title: Služba Azure Container Instances – nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se ke službě Azure Container Instances
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 99882bd23d7b94afc550247172e5b70deb23bec9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791402"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Nejčastější dotazy k Azure Container Instances

Tento článek se zabývá nejčastější dotazy týkající se Azure Container Instances.

## <a name="deployment"></a>Nasazení

### <a name="how-large-can-my-container-image-be"></a>Jak velká může Moje image kontejneru být?

Maximální velikost pro bitovou kopii kontejneru nasadit v Azure Container Instances je 15 GB. Je možné nasadit větší obrázky v závislosti na přesné dostupnosti v tuto chvíli můžete nasadit, ale to není zaručeno.

Velikost vaší image kontejneru má vliv na dobu potřebnou k nasazení, takže obecně že je žádoucí uchovat imagí kontejnerů v co nejmenší.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Jak můžu zrychlit nasazení Můj kontejner?

Protože jednou z hlavní faktory doba nasazení je velikost bitové kopie, podívejte se jak zmenšit velikost. Odstraňte vrstvy nebo zmenšete velikost vrstvy na obrázku (výběrem světlejší základní image operačního systému). Například pokud používáte kontejnery Linuxu, zvažte použití Alpine jako základní image, spíše než úplné Ubuntu Server. Podobně pro kontejnery Windows, pokud je to možné použijte základní image Nano serveru. 

Také byste měli zkontrolovat seznam z mezipaměti imagí v Image kontejneru v Azure prostřednictvím [bitové kopie v mezipaměti seznamu](/rest/api/container-instances/listcachedimages) rozhraní API. Je možné přepnout na vrstvu bitové kopie pro některé z imagí z mezipaměti. 

Další informace naleznete v [podrobné pokyny](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) na zkrácení doby spuštění kontejneru.

### <a name="what-windows-base-os-images-are-supported"></a>Jaké Windows základní bitové kopie operačního systému jsou podporovány?

#### <a name="windows-server-2016-base-images"></a>Základní Image Windows serveru 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Windows imagí založených na půlroční kanál verze 1709 nebo 1803 nejsou podporovány.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>2019 systému Windows Server a klienta základní Image (preview)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Obrázek vrstvu .NET nebo .NET Core používat v Můj kontejner? 

Použijte nejmenší bitovou kopii, která splňuje vaše požadavky. Pro Linux, můžete použít *runtime alpine* image .NET Core, která se podporuje od verze rozhraní .NET Core 2.1. Pro Windows, pokud používáte úplné rozhraní .NET Framework, pak budete muset použít bitovou kopii systému Windows Server Core (jen pro modul runtime image, jako například *4.7.2-windowsservercore-ltsc2016*). Pouze modul runtime bitové kopie jsou menší, ale nepodporují úlohy, které vyžadují sady .NET SDK.

## <a name="availability-and-quotas"></a>Dostupnost a kvóty

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Kolik jader a paměti mám přidělit pro moje kontejnery nebo skupinu kontejnerů?

To opravdu závisí na velikosti pracovní zátěže. Začněte v malém a testování výkonu naleznete v tématu Jak kontejnery. [Monitorovat využití prostředků procesoru a paměti](container-instances-monitor.md)a pak přidejte jader, paměť, v závislosti na charakteru procesy, které nasadíte v kontejneru. 

Nezapomeňte také zkontrolovat [dostupnost prostředků](container-instances-region-availability.md#availability---general) pro oblast nasazujete v pro horní mez na jader procesoru a paměti, které jsou k dispozici pro skupinu kontejnerů. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Jaké základní infrastruktury ACI ke spuštění?

Služba Azure Container Instances, zaměřuje jako služba bez serveru kontejnerů na vyžádání, takže budeme rádi, když se zaměřuje na vývoj kontejnerů a bez starostí o infrastrukturu. Pro ty, které jsou zvědaví nebo požadavek provést porovnání na výkon, ACI běží na sadu virtuálních počítačů Azure z různých SKU, především z F a D series. Očekáváme, že toto v budoucnu změnit, protože k vývoji a optimalizace služby. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Chci nasadit tisíc jader ve službě ACI – získat Můj o zvýšení kvóty?
 
Ano (někdy). Zobrazit [kvóty a omezení](container-instances-quotas.md) článku pro aktuální kvóty a omezení, které je možné zvýšit požadavkem.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Je možné nasadit s více než 4 jádry a 16 GB paměti RAM?

Zatím ne. V současné době jedná se o maximální hodnoty pro skupinu kontejnerů. Obraťte se na podporu Azure se zvláštní požadavky nebo požadavky. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Kdy bude ACI v určité oblasti?

Aktuální dostupnosti oblast je publikována [tady](container-instances-region-availability.md#availability---general). Pokud máte požadavek na konkrétní oblasti, obraťte se na podporu Azure.

## <a name="features-and-scenarios"></a>Funkce a scénáře

### <a name="how-do-i-scale-a-container-group"></a>Jak se škáluje skupinu kontejnerů?

V současné době škálování není k dispozici pro kontejnery nebo skupiny kontejnerů. Pokud je potřeba spustit více instancí, použijte naše rozhraní API pro automatizaci a vytvořit další požadavky pro vytvoření skupiny kontejnerů ve službě. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Jaké funkce jsou dostupné pro instance s ovladači ve vlastní virtuální síti?

Můžete nasadit skupiny kontejnerů ve službě Azure virtual network podle vašeho výběru a delegovat privátních IP adres do kontejneru skupiny pro směrování provozu virtuální sítě napříč vašimi prostředky Azure. Nasazení skupiny kontejnerů do virtuální sítě je aktuálně ve verzi preview a některé aspekty této funkce mohou změnit před obecné dostupnosti (GA). Zobrazit [omezení verze Preview](container-instances-vnet.md#preview-limitations) aktualizované informace.

## <a name="pricing"></a>Ceny

### <a name="when-does-the-meter-start-running"></a>Když měřič začít spouštět?

Doba přidělení skupiny kontejnerů se počítá od okamžiku začneme stáhněte si svůj první kontejner image (pro nové nasazení) nebo vaší skupině kontejnerů restartování (pokud už nasadili), dokud nebude zastaven skupiny kontejnerů. Podrobnosti najdete na [Container Instances ceny](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Zastavit se účtují poplatky, když se zastaví Moje kontejnery?

Měřiče zastaví po ukončení vaší skupiny celého kontejneru. Za předpokladu, kontejner ve vaší skupině kontejnerů běží, jsme se uloží prostředky v případě, že chcete znovu spouštění kontejnerů. 

## <a name="next-steps"></a>Další postup

* [Další informace](container-instances-overview.md) o službě Azure Container Instances.
* [Řešení běžných problémů s](container-instances-troubleshooting.md) ve službě Azure Container Instances.