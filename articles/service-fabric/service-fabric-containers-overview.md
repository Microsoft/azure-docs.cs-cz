---
title: Přehled servisní tkaniny a kontejnerů
description: Přehled Service Fabric a použití kontejnerů k nasazení aplikací mikroslužeb. Tento článek obsahuje přehled o tom, jak lze použít kontejnery a dostupné funkce v Service Fabric.
ms.topic: conceptual
ms.date: 8/8/2018
ms.openlocfilehash: 884cefa3d6a60f55269afac73c40b9f6b21518f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458221"
---
# <a name="service-fabric-and-containers"></a>Servisní tkanina a kontejnery

## <a name="introduction"></a>Úvod

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.

Service Fabric je [microsoft kontejner orchestrátor](service-fabric-cluster-resource-manager-introduction.md) pro nasazení mikroslužeb v clusteru počítačů. Service Fabric těží z poučení získaných během svých let běží služby společnosti Microsoft v masivním měřítku.

Mikroslužby je možné vyvíjet mnoha způsoby, od použití [programovacích modelů Service Fabric](service-fabric-choose-framework.md) přes [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) až po nasazení [libovolného vlastního kódu](service-fabric-guest-executables-introduction.md). Nebo pokud chcete pouze [nasadit a spravovat kontejnery](service-fabric-containers-overview.md), Service Fabric je také skvělou volbou.

Ve výchozím nastavení Service Fabric nasazuje a aktivuje tyto služby jako procesy. Procesy poskytují nejrychlejší aktivaci a nejvyšší hustotu využití prostředků v clusteru. Service Fabric můžete také nasadit služby v inaimages kontejneru. Můžete také kombinovat služby v procesech a služby v kontejnerech ve stejné aplikaci.

Chcete-li přejít přímo do a vyzkoušet kontejnery na Service Fabric, zkuste rychlý start, kurz nebo ukázku:  

[Úvodní příručka: Nasazení aplikace kontejneru Linuxu do service fabricu](service-fabric-quickstart-containers-linux.md)  
[Úvodní příručka: Nasazení aplikace kontejneru systému Windows do service fabricu](service-fabric-quickstart-containers.md)  
[Kontejnerizace existující aplikace v .NET](service-fabric-host-app-in-a-container.md)  
[Ukázky kontejnerů Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Co jsou kontejnery

Kontejnery vyřešit problém spouštění aplikací spolehlivě v různých výpočetních prostředích tím, že poskytuje neměnné prostředí pro spuštění aplikace. Kontejnery zabalit aplikaci a všechny jeho závislosti, jako jsou knihovny a konfigurační soubory, do vlastní izolované 'box', který obsahuje vše potřebné ke spuštění softwaru uvnitř kontejneru. Všude tam, kde je kontejner spuštěn, aplikace uvnitř má vždy vše, co potřebuje ke spuštění, jako jsou správné verze jeho závislých knihoven, všechny konfigurační soubory a cokoli jiného, co potřebuje ke spuštění.

Kontejnery běží přímo nad jádrem a mají izolované zobrazení systému souborů a dalších prostředků. Aplikace v kontejneru nemá žádné znalosti o jiných aplikacích nebo procesech mimo jeho kontejner. Každá aplikace a její runtime, závislosti a systémové knihovny jsou spuštěny uvnitř kontejneru s úplným soukromým přístupem k vlastnímu izolovanému zobrazení operačního systému kontejneru. Kromě toho, že usnadňuje poskytování všech závislostí vaší aplikace, které potřebuje ke spuštění v různých výpočetních prostředích, zabezpečení a izolace prostředků jsou důležité výhody použití kontejnerů s Service Fabric -- který jinak spouští služby v Proces.

Ve srovnání s virtuálními počítači mají kontejnery následující výhody:

* **Malé**: Kontejnery používají jeden úložný prostor a verze vrstev a aktualizace pro zvýšení efektivity.
* **Rychlé**: Kontejnery nemusí zavádět celý operační systém, takže se mohou spustit mnohem rychleji – obvykle během několika sekund.
* **Přenositelnost**: Kontejnerizovaná bitová kopie aplikace může být přenesena tak, aby se spouštěla v cloudu, místně, uvnitř virtuálních počítačů nebo přímo na fyzických počítačích.
* **Zásady správného řízení prostředků**: Kontejner může omezit fyzické prostředky, které může spotřebovat na svém hostiteli.

### <a name="container-types-and-supported-environments"></a>Typy kontejnerů a podporovaná prostředí

Service Fabric podporuje kontejnery na Linuxu i Windows a podporuje režim izolace Hyper-V v systému Windows.

#### <a name="docker-containers-on-linux"></a>Kontejnery Dockeru na Linuxu

Docker poskytuje api pro vytváření a správu kontejnerů nad kontejnery jádra Linuxu. Docker Hub poskytuje centrální úložiště pro ukládání a načítání iontů kontejnerů.
Kurz založený na Linuxu najdete v [tématu Vytvoření první aplikace kontejneru Service Fabric na Linuxu](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Kontejnery Windows Serveru

Windows Server 2016 poskytuje dva různé typy kontejnerů, které se liší podle úrovně izolace. Kontejnery Windows Server a Kontejnery Dockeru jsou podobné, protože oba mají oddělení názvů a izolaci systému souborů, zatímco sdílejí jádro s hostitelem, na který jsou spuštěni. V Systému Linux je tato izolace tradičně poskytována skupinami cgroups a obory názvů a kontejnery systému Windows Server se chovají podobně.

Kontejnery systému Windows s podporou technologie Hyper-V poskytují větší izolaci a zabezpečení, protože žádný kontejner nesdílí jádro operačního systému s žádným jiným kontejnerem nebo s hostitelem. S touto vyšší úrovní izolace zabezpečení jsou kontejnery s podporou technologie Hyper-V zaměřeny na potenciálně nepřátelské scénáře s více tenanty.
Kurz založený na systému Windows najdete v [tématu Vytvoření první aplikace kontejneru Service Fabric v systému Windows](service-fabric-get-started-containers.md).

Následující obrázek znázorňuje různé typy úrovní virtualizace a izolace, které jsou k dispozici.
![Platforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scénáře pro použití kontejnerů

Zde jsou typické příklady, kde kontejner je dobrá volba:

* **Výtah a posun iIS**: Existující [aplikaci mvc ASP.NET](https://www.asp.net/mvc) můžete umístit do kontejneru namísto migrace do ASP.NET jádra. Tyto ASP.NET aplikací MVC závisí na Internetové informační službě (IIS). Tyto aplikace můžete zabalit do iobrazek kontejnerů z předvytvořené bitové kopie služby IIS a nasadit je pomocí service fabric. Informace o kontejnerech Windows [najdete v tématu Image kontejnerů na Windows Serveru.](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)

* **Kombinace kontejnerů a mikroslužeb Service Fabric**: Pro část aplikace použijte existující image kontejneru. Kontejner [NGINX](https://hub.docker.com/_/nginx/) můžete například použít pro webový front-end vaší aplikace a stavové služby pro intenzivnější back-end ový výpočt.

* **Snížit dopad "hlučné sousedé" služby**: Můžete použít možnost zásad správného řízení prostředků kontejnerů omezit prostředky, které služba používá na hostitele. Pokud služby může spotřebovat mnoho prostředků a ovlivnit výkon jiných (například dlouhotrvající operace podobné dotazu), zvažte uvedení těchto služeb do kontejnerů, které mají zásadsprávné řízení prostředků.

## <a name="service-fabric-support-for-containers"></a>Podpora služby Service Fabric pro kontejnery

Service Fabric podporuje nasazení kontejnerů Dockeru na Linuxu a kontejnerů Windows Serveru na Windows Serveru 2016 spolu s podporou režimu izolace Hyper-V. 

Service Fabric poskytuje [model aplikace,](service-fabric-application-model.md) ve kterém kontejner představuje hostitele aplikace, ve kterém jsou umístěny více replik služby. Service Fabric také podporuje [spustitelný scénář hosta,](service-fabric-guest-executables-introduction.md) ve kterém nepoužíváte předdefinované service fabric programovací modely, ale místo toho balíček existující aplikace, napsané pomocí libovolného jazyka nebo rozhraní, uvnitř kontejneru. Tento scénář je běžné použití pro kontejnery.

Služby Service Fabric můžete také spustit [uvnitř kontejneru](service-fabric-services-inside-containers.md). Podpora pro spuštění služby Service Fabric uvnitř kontejnerů je aktuálně omezená.

Service Fabric poskytuje několik možností kontejneru, které vám pomohou vytvářet aplikace, které se skládají z kontejnerizovaných mikroslužeb, jako jsou:

* Nasazení a aktivace image kontejneru.
* Zásady správného řízení prostředků včetně nastavení hodnot prostředků ve výchozím nastavení v clusterech Azure.
* Ověřování úložiště.
* Port kontejneru pro mapování hostitelského portu.
* Zjišťování a komunikace mezi kontejnery.
* Možnost konfigurovat a nastavovat proměnné prostředí.
* Možnost nastavit pověření zabezpečení v kontejneru.
* Výběr různých síťových režimů pro kontejnery.

Úplný přehled podpory kontejnerů v Azure, například jak vytvořit cluster Kubernetes se službou Azure Kubernetes, jak vytvořit privátní registr Dockeru v registru kontejnerů Azure a další informace najdete v [tématu Azure for Containers](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o podporu Service Fabric poskytuje pro spuštění kontejnerů. Dále si projdeme příklady jednotlivých funkcí, abychom vám ukázali, jak je používat.

[Vytvoření první aplikace Service Fabric typu kontejner v Linuxu](service-fabric-get-started-containers-linux.md)  
[Vytvoření první aplikace Service Fabric typu kontejner v systému Windows](service-fabric-get-started-containers.md)  
[Další informace o kontejnerech s Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png