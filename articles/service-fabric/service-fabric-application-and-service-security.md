---
title: Další informace o zabezpečení aplikace Azure Service Fabric | Microsoft Docs
description: Přehled o tom, jak bezpečně spustit mikroslužeb aplikace v Service Fabric. Informace o spuštění služby a po spuštění skriptu v rámci různých zabezpečení účtů, ověřování a autorizaci uživatelů, spravovat tajné klíče aplikace, zabezpečené komunikace služby, použijte Brána rozhraní API a data zabezpečení aplikací v klidovém stavu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: fa6d46186ad833b68e60c24f742d210b7845759a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207906"
---
# <a name="service-fabric-application-and-service-security"></a>Aplikace Service Fabric a zabezpečení služby
Můžete zahrnout architektura mikroslužeb [spoustu výhod](service-fabric-overview-microservices.md). Správa zabezpečení mikroslužeb, je však výzvy a jiné než pro správu zabezpečení tradiční monolitický aplikace. 

S monolitu aplikace obvykle běží na jeden nebo více serverů v síti, a je snazší identifikaci zveřejněné porty a rozhraní API a IP adresu. Je často jeden hraniční nebo hranic a jedna databáze k ochraně. Pokud daný systém ohrožení kvůli porušení zabezpečení nebo útok, je pravděpodobné, že vše v rámci systému bude k dispozici pro útočník. S mikroslužeb systém je složitější.  Služby jsou decentralizované a distribuován do mnoha hostitelů a migrace mezi hostiteli.  Správné zabezpečení můžete omezit oprávnění, které může útočník získat a množství dat, které jsou k dispozici v jednom útoku před nedodržením jedna služba.  Komunikace není interní, ale se stane, přes síť, a existuje mnoho zveřejněné porty a interakce mezi službami. Zároveň budete vědět, co jsou tyto služby interakce a při jejich dojít, je nezbytné k zabezpečení vaší aplikace.

V tomto článku není průvodce mikroslužeb zabezpečení, existuje mnoho těchto zdrojů online, ale popisuje, jak různých aspektů zabezpečení lze provést v Service Fabric.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Často je nutné pro prostředky a rozhraní API vystavené služby omezeny na určité důvěryhodných uživatelů nebo klienti. Ověřování je proces spolehlivě zjištění identity uživatele.  Autorizace je proces, který umožňuje rozhraní API nebo služeb dostupné pro některé ověřeného uživatele, ale jiné ne.

### <a name="authentication"></a>Authentication
Prvním krokem k rozhodování o úroveň rozhraní API vztahu důvěryhodnosti je ověřování. Ověřování je proces spolehlivě zjištění identity uživatele.  Ve scénářích mikroslužbu ověřování je obvykle zpracovávány centrálně. Pokud používáte bránu rozhraní API, můžete [snižování zátěže ověřování](/azure/architecture/patterns/gateway-offloading) k bráně. Pokud tuto metodu použijte, ujistěte se, že jednotlivé služby není dostupný přímo (bez bránou rozhraní API), není-li zvýšit zabezpečení na místě k ověření zprávy jestli pocházejí z brány nebo ne.

Je-li služby je přístupná přímo, jako ověřovací služba Azure Active Directory nebo vyhrazené ověřování mikroslužby, který funguje jako zabezpečení služby tokenů (STS) lze použít k ověřování uživatelů. Rozhodnutí o důvěryhodnosti jsou sdíleny mezi službami s tokeny zabezpečení nebo soubory cookie. 

Pro ASP.NET Core, primární mechanismus pro [ověřování uživatelů](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) je systém členství ASP.NET Core Identity. Identitu ASP.NET Core uchovává informace o uživateli (včetně přihlašovací údaje, rolí a deklarací identity) v úložišti dat konfigurovat tak, že vývojář. Jádro ASP.NET Identity podporuje dvoufaktorové ověřování.  Externí zprostředkovatelé ověřování jsou také podporovány, takže uživatelé mohou přihlásit pomocí stávající ověřovací procesy od poskytovatelů, jako je Microsoft, Google, Facebook nebo Twitter. 

### <a name="authorization"></a>Autorizace
Po ověření, služby, třeba autorizaci přístupu uživatelů nebo zjistit, co uživatel je moct provádět. Tento postup umožňuje službě aby rozhraní API dostupné některé ověřeným uživatelům, ale ne všechny. Autorizace je ortogonální a nezávislé na ověřování, což je proces pro zjišťování, který je uživatel. Ověřování může vytvořit jeden nebo více identit pro aktuálního uživatele.

[Autorizace ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) můžete provést podle role uživatelů nebo na základě vlastní zásad, která by mohla obsahovat zkontrolujete deklarace identity nebo jiné heuristiky.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Omezte a zabezpečený přístup pomocí služby gateway rozhraní API
Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. [Brána rozhraní API](/azure/architecture/microservices/gateway) nachází mezi klienty a služby a vstupním bodem ke všem službám, které poskytuje aplikace. Slouží jako reverzní proxy server, směrování požadavky od klientů ke službám. Je také provádět různé úlohy mezi vyjímání, jako je například ověřování a autorizace, ukončení protokolu SSL a omezení rychlosti. Pokud nemáte nasadit bránu, musí klienti odesílat žádosti přímo do front-endové služby.

V Service Fabric bránu být jakékoli bezstavové služby, jako [aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), nebo jiné služby, které jsou určené pro příchozí provoz, například [Træfik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/), nebo [Azure API Management](https://docs.microsoft.com/azure/api-management).

API Management se integruje přímo s Service Fabric, že vám umožní publikovat rozhraní API s bohatou sadu pravidel směrování k vašim službám Service Fabric back-end.  Zabezpečený přístup k back-end služby, zabránit před útoky DOS pomocí omezování nebo ověřte klíče rozhraní API, tokeny JWT, certifikáty a jiné přihlašovací údaje. Další informace, přečtěte si [Service Fabric s Azure API Management přehled](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Správa tajných klíčů aplikací
Tajné klíče může být žádné citlivé informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu. Tento článek používá Azure Key Vault pro správu klíčů a tajných klíčů. Ale *pomocí* tajných klíčů v aplikaci je Cloudová platforma vznikl k aplikacím umožňují nasadit do clusteru s podporou hostovat kdekoli.

Doporučený způsob, jak spravovat nastavení konfigurace služby je prostřednictvím [služby balíčky konfigurace][config-package]. Konfigurace balíčků jsou verzí a aktualizovat prostřednictvím spravované postupné upgrady s vyhodnocení stavu a automatického vrácení zpět. Jedná se upřednostňované globální konfiguraci, protože snižuje pravděpodobnost výpadkem globální služby. Šifrované tajné klíče jsou žádná výjimka. Service Fabric obsahuje integrované funkce pro šifrování a dešifrování hodnot v konfiguračním souboru souborech Settings.xml balíček pomocí šifrování certifikátu.

Následující diagram znázorňuje základní postup pro tajný správy v aplikaci Service Fabric:

![Přehled tajný správy][overview]

V tomto toku existují čtyři hlavní kroky:

1. Získejte certifikát dat šifrování.
2. Nainstalujte certifikát v clusteru.
3. Šifrování tajný hodnoty při nasazení aplikace pomocí certifikátu a vložit je do služby souborech Settings.xml konfigurační soubor.
4. Číst šifrovaných hodnot mimo souborech Settings.xml dešifrování stejným certifikátem šifrování. 

[Azure Key Vault] [ key-vault-get-started] zde slouží jako umístění úložiště bezpečné pro certifikáty a jako způsob, jak získat certifikáty, které jsou nainstalované na clusterů Service Fabric v Azure. Pokud nejsou nasazení do Azure, není nutné používat ke správě tajných klíčů v Service Fabric aplikace Key Vault.

Příklad, naleznete v části [spravovat tajné klíče aplikace](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpečené hostitelské prostředí
Pomocí Azure Service Fabric můžete zabezpečit aplikace, které jsou spuštěny v clusteru v rámci jiné uživatelské účty. Service Fabric také pomáhá zabezpečit prostředky, které jsou používány aplikací v době nasazení podle uživatelských účtů – například soubory, adresářů a certifikáty. Díky spuštěné aplikace, i v prostředí sdílené hostované bezpečnější od sebe navzájem.

Manifest aplikace deklaruje, že objekty zabezpečení (uživatele a skupiny), vyžaduje spuštění služeb a zabezpečeným prostředkům.  Tyto funkce zabezpečení, které objekty se odkazuje v zásadách, například při spustit jako, koncový bod vazby, balíček sdílení nebo zásady zabezpečení přístupu.  Zásady jsou poté použity prostředky služby v **ServiceManifestImport** oddílu manifest aplikace.

Pokud deklarace objekty zabezpečení, můžete také definovat a vytvořit skupiny uživatelů, aby pro každou skupinu pro správu společně lze přidat jeden nebo více uživatelů. To je užitečné, pokud existuje více uživatelů pro různé služby vstupní body a vyžadují, aby byla určité společné oprávnění, které jsou k dispozici na úrovni skupiny.

Ve výchozím nastavení se aplikace Service Fabric běžet pod účtem, proces Fabric.exe kompatibilní se. Service Fabric taky poskytuje možnost spouštět aplikace pod účtem místního uživatelského účtu nebo účtu local system, který je určený v rámci manifest aplikace. Další informace najdete v tématu [spuštění služby jako účet místního uživatele nebo místní systémový účet](service-fabric-application-runas-security.md).  Můžete také [spustit skript spuštění služby jako účet místního uživatele nebo systému](service-fabric-run-script-at-service-startup.md).

Když spouštíte Service Fabric na samostatné clusteru se systémem Windows, můžete spustit službu pod [účtů domény služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo [skupiny účty spravované služby](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpečené kontejnery
Service Fabric poskytuje mechanismus pro služby uvnitř kontejneru pro přístup k certifikátu, který je nainstalován na uzly v clusteru systému Windows nebo Linux (verze 5.7 nebo vyšší). Tento certifikát PFX, který slouží pro ověřování aplikace nebo služby nebo zabezpečenou komunikaci s jinými službami. Další informace najdete v tématu [Import certifikátu do kontejneru](service-fabric-securing-containers.md).

Kromě toho Service Fabric také podporuje gMSA (skupinové účty spravované služby) pro Windows kontejnery. Další informace najdete v tématu [nastavit gMSA pro kontejnery Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Zabezpečené komunikace služby
V Service Fabric běží služba někde v clusteru Service Fabric, obvykle se distribuují napříč více virtuálními počítači. Service Fabric nabízí několik možností pro zabezpečení komunikace služby.

Můžete povolit koncové body HTTPS v vaše [ASP.NET Core nebo Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webové služby.

Můžete vytvořit zabezpečeného spojení mezi reverzní proxy server a služby, a tak umožňuje zabezpečený kanál začátku do konce. Připojení k zabezpečení služeb je podporováno pouze v případě, že reverzní proxy server je nakonfigurován pro naslouchání na HTTPS. Informace o konfiguraci reverzní proxy server, najdete v tématu [reverzní proxy server v Azure Service Fabric](service-fabric-reverseproxy.md).  [Připojení ke službě zabezpečené](service-fabric-reverseproxy-configure-secure-communication.md) popisoval metodu pro vytvoření zabezpečeného spojení mezi reverzní proxy server a služby.

Rozhraní spolehlivé služby poskytuje několik předem komunikace zásobníky a nástroje, které můžete použít k vylepšení zabezpečení. Zjistěte, jak zlepšit zabezpečení, pokud používáte vzdálenou komunikaci služby (v [C#](service-fabric-reliable-services-secure-communication.md) nebo [Java](service-fabric-reliable-services-secure-communication-java.md)) nebo pomocí [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Šifrování dat aplikací
Každý [typ uzlu](service-fabric-cluster-nodetypes.md) Cluster Service Fabric běžící v Azure je zálohovaný díky [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric.  Pokud vaše služby uložit data do diskem připojená data, můžete [šifrování těchto dat disků](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) chránit data aplikací.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Spusťte instalační skript při spuštění služby](service-fabric-run-script-at-service-startup.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Další informace o zabezpečení clusteru](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png