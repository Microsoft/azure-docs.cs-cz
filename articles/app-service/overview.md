---
title: Přehled
description: Zjistěte, jak služba Azure App Service pomáhá nasazovat, hostovat a používat webové aplikace
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767223"
---
# <a name="app-service-overview"></a>Přehled služby App Service

*Azure App Service* je služba založená na protokolu HTTP pro hostování webových aplikací, rozhraní REST API a mobilní back-endy. Můžete vyvíjet ve svém oblíbeném jazyce, ať už jde o .NET, .NET Core, Javu, Ruby, Node.js, PHP nebo Python. Aplikace se snadno spouštějí a mění v prostředích založených na systémech Windows i [Linux](#app-service-on-linux).

App Service nejen zvyšuje výkon aplikace Microsoft Azure, jako je zabezpečení, Vyrovnávání zatížení, automatické škálování a automatizovaná správa. Můžete také využít své možnosti DevOps, jako je průběžné nasazování z Azure DevOps, GitHubu, Docker Hub a dalších zdrojů, Správa balíčků, přípravná prostředí, vlastní doména a certifikáty TLS/SSL. 

Se službou App Service platíte jenom za výpočetní prostředky Azure, které využijete. Výpočetní prostředky, které využijete, se určují podle _plánu App Service_ , na kterém spouštíte aplikace. Další informace najdete v tématu [Přehled plánů Azure App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Proč používat App Service?

Tady jsou některé klíčové funkce App Service:

* **Více jazyků a rozhraní** – App Service má prvotřídní podporu pro ASP.NET, ASP.NET Core, Java, Ruby, Node.js, php nebo Python. Můžete také spouštět [PowerShell a další skripty nebo spustitelné soubory](webjobs-create.md) jako služby na pozadí.
* **Spravované provozní prostředí** – App Service automaticky [opraví a udržuje operační systémy a jazykové architektury](overview-patch-os-runtime.md) za vás. Věnujte čas psaní skvělých aplikací a nechte si Azure starosti o této platformě.
* **Kontejnery a Docker** – ukotvovat vaší aplikace a hostovat vlastní kontejner systému Windows nebo Linux v App Service. Spouštějte aplikace s více kontejnery pomocí Docker Compose. Migrujte své dovednosti v Docker přímo na App Service.
* **Optimalizace DevOps** – Můžete nastavit [kontinuální integraci a nasazování](deploy-continuous-deployment.md) pomocí služeb Azure DevOps, GitHub, BitBucket, Docker Hub nebo Azure Container Registry. Aktualizace lze podporovat prostřednictvím [testovacího a přípravného prostředí](deploy-staging-slots.md). Spravujte aplikace ve službě App Service pomocí prostředí [Azure PowerShell](/powershell/azure/) nebo [multiplatformního rozhraní příkazového řádku (CLI)](/cli/azure/install-azure-cli).
* **Globální škálování s vysokou dostupností:** Můžete ručně i automaticky škálovat pro [vertikální](manage-scale-up.md) nebo [horizontální](../azure-monitor/autoscale/autoscale-get-started.md) navýšení kapacity. Aplikace je možné hostovat kdekoli v globální infrastruktuře datacenter Microsoftu a smlouva [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pro App Service zaručuje vysokou dostupnost.
* **Připojení k platformám SaaS a místním datům** – Můžete si vybírat z více než 50 [konektorů](../connectors/apis-list.md) pro podnikové systémy (například SAP), služby SaaS (například Salesforce) a internetové služby (například Facebook). Získejte přístup k místním datům pomocí [hybridních připojení](app-service-hybrid-connections.md) a [virtuálních sítí Azure](web-sites-integrate-with-vnet.md).
* **Zabezpečení a dodržování předpisů** – Služba App Service je [kompatibilní se standardy ISO, SOC a PCI](https://www.microsoft.com/en-us/trustcenter). Ověřování uživatelů pomocí [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [facebooku](configure-authentication-provider-facebook.md), [Twitteru](configure-authentication-provider-twitter.md)nebo [účet Microsoft](configure-authentication-provider-microsoft.md). Můžete vytvořit [omezení IP adres](app-service-ip-restrictions.md) a [spravovat identity služby](overview-managed-identity.md).
* **Šablony aplikací** – Vyberte si z rozsáhlého seznamu šablon aplikací na webu [Azure Marketplace](https://azure.microsoft.com/marketplace/), například pro systémy WordPress, Joomla nebo Drupal.
* **Visual Studio a Visual Studio Code** integrované nástroje pro integraci v aplikaci Visual studio a Visual Studio Code zjednodušují práci při vytváření, nasazování a ladění.
* **Rozhraní API a mobilní funkce** – App Service poskytují podporu CORS pro scénáře rozhraní RESTful API a zjednodušují scénáře mobilních aplikací tím, že umožňují ověřování, offline synchronizaci dat, nabízená oznámení a další.
* **Kód bez serveru** – Můžete spouštět fragment kódu nebo skript na vyžádání, aniž byste museli explicitně zřizovat nebo spravovat infrastrukturu, a platíte jenom za výpočetní čas, který váš kód skutečně využije (viz [Azure Functions](../azure-functions/index.yml)).

Kromě App Service nabízí Azure další služby, které lze použít k hostování webů a webových aplikací. Pro většinu scénářů je App Service nejlepší volbou.  V případě architektury mikroslužeb zvažte [službu Azure Spring-Cloud Service](../spring-cloud/index.yml) nebo [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Pokud potřebujete větší kontrolu nad virtuálními počítači, na kterých je váš kód spuštěný, zvažte [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Příklady typických aplikačních scénářů, k nimž můžete použít službu Web Apps, naleznete v tématu [Srovnání Azure App Service, Virtual Machines, Service Fabric a Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service v Linuxu

App Service může také hostovat webové aplikace nativně v systému Linux pro podporované balíčky aplikací. Může také spouštět vlastní kontejnery Linux (označované také jako Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Předdefinované jazyky a architektury

App Service v systému Linux podporuje několik vestavěných imagí specifických pro jazyk. Stačí nasadit kód. Mezi podporované jazyky patří: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core a Ruby. Spusťte [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes) , chcete-li zobrazit nejnovější jazyky a podporované verze. Pokud modul runtime, který vaše aplikace potřebuje, není podporovaný ve vestavěných bitových kopiích, můžete ho nasadit pomocí vlastního kontejneru.

Zastaralé moduly runtime se pravidelně odstraňují z Web Apps v portálu pro vytváření a konfiguraci na portálu. Tyto moduly runtime jsou skryté na portálu, pokud jsou nepoužívané organizací údržby nebo mají závažné ohrožení zabezpečení. Tyto možnosti jsou skryté pro zákazníky na nejnovější moduly runtime, kde budou úspěšné. 

Pokud je zastaralý modul runtime na portálu skrytý, všechny existující weby, které tuto verzi používají, budou i nadále běžet. Pokud je modul runtime úplně odebraný z App Serviceé platformy, vlastníkům předplatného Azure se před odebráním pošle e-mailové oznámení.

Pokud potřebujete vytvořit jinou webovou aplikaci se zastaralou verzí modulu runtime, která se už na portálu nezobrazuje, přečtěte si téma Průvodce konfigurací jazyka, kde najdete pokyny, jak získat verzi vašeho webu pro modul runtime. Pomocí rozhraní příkazového řádku Azure můžete vytvořit další lokalitu se stejným modulem runtime. Alternativně můžete pomocí tlačítka **Exportovat šablonu** v okně webové aplikace na portálu Exportovat šablonu ARM webu. Tuto šablonu můžete použít k nasazení nové lokality se stejným modulem runtime a konfigurací.

### <a name="limitations"></a>Omezení

- App Service v systému Linux není ve [sdílené](https://azure.microsoft.com/pricing/details/app-service/plans/) cenové úrovni podporováno. 
- Aplikace pro Windows a Linux nemůžete kombinovat ve stejném plánu App Service.  
- V minulosti nemůžete kombinovat aplikace pro Windows a Linux ve stejné skupině prostředků. Všechny skupiny prostředků vytvořené od 21. ledna 2021 ale podporují tento scénář. Pro skupiny prostředků vytvořené před 21. ledna 2021 se možnost Přidat nasazení smíšené platformy nasadí napříč oblastmi Azure (včetně národních cloudových oblastí) brzo.
- Azure Portal zobrazuje jenom funkce, které aktuálně fungují pro aplikace pro Linux. Když jsou funkce povolené, aktivují se na portálu.
- Při nasazení do vestavěných imagí se vašemu kódu a obsahu přidělují svazek úložiště pro webový obsah, který je zálohovaný Azure Storage. Latence disku tohoto svazku je vyšší a více proměnných, než je latence systému souborů kontejnerů. Aplikace, které vyžadují velký přístup jen pro čtení k souborům obsahu, mohou využívat možnost vlastní kontejner, která místo na svazku obsahu umístí soubory do systému souborů kontejnerů.

## <a name="next-steps"></a>Další kroky

Vytvořte svou první aplikaci.

> [!div class="nextstepaction"]
> [ASP.NET Core (v systému Windows nebo Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (ve Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (v systému Windows nebo Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (v Linuxu)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (v systému Windows nebo Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (v systému Windows nebo Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (on Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (v systému Windows nebo Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Vlastní kontejner (Windows nebo Linux)](tutorial-custom-container.md)
