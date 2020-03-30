---
title: Vytvoření back-endu mobilní aplikace bez serveru s funkcemi Azure a dalšími službami
description: Seznamte se s výpočetními službami používanými k vytvoření pevného back-endu mobilní aplikace bez serveru.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240148"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Vytváření mobilních back-endových komponent s výpočetními službami
Každá mobilní aplikace potřebuje back-end, který je zodpovědný za ukládání dat, obchodní logiku a zabezpečení. Správa infrastruktury pro hostování a spouštění kódu back-end vyžaduje velikost, zřízení a škálování více serverů. Musíte také spravovat aktualizace operačního systému a související hardware a použít opravy zabezpečení. Potom je třeba sledovat všechny tyto součásti infrastruktury pro výkon, dostupnost a odolnost proti chybám. 

Architektura bez serveru se hodí pro tento typ scénáře, protože nemáte žádné servery ke správě a žádné aktualizace operačního systému nebo souvisejícího softwaru nebo hardwaru ke správě. Architektura bez serveru šetří čas a náklady vývojářům, což znamená rychlejší uvedení na trh a zaměření energie na vytváření aplikací.

## <a name="benefits-of-compute"></a>Výhody výpočetních prostředků
- Abstrakce serverů znamená, že se nemusíte starat o hostování, opravy a zabezpečení, což vám umožní soustředit se výhradně na kód.
- Okamžité a efektivní škálování zajišťuje, že prostředky jsou zřízeny automaticky nebo na vyžádání v libovolném měřítku, které potřebujete.
- Vysoká dostupnost a odolnost proti chybám.
- Mikrofakturace zajišťuje, že se vám budou účtovat jenom když je váš kód skutečně spuštěný.
- Kód běží v cloudu napsaném v jazyce, který si vyberete.

Pomocí následujících služeb můžete v mobilních aplikacích povolit výpočetní funkce bez serveru.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) je výpočetní prostředí řízené událostmi, které můžete použít ke spuštění kódu napsaného v programovacím jazyce podle vašeho výběru, bez obav o servery. Není nutné spravovat aplikaci nebo infrastrukturu, na které chcete spustit. Funkce se škálují na vyžádání a platíte pouze za dobu, po kterou se váš kód spustí. Funkce Azure jsou skvělý způsob, jak implementovat rozhraní API pro mobilní aplikaci. Jsou snadno implementovat a udržovat a jsou přístupné prostřednictvím protokolu HTTP.

**Klíčové funkce**
- Událostmi řízené a škálovatelné, kde můžete použít aktivační události a vazby k definování, kdy je funkce vyvolána a jaká data se připojí.
- Přineste si vlastní závislosti, protože funkce podporuje NuGet a NPM, takže můžete použít své oblíbené knihovny.
- Integrované zabezpečení, takže můžete chránit funkce spouštěné http s poskytovateli OAuth, jako jsou Azure Active Directory, Facebook, Google, Twitter a účet Microsoft.
- Zjednodušená integrace s různými [službami](/azure/azure-functions/functions-overview) Azure a softwarem jako nabídkami služeb (SaaS).
- Flexibilní vývoj, abyste mohli kódovat své funkce přímo na webu Azure Portal nebo nastavit průběžnou integraci a nasadit kód prostřednictvím GitHubu, služeb Azure DevOps a dalších podporovaných vývojových nástrojů.
- Funkce runtime je open source a jsou k dispozici na [GitHubu](https://github.com/azure/azure-webjobs-sdk-script).
- Vylepšené vývojové prostředí, kde můžete kódovat, testovat a ladit místně pomocí jejich upřednostňovaného editoru nebo snadno použitelného webového rozhraní s monitorováním s integrovanými nástroji a integrovanými funkcemi DevOps.
- Různé programovací jazyky a možnosti hostování pro vývoj, například C#, Node.js, Java, JavaScript nebo Python.
- Cenový model platby za použití znamená, že platíte pouze za čas strávený spuštěním kódu.

**Odkazy**
- [Portál Azure](https://portal.azure.com)
- [Dokumentace ke službě Azure Functions](/azure/azure-functions/)
- [Příručka pro vývojáře v Azure Functions](/azure/azure-functions/functions-reference)
- [Rychlé starty](/azure/azure-functions/functions-create-first-function-vs-code)
- [ukázky](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Pomocí [služby Azure App Service](https://azure.microsoft.com/services/app-service/)můžete vytvářet a hostovat webové aplikace a rozhraní API RESTful v programovacím jazyce podle vašeho výběru bez správy infrastruktury. Nabízí automatické škálování a vysokou dostupnost, podporuje Windows i Linux a umožňuje automatizovaná nasazení z GitHubu, Azure DevOps nebo libovolného úložiště Git.

**Klíčové funkce**
- Podpora více jazyků a architektury pro ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP nebo Python. Můžete také spouštět PowerShell a další skripty nebo spustitelné soubory jako služby na pozadí.
- Optimalizace DevOps prostřednictvím průběžné integrace a nasazení s Azure DevOps, GitHub, BitBucket, Docker Hub nebo Azure Container Registry. Spravujte aplikace ve službě App Service pomocí prostředí Azure PowerShell nebo multiplatformního rozhraní příkazového řádku (CLI).
- Globální škálování s vysokou dostupností pro ruční nebo automatické škálování.
- Připojení k platformám SaaS a místním datům a můžete si vybrat z více než 50 konektorů pro podnikové systémy, jako je SAP, služby SaaS, jako je Salesforce, a internetové služby, jako je Facebook. Přístup k místním datům pomocí hybridních připojení a virtuálních sítí Azure.
- Služba Azure App Service je kompatibilní s ISO, SOC a PCI. Ověřujte uživatele pomocí Služby Azure Active Directory nebo přihlášením pro sociální média, jako je Google, Facebook, Twitter a Microsoft. Můžete vytvořit omezení IP adres a spravovat identity služby.
- Šablony aplikací si můžete vybrat z rozsáhlého seznamu šablon aplikací na Azure Marketplace, jako je WordPress, Joomla a Drupal.
- Integrace visual studia s vyhrazenými nástroji v sadě Visual Studio zjednodušuje práci při vytváření, nasazování a ladění.

**Odkazy**
- [Portál Azure](https://portal.azure.com/)
- [Dokumentace služby Azure App Service](/azure/app-service/)
- [Rychlé starty](/azure/app-service/app-service-web-get-started-dotnet)
- [ukázky](/azure/app-service/samples-cli)
- [Výukové kurzy](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Služba Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) spravuje vaše hostované prostředí Kubernetes. AKS umožňuje rychlé a snadné nasazení a správu kontejnerizovaných aplikací bez odborných znalostí orchestrace kontejnerů. Odstraňuje také zátěž spojenou s probíhajícím provozem a údržbou. AKS ustanovení, upgrady a škálování prostředků na vyžádání, aniž by vaše aplikace offline.

**Klíčové funkce**
- Snadno migrujte existující aplikace do kontejnerů a spouštět je v rámci AKS.
- Zjednodušte nasazení a správu aplikací založených na mikroslužbách.
- Zabezpečte devops pro AKS, abyste dosáhli rovnováhy mezi rychlostí a zabezpečením a dodávali kód rychleji ve velkém měřítku.
- Snadno škálujte pomocí aks a azure kontejneru instance zřídit pody uvnitř instance kontejneru, které začínají během několika sekund.
- Nasazujte a spravujte zařízení IoT na vyžádání.
- Trénujte modely strojového učení s využitím nástrojů, jako jsou TensorFlow a KubeFlow.

**Odkazy**
- [Portál Azure](https://portal.azure.com/)
- [Dokumentace ke službě Azure Kubernetes](/azure/aks/)
- [Rychlé starty](/azure/aks/kubernetes-walkthrough-portal)
- [Výukové kurzy](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) je skvělé řešení pro všechny scénáře, které mohou pracovat v izolovaných kontejnerech, jako jsou jednoduché aplikace, automatizace úloh a úlohy sestavení. Vyvíjejte aplikace rychle bez správy virtuálních aplikací.

**Klíčové funkce**
- Rychlé spuštění časy jako instance kontejneru můžete spustit kontejnery v Azure během několika sekund, bez nutnosti zřizování a správu virtuálních počítačů.
- Připojení veřejné IP adresy a vlastní název DNS.
- Zabezpečení na úrovni hypervisoru, které zaručuje, že vaše aplikace je v kontejneru stejně izolovaná jako ve virtuálním počítači.
- Vlastní velikosti pro optimální využití tím, že umožňují přesné specifikace procesorových jader a paměti. Platíte podle toho, co potřebujete, a účtuje se po sekundách, takže můžete podrobně upravovat náklady podle vašich aktuálních potřeb.
- Trvalé úložiště pro načtení a trvalý stav. Instance kontejnerů nabízí přímé připojení sdílených složek Azure Files.
- Linux a Windows kontejnery naplánované se stejným rozhraním API.

**Odkazy**
- [Portál Azure](https://portal.azure.com/)
- [Dokumentace k instancí kontejnerů Azure](/azure/container-instances/)
- [Rychlé starty](/azure/container-instances/container-instances-quickstart-portal)
- [ukázky](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Výukové kurzy](/azure/container-instances/container-instances-tutorial-prepare-app)