---
title: Přehled Service Fabric a kontejnery | Dokumentace Microsoftu
description: Přehled Service Fabric a použití kontejnerů pro nasazení aplikací mikroslužeb. Tento článek obsahuje základní informace o používání kontejnerů a možnosti dostupné v Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: msfussell
ms.openlocfilehash: 6715142be7f40955861afa634bf6e2472c9f7294
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005771"
---
# <a name="service-fabric-and-containers"></a>Service Fabric a kontejnery

## <a name="introduction"></a>Úvod

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.

Service Fabric je společnosti Microsoft [orchestrátor kontejnerů](service-fabric-cluster-resource-manager-introduction.md) pro nasazuje mikroslužby napříč clusterem počítačů. Service Fabric výhody prospěchu jeho letech službami společnosti Microsoft ve velkém měřítku.

Mikroslužby je možné vyvíjet mnoha způsoby, od použití [programovacích modelů Service Fabric](service-fabric-choose-framework.md) přes [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) až po nasazení [libovolného vlastního kódu](service-fabric-guest-executables-introduction.md). Nebo, pokud chcete jen [nasazení a správu kontejnerů](service-fabric-containers-overview.md), Service Fabric je také skvělou volbou.

Ve výchozím nastavení Service Fabric nasadí a aktivuje tyto služby jako procesy. Procesy poskytují nejrychlejší aktivace a nejvyšší hustota využití prostředků v clusteru. Service Fabric dokáže nasadit také služby v imagí kontejnerů. Můžete také kombinovat služby v procesech a služby v kontejnerech, ve stejné aplikaci.

Pustit do práce a vyzkoušejte si kontejnerů v Service Fabric, zkuste použít rychlý start, kurz nebo vzorku:  

[Rychlý start: Nasazení aplikace typu kontejner Linuxu do Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Rychlý start: Nasazení aplikace typu kontejner Windows do Service Fabric](service-fabric-quickstart-containers.md)  
[Kontejnerizace existující aplikace v .NET](service-fabric-host-app-in-a-container.md)  
[Ukázky kontejnerů Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Co jsou kontejnery

Kontejnery vyřešit problém spolehlivě spouštění aplikací v různých výpočetních prostředí tím, že poskytuje neměnné prostředí pro aplikaci, aby běžela. Kontejnery zabalit aplikaci a všechny jeho závislosti, jako jsou knihovny a konfigurační soubory do své vlastní izolované "pole", které obsahuje vše potřebné pro spuštění softwaru uvnitř kontejneru. Bez ohledu na to se kontejner spustí, aplikace je vždy obsahuje vše, co je nutné ho spustit jako je například správné verze z jeho závislých knihoven, všechny konfigurační soubory a cokoli, co je nutné ho spustit.

Kontejnery běží přímo nad jádra a mít pohled izolovaný systém souborů a dalších prostředků. Aplikace v kontejneru je vůbec nezná jinými aplikacemi nebo procesy vně svého kontejneru. Každá aplikace a jeho knihoven modulu runtime, závislosti a systému spustit uvnitř kontejneru úplné, soukromého přístup do zobrazení kontejneru vlastní samostatný operační systém. Kromě toho, že usnadňuje poskytují všechny závislé součásti vaší aplikace je nutné ho spustit v různých výpočetních prostředích, zabezpečení a izolaci prostředků jsou důležité výhody použití kontejnerů s využitím Service Fabric – které jinak spuštění služby v proces.

Porovnání s virtuálními počítači, kontejnery mají následující výhody:

* **Malé**: kontejnery pomocí jednoho místa a verze vrstvy a aktualizace zvýšíte efektivitu.
* **Rychlé**: kontejnery nemají spustit celý operační systém, aby mohli začít mnohem rychleji – obvykle v řádu sekund.
* **Přenositelnost**: image kontejnerizované aplikace můžete přenést na spouštění v cloudu, v místním prostředí, uvnitř virtuálních počítačů, nebo přímo na fyzických počítačích.
* **Zásady správného řízení prostředků**: kontejneru můžete omezit fyzické prostředky, které můžou využívat svého hostitele.

### <a name="container-types-and-supported-environments"></a>Typy kontejnerů a podporovaných prostředí

Service Fabric podporuje kontejnery v Linuxu i Windows a podporuje režimu izolace Hyper-V na Windows.

#### <a name="docker-containers-on-linux"></a>Kontejnery dockeru v Linuxu

Docker nabízí rozhraní API pro vytváření a správu kontejnerů nad jádra kontejnery Linuxu. Docker Hubu poskytuje centrální úložiště pro ukládání a načítání imagí kontejneru.
Kurz založených na Linuxu najdete v tématu [vytvořit svou první aplikaci typu kontejner Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Kontejnery Windows serveru

Windows Server 2016 poskytuje dva různé typy kontejnerů, které se liší podle úrovně izolace. Kontejnery Windows serveru a kontejnery Dockeru jsou podobné, protože obě mají obor názvů a soubor izolace systému při sdílení jádro s hostitelem, které, na kterém běží. V systému Linux Tato izolace tradičně poskytl cgroup a obory názvů a kontejnery Windows serveru se chovají podobně.

Kontejnery Windows s podporou technologie Hyper-V poskytnout další izolace a zabezpečení, protože žádný kontejner sdílí jádro operačního systému se žádný kontejner nebo s hostitelem. S touto vyšší úrovní zabezpečení, kterou cílí kontejnery Hyper-V povolena na scénářích potenciálně nebezpečný, více tenantů.
Kurz založené na Windows, naleznete v tématu [vytvořit svou první aplikaci typu kontejner Service Fabric na Windows](service-fabric-get-started-containers.md).

Následující obrázek znázorňuje různé typy virtualizace a izolace úrovně, které jsou k dispozici.
![Platforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scénáře použití kontejnerů

Tady jsou příklady typických kde kontejner je dobrou volbou:

* **IIS lift a shift**: můžete umístit existující [ASP.NET MVC](https://www.asp.net/mvc) aplikace v kontejneru místo migrace na ASP.NET Core. Tyto aplikace ASP.NET MVC jsou závislé na Internetové informační služby (IIS). Vytvořit balíček tyto aplikace do Image kontejneru z vytvořených image služby IIS a nasadit je s využitím Service Fabric. Zobrazit [Imagí kontejnerů v systému Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) informace o kontejnerech Windows.

* **Kombinovat kontejnery a mikroslužby pro Service Fabric**: použít stávající image kontejneru pro součást aplikace. Například můžete použít [kontejner NGINX](https://hub.docker.com/_/nginx/) pro front-endu webové aplikace a stavové služby pro více náročné na výpočty back-end.

* **Omezit dopad služeb "" hlučným sousedům""**: možnost zásad správného řízení prostředků kontejnerů můžete použít k omezení prostředků, které služba používá na hostiteli. Pokud služby může využívat víc prostředků a ovlivnit tak výkon jiných (jako je například dlouhotrvající, dotaz jako operace), zvažte uvedení do kontejnerů, které mají zásady správného řízení prostředků těchto služeb.

## <a name="service-fabric-support-for-containers"></a>Podpora Service Fabric pro kontejnery

Service Fabric podporuje nasazení kontejnerů Dockeru v Linuxu a Windows Server kontejnery ve Windows serveru 2016, společně s podporou pro režimu izolace Hyper-V. 

Service Fabric nabízí [aplikační model](service-fabric-application-model.md) v, která představuje kontejner hostitele aplikace v několika službu jsou umístěny repliky. Service Fabric také podporuje [scénář spustitelný soubor typu Host](service-fabric-guest-executables-introduction.md) ve které jste nepoužívejte integrovaných programovacích modelů Service Fabric, ale místo toho balíček existující aplikace napsané v libovolném jazyce nebo rozhraní .NET framework, uvnitř kontejner. Tento scénář je běžným případem použití pro kontejnery.

Můžete také spustit [služeb Service Fabric uvnitř kontejneru](service-fabric-services-inside-containers.md). Podpora pro spuštění služeb Service Fabric v kontejnerech je aktuálně omezená.

Service Fabric poskytuje několik možnosti pro kontejnery, které vám pomůžou vytvářet aplikace, které se skládají z kontejnerizované mikroslužby, jako například:

* Nasazení image kontejneru a aktivace.
* Zásady správného řízení prostředků včetně nastavení zdrojů hodnot ve výchozím nastavení v clusterech Azure.
* Ověřování úložiště.
* Port kontejneru do hostitele mapování portů.
* Zjišťování – kontejnery a komunikace.
* Možnost konfigurace a nastavení proměnných prostředí.
* Možnost nastavit přihlašovací údaje zabezpečení na kontejneru.
* Volba jiné síťové režimy pro kontejnery.

Komplexní přehled kontejnerů podporují v Azure, jako je například způsob, jak vytvořit Kubernetes cluster pomocí služby Azure Kubernetes Service vytvoření privátního registru Dockeru v Azure Container Registry a další informace najdete v tématu [Azure pro kontejnery](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o podpoře, kterou Service Fabric poskytuje pro spouštění kontejnerů. Dále jsme se nevede příklady funkce, které se dozvíte, jak je používat.

[Vytvoření první aplikace kontejneru Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md)  
[Vytvoření první aplikace kontejneru Service Fabric na Windows](service-fabric-get-started-containers.md)  
[Další informace o kontejnerech Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png