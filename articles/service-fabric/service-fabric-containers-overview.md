---
title: "Přehled Service Fabric a kontejnery | Microsoft Docs"
description: "Přehled Service Fabric a použití kontejnerů k nasazení aplikací mikroslužby. Tento článek obsahuje přehled použití kontejnery a možnosti dostupné v Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: f12dc08953372b2dfae773df11cf1f47b42a1b89
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-and-containers"></a>Service Fabric a kontejnery
> [!NOTE]
> Nasazení kontejnerů do clusteru Service Fabric v systému Windows 10 nebo s Docker CE není podporována. 
>   

> [!NOTE]
> Service Fabric verze 6.1 nabízí podporu verze Preview pro Windows Server verze 1709. Otevřené sítě a služba DNS Service Fabricu s Windows Serverem verze 1709 nefungují. 
> 

## <a name="introduction"></a>Úvod
Azure Service Fabric je [orchestrator](service-fabric-cluster-resource-manager-introduction.md) služeb mezi clustery počítačů za několik let využití a optimalizace v masivním měřítku služeb společnosti Microsoft. Služby mohou být vytvořeny v mnoha směrech pomocí [Service Fabric programovací modely](service-fabric-choose-framework.md) nasazení [spustitelné soubory hosta](service-fabric-guest-executables-introduction.md). Ve výchozím nastavení Service Fabric nasadí a aktivuje tyto služby jako procesy. Procesy poskytují nejrychlejší aktivace a nejvyšší hustotu využití prostředků v clusteru. Service Fabric lze také nasadit services v kontejneru bitové kopie. Je důležité je možné kombinovat služby v procesy a služby v kontejnerech ve stejné aplikaci.   

## <a name="what-are-containers"></a>Co jsou kontejnery?
Kontejnery jsou zapouzdřené, jednotlivě nasadit součásti, které běží jako izolovaných instancí na stejné jádra využívat výhody virtualizace, které poskytuje operační systém. Proto každou aplikaci a její runtime, závislosti a systémové knihovny spusťte uvnitř kontejneru s úplnou, soukromý přístup do kontejneru vlastní izolované zobrazení konstrukce operačního systému. Společně s přenositelnost je tato úroveň zabezpečení a prostředků izolace hlavní výhody pro kontejnery pomocí Service Fabric, který jinak běží služby v procesech.

Kontejnery jsou technologie virtualizace, která Virtualizuje příslušný operační systém z aplikací. Kontejnery zadejte neměnné prostředí pro spouštění s různým stupněm izolace aplikací. Kontejnery běží přímo nad jádra a mít pohled izolované systému souborů a dalším prostředkům. Kontejnery ve srovnání s virtuálními počítači, mají následující výhody:

* **Malé**: kontejnery použít ke zvýšení efektivity jednoho úložiště a vrstvy verze a aktualizací.
* **Rychlé**: kontejnery nemusíte spouštěcí celý operační systém, aby se mohl spustit mnohem rychlejší, obvykle v sekundách.
* **Přenositelnost**: kontejnerové aplikace image můžete přenést na spouštění v cloudu, místně, uvnitř virtuálních počítačů, nebo přímo na fyzických počítačů.
* **Zásady správného řízení prostředků**: Kontejner může omezit fyzické prostředky, které můžou využívat na jeho hostitele.

## <a name="container-types-and-supported-environments"></a>Typy kontejneru a podporovaných prostředí
Service Fabric podporuje kontejnery na Linuxu a Windows a na k tomu také podporuje technologie Hyper-V izolovaném režimu. 

> [!NOTE]
> Nasazení kontejnerů do clusteru Service Fabric na Windows 10 se aktuálně nepodporuje. 
> 

### <a name="docker-containers-on-linux"></a>Kontejnery docker v systému Linux
Docker poskytuje vysoké úrovně rozhraní API pro vytváření a Správa kontejnerů nad kontejnery jádra systému Linux. Úložiště docker Hub je centrální úložiště pro ukládání a načítání obrázků kontejneru.
Podívejte se kurz [nasadit kontejner Docker do Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Windows Server kontejnery
Windows Server 2016 nabízí dva různé typy kontejnery, které se liší v úroveň izolace zadaná. Windows Server kontejnery a Docker kontejnery jsou podobné, protože oba mají obor názvů a soubor izolace systému, ale jádra nasdílet, na kterém běží na hostiteli. V systému Linux, tato izolace tradičně poskytl `cgroups` a `namespaces`, a Windows Server kontejnery chovají podobně.

Kontejnery Windows s podporou Hyper poskytnout další izolace a zabezpečení, protože každý kontejner nesdílí jádra operačního systému, jiné kontejnery nebo s hostiteli. S Tato vyšší úroveň izolace zabezpečení technologie Hyper-V povolena kontejnery cílí na k tomuto účelu, víceklientské scénáře.
Podívejte se kurz [nasazení kontejneru systému Windows pro Service Fabric](service-fabric-get-started-containers.md).

Následující obrázek ukazuje různé typy virtualizace a izolaci úrovně, které jsou k dispozici v operačním systému.
![Platforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scénáře použití kontejnerů
Zde jsou příklady typických kde kontejner je vhodné použít:

* **Služba IIS navýšení a posunutí**: Pokud máte existující [ASP.NET MVC](https://www.asp.net/mvc) aplikace, které chcete nadále používat, jejich umístění v kontejneru místo migrace je ASP.NET Core. Tyto aplikace ASP.NET MVC závisí na Internetové informační služby (IIS). Můžete balíček tyto aplikace do bitové kopie kontejner z image precreated služby IIS a jejich nasazení pomocí Service Fabric. V tématu [kontejneru bitové kopie v systému Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) informace o Windows kontejnery.
* **Kombinovat kontejnery a mikroslužeb Service Fabric**: pomocí stávající image kontejner pro součást aplikace. Například můžete použít [kontejner nginx a SVÁŽE](https://hub.docker.com/_/nginx/) pro front-end webové aplikace a stavové služby pro více náročné výpočty back-end.
* **Snížení vlivu služby "aktivní okolí"**: možnost zásad správného řízení prostředků kontejnerů můžete použít k omezení prostředků, které služba používá na hostiteli. Pokud služby může využívat mnoho prostředků a vliv na výkon jiných (třeba dlouho běžící, dotaz jako operace), vezměte v úvahu uvedení těchto služeb do kontejnery, které mají zásady správného řízení zdrojů.

## <a name="service-fabric-support-for-containers"></a>Podpora Service Fabric pro kontejnery
Service Fabric podporuje nasazení kontejnerů Docker na Linux a Windows Server kontejnery v systému Windows Server 2016, společně s podporou pro technologii Hyper-V izolovaném režimu. 

V Service Fabric [aplikačního modelu](service-fabric-application-model.md), kontejner představuje hostitele aplikace ve více služby, která jsou umístěna repliky. Service Fabric můžete spustit všechny kontejnery a tento scénář je podobná [spustitelné scénář hostovaného](service-fabric-guest-executables-introduction.md), kde balíček existující aplikaci uvnitř kontejneru. Tento scénář je běžný případ použití pro kontejnery a příklady spuštění aplikace napsané v libovolném jazyce nebo rozhraní, ale ne pomocí předdefinovaných programovací modely Service Fabric.

Kromě toho můžete spustit [služby Service Fabric uvnitř kontejnery](service-fabric-services-inside-containers.md) také. Podpora pro spuštěné služby Service Fabric uvnitř kontejnery je aktuálně omezená a bude lepší v budoucích verzích.

Service Fabric má několik kontejneru možnosti, které vám pomůžou vytvářet aplikace, které se skládají z mikroslužeb, která jsou kontejnerizované. Service Fabric nabízí následující možnosti pro kontejnerové služby:

* Nasazení bitové kopie kontejneru a aktivaci.
* Nastavení hodnot prostředků ve výchozím nastavení v clusterech Azure včetně prostředků zásad správného řízení.
* Ověření úložiště.
* Port kontejneru na mapování portů hostitele.
* Zjišťování – kontejnery a komunikace.
* Možnost konfigurace a nastavení proměnných prostředí.
* Možnost nastavit pověření zabezpečení na kontejneru.
* Volba různé sítě režimy kontejnery.

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o kontejnery, Service Fabric je kontejner orchestrator, a že Service Fabric obsahuje funkce, které podporují kontejnery. Jako další krok jsme přejděte přes příklady každé funkce, které chcete ukazují, jak je používat.

[Vytvoření první aplikace Service Fabric kontejneru v systému Windows](service-fabric-get-started-containers.md)

[Vytvoření první aplikace Service Fabric kontejneru v systému Linux](service-fabric-get-started-containers-linux.md)

[Další informace o Windows kontejnery](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
