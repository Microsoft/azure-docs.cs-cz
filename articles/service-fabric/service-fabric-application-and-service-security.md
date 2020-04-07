---
title: Informace o zabezpečení aplikací Azure Service Fabric
description: Přehled, jak bezpečně spouštět aplikace mikroslužeb na Service Fabric. Zjistěte, jak spouštět služby a spouštěcí skripty pod různými účty zabezpečení, ověřovat a autorizovat uživatele, spravovat tajné kódy aplikací, zabezpečit komunikaci se službami, používat bránu rozhraní API a zabezpečit data aplikací v klidovém stavu.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: e9b4a1209838bdd5eee401b0defb01839b5cf684
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756237"
---
# <a name="service-fabric-application-and-service-security"></a>Zabezpečení aplikací a služeb Service Fabric
Architektura mikroslužeb může přinést [mnoho výhod](service-fabric-overview-microservices.md). Správa zabezpečení mikroslužeb je však výzvou a liší se od správy zabezpečení tradičních monolitických aplikací. 

S monolitem je aplikace obvykle spuštěna na jednom nebo více serverech v rámci sítě a je snadnější identifikovat vystavené porty a porty API a IP adresu. Často existuje jeden obvod nebo hranice a jedna databáze k ochraně. Pokud je tento systém ohrožen z důvodu narušení zabezpečení nebo útoku, je pravděpodobné, že útočník bude mít k dispozici vše v systému. S mikroslužeb, systém je složitější.  Služby jsou decentralizované a distribuované mezi mnoha hostiteli a migrují z hostitele na hostitele.  Při správném zabezpečení omezíte oprávnění, která může útočník získat, a množství dat dostupných v jednom útoku porušením jedné služby.  Komunikace není interní, ale probíhá v síti a existuje mnoho exponovaných portů a interakcí mezi službami. Vědět, co tyto interakce služby jsou a kdy k nim dojde, je zásadní pro zabezpečení aplikace.

Tento článek není průvodce zabezpečení mikroslužeb, existuje mnoho takových prostředků k dispozici online, ale popisuje, jak lze provést různé aspekty zabezpečení v Service Fabric.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Často je nutné, aby prostředky a řešení API vystavené službou byly omezeny na určité důvěryhodné uživatele nebo klienty. Ověřování je proces spolehlivého zjišťování identity uživatele.  Autorizace je proces, který zpřístupňuje rozhraní API nebo služby některým ověřeným uživatelům, nikoli však jiným uživatelům.

### <a name="authentication"></a>Authentication
Prvním krokem k rozhodování o důvěryhodnosti na úrovni rozhraní API je ověřování. Ověřování je proces spolehlivého zjišťování identity uživatele.  Ve scénářích mikroslužeb ověřování se obvykle zpracovává centrálně. Pokud používáte bránu rozhraní API, můžete [převést ověřování](/azure/architecture/patterns/gateway-offloading) na bránu. Pokud použijete tento přístup, ujistěte se, že jednotlivé služby nelze dosáhnout přímo (bez brány rozhraní API), pokud je zavedeno další zabezpečení k ověření zpráv, zda pocházejí z brány nebo ne.

Pokud ke službám lze přistupovat přímo, lze k ověření uživatelů použít ověřovací službu, jako je Azure Active Directory nebo vyhrazená mikroslužba pro ověřování, která funguje jako služba tokenů zabezpečení (STS). Rozhodnutí o důvěryhodnosti jsou sdílena mezi službami pomocí tokenů zabezpečení nebo souborů cookie. 

Pro ASP.NET Core je primárním mechanismem pro [ověřování uživatelů](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) systém členství ASP.NET základní identity. ASP.NET Core Identity ukládá informace o uživateli (včetně přihlašovacích informací, rolí a deklarací) v úložišti dat nakonfigurovaném vývojářem. ASP.NET Core Identity podporuje dvoufaktorové ověřování.  Externí poskytovatelé ověřování jsou také podporováni, takže se uživatelé mohou přihlásit pomocí stávajících procesů ověřování od poskytovatelů, jako je Microsoft, Google, Facebook nebo Twitter.

### <a name="authorization"></a>Autorizace
Po ověření musí služby autorizovat přístup uživatele nebo určit, co je uživatel schopen udělat. Tento proces umožňuje službě zpřístupnit rozhraní API některým ověřeným uživatelům, ale ne všem. Autorizace je ortogonová a nezávislá na ověřování, což je proces zjišťování, kdo je uživatel. Ověřování může vytvořit jednu nebo více identit pro aktuálního uživatele.

[ASP.NET základní autorizace](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) lze provést na základě rolí uživatelů nebo na základě vlastních zásad, které mohou zahrnovat kontrolu deklarací nebo jiné heuristiky.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Omezení a zabezpečení přístupu pomocí brány rozhraní API
Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. [Brána rozhraní API](/azure/architecture/microservices/gateway) je vzdálená mezi klienty a službami a je vstupním bodem pro všechny služby, které vaše aplikace poskytuje. Funguje jako reverzní proxy server, směrování požadavků od klientů ke službám. Může také provádět různé průřezové úlohy, jako je ověřování a autorizace, ukončení TLS a omezení rychlosti. Pokud bránu nenasazujete, klienti musí odesílat požadavky přímo do front-endových služeb.

V Service Fabric, brána může být bezstavové služby, jako je [například aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)nebo jiné služby určené pro příchozí přenos dat, jako je [traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)nebo Azure API [Management](https://docs.microsoft.com/azure/api-management).

Správa rozhraní API se integruje přímo s Service Fabric, což vám umožní publikovat rozhraní API s bohatou sadou směrovacích pravidel do vašich back-end service fabric služeb.  Můžete zabezpečit přístup ke službám back-endu, zabránit útokům DOS pomocí omezení nebo ověřit klíče rozhraní API, tokeny JWT, certifikáty a další pověření. Další informace najdete v článku [Service Fabric s přehledem správy rozhraní AZURE API](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Správa tajných klíčů aplikací
Tajné klíče mohou být jakékoli citlivé informace, jako jsou řetězce připojení úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány ve formátu prostého textu. Tento článek používá Azure Key Vault ke správě klíčů a tajných kódů. Použití *tajných* kódů v aplikaci je však cloudová platforma nezávislá, aby aplikace mohly být nasazeny do clusteru hostovaného kdekoli.

Doporučený způsob správy nastavení konfigurace služby je prostřednictvím [balíčků konfigurace služby][config-package]. Konfigurační balíčky jsou verzí a aktualizovatelné prostřednictvím spravovaných postupných inovací s ověřením stavu a automatickým vrácením zpět. To je upřednostňováno před globální konfigurací, protože snižuje pravděpodobnost výpadku globální služby. Šifrované tajné klíče nejsou výjimkou. Service Fabric má vestavěné funkce pro šifrování a dešifrování hodnot v konfiguračním balíčku Soubor USoubor Settings.xml pomocí šifrování certifikátů.

Následující diagram znázorňuje základní tok pro správu tajných kódů v aplikaci Service Fabric:

![přehled správy tajných správě][overview]

V tomto toku jsou čtyři hlavní kroky:

1. Získejte certifikát zašepletování dat.
2. Nainstalujte certifikát do clusteru.
3. Při nasazování aplikace pomocí certifikátu šifrujte tajné hodnoty a vložte je do konfiguračního souboru Settings.xml služby.
4. Čtení šifrovaných hodnot z souboru Settings.xml dešifrováním se stejným certifikátem šifrování. 

[Azure Key Vault][key-vault-get-started] se zde používá jako bezpečné úložiště pro certifikáty a jako způsob, jak získat certifikáty nainstalované v clusterech Service Fabric v Azure. Pokud se nenasazujete do Azure, není nutné používat trezor klíčů ke správě tajných kódů v aplikacích Service Fabric.

Příklad naleznete v tématu [Správa tajných kódů aplikací](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpečte hostitelské prostředí
Pomocí Azure Service Fabric, můžete zabezpečit aplikace, které jsou spuštěny v clusteru pod různými uživatelskými účty. Service Fabric také pomáhá zabezpečit prostředky, které jsou používány aplikacemi v době nasazení v rámci uživatelských účtů – například soubory, adresáře a certifikáty. Díky tomu jsou spuštěné aplikace, a to i ve sdíleném hostovaném prostředí, bezpečnější od sebe navzájem.

Manifest aplikace deklaruje objekty zabezpečení (uživatelé a skupiny) požadované spuštění služby a zabezpečené prostředky.  Tyto objekty zabezpečení jsou odkazovány v zásadách, například run-as, vazby koncového bodu, sdílení balíčků nebo zásady přístupu k zabezpečení.  Zásady jsou pak použity pro prostředky služby v **ServiceManifestImport** části manifestu aplikace.

Při deklarování objektů zabezpečení můžete také definovat a vytvářet skupiny uživatelů tak, aby jeden nebo více uživatelů bylo možné přidat do každé skupiny, která má být spravována společně. To je užitečné, pokud existuje více uživatelů pro různé vstupní body služby a potřebují mít určitá společná oprávnění, která jsou k dispozici na úrovni skupiny.

Ve výchozím nastavení service fabric aplikace spustit pod účtem, který Fabric.exe proces běží pod. Service Fabric také poskytuje možnost spouštět aplikace pod místním uživatelským účtem nebo místním systémovým účtem, který je určen v manifestu aplikace. Další informace naleznete [v tématu Spuštění služby jako místního uživatelského účtu nebo místního systémového účtu](service-fabric-application-runas-security.md).  Spouštěcí skript služby můžete spustit také [jako místní uživatelský nebo systémový účet](service-fabric-run-script-at-service-startup.md).

Pokud používáte service fabric v samostatném clusteru se systémem Windows, můžete spustit službu v rámci [účtů domény služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo účtů [spravovaných služeb služby služby služby Active](service-fabric-run-service-as-gmsa.md)Directory .

## <a name="secure-containers"></a>Zabezpečené kontejnery
Service Fabric poskytuje mechanismus pro služby uvnitř kontejneru pro přístup k certifikátu, který je nainstalován na uzlech v clusteru Windows nebo Linux (verze 5.7 nebo vyšší). Tento certifikát PFX lze použít k ověření aplikace nebo služby nebo k zabezpečené komunikaci s jinými službami. Další informace naleznete [v tématu Import certifikátu do kontejneru](service-fabric-securing-containers.md).

Kromě toho Service Fabric také podporuje gMSA (skupiny účtů spravované služby) pro kontejnery systému Windows. Další informace naleznete v [tématu Nastavení gMSA pro kontejnery systému Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Zabezpečená komunikace se službami
V Service Fabric služba běží někde v clusteru Service Fabric, obvykle distribuovány mezi více virtuálních počítačích. Service Fabric poskytuje několik možností pro zabezpečení komunikace služby.

Koncové body HTTPS můžete povolit ve svých ASP.NET webových služeb [Core nebo Java.](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)

Můžete navázat zabezpečené připojení mezi reverzní proxy server a služby, což umožňuje ukončení zabezpečeného kanálu. Připojení k zabezpečeným službám je podporováno pouze v případě, že je reverzní proxy server nakonfigurován tak, aby naslouchal protokolu HTTPS. Informace o konfiguraci reverzního proxy serveru načtete [reverzní proxy server v azure service fabric](service-fabric-reverseproxy.md).  [Připojení k zabezpečené službě](service-fabric-reverseproxy-configure-secure-communication.md) popisuje, jak vytvořit zabezpečené připojení mezi reverzním proxy serverem a službami.

Architektura aplikací služby spolehlivé služby poskytuje několik předem vytvořených komunikačních zásobníků a nástrojů, které můžete použít ke zlepšení zabezpečení. Přečtěte si, jak zlepšit zabezpečení, když používáte vzdálené komunikace (v [jazyce C#](service-fabric-reliable-services-secure-communication.md) nebo [Java)](service-fabric-reliable-services-secure-communication-java.md)nebo pomocí [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Šifrování dat aplikací v klidovém stavu
Každý [typ uzlu](service-fabric-cluster-nodetypes.md) v clusteru Service Fabric spuštěném v Azure je zálohován [škálovací sadou virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric.  Pokud vaše služby ukládají data na připojený datový disk, můžete [tyto datové disky zašifrovat](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) a chránit tak data aplikace.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* [Spuštění instalačního skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)
* [Informace o zabezpečení clusteru](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png