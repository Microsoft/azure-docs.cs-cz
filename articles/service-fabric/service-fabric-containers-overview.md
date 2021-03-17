---
title: Přehled Service Fabric a kontejnerů
description: Přehled Service Fabric a použití kontejnerů k nasazení aplikací mikroslužeb. Tento článek poskytuje přehled o tom, jak můžou být kontejnery použity, a dostupné možnosti v Service Fabric.
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091653"
---
# <a name="service-fabric-and-containers"></a>Service Fabric a kontejnery

## <a name="introduction"></a>Úvod

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů.

Service Fabric je produkt [Orchestrator](service-fabric-cluster-resource-manager-introduction.md) Microsoftu pro nasazení mikroslužeb napříč clusterem počítačů. Service Fabric výhody z lekcí, které se během svých let dozvěděly o službách v Microsoftu v obrovském měřítku.

Mikroslužby je možné vyvíjet mnoha způsoby, od použití [programovacích modelů Service Fabric](service-fabric-choose-framework.md) přes [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) až po nasazení [libovolného vlastního kódu](service-fabric-guest-executables-introduction.md). Nebo pokud chcete pouze [nasazovat a spravovat kontejnery](service-fabric-containers-overview.md), Service Fabric je také skvělou volbou.

Ve výchozím nastavení Service Fabric nasadí a aktivuje tyto služby jako procesy. Procesy poskytují nejrychlejší aktivaci a nejvyšší využití hustoty prostředků v clusteru. Service Fabric můžou nasazovat i služby v imagích kontejnerů. Služby v kontejnerech a služby můžete v rámci stejné aplikace kombinovat také v procesech.

Pokud chcete přejít přímo do kontejneru a vyzkoušet si kontejnery na Service Fabric, vyzkoušejte si rychlý Start, kurz nebo ukázku:  

[Rychlý Start: nasazení aplikace typu kontejner pro Linux na Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Rychlý Start: nasazení aplikace typu kontejner pro Windows do Service Fabric](service-fabric-quickstart-containers.md)  
[Kontejnerizace existující aplikace v .NET](service-fabric-host-app-in-a-container.md)  
[Ukázky kontejnerů Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Co jsou kontejnery

Kontejnery řeší problém probíhajících aplikací v různých výpočetních prostředích, protože poskytují neměnné prostředí, ve kterém se aplikace spouští. Kontejnery zabalí aplikaci a všechny její závislosti, jako jsou knihovny a konfigurační soubory, do svého samostatného izolovaného pole, které obsahuje vše potřebné ke spuštění softwaru uvnitř kontejneru. Bez ohledu na to, kde je kontejner spuštěný, má aplikace uvnitř IT vždycky vše, co potřebuje ke spuštění, jako jsou správné verze závislých knihoven, všechny konfigurační soubory a cokoli jiného, co musí spustit.

Kontejnery se spouštějí přímo nad jádrem a mají izolované zobrazení systému souborů a dalších prostředků. Aplikace v kontejneru nemá žádné znalosti žádné jiné aplikace ani procesy mimo svůj kontejner. Každá aplikace a její modul runtime, závislosti a systémové knihovny jsou spouštěny v kontejneru s plným privátním přístupem k vlastnímu izolovanému zobrazení kontejneru operačního systému. Kromě toho, že je možné snadno poskytnout všechny závislosti vaší aplikace, které potřebuje spustit v různých výpočetních prostředích, jsou zabezpečení a izolace prostředků důležité přínosy při používání kontejnerů s Service Fabric – což jinak spouští služby v procesu.

V porovnání s virtuálními počítači mají kontejnery následující výhody:

* **Malé**: kontejnery používají k zvýšení efektivity jeden prostor úložiště a verze vrstev a aktualizace.
* **Rychlé**: kontejnery nemusejí spouštět celý operační systém, takže můžou začít mnohem rychleji – obvykle během několika sekund.
* **Přenositelnost**: kontejnerová image aplikace se dá přenést tak, aby běžela v cloudu, místně, v rámci virtuálních počítačů nebo přímo na fyzických počítačích.
* Zásady **správného řízení prostředků**: kontejner může omezit fyzické prostředky, které může na svém hostiteli spotřebovat.

### <a name="container-types-and-supported-environments"></a>Typy kontejnerů a podporovaná prostředí

Service Fabric podporuje kontejnery v systémech Linux i Windows a podporuje režim izolace technologie Hyper-V ve Windows.

#### <a name="docker-containers-on-linux"></a>Kontejnery Docker v systému Linux

Docker poskytuje rozhraní API k vytváření a správě kontejnerů nad kontejnery jádra systému Linux. Docker Hub poskytuje centrální úložiště pro ukládání a načítání imagí kontejneru.
Kurz založený na systému Linux najdete v tématu [Vytvoření první Service Fabric aplikace typu kontejner na platformě Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Kontejnery Windows Serveru

Windows Server 2016 a novější poskytují dva různé typy kontejnerů, které se liší podle úrovně izolace. Kontejnery Windows serveru a kontejnery Docker jsou podobné, protože oba mají izolovaný obor názvů a systém souborů a zároveň sdílejí jádro s hostitelem, na kterém běží. V systému Linux byla tato izolace tradičně poskytována cgroups a obory názvů a kontejnery Windows serveru se chovají podobně.

Kontejnery Windows s podporou technologie Hyper-V poskytují větší izolaci a zabezpečení, protože žádný kontejner nesdílí jádro operačního systému s žádným jiným kontejnerem nebo s hostitelem. V této vyšší úrovni izolace zabezpečení jsou kontejnery s podporou technologie Hyper-V cílené na potenciálně nepřátelské a víceklientské scénáře.
Kurz založený na systému Windows najdete v tématu [Vytvoření první Service Fabric aplikace typu kontejner ve Windows](service-fabric-get-started-containers.md).

Na následujícím obrázku jsou znázorněny různé typy virtualizace a úrovně izolace.
![Platforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scénáře použití kontejnerů

Tady jsou typické příklady, kde je kontejner dobrou volbou:

* **Zvednutí a posunutí služby IIS**: existující aplikaci [ASP.NET MVC](https://www.asp.net/mvc) můžete vložit do kontejneru místo migrace na ASP.NET Core. Tyto aplikace ASP.NET MVC jsou závislé na Internetová informační služba (IIS). Tyto aplikace můžete zabalit do kontejnerových imagí z předvytvořené bitové kopie služby IIS a nasadit je pomocí Service Fabric. Informace o kontejnerech Windows najdete v tématu [Image kontejnerů na Windows serveru](/virtualization/windowscontainers/quick-start/quick-start-windows-server) .

* **Kombinovat kontejnery a Service Fabric mikroslužby**: pro část aplikace použijte existující image kontejneru. Například můžete použít [kontejner Nginx](https://hub.docker.com/_/nginx/) pro webový front-end aplikace a stavové služby pro náročnější výpočet back-endu.

* **Snižte dopad služeb s vysokou úrovní šumu**: pomocí možností zásad správného řízení prostředků kontejnerů můžete omezit prostředky, které služba používá na hostiteli. Pokud služby můžou spotřebovávat mnoho prostředků a ovlivnit výkon ostatních (například dlouhotrvající operace typu dotazování), zvažte, že tyto služby vložíte do kontejnerů, které mají zásady správného řízení prostředků.

## <a name="service-fabric-support-for-containers"></a>Podpora služby Service Fabric pro kontejnery

Service Fabric podporuje nasazení kontejnerů Docker v systémech Linux a kontejnerů Windows serveru v systému Windows Server 2016 a novějších společně s podporou režimu izolace technologie Hyper-V.

> [!NOTE]
> Kontejnery nejsou podporované v místních clusterech s jedním uzlem Service Fabric (žádné clustery Linux v OneBox ani clustery Windows v místních instalacích Service Fabric).

Service Fabric poskytuje [model aplikace](service-fabric-application-model.md) , ve kterém kontejner představuje hostitele aplikace, ve kterém je umístěno více replik služby. Service Fabric také podporuje [scénář hostující spustitelný soubor](service-fabric-guest-executables-introduction.md) , ve kterém nepoužíváte předdefinované Service Fabric programovací modely, ale místo toho zabalíte existující aplikaci, která je napsaná pomocí libovolného jazyka nebo architektury uvnitř kontejneru. Tento scénář je běžným případem použití kontejnerů.

Můžete také spustit [Service Fabric služby uvnitř kontejneru](service-fabric-services-inside-containers.md). Podpora pro spouštění služeb Service Fabric Services uvnitř kontejnerů je aktuálně omezená.

Service Fabric poskytuje několik možností kontejneru, které vám pomůžou sestavovat aplikace, které se skládají z kontejnerových mikroslužeb, jako například:

* Nasazení a aktivace image kontejneru
* Zásady správného řízení prostředků, včetně nastavení hodnot prostředků ve výchozím nastavení v clusterech Azure.
* Ověřování úložiště.
* Port kontejneru pro mapování portu hostitele.
* Zjišťování a komunikace kontejneru do kontejneru.
* Možnost konfigurace a nastavení proměnných prostředí.
* Možnost nastavení zabezpečovacích přihlašovacích údajů v kontejneru.
* Výběr různých síťových režimů pro kontejnery.

Úplný přehled podpory kontejnerů v Azure, jako je vytvoření clusteru Kubernetes pomocí služby Azure Kubernetes, jak vytvořit privátní registr Docker v systému Azure Container Registry a další informace najdete v tématu [Azure for Containers](../containers/index.yml).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o podpoře, které Service Fabric poskytuje pro spuštěné kontejnery. V dalším kroku provedeme příklady jednotlivých funkcí a ukážeme vám, jak je používat.

[Vytvoření první aplikace Service Fabric typu kontejner v Linuxu](service-fabric-get-started-containers-linux.md)  
[Vytvoření první aplikace Service Fabric typu kontejner v systému Windows](service-fabric-get-started-containers.md)  
[Další informace o kontejnerech Windows](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
