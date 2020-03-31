---
title: Služba zřizování zařízení služby IoT Hub – koncepty automatického zřizování
description: Tento článek obsahuje koncepční přehled fází automatického zřizování zařízení pomocí služby DPS (IoT Device Provisioning Service), služby IoT Hub a sad SDK klienta.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975325"
---
# <a name="auto-provisioning-concepts"></a>Koncepty automatického zřizování

Jak je popsáno v [přehledu](about-iot-dps.md), služba zřizování zařízení je pomocná služba, která umožňuje zřizování zařízení za chvíli do služby IoT hub, aniž by bylo nutné lidský zásah. Po úspěšném zřizování se zařízení přímo připojují ke svému určenému centru IoT Hub. Tento proces se označuje jako automatické zřizování a poskytuje out-of-the-box registrace a počáteční konfigurace pro zařízení.

## <a name="overview"></a>Přehled

Automatické zřizování Azure IoT lze rozdělit do tří fází:

1. **Konfigurace služby** – jednorázová konfigurace azure iot hub a služby IoT Hub Device Provisioning Service, jejich vytvoření a vytváření propojení mezi nimi.

   > [!NOTE]
   > Bez ohledu na velikost vašeho řešení IoT, i když máte v plánu podporovat miliony zařízení, jedná se o **jednorázovou konfiguraci**.

2. **Registrace zařízení** – proces zpřístupňování instance služby Zřizování zařízení o zařízeních, která se pokusí zaregistrovat v budoucnu. [Registrace](concepts-service.md#enrollment) se provádí konfigurací informací o identitě zařízení ve službě zřizování, jako "individuální registrace" pro jedno zařízení nebo "registrace skupiny" pro více zařízení. Identita je založena na [mechanismu ověřování,](concepts-security.md#attestation-mechanism) který je zařízení navrženo k použití, což umožňuje službě zřizování dosvědčit pravost zařízení při registraci:

   - **Čip TPM**: nakonfigurován jako "individuální registrace", identita zařízení je založena na ID registrace čipu TPM a veřejného ověřovacího klíče. Vzhledem k tomu, že čip TPM je [specifikace](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), služba očekává pouze dokončování podle specifikace, bez ohledu na implementaci čipu TPM (hardware nebo software). Podrobnosti o atestaci založeném na čipu TPM najdete v tématu [Zřizování zařízení: Ověření identity čipem TPM.](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) 

   - **X509**: nakonfigurován jako "individuální zápis" nebo "registrace skupiny", identita zařízení je založena na digitálním certifikátu X.509, který je odeslán do zápisu jako soubor .pem nebo .cer.

   > [!IMPORTANT]  
   > I když není podmínkou pro použití služby device provisioning Services, důrazně doporučujeme, aby vaše zařízení používat hardware Security Module (HSM) pro ukládání citlivých informací o identitě zařízení, jako jsou klíče a certifikáty X.509.

3. **Registrace a konfigurace zařízení** - iniciovaná při spuštění registračním softwarem, který je vytvořen pomocí sady SDK klienta Device Provisioning Service vhodné pro zařízení a mechanismus ověřování. Software naváže připojení ke službě zřizování pro ověřování zařízení a následnou registraci v centru IoT Hub. Po úspěšné registraci je zařízení vybaveno jedinečným ID zařízení služby IoT Hub a informacemi o připojení, což mu umožňuje vyžádat počáteční konfiguraci a zahájit proces telemetrie. V produkčním prostředí může k této fázi dojít týdny nebo měsíce po předchozích dvou fázích.

## <a name="roles-and-operations"></a>Role a operace

Fáze diskutované v předchozí části mohou tkat týdny nebo měsíce, vzhledem k výrobním skutečnostem, jako je výrobní čas, doprava, celní proces atd. Kromě toho mohou span aktivity přes více rolí vzhledem k různým zapojeným entit. Tato část se podrobněji podíváme na různé role a operace související s každou fází a pak ilustruje tok v sekvenčním diagramu. 

Automatické zřizování také klade požadavky na výrobce zařízení, specifické pro povolení mechanismu ověřování. Výrobní operace mohou také nastat nezávisle na načasování fází automatického zřizování, zejména v případech, kdy jsou nová zařízení pořízena po již navázání automatického zřizování.

Řada rychlých startů jsou k dispozici v obsahu vlevo, které vám pomohou vysvětlit automatické zřizování prostřednictvím praktických zkušeností. Aby se usnadnil/zjednodušil proces učení, používá se software k simulaci fyzického zařízení pro registraci a registraci. Některé rychlé starty vyžadují, abyste splnili operace pro více rolí, včetně operací pro neexistující role, vzhledem k simulované povaze rychlých startů.

| Role | Operace | Popis |
|------| --------- | ------------|
| Výrobce | Zakódovat identitu a adresu URL registrace | Na základě použitého mechanismu ověřování je výrobce zodpovědný za kódování informací o identitě zařízení a registrační adresy URL služby Device Provisioning Service.<br><br>**Rychlé starty**: vzhledem k tomu, že je zařízení simulováno, neexistuje žádná role výrobce. Podrobnosti o tom, jak tyto informace získat, který se používá při kódování ukázkové registrační aplikace, naleznete v roli vývojáře. |
| | Zadejte identitu zařízení | Jako původce informací o identitě zařízení je výrobce odpovědný za jejich sdělování operátorovi (nebo určenému zástupci) nebo za jeho přímou registraci do služby device provisioning service prostřednictvím rozhraní API.<br><br>**Rychlé starty**: vzhledem k tomu, že je zařízení simulováno, neexistuje žádná role výrobce. Podrobnosti o tom, jak získat identitu zařízení, která se používá k registraci simulovaného zařízení v instanci služby Device Provisioning Service, najdete v roli Operátor. |
| Operátor | Konfigurace automatického zřizování | Tato operace odpovídá první fázi automatického zřizování.<br><br>**Rychlé starty**: Provedete roli Operátor, konfigurace služby zřizování zařízení a ioT hub instance ve vašem předplatném Azure. |
|  | Registrace identity zařízení | Tato operace odpovídá druhé fázi automatického zřizování.<br><br>**Rychlé starty**: Provedete roli Operátor a zaregistrujete simulované zařízení do instance služby Zřizování zařízení. Identita zařízení je určena simulační metodou, která je simulována v rychlém startu (TPM nebo X.509). Podrobnosti o potvrzení naleznete v roli vývojáře. |
| Služba zřizování zařízení,<br>IoT Hub | \<všechny operace\> | Pro produkční implementaci s fyzickými zařízeními a rychlé starty se simulovanými zařízeními jsou tyto role splněny prostřednictvím služeb IoT, které nakonfigurujete ve vašem předplatném Azure. Role/operace fungují přesně stejně, protože služby IoT jsou lhostejné k zřizování fyzických vs. simulovaných zařízení. |
| Developer | Sestavení/nasazení registračního softwaru | Tato operace odpovídá třetí fázi automatického zřizování. Vývojář je zodpovědný za vytváření a nasazování registračního softwaru do zařízení pomocí příslušné sady SDK.<br><br>**Rychlé starty**: Ukázková registrační aplikace, kterou vytvoříte, simuluje skutečné zařízení pro vaši platformu/jazyk, který běží na vaší pracovní stanici (namísto jeho nasazení do fyzického zařízení). Registrační aplikace provádí stejné operace jako jeden nasazený do fyzického zařízení. Zadáte metodu ověřování (certifikát TPM nebo X.509) plus adresu URL registrace a "Obor ID" instance služby Zřizování zařízení. Identita zařízení je určena logikou ověření sady SDK za běhu na základě zadané metody: <ul><li>**Atestace TPM** - vaše vývojová pracovní stanice spouští [aplikaci simulátoru TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po spuštění se k extrahování "ověřovacího klíče" čipu TPM a "Registračního ID" pro použití při registraci identity zařízení používá samostatná aplikace. Logika ověření sady SDK také používá simulátor během registrace k prezentaci podepsaného tokenu SAS pro ověřování a ověřování registrace.</li><li>**X509 atestace** - používáte nástroj pro [generování certifikátu](how-to-use-sdk-tools.md#x509-certificate-generator). Po vygenerování vytvoříte soubor certifikátu potřebný pro použití při zápisu. Logika ověření sady SDK také používá certifikát při registraci k prezentaci pro ověření a ověření zápisu.</li></ul> |
| Zařízení | Bootup a registr | Tato operace odpovídá třetí fázi automatického zřizování, kterou plní software pro registraci zařízení vytvořený vývojářem. Podrobnosti najdete v roli vývojáře. Při prvním spuštění: <ol><li>Aplikace se připojí k instanci Služby zřizování zařízení podle globální adresy URL a služby "ID Obor" zadané během vývoje.</li><li>Po připojení je zařízení ověřeno podle metody ověření a identity zadané během registrace.</li><li>Po ověření se zařízení zaregistruje s instancí služby IoT Hub určené instancí zřizovací služby.</li><li>Po úspěšné registraci se jedinečné ID zařízení a koncový bod služby IoT Hub vrátí do registrační aplikace pro komunikaci s IoT Hub.</li><li> Odtud zařízení můžete stáhnout jeho počáteční stav [dvojčete zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) pro konfiguraci a zahájit proces vykazování telemetrických dat.</li></ol>**Rychlé starty**: vzhledem k tomu, že je zařízení simulováno, registrační software běží na vývojové pracovní stanici.|

Následující diagram shrnuje role a pořadí operací během automatického zřizování zařízení:
<br><br>
[![Pořadí automatického zřizování zařízení](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Volitelně může výrobce také provádět operaci "Zapsat identitu zařízení" pomocí rozhraní API služby zřizování zařízení (namísto prostřednictvím operátora). Podrobné informace o tomto řazení a další informace najdete v tématu [Nula dotykové zařízení registrace s Azure IoT video](https://youtu.be/cSbDRNg72cU?t=2460) (od značky 41:00)

## <a name="roles-and-azure-accounts"></a>Role a účty Azure

Jak je každá role mapována na účet Azure je závislá na scénáři a existuje poměrně málo scénářů, které mohou být zapojeny. Běžné vzory níže by měly pomoci poskytnout obecné znalosti o tom, jak jsou role obecně mapovány na účet Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Výrobce čipů poskytuje bezpečnostní služby

V tomto scénáři výrobce spravuje zabezpečení pro zákazníky úrovně jedna. Tento scénář může být upřednostňován těmito zákazníky první úrovně, protože nemusí spravovat podrobné zabezpečení. 

Výrobce zavádí zabezpečení do modulů hardwarového zabezpečení (HSM). Toto zabezpečení může zahrnovat výrobce získání klíčů, certifikátů atd. Výrobce může také generovat tyto informace o zabezpečení pro své zákazníky.

V tomto scénáři může existovat dva účty Azure:

- **#1 účtu:** Pravděpodobně do určité míry sdíleny napříč rolemi operátora a vývojáře. Tato strana může zakoupit čipy HSM od výrobce. Tyto čipy jsou odkazovány na instance DPS spojené s #1 účtu. S registrací DPS může tato strana pronajímat zařízení více zákazníkům úrovně dvě změnou konfigurace nastavení registrace zařízení v DPS. Tato strana může mít také služby IoT huby přidělené pro back-endové systémy koncových uživatelů pro rozhraní za účelem přístupu k telemetrii zařízení atd. V tomto druhém případě nemusí být druhý účet nutný.

- **#2 účtu**: Koncoví uživatelé, zákazníci druhé úrovně mohou mít vlastní centra IoT. Strana přidružená k účtu #1 pouze nakvačuje pronajatá zařízení do správného centra v tomto účtu. Tato konfigurace vyžaduje propojení dps a ioT hubů napříč účty Azure, což se dá dělat pomocí šablon Azure Resource Manager.

#### <a name="all-in-one-oem"></a>Vše v jednom OEM

Výrobcem by mohl být "All-in-one OEM", kde by byl zapotřebí pouze jeden účet výrobce. Výrobce zpracovává zabezpečení a zřizování od konce do konce.

Výrobce může zákazníkům, kteří si zařízení zakoupí, poskytnout cloudovou aplikaci. Tato aplikace by rozhraní s ioT hub přidělené výrobcem.

Příkladem tohoto scénáře jsou prodejní automaty nebo automaty na kávovary.




## <a name="next-steps"></a>Další kroky

Může být užitečné záložku tento článek jako referenční bod, jak si práci si cestu přes odpovídající automatické zřizování Rychlé starty. 

Začněte dokončením úvodního úvodního panelu "Nastavení automatického zřizování", který nejlépe vyhovuje vašim preferencím nástroje pro správu, který vás provede fází "Konfigurace služby":

- [Nastavení automatického zřizování pomocí azure cli](quick-setup-auto-provision-cli.md)
- [Nastavení automatického zřizování pomocí portálu Azure](quick-setup-auto-provision.md)
- [Nastavení automatického zřizování pomocí šablony Správce prostředků](quick-setup-auto-provision-rm.md)

Pak pokračujte s rychlým startem "Automatické zřízení simulované zařízení", který vyhovuje mechanismu ověřování zařízení a předvolbě služby Device Provisioning Service SDK/jazyk. V tomto rychlém startu procházíte fázemi "Registrace zařízení" a "Registrace a konfigurace zařízení": 

|  | Mechanismus atestace simulovaných zařízení | Sada SDK/jazyk rychlého startu |  |
|--|--|--|--|
|  | Čip TPM (Trusted Platform Module) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C #](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certifikát X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C #](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




