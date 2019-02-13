---
title: Další informace o zabezpečení aplikace Azure Service Fabric | Dokumentace Microsoftu
description: Přehled o tom, jak bezpečně spouštět aplikace mikroslužby na platformě Service Fabric. Zjistěte, jak spustit služby a po spuštění skriptu v rámci účtů zabezpečení, ověřování a autorizaci uživatelů, Správa tajných klíčů aplikací, zabezpečená komunikace služeb, použít bránu rozhraní API a zabezpečení aplikačních dat v klidovém stavu.
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
ms.openlocfilehash: 91e7fdd215d246156f601d3b5e6e05b7f8f71f59
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116452"
---
# <a name="service-fabric-application-and-service-security"></a>Zabezpečení služby a aplikace Service Fabric
Architektura mikroslužeb přinést [řadu výhod](service-fabric-overview-microservices.md). Správa zabezpečení mikroslužeb, ale výzvy a jiné než Správa zabezpečení tradiční monolitické aplikace. 

S monolitu na části aplikace obvykle běží na jeden nebo více serverů v síti a je pak snadno identifikujete vystavené porty a rozhraní API a IP adresu. Často dochází jeden hraniční nebo hranice a jednu databázi chránit. Pokud z důvodu porušení zabezpečení nebo útoku dojde k ohrožení systému, je pravděpodobné, že všechno v rámci systému bude k dispozici pro útočníka. S využitím mikroslužeb systém je složitější.  Služby jsou decentralizované a distribuovat napříč mnoha hostitelích a migrace mezi hostiteli.  Správné zabezpečení můžete omezit oprávnění, které může útočník získat a množství dat, které jsou k dispozici v rámci jednoho útoku porušení jednu službu.  Není interní komunikaci, ale se stane po síti a existuje mnoho vystavené porty a interakce mezi službami. Vědět, co jsou to tyto služby interakce a kdy k nim dojde, je nezbytné k zabezpečení vašich aplikací.

Tento článek není příručka k zabezpečení mikroslužeb, k dispozici řada těchto zdrojů online, ale popisuje, jak různé aspekty zabezpečení se dají naplnit v Service Fabric.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Často je nezbytné pro rozhraní API, vystavené služby omezená na určité důvěryhodnými uživateli nebo klienty a prostředky. Ověřování je proces spolehlivě zjištění identity uživatele.  Autorizace je proces, který umožňuje rozhraní API nebo služeb dostupných na některé ověření uživatele, ale ne pro jiné.

### <a name="authentication"></a>Authentication
Prvním krokem při rozhodování o důvěryhodnosti úroveň rozhraní API je ověřování. Ověřování je proces spolehlivě zjištění identity uživatele.  Ve scénářích mikroslužeb ověřování je obvykle zpracovávána centrálně. Pokud používáte bránu rozhraní API, můžete si [snižování zátěže ověřování](/azure/architecture/patterns/gateway-offloading) k bráně. Pokud použijete tento přístup, ujistěte se, že jednotlivé služby přímo (bez brány rozhraní API) není dostupný, není-li zvýšit zabezpečení se používají k ověření zprávy, jestli pocházejí z brány nebo ne.

Pokud přímo přístupné služby, jako ověřovací služba Azure Active Directory nebo vyhrazené ověřování mikroslužby funguje jako bezpečnostní služby tokenu (STS) se dá použít k ověřování uživatelů. Rozhodnutí o důvěryhodnosti jsou sdíleny mezi službami s tokeny zabezpečení nebo soubory cookie. 

Pro ASP.NET Core, hlavní mechanismus pro [ověřování uživatelů](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) je systém členství technologie ASP.NET Core Identity. ASP.NET Core Identity ukládá informace o uživatelském (včetně přihlašovací údaje, rolí a deklarací identity) v úložišti dat nakonfigurované vývojářem. ASP.NET Core Identity podporuje dvojúrovňového ověřování.  Externí zprostředkovatelé ověřování jsou také podporovány, takže uživatelé můžou přihlásit pomocí existujících procesů ověřování od poskytovatelů, jako je Microsoft, Google, Facebook nebo Twitter. 

### <a name="authorization"></a>Autorizace
Po ověření služby potřebovat autorizaci přístupu uživatelů nebo zjistit, co uživatel je možné provádět. Tento proces umožňuje službě vytvořit rozhraní API dostupné pro některé ověřeného uživatele, ale ne pro všechny. Autorizace je ortogonální a nezávisle na ověřování, což je proces zjišťování, který je uživatel. Ověřování můžou vytvořit jednu nebo více identit pro aktuálního uživatele.

[Ověřování ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) můžete provést podle role uživatelů nebo založená na vlastní zásadu, která by mohla obsahovat kontrola deklarace identity nebo jiných heuristické metody.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Omezení a zabezpečení přístupu k použití brány rozhraní API
Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. [Brána rozhraní API](/azure/architecture/microservices/gateway) umístěná mezi klienty a služby a je vstupním bodem ke všem službám, které poskytuje aplikace. Slouží jako reverzní proxy server, směrování požadavků od klientů ke službám. Mohou také provádět různé vyskytující úlohy, jako je například ověřování a autorizace, ukončování protokolu SSL a omezení četnosti. Pokud se nenasazují bránu, musí klienti odesílají žádosti o přímo do front-endové služby.

V Service Fabric, může být brána Bezstavová služba, jako [aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), nebo jiná služba navržená pro příjem provozu, jako například [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/), nebo [Azure API Management](https://docs.microsoft.com/azure/api-management).

API Management se integruje přímo do Service Fabric umožňuje publikovat rozhraní API s bohatou sadou pravidel směrování pro back-end služby Service Fabric.  Zabezpečený přístup ke službám back-endu, útokům DOS pomocí omezování nebo ověření klíče rozhraní API, tokeny JWT, certifikáty a jiné přihlašovací údaje. Další informace najdete v článku [Service Fabric s Azure API Management přehled](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Správa tajných klíčů aplikací
Tajné klíče může být žádné citlivé údaje, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovat ve formátu prostého textu. Tento článek používá Azure Key Vault pro správu klíčů a tajných kódů. Ale *pomocí* tajných kódů v aplikaci je cloud více platforem, aby umožňovala nasadit do clusteru už jsou hostované kdekoli.

Doporučený způsob, jak spravovat nastavení konfigurace služby je prostřednictvím [balíčky pro konfiguraci služby][config-package]. Balíčky pro konfiguraci se systémovou správou verzí a aktualizovat prostřednictvím spravované postupné upgrady pomocí ověření stavu a automatické vrácení zpět. To je upřednostňována před globální konfiguraci, protože snižuje pravděpodobnost globální výpadku. Šifrované tajné klíče jsou žádná výjimka. Service Fabric obsahuje integrované funkce pro šifrování a dešifrování hodnot v souboru Settings.xml balíček konfigurace pomocí certifikátu šifrování.

Následující diagram znázorňuje základní postup pro správy tajných kódů v aplikaci Service Fabric:

![Přehled správy tajných kódů][overview]

Existují čtyři hlavní kroky v tomto toku:

1. Certifikát šifrování dat získáte.
2. Instalace certifikátu ve vašem clusteru.
3. Šifrovat hodnoty tajných kódů při nasazení aplikace pomocí certifikátu a vložit do konfiguračního souboru Settings.xml služby.
4. Číst šifrované hodnoty mimo Settings.xml dešifrování pomocí stejného certifikátu šifrování. 

[Služba Azure Key Vault] [ key-vault-get-started] zde slouží jako umístění úložiště pro bezpečné pro certifikáty a jako způsob, jak získat certifikátů nainstalovaných na clustery Service Fabric v Azure. Pokud nenasazujete do Azure, není potřeba pomocí služby Key Vault pro správu tajných kódů aplikace Service Fabric.

Příklad najdete v tématu [Správa tajných klíčů aplikací](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpečení hostitelské prostředí
Pomocí Azure Service Fabric můžete zabezpečit aplikace, které jsou spuštěny v clusteru pod různými uživatelskými účty. Service Fabric také pomáhá je zabezpečit prostředky používané aplikacemi v okamžiku nasazení podle uživatelských účtů – například soubory, adresáře a certifikáty. Díky spouštění aplikací, i do sdíleného hostovaného prostředí, bezpečnější od sebe.

Manifest aplikace deklaruje, že objekty zabezpečení (uživatelé a skupiny), vyžaduje spuštění služeb a zabezpečeným prostředkům.  Tyto funkce zabezpečení, které objekty je odkazováno v zásadách, například při spustit jako, vazby koncového bodu, balíček pro sdílení obsahu nebo zásad zabezpečení přístupu.  Zásady se potom použijí k prostředkům služby v **ServiceManifestImport** manifestu aplikace.

Při deklarování objekty zabezpečení, můžete také definovat a vytvořit skupiny uživatelů, tak, aby jeden nebo více uživatelů můžete přidat ke každé skupině pro správu společně. To je užitečné, pokud existuje více uživatelů pro jiné služby vstupní body a potřebují mít určitá oprávnění zabezpečení, které jsou k dispozici na úrovni skupiny.

Ve výchozím nastavení aplikace Service Fabric spustit pod účtem, který spouští proces Fabric.exe pod. Service Fabric nabízí také možnost spouštět aplikace v rámci místní uživatelský účet nebo účet místní systém, který je specifikovaný v manifestu aplikace. Další informace najdete v tématu [spuštění služby jako místní uživatelský účet nebo účet místní systém](service-fabric-application-runas-security.md).  Můžete také [spuštění skriptu spuštění služby jako místní uživatel nebo systémový účet](service-fabric-run-script-at-service-startup.md).

Když používáte na Windows samostatného clusteru Service Fabric, můžete spustit služby v rámci [doménových účtů služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo [skupiny spravované účty služeb](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpečené kontejnery
Service Fabric poskytuje mechanismus pro služby uvnitř kontejnerů pro přístup k certifikátu, který je nainstalován na uzlech v clusteru s Windows nebo Linuxem (verze 5.7 nebo novější). Tento certifikát PFX lze použít k ověřování aplikace nebo služby nebo zabezpečenou komunikaci s dalšími službami. Další informace najdete v tématu [Import certifikátu do kontejneru](service-fabric-securing-containers.md).

Navíc Service Fabric také podporuje gMSA (skupinových účtů spravované služby) pro kontejnery Windows. Další informace najdete v tématu [nastavení gMSA pro kontejnery Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Zabezpečená komunikace služeb
V Service Fabric služba někde běží v clusteru Service Fabric, obvykle distribuují na několika virtuálních počítačů. Service Fabric poskytuje několik možností pro zabezpečení komunikace služby.

Můžete povolit koncové body HTTPS ve vaší [ASP.NET Core nebo Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webové služby.

Můžete vytvořit zabezpečené připojení mezi reverzního proxy serveru a služby, což umožní zabezpečený kanál začátku do konce. Připojení k zabezpečení služeb je podporováno pouze v případě, že reverzního proxy je nakonfigurovaná k naslouchání na adrese HTTPS. Informace týkající se konfigurace reverzního proxy serveru, najdete v článku [reverzní proxy server v Azure Service Fabric](service-fabric-reverseproxy.md).  [Připojte se k zabezpečení služby](service-fabric-reverseproxy-configure-secure-communication.md) popisuje, jak vytvořit zabezpečené připojení mezi reverzního proxy serveru a služby.

Rozhraní framework aplikace Reliable Services poskytuje několik nástrojů, které můžete použít ke zlepšení zabezpečení a předem připravených komunikační balíky. Zjistěte, jak zlepšit zabezpečení při použití vzdálené komunikace služby (v [ C# ](service-fabric-reliable-services-secure-communication.md) nebo [Java](service-fabric-reliable-services-secure-communication-java.md)) nebo pomocí [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Šifrování neaktivních uložených dat aplikace
Každý [typ uzlu](service-fabric-cluster-nodetypes.md) v clusteru Service Fabric běžící v Azure je zajištěná [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric.  Pokud vaše služby ukládání dat do přídavný datový disk, můžete si [šifrování těchto dat disků](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) k ochraně dat aplikací.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Spusťte instalační skript při spuštění služby](service-fabric-run-script-at-service-startup.md)
* [Zadání prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Další informace o zabezpečení clusteru](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png