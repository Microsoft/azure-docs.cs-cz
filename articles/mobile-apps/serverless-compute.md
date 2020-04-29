---
title: Vytvoření back-endu mobilní aplikace bez serveru s Azure Functions a dalšími službami
description: Seznamte se s výpočetními službami, které se používají k vytvoření pevného back-endu bezserverových mobilních aplikací.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240148"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Vytváření mobilních back-endové komponent pomocí výpočetních služeb
Každá mobilní aplikace potřebuje back-end, který je zodpovědný za úložiště dat, obchodní logiku a zabezpečení. Správa infrastruktury pro hostování a spouštění back-endu kódu vyžaduje, abyste mohli měnit, zřizovat a škálovat víc serverů. Také je nutné spravovat aktualizace operačního systému a související hardware a použít opravy zabezpečení. Pak potřebujete monitorovat všechny tyto součásti infrastruktury pro výkon, dostupnost a odolnost proti chybám. 

Pro tento typ scénáře jsou k dispozici i architektura bez serveru, protože nemáte žádné servery, které by bylo možné spravovat, a žádné operační systémy ani související softwarové nebo hardwarové aktualizace. Architektura bez serveru šetří čas a náklady na vývojáře, což znamená rychlejší dobu uvedení na trh a cílené úspory energie při sestavování aplikací.

## <a name="benefits-of-compute"></a>Výhody COMPUTE
- Abstrakce serverů znamená, že si nemusíte dělat starosti s hostováním, opravou a zabezpečením, což vám umožní soustředit se pouze na kód.
- Rychlé a efektivní škálování zajišťuje, že prostředky se zřídí automaticky nebo na vyžádání v jakémkoli měřítku, které potřebujete.
- Vysoká dostupnost a odolnost proti chybám.
- Po mikrofakturaci je zajištěno, že se vám bude účtovat pouze v případě, že váš kód skutečně běží.
- Kód běží v cloudu napsaný v jazyce podle vašeho výběru.

Pomocí následujících služeb můžete v mobilních aplikacích povolit výpočetní funkce bez serveru.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) je výpočetní prostředí řízené událostmi, které můžete použít ke spuštění kódu napsaného v programovacím jazyce podle vašeho výběru, aniž byste se museli starat o servery. Nemusíte spravovat aplikaci ani infrastrukturu, aby ji bylo možné spustit. Služba Functions se škáluje na vyžádání a platíte jenom za čas, kdy je váš kód spuštěný. Azure Functions je skvělý způsob, jak implementovat rozhraní API pro mobilní aplikace. Snadno se implementují a udržují a jsou přístupné prostřednictvím protokolu HTTP.

**Klíčové funkce**
- Založené na událostech a škálovatelné, kde můžete použít triggery a vazby k definování, kdy je funkce vyvolána a k jakým datům se připojí.
- Využijte vlastní závislosti, protože funkce podporují NuGet a NPM, takže můžete používat své oblíbené knihovny.
- Integrované zabezpečení, abyste mohli chránit funkce aktivované protokolem HTTP poskytovateli OAuth, jako jsou Azure Active Directory, Facebook, Google, Twitter a účet Microsoft.
- Zjednodušená integrace s různými [službami Azure](/azure/azure-functions/functions-overview) a SaaS (software jako služba).
- Flexibilní vývoj, abyste mohli nastavovat své funkce přímo v Azure Portal nebo nastavit průběžnou integraci a nasazovat kód prostřednictvím GitHubu, Azure DevOps Services a dalších podporovaných vývojových nástrojů.
- Modul runtime Functions je open source a dostupný na [GitHubu](https://github.com/azure/azure-webjobs-sdk-script).
- Vylepšené vývojové prostředí, ve kterém můžete místně Code, testovat a ladit pomocí preferovaného editoru nebo snadno použitelného webového rozhraní s monitorováním integrovaných nástrojů a integrovaných funkcí DevOps.
- Řada programovacích jazyků a možností hostování pro vývoj, jako je C#, Node. js, Java, JavaScript nebo Python.
- Cenový model pro platby za použití znamená, že platíte jenom za čas strávený spouštěním kódu.

**Odkazy**
- [portál Azure](https://portal.azure.com)
- [Dokumentace ke službě Azure Functions](/azure/azure-functions/)
- [Příručka pro vývojáře v Azure Functions](/azure/azure-functions/functions-reference)
- [Rychlé starty](/azure/azure-functions/functions-create-first-function-vs-code)
- [ukázky](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Pomocí [Azure App Service](https://azure.microsoft.com/services/app-service/)můžete vytvářet a hostovat webové aplikace a rozhraní API RESTful v programovacím jazyce podle vašeho výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje Windows i Linux a umožňuje automatizované nasazení z GitHubu, Azure DevOps nebo jakéhokoli úložiště Git.

**Klíčové funkce**
- Podpora více jazyků a platforem pro ASP.NET, ASP.NET Core, Java, Ruby, Node. js, PHP nebo Python. Můžete také spouštět PowerShell a další skripty nebo spustitelné soubory jako služby na pozadí.
- DevOps optimalizace prostřednictvím průběžné integrace a nasazování pomocí Azure DevOps, GitHubu, BitBucket, Docker Hub nebo Azure Container Registry. Spravujte aplikace ve službě App Service pomocí prostředí Azure PowerShell nebo multiplatformního rozhraní příkazového řádku (CLI).
- Globální škálování s vysokou dostupností pro ruční nebo automatické horizontální navýšení kapacity
- Připojení k platformám SaaS a místním datům k výběru z více než 50 konektorů pro podnikové systémy, jako jsou SAP, SaaS Services, jako je Salesforce nebo služby pro Internet, jako je Facebook. Pomocí hybridních připojení a virtuálních sítí Azure získáte přístup k místním datům.
- Azure App Service je kompatibilní s normami ISO, SOC a PCI. Ověřování uživatelů pomocí Azure Active Directory nebo s přihlášením pro sociální média, jako jsou Google, Facebook, Twitter a Microsoft. Můžete vytvořit omezení IP adres a spravovat identity služby.
- Šablony aplikací, které si můžete vybrat z rozsáhlého seznamu šablon aplikací v Azure Marketplace, jako je WordPress, Joomla a Drupal.
- Integrace sady Visual Studio s vyhrazenými nástroji v aplikaci Visual Studio zjednodušuje práci při vytváření, nasazování a ladění.

**Odkazy**
- [portál Azure](https://portal.azure.com/)
- [Dokumentace k Azure App Service](/azure/app-service/)
- [Rychlé starty](/azure/app-service/app-service-web-get-started-dotnet)
- [ukázky](/azure/app-service/samples-cli)
- [Výukové kurzy](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) spravuje hostované prostředí Kubernetes. AKS umožňuje rychle a snadno nasazovat a spravovat aplikace s využitím kontejnerů bez odbornosti pro orchestraci kontejnerů. Také eliminuje zatížení průběžných operací a údržby. AKS zřídí, upgraduje a škáluje prostředky na vyžádání bez nutnosti přepínat aplikace do offline režimu.

**Klíčové funkce**
- Snadno Migrujte stávající aplikace do kontejnerů a spouštějte je v rámci AKS.
- Zjednodušte nasazování a správu aplikací založených na mikroslužbách.
- Zabezpečte DevOps pro AKS, abyste dosáhli rovnováhy mezi rychlostí a zabezpečením a rychleji poskytovali kód ve velkém měřítku.
- Využijte možnost snadného škálování pomocí AKS a Azure Container Instances ke zřízení lusků v rámci Container Instances, která začínají v sekundách.
- Nasazení a Správa zařízení IoT na vyžádání.
- Naučte modely strojového učení pomocí nástrojů, jako jsou TensorFlow a KubeFlow.

**Odkazy**
- [portál Azure](https://portal.azure.com/)
- [Dokumentace ke službě Azure Kubernetes](/azure/aks/)
- [Rychlé starty](/azure/aks/kubernetes-walkthrough-portal)
- [Výukové kurzy](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) je skvělé řešení pro jakýkoli scénář, který může fungovat v izolovaných kontejnerech, jako jsou jednoduché aplikace, automatizace úloh a úlohy sestavení. Rychle vyvíjet aplikace bez nutnosti spravovat virtuální počítače.

**Klíčové funkce**
- Rychlé časy spuštění, protože Container Instances můžou začít kontejnery v Azure během několika sekund, aniž by bylo potřeba zřizovat a spravovat virtuální počítače.
- Veřejné IP připojení a vlastní název DNS.
- Zabezpečení na úrovni hypervisoru, které zaručuje vaši aplikaci v kontejneru jako izolované, stejně jako na virtuálním počítači.
- Vlastní velikosti pro optimální využití tím, že umožňují přesné specifikace jader procesoru a paměti. Platíte podle toho, co potřebujete, a účtuje se po sekundách, takže můžete podrobně upravovat náklady podle vašich aktuálních potřeb.
- Trvalé úložiště pro načtení a zachování stavu. Container Instances nabízí přímé připojení ke sdíleným složkám souborů Azure.
- Kontejnery Linux a Windows naplánované se stejným rozhraním API.

**Odkazy**
- [portál Azure](https://portal.azure.com/)
- [Dokumentace k Azure Container Instances](/azure/container-instances/)
- [Rychlé starty](/azure/container-instances/container-instances-quickstart-portal)
- [ukázky](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Výukové kurzy](/azure/container-instances/container-instances-tutorial-prepare-app)