---
title: Vytvářejte back-end mobilní aplikace bez serveru s využitím Azure Functions a dalších služeb
description: Přečtěte si o výpočetních službách, které vám umožní vytvářet Solid, bezserverovou back-end mobilní aplikace.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795858"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Vytváření komponent pro mobilní back-end pomocí výpočetních služeb
Každá mobilní aplikace potřebuje back-end, který je zodpovědný za úložiště dat, obchodní logiku a zabezpečení. Správa infrastruktury pro hostování a spouštění back-endu vyžaduje, abyste nařídili, zřídili a škálovat spoustu serverů, spravovali aktualizace operačního systému a hardware, použili opravy zabezpečení a pak sledovali všechny tyto součásti infrastruktury pro výkon. dostupnost a odolnost proti chybám. To je v případě, že architektura bez serveru je užitečná, protože vývojáři nemají žádné servery pro správu, žádný operační systém ani související aktualizace softwaru a hardwaru ke správě. Ušetří spoustu času a nákladů pro vývojáře, což znamená rychlejší použití času na uvedení na trh a zaměřených energie na sestavování aplikací.

## <a name="benefits-of-compute"></a>Výhody COMPUTE
- Abstrakce serverů: nemusíte si dělat starosti s hostováním, opravou a zabezpečením, což vývojářům umožní soustředit se na kód.
- Rychlé a efektivní škálování zajišťuje, že se prostředky automaticky zřídí nebo na vyžádání v jakémkoli měřítku, které potřebujete.
- Vysoká dostupnost a odolnost proti chybám.
- Po mikrofakturaci je zajištěno, že se vám budou účtovat poplatky za to, kdy váš kód skutečně běží.
- Pište kód, který běží v cloudu v jazyce podle vašeho výběru.

Pomocí následujících služeb můžete v mobilních aplikacích povolit výpočetní funkce bez serveru.

## <a name="azure-functions"></a>Funkce Azure
[Azure Functions](https://azure.microsoft.com/services/functions/) je výpočetní prostředí řízené událostmi, které umožňuje spuštění kódu napsaného v programovacím jazyce podle vašeho výběru, aniž byste se museli starat o servery. Vývojáři nemusí spravovat aplikaci nebo infrastrukturu, na které ji spouští. Služba Functions se škáluje na vyžádání a platíte jenom za čas, kdy je váš kód spuštěný. Azure Functions jsou skvělým způsobem, jak implementovat rozhraní API pro mobilní aplikace, protože je velmi snadné implementovat a udržovat a přistupovat přes HTTP.

**Klíčové funkce**
- Založené na **událostech a škálovatelné** , kde můžou vývojáři použít **triggery a vazby** k definování při vyvolání funkce a k tomu, k jakým datům se připojí.
- **Přineste si vlastní závislosti** – Functions podporuje NuGet a NPM, takže můžete používat své oblíbené knihovny.
- **Integrované zabezpečení** umožňuje chránit funkce aktivované protokolem HTTP poskytovateli OAuth, jako jsou Azure Active Directory, Facebook, Google, Twitter a účet Microsoft.
- **Zjednodušená integrace** s různými [službami Azure](/azure/azure-functions/functions-overview#integrations) a nabídkami software jako služba (SaaS).
- **Flexibilní vývoj** vám umožní kódovat vaše funkce přímo na portálu nebo nastavit průběžnou integraci a nasadit kód prostřednictvím GitHubu, Azure DevOps Services a dalších podporovaných vývojových nástrojů.
- Běhový modul Functions je **Open Source** a dostupný na [GitHubu](https://github.com/azure/azure-webjobs-sdk-script).
-  **Vylepšené vývojové prostředí** , kde můžou vývojáři psát, testovat a ladit místně pomocí preferovaného editoru nebo snadno použitelného webového rozhraní s monitorováním integrovaných nástrojů a integrovaných funkcí DevOps.
- **Celá řada programovacích jazyků a možností hostování** – vývoj C#pomocí, Node. js, Java, JavaScriptu nebo Pythonu
- **Cenový model platba za použití** – Platíte jen za čas, kdy byl kód spuštěný.

**Odkazy**
- [Azure Portal](https://portal.azure.com)
- [Nápovědě](/azure/azure-functions/)
- [Azure Functions příručka pro vývojáře](/azure/azure-functions/functions-reference)
- [Rychlé starty](/azure/azure-functions/functions-create-first-function-vs-code)
- [Ukázky](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>Aplikační služba
[Azure App Service](https://azure.microsoft.com/services/app-service/) Azure App Service umožňuje sestavovat a hostovat webové aplikace a rozhraní API RESTful v programovacím jazyce podle vašeho výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Azure DevOps nebo libovolného úložiště Git.

**Klíčové funkce**
- Podpora **více jazyků a platforem** pro ASP.NET, ASP.NET Core, Java, Ruby, Node. js, php nebo Python. Můžete také spustit PowerShell a další skripty nebo spustitelné soubory jako služby na pozadí.
- **Optimalizace DevOps** – nastavení průběžné integrace a nasazování pomocí Azure DevOps, GitHubu, BitBucket, Docker Hub nebo Azure Container Registry. Spravujte své aplikace v App Service pomocí Azure PowerShell nebo rozhraní příkazového řádku pro více platforem (CLI).
- **Globální škálování s vysokou dostupností** pro ruční nebo automatické horizontální navýšení kapacity
- **Připojení k platformám SaaS a místním datům** k výběru z více než 50 konektorů pro podnikové systémy (například SAP), SaaS Services (například Salesforce) a služeb sítě Internet (například Facebook). Přístup k místním datům pomocí Hybrid Connections a virtuálních sítí Azure.
- **Zabezpečení a dodržování předpisů** – Azure App Service jsou kompatibilní s normami ISO, SOC a PCI. Ověřování uživatelů pomocí Azure Active Directory nebo prostřednictvím účtu pro sociální přihlášení (Google, Facebook, Twitter a Microsoft) Vytvořte omezení podle IP adresy a spravujte identity služby.
- **Šablony aplikací** , které si můžete vybrat z rozsáhlého seznamu šablon aplikací v Azure Marketplace, jako je WordPress, Joomla a Drupal.
- **Integrace sady Visual Studio** s vyhrazenými nástroji v aplikaci Visual Studio zjednodušuje práci při vytváření, nasazování a ladění.

**Odkazy**
- [Azure Portal](https://portal.azure.com/)
- [Nápovědě](/azure/app-service/)
- [Rychlé starty](/azure/app-service/app-service-web-get-started-dotnet)
- [Ukázky](/azure/app-service/samples-cli)
- [Kurzy](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) spravuje hostované prostředí Kubernetes a umožňuje rychle a snadno nasazovat a spravovat kontejnerizované aplikace bez znalosti orchestrace kontejnerů. Zároveň eliminuje režii spojenou s probíhajícími operacemi a údržbou díky zřizování, upgradování a škálování prostředků na vyžádání bez nutnosti odpojovat aplikace. 

**Klíčové funkce**
- **Snadno Migrujte stávající aplikace** do kontejnerů a spouštějte je v rámci AKS.
- **Zjednodušte nasazování a správu** aplikací založených na mikroslužbách.
- **Zabezpečte DevOps pro AKS** , abyste dosáhli rovnováhy mezi rychlostí a zabezpečením a rychleji poskytovali kód ve velkém měřítku.
- **Škálujte s využitím AKS a ACI** k zřizování lusků v ACI, které začínají v sekundách.
- **Nasazení a Správa zařízení IoT** na vyžádání.
- **Školení modelů ve službě Machine Learning** s využitím nástrojů, jako je TensorFlow a KubeFlow.

**Odkazy**
- [Azure Portal](https://portal.azure.com/)
- [Nápovědě](/azure/aks/)
- [Rychlé starty](/azure/aks/kubernetes-walkthrough-portal)
- [Kurzy](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances (ACI)](https://azure.microsoft.com/services/container-instances/) je skvělé řešení pro jakýkoli scénář, který může fungovat v izolovaných kontejnerech, včetně jednoduchých aplikací, automatizace úloh a úloh sestavení. Rychle vyvíjet aplikace bez nutnosti spravovat virtuální počítače.

**Klíčové funkce**
- **Rychlé časy spuštění** , protože ACI může spustit kontejnery v Azure během několika sekund, aniž by bylo potřeba zřizovat a spravovat virtuální počítače.
- **Veřejné IP připojení a vlastní název DNS**.
- **Zabezpečení na úrovni hypervisoru** , které zaručuje vaši aplikaci v kontejneru jako izolované, stejně jako na virtuálním počítači.
- **Vlastní velikosti** pro optimální využití tím, že umožňují přesné specifikace jader procesoru a paměti. Platíte podle toho, co potřebujete, a účtuje se po sekundách, takže můžete podrobně upravovat náklady podle vašich aktuálních potřeb.
- **Trvalé úložiště** pro načtení a zachování stavu ACI nabízí přímé připojení ke sdíleným složkám souborů Azure.
- **Kontejnery Linux a Windows** naplánované se stejným rozhraním API.

**Odkazy**
- [Azure Portal](https://portal.azure.com/)
- [Nápovědě](/azure/container-instances/)
- [Rychlé starty](/azure/container-instances/container-instances-quickstart-portal)
- [Ukázky](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Kurzy](/azure/container-instances/container-instances-tutorial-prepare-app)