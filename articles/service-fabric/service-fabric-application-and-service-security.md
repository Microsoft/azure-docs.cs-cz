---
title: Přečtěte si o zabezpečení aplikací Service Fabric Azure
description: Přehled, jak bezpečně spouštět aplikace mikroslužeb na Service Fabric. Naučte se spouštět služby a spouštěcí skripty v různých účtech zabezpečení, ověřovat a autorizovat uživatele, spravovat tajné klíče aplikací, zabezpečenou komunikaci služeb, používat bránu API a zabezpečovat neaktivní data aplikací.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f17840f31d2a4c12a1d4618bd16e81dcc2cc8a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256573"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric zabezpečení aplikací a služeb
Architektura mikroslužeb může přinést [mnoho výhod](service-fabric-overview-microservices.md). Správa zabezpečení mikroslužeb je ale výzvou a odlišná od správy tradičních aplikací monolitické. 

U monolitu je aplikace obvykle spuštěná na jednom nebo několika serverech v síti a je snazší identifikovat vystavené porty a rozhraní API a IP adresu. Pro ochranu je často k dispozici jeden hraniční nebo hraniční a jedna databáze. Pokud dojde k ohrožení bezpečnosti tohoto systému z důvodu narušení zabezpečení nebo útoku, je pravděpodobně možné, že bude k dispozici vše v rámci systému. U mikroslužeb je systém složitější.  Služby jsou decentralizované a distribuované napříč mnoha hostiteli a migrují se z hostitele na hostitele.  Se správným zabezpečením omezíte oprávnění, které může útočník získat, a množství dat, která jsou k dispozici v jednom útoku tím, že dojde k porušení jedné služby.  Komunikace není interní, ale probíhá přes síť a existuje mnoho exponovaných portů a interakcí mezi službami. Znalost toho, co tyto interakce služeb jsou a kdy k nim dojde, je rozhodující pro zabezpečení vaší aplikace.

Tento článek není průvodce zabezpečením mikroslužeb, ale k dispozici je mnoho prostředků, které jsou k dispozici online, ale popisuje, jak lze v Service Fabric dosáhnout různých aspektů zabezpečení.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Pro prostředky a rozhraní API vystavené službou je často potřeba omezit na určité důvěryhodné uživatele nebo klienty. Ověřování je proces spolehlivého zjišťování identity uživatele.  Autorizace je proces, který zpřístupňuje rozhraní API nebo služby pro některé ověřené uživatele, ale ne jiné.

### <a name="authentication"></a>Authentication
Prvním krokem pro rozhodování o důvěryhodnosti na úrovni rozhraní API je ověřování. Ověřování je proces spolehlivého zjišťování identity uživatele.  Ve scénářích mikroslužeb se ověřování obvykle zpracovává centrálně. Pokud používáte bránu API, můžete na bránu přesměrovat [ověřování](/azure/architecture/patterns/gateway-offloading) . Pokud použijete tento přístup, ujistěte se, že k jednotlivým službám nemůžete získat přímý přístup (bez brány API), pokud se nejedná o další zabezpečení pro ověřování zpráv bez ohledu na to, jestli pocházejí z brány, nebo ne.

Pokud se k službám dají získat přístup přímo, můžete k ověřování uživatelů použít ověřovací službu, jako je Azure Active Directory nebo vyhrazená mikroslužba ověřování, která funguje jako služba tokenů zabezpečení (STS). Rozhodnutí o důvěryhodnosti se sdílí mezi službami s tokeny zabezpečení nebo soubory cookie. 

Pro ASP.NET Core je primárním mechanismem [ověřování uživatelů](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) ASP.NET Core systém členství v identitách. ASP.NET Core identity ukládá informace o uživateli (včetně přihlašovacích údajů, rolí a deklarací) do úložiště dat nakonfigurovaného vývojářem. ASP.NET Core identity podporuje dvojúrovňové ověřování.  Podporují se taky externí poskytovatelé ověřování, takže se uživatelé můžou přihlašovat pomocí stávajících procesů ověřování od poskytovatelů, jako je Microsoft, Google, Facebook nebo Twitter.

### <a name="authorization"></a>Autorizace
Po ověření potřebují služby autorizaci přístupu uživatele nebo určení toho, co může uživatel dělat. Tento proces umožňuje službě zpřístupnit rozhraní API některým ověřeným uživatelům, ale ne všem. Autorizace je kolmá a nezávislá na ověřování, což je proces, který zjišťuje, kdo je uživatel. Ověřování může pro aktuálního uživatele vytvořit jednu nebo více identit.

[Autorizaci ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) můžete provádět na základě rolí uživatelů nebo na základě vlastních zásad, které můžou zahrnovat kontrolu deklarací identity nebo jiné heuristiky.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Omezení a zabezpečení přístupu pomocí brány rozhraní API
Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. [Brána rozhraní API](/azure/architecture/microservices/gateway) se nachází mezi klienty a službami a je vstupním bodem pro všechny služby, které vaše aplikace poskytuje. Funguje jako reverzní proxy server, směrování požadavků od klientů na služby. Může také provádět různé úlohy křížového vyjmutí, jako je ověřování a autorizace, ukončení protokolu TLS a omezení rychlosti. Pokud bránu nesadíte, klienti musí odesílat požadavky přímo do front-endové služby.

V Service Fabric může být bránou Bezstavová služba, jako je například [aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), nebo jiná služba určená pro příchozí přenos dat, jako je [Traefik](https://docs.traefik.io/), [Event Hubs](../event-hubs/index.yml), [IoT Hub](../iot-hub/index.yml)nebo [Azure API Management](../api-management/index.yml).

API Management se integruje přímo s Service Fabric, což vám umožní publikovat rozhraní API s bohatou sadou pravidel směrování pro vaše back-end Service Fabric služby.  Můžete zabezpečit přístup ke službám back-end, zabránit útokům DOS pomocí omezování nebo ověřit klíče rozhraní API, tokeny JWT, certifikáty a další přihlašovací údaje. Pokud se chcete dozvědět víc, přečtěte si téma [Service Fabric s Azure API Management Overview](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Správa tajných klíčů aplikací
Tajné kódy můžou obsahovat citlivé informace, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu. Tento článek používá Azure Key Vault ke správě klíčů a tajných kódů. *Používání* tajných klíčů v aplikaci je ale cloudová platforma – nezávislá umožňuje nasazení aplikací do clusteru hostovaného kdekoli.

Doporučený způsob, jak spravovat nastavení konfigurace služby, je prostřednictvím [balíčků konfigurace služby][config-package]. Konfigurační balíčky mají verzi a lze je aktualizovat prostřednictvím spravovaných postupných upgradů s ověřením stavu a automatického vrácení zpět. Tato možnost je preferována globální konfiguraci, protože snižuje pravděpodobnost výpadku globálních služeb. Šifrované tajné klíče nejsou výjimkou. Service Fabric obsahuje integrované funkce pro šifrování a dešifrování hodnot v konfiguračním balíčku Settings.xml souboru pomocí šifrování certifikátu.

Následující diagram znázorňuje základní tok pro správu tajných kódů v Service Fabric aplikaci:

![Přehled správy tajných kódů][overview]

Tento tok obsahuje čtyři hlavní kroky:

1. Získejte certifikát pro zakódování dat.
2. Nainstalujte certifikát do clusteru.
3. Šifrování tajných hodnot při nasazování aplikace s certifikátem a jejich vložení do konfiguračního souboru Settings.xml služby.
4. Dešifrování zašifrovaných hodnot z Settings.xml dešifruje se stejným certifikátem pro zakódování. 

[Azure Key Vault][key-vault-get-started] se tady používá jako bezpečné umístění úložiště pro certifikáty a jako způsob, jak získat certifikáty nainstalované v clusterech Service Fabric v Azure. Pokud neprovádíte nasazení do Azure, nemusíte používat Key Vault ke správě tajných kódů v aplikacích Service Fabric.

Příklad najdete v tématu [Správa tajných klíčů aplikací](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpečení hostitelského prostředí
Pomocí Azure Service Fabric můžete zabezpečit aplikace spuštěné v clusteru v rámci různých uživatelských účtů. Service Fabric také pomáhá zabezpečit prostředky používané aplikacemi v době nasazení v rámci uživatelských účtů, například soubory, adresáře a certifikáty. Díky tomu jsou aplikace spuštěné, i ve sdíleném hostovaném prostředí, od druhé.

Manifest aplikace deklaruje objekty zabezpečení (uživatelé a skupiny) vyžadované ke spuštění služeb a zabezpečených prostředků.  Na tyto objekty zabezpečení se odkazuje v zásadách, jako je například vazba koncového bodu, sdílení balíčků nebo zásady přístupu zabezpečení.  Zásady se pak aplikují na prostředky služby v části **ServiceManifestImport** manifestu aplikace.

Při deklarování objektů zabezpečení můžete také definovat a vytvářet skupiny uživatelů, aby bylo možné do každé skupiny přidat jednoho nebo více uživatelů, aby je bylo možné spravovat dohromady. To je užitečné v případě, že existuje více uživatelů pro různé vstupní body služby a potřebují určitá společná oprávnění, která jsou k dispozici na úrovni skupiny.

Ve výchozím nastavení Service Fabric aplikace běží pod účtem, pod kterým běží proces Fabric.exe. Service Fabric taky nabízí možnost spouštět aplikace pod místním uživatelským účtem nebo účtem místního systému, který je zadaný v manifestu aplikace. Další informace najdete v tématu [spuštění služby jako místní uživatelský účet nebo účet místního systému](service-fabric-application-runas-security.md).  [Spouštěcí skript služby můžete také spustit jako místní uživatel nebo systémový účet](service-fabric-run-script-at-service-startup.md).

Když spouštíte Service Fabric na samostatném clusteru se systémem Windows, můžete spustit službu pod [účtem domény služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo [skupinovými účty spravované služby](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpečené kontejnery
Service Fabric poskytuje mechanismus pro služby uvnitř kontejneru pro přístup k certifikátu, který je nainstalovaný na uzlech v clusteru s Windows nebo Linux (verze 5,7 nebo vyšší). Tento certifikát PFX se dá použít k ověřování aplikace nebo služby nebo k zabezpečení komunikace s ostatními službami. Další informace najdete v tématu [Import certifikátu do kontejneru](service-fabric-securing-containers.md).

Kromě toho Service Fabric podporuje také gMSA (seskupení účtů spravované služby) pro kontejnery Windows. Další informace najdete v tématu [Nastavení gMSA pro kontejnery Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Zabezpečená komunikace služby
V Service Fabric se služba spouští někde v clusteru Service Fabric, který se obvykle distribuuje napříč několika virtuálními počítači. Service Fabric poskytuje několik možností pro zabezpečení komunikace služby.

Koncovým bodům HTTPS můžete povolit v [ASP.NET Core nebo Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) Web Services.

Můžete vytvořit zabezpečené připojení mezi reverzním proxy serverem a službami, čímž povolíte koncovému zabezpečenému kanálu. Připojení k zabezpečeným službám se podporuje jenom v případě, že je reverzní proxy nakonfigurované pro naslouchání na HTTPS. Informace o konfiguraci reverzního proxy serveru najdete [v tématu reverzní proxy server v Azure Service Fabric](service-fabric-reverseproxy.md).  [Připojení k zabezpečené službě](service-fabric-reverseproxy-configure-secure-communication.md) popisuje, jak vytvořit zabezpečené připojení mezi reverzním proxy serverem a službami.

Rozhraní Reliable Services Application Framework poskytuje několik předem připravených komunikačních zásobníků a nástrojů, které můžete použít ke zvýšení zabezpečení. Zjistěte, jak zvýšit zabezpečení při použití vzdálené komunikace služby (v [jazyce C#](service-fabric-reliable-services-secure-communication.md) nebo [Java](service-fabric-reliable-services-secure-communication-java.md)) nebo pomocí [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Šifrovat data aplikace v klidovém umístění
Každý [typ uzlu](service-fabric-cluster-nodetypes.md) v clusteru Service Fabric spuštěném v Azure je zálohovaný pomocí [sady škálování virtuálních počítačů](../virtual-machine-scale-sets/overview.md). Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric.  Pokud vaše služby ukládají data na připojený datový disk, můžete [tyto datové disky zašifrovat](../virtual-machine-scale-sets/disk-encryption-powershell.md) a chránit tak data aplikací.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* [Spuštění instalačního skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Další informace o zabezpečení clusteru](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
