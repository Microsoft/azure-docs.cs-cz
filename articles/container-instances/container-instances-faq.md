---
title: Nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se služby Azure Container Instances
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: e4760463a4d71e4c097b082aa6b93d8b339f94ca
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516966"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Nejčastější dotazy týkající se Azure Container Instances

Tento článek se zabývá nejčastějšími dotazy týkajícími se Azure Container Instances.

## <a name="deployment"></a>Nasazení

### <a name="how-large-can-my-container-image-be"></a>Jak velký je můj obrázek kontejneru?

Maximální velikost nasadíelné image kontejneru na Azure Container Instances je 15 GB. Je možné, že budete moci nasadit větší obrázky v závislosti na přesné dostupnosti v době, kdy jste nasadili, ale to není zaručeno.

Velikost Image kontejneru má vliv na dobu potřebnou k nasazení, takže obecně chcete uchovávat image kontejnerů co nejmenším možným způsobem.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Jak můžu zrychlit nasazení mého kontejneru?

Vzhledem k tomu, že jedním z hlavních determinantů časů nasazení je velikost obrázku, hledejte způsob, jak velikost zmenšit. Odeberte vrstvy, které nepotřebujete, nebo zmenšete velikost vrstev v imagi (tím, že vybíráte světlejší základní bitovou kopii operačního systému). Pokud například používáte kontejnery Linux, zvažte použití Alpine jako základní image, nikoli úplného serveru Ubuntu. Podobně pro kontejnery Windows použijte základní image nano serveru, pokud je to možné. 

Měli byste také kontrolovat seznam imagí předem uložených v mezipaměti ve službě Azure Container images, které jsou k dispozici prostřednictvím rozhraní API pro [vypsání imagí v mezipaměti](/rest/api/container-instances/location/listcachedimages) . Může být možné přepnout vrstvu obrázku pro jednu z imagí z mezipaměti před uložením na více systémů. 

Přečtěte si podrobnější [pokyny](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) k omezení času spuštění kontejneru.

### <a name="what-windows-base-os-images-are-supported"></a>Jaké základní image operačního systému Windows jsou podporované?

> [!NOTE]
> V důsledku problémů s zpětnou kompatibilitou po aktualizacích Windows v 2020 obsahují následující verze imagí minimální číslo verze, které doporučujeme použít ve své základní imagi. Aktuální nasazení, která používají starší verze imagí, nejsou ovlivněná, ale nová nasazení by měla splňovat následující základní image. 

#### <a name="windows-server-2016-base-images"></a>Základní image Windows serveru 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016` `10.0.14393.3506` nebo novější
* [Jádro Windows serveru](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`  `10.0.14393.3506` nebo novější

> [!NOTE]
> Bitové kopie systému Windows založené na Semi-Annualovém kanálu verze 1709 nebo 1803 nejsou podporovány.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 a základní image klienta (Preview)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809` `10.0.17763.1040` nebo novější
* [Jádro Windows serveru](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019` , `1809` `10.0.17763.1040` nebo novější
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809` `10.0.17763.1040` nebo novější

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Jakou vrstvu imagí .NET nebo .NET Core mám použít v kontejneru? 

Použijte nejmenší obrázek, který splňuje vaše požadavky. Pro Linux byste mohli použít bitovou kopii .NET Core pro *modul runtime* , která byla od vydání .net Core 2,1 podporovaná. V případě systému Windows, pokud používáte úplný .NET Framework, je nutné použít základní bitovou kopii systému Windows Server (bitovou kopii pouze za běhu, například  *4.7.2-windowsservercore-ltsc2016*). Bitové kopie, které jsou jen za běhu, jsou menší, ale nepodporují úlohy, které vyžadují sadu .NET SDK.

## <a name="availability-and-quotas"></a>Dostupnost a kvóty

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Kolik jader a paměti mám přidělit pro moje kontejnery nebo skupinu kontejnerů?

Tato skutečnosti závisí na vašich úlohách. Začněte s malým a testovacím výkonem, abyste viděli, jak vaše kontejnery dělají. [Monitorujte využití prostředků procesoru a paměti](container-instances-monitor.md)a pak přidejte jádra nebo paměť na základě typu procesů, které nasadíte do kontejneru.

Nezapomeňte také zkontrolovat [dostupnost prostředků](container-instances-region-availability.md) pro oblast, do které nasazujete, v horních mezích jader procesoru a paměti dostupných na jednu skupinu kontejnerů. 

> [!NOTE]
> Základní infrastruktura služby používá malý objem prostředků skupiny kontejnerů. Vaše kontejnery budou mít přístup k většině prostředků přiřazených ke skupině, ale ne ke všem. Z tohoto důvodu Naplánujte malou vyrovnávací paměť prostředků při žádosti o prostředky pro kontejnery ve skupině.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Jakou základní infrastrukturu ACI spustit?

Azure Container Instances se zaměřuje na službu bez serveru, která je na vyžádání, takže chceme mít fokus na vývoji vašich kontejnerů a nemusíte si dělat starosti s infrastrukturou. Pro ty, které se zajímá nebo chtějí porovnávat výkon, ACI běží na sadách virtuálních počítačů Azure různých SKU, hlavně z řad F a D. Očekáváme, že toto se v budoucnu změní, protože dál vyvíjíme a optimalizujete službu. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Chci nasadit tisíce jader na ACI – můžu zvýšit svou kvótu?
 
Ano (někdy). Prohlédněte si článek [kvóty a omezení](container-instances-quotas.md) pro aktuální kvóty a omezení lze zvýšit podle požadavků.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Můžu nasazovat s více než 4 jádry a 16 GB paměti RAM?

Zatím ne. V současné době jsou to maxima pro skupinu kontejnerů. Kontaktujte podporu Azure o konkrétní požadavky nebo požadavky. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Kdy bude ACI v konkrétní oblasti?

[Tady](container-instances-region-availability.md)se zveřejňuje aktuální dostupnost oblasti. Pokud máte požadavek na konkrétní oblast, obraťte se na podporu Azure.

## <a name="features-and-scenarios"></a>Funkce a scénáře

### <a name="how-do-i-scale-a-container-group"></a>Návody škálovat skupinu kontejnerů?

V současné době není pro kontejnery nebo skupiny kontejnerů k dispozici škálování. Pokud potřebujete spustit více instancí, použijte naše rozhraní API k automatizaci a vytvoření dalších žádostí o vytvoření skupiny kontejnerů do této služby. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Jaké funkce jsou k dispozici pro instance spuštěné ve vlastní virtuální síti?

[Skupiny kontejnerů můžete nasadit ve virtuální síti Azure](container-instances-vnet.md) podle svého výběru a delegovat privátní IP adresy na skupiny kontejnerů, abyste mohli směrovat provoz v rámci virtuální sítě napříč prostředky Azure. Síťové scénáře a omezení týkající se Azure Container Instances najdete v tématu [scénáře a prostředky virtuální sítě](container-instances-virtual-network-concepts.md).

## <a name="pricing"></a>Ceny

### <a name="when-does-the-meter-start-running"></a>Kdy se měřič spouští?

Doba trvání skupiny kontejnerů se počítá od okamžiku, kdy začneme načítat image prvního kontejneru (pro nové nasazení) nebo když se skupina kontejnerů restartuje (Pokud je už nasazená), až do zastavení skupiny kontejnerů. Podrobnosti najdete v tématu [Container Instances Price](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Při zastavení mých kontejnerů se mi účtují?

Až se zastaví celá skupina kontejnerů, měřiče se nespustí. Pokud je v kontejneru ve skupině kontejnerů spuštěný kontejner, uchovávají se prostředky pro případ, že budete chtít znovu spustit kontejnery. 

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace](container-instances-overview.md) o Azure Container Instances.
* [Řešení běžných problémů](container-instances-troubleshooting.md) v Azure Container Instances.
