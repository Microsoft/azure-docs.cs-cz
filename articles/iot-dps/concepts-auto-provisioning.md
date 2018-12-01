---
title: Služba zřizování zařízení IoT Hub - konceptům automatického zřizování
description: Tento článek obsahuje přehled fází zařízení automatického zřizování, pomocí IoT Device Provisioning Service, služby IoT Hub a klientské sady SDK.
author: wesmc7777
ms.author: wesmc
ms.date: 06/01/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 10648551728e4f3cb41b82433e4cd0d442f9daeb
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679252"
---
# <a name="auto-provisioning-concepts"></a>Koncepty automatického zřizování

Jak je popsáno v [přehled](about-iot-dps.md), služby Device Provisioning je pomocná služba, která umožňuje just-in-time zřizování zařízení do služby IoT hub, které nevyžaduje zásah člověka. Po úspěšném zřízení zařízení připojit přímo pomocí jejich určené služby IoT Hub. Tento proces se označuje jako automatického zřizování a poskytuje že Out-of-the-box registrace a počáteční konfiguraci prostředí pro zařízení.

## <a name="overview"></a>Přehled

Azure IoT automatického zřizování můžete rozdělené do tří fází:

1. **Konfigurace služby** -jednorázovou konfiguraci instancí služby Azure IoT Hub a IoT Hub Device Provisioning Service, jejich zřízení a vytvoření propojení mezi nimi.

   > [!NOTE]
   > Bez ohledu na velikost vašeho řešení IoT, i pokud plánujete podporu milionů zařízení, jedná se **jednorázovou konfiguraci**.

2. **Registrace zařízení** – proces zpřístupnění instanci služby Device Provisioning upozornit zařízení, která se pokusí zaregistrovat v budoucnu. [Registrace](concepts-service.md#enrollment) provádí konfigurace informací o identit zařízení ve zřizovací službě, jako "jednotlivou registraci" pro jedno zařízení, nebo "skupinovou registraci" pro více zařízení. Identita je založena na [mechanismus ověřování](concepts-security.md#attestation-mechanism) zařízení je navržen pro použití, která umožňuje službě zřizování ověřit pravost zařízení během registrace:

   - **TPM**: nakonfigurovaný jako "jednotlivou registraci", identitu tohoto zařízení je založeno na ID registrace TPM a veřejné ověřovací klíč. Vzhledem k tomu, že je čip TPM [specifikace](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), služba očekává jenom ověřit podle specifikace, bez ohledu na implementaci čipu TPM (hardwarové nebo softwarové). Zobrazit [Device provisioning: ověření Identity s TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) podrobnosti o ověřováním identity založeným na TPM. 

   - **X509**: nakonfigurované jako "jednotlivé registrace" nebo "skupinovou registraci" identitu tohoto zařízení je založen na digitální certifikát X.509, který se nahraje do registrace jako soubor .pem nebo .cer.

   > [!IMPORTANT]  
   > I když se nejedná o nezbytný předpoklad pro používání služby Device Provisioning, důrazně doporučujeme, aby využití zařízení hardwaru modul zabezpečení (HSM) k uložení informace o identitě citlivé zařízení, jako jsou klíče a certifikáty X.509.

3. **Registrace zařízení a konfiguraci** – při spuštění inicializováno software pro registraci, který se vytvořil pomocí klienta služby Device Provisioning Service SDK, které jsou vhodné pro zařízení a ověření mechanismu. Software naváže připojení ke službě zřizování pro ověřování zařízení a následné registrace ve službě IoT Hub. Po úspěšné registraci zařízení je součástí jeho služby IoT Hub jedinečné ID a připojení informace o zařízení, díky kterému jej o přijetí změn jeho počáteční konfigurace a zahajte proces telemetrie. V produkčním prostředí může dojít této fáze týdnů či měsíců po předchozí dvě fáze.

## <a name="roles-and-operations"></a>Role a operace

Fáze popsané v předchozí části se týkají týdny nebo měsíce, z důvodu produkční skutečnosti jako dobu výroby, přesouvání, celních procesu atd. Kromě toho se může mít rozsah aktivity napříč více rolí, které jsou uvedeny různé entitami. Tato část podrobněji podíváme na různými rolemi a operace související s každou fázi pak znázorňuje postup sekvenčního diagramu. 

Automatického zřizování také uvádí požadavky na výrobce zařízení, specifická pro povolení mechanismu ověřování. Výrobní operací může vzniknout také nezávisle na časování fáze automatického zřizování, zejména v případech, kde jsou pořízeny nová zařízení po automatickém zřizování už bylo navázáno.

Řadu šablon rychlý Start jsou k dispozici v obsahu na levé straně, které pomáhá vysvětlit automatického zřizování prostřednictvím praktických zkušeností. Pro usnadnění/zjednodušení studijní postup, se používá software pro simulaci fyzické zařízení pro registraci. Některé šablony rychlý start vyžaduje splnění operace víc rolí, včetně operací pro neexistující role vzhledem k povaze simulované postupů rychlý start.

| Role | Operace | Popis | 
|------| --------- | ------------| 
| Výrobce | Kódování adresy URL pro identity a registrace | Založené na mechanismu ověřování používá, výrobce zodpovídá za kódování informace o identitu zařízení a adresa URL pro registraci služby Device Provisioning.<br><br>**Rychlé starty**: vzhledem k tomu, že se simulované zařízení, neexistuje žádná role výrobce. Naleznete v tématu role pro vývojáře pro podrobnosti o tom, jak získat tyto informace, které se používá v oblasti kódování ukázkové registrační aplikace. |  
| | Zadejte identitu zařízení | Jako odesílatel požadavku dostane informaci informace o identity zařízení výrobce zodpovídá za komunikaci s operátoru (nebo agenta do určeným), nebo přímo jeho registrace do služby Device Provisioning Service přes rozhraní API.<br><br>**Rychlé starty**: vzhledem k tomu, že se simulované zařízení, neexistuje žádná role výrobce. Naleznete v tématu role operátora podrobnosti o tom, jak získat identity zařízení, která se používá k registraci zařízení s Simulovaná v instanci služby Device Provisioning. | 
| Operátor | Konfigurace automatického zřizování | Tato operace odpovídá s první fází automatického zřizování.<br><br>**Rychlé starty**: provádění role operátora konfigurace služby Device Provisioning a IoT Hub instancí ve vašem předplatném Azure. | Nastavení automatického zřizování |
|  | Registrace identity zařízení | Tato operace odpovídá druhé fázi automatického zřizování.<br><br>**Rychlé starty**: provádění role operátora, registraci zařízení s Simulovaná v instanci služby Device Provisioning. Identita zařízení je určena metoda ověření identity se simulované v tomto rychlém startu (TPM nebo X.509). Podívejte se role pro vývojáře o ověření identity. | 
| Služba Device Provisioning Service,<br>IoT Hub | \<všechny operace\> | Pro obě provozní implementace s fyzickými zařízeními a šablon rychlý start s Simulovaná zařízení jsou splněny tyto role prostřednictvím služby IoT, které jste nakonfigurovali ve vašem předplatném Azure. Role/operace fungovat stejně, jako je mi to pro zřizování fyzických nebo simulovaných zařízení služby IoT. | 
| Developer | Sestavení/nasadit software pro registraci | Tato operace odpovídá třetí fázi automatického zřizování. Vývojář je zodpovědný za vytváření a nasazování do zařízení, software pro registraci pomocí příslušné sady SDK.<br><br>**Rychlé starty**: ukázkové registrační aplikace vytvoříte simuluje skutečné zařízení pro platformu a jazyk podle vlastní volby, který se spouští na pracovní stanici (namísto jeho nasazení na fyzické zařízení). Registrace aplikace provede stejné operace jako jeden nasazen na fyzickém zařízení. Zadejte metodu ověřování (TPM nebo X.509 certificate), a také adresa URL pro registraci a "Rozsah ID" instance služby Device Provisioning. Identita zařízení je určena logiku ověření SDK za běhu, založené na metodě, kterou zadáte: <ul><li>**Ověření identity čipem TPM** – spustí stanici vývoje [aplikaci simulátoru TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po spuštění samostatné aplikace slouží k extrahování čipu TPM "Ověřovací klíč" a "ID registrace" pro použití v registraci identitu zařízení. Logiku ověření SDK také používá simulátor během registrace, zobrazíte podepsaný token SAS pro ověřování a ověřování pro zápis.</li><li>**X509 ověření** – použijte nástroj, který [vygenerovat certifikát](how-to-use-sdk-tools.md#x509-certificate-generator). Po vygenerování vytvoříte soubor certifikátu, vyžaduje se pro použití při zápisu. Logiku ověření SDK také používá certifikát během registrace, chcete-li k dispozici pro ověřování a ověření registrace.</li></ul> | 
| Zařízení | Spouštění a registrace | Tato operace odpovídá třetí fázi automatického zřizování, pracující na software pro registraci zařízení sestavených vývojáři. Viz podrobnosti role pro vývojáře. Při prvním spuštění: <ol><li>Aplikace se připojí k instanci služby Device Provisioning Service, za globální adresa URL a služby "Rozsah ID" zadaný během vývoje.</li><li>Po připojení zařízení byl ověřen podle metody ověřování a identita zadaný během registrace.</li><li>Po ověření, je zařízení zaregistrované ve službě IoT Hub instance určené parametrem zřizování instance služby.</li><li>Po úspěšné registraci se koncový bod zařízení jedinečné ID a IoT Hub vrátí registrační aplikaci pro komunikaci se službou IoT Hub.</li><li> Odtud můžete zařízení stáhnout úvodního [dvojče zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) pro konfigurace a zahajte proces vytváření sestav telemetrická data.</li></ol>**Rychlé starty**: vzhledem k tomu, že se simulované zařízení, software pro registraci běží na pracovní stanici vývoje.| 

Následující diagram obsahuje Souhrn rolí a pořadí operací během automatického zřizování zařízení:
<br><br>
[![Pořadí automatického zřizování pro zařízení](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Volitelně výrobce můžete také provést pomocí rozhraní API služby zřizování zařízení operace "Registraci identity zařízení" (nikoli prostřednictvím operátoru). Podrobné informace o této klasifikace a další, najdete v článku [nulové registrace zařízení dotykové ovládání s videem Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (cena začíná na 41:00 značky)

## <a name="roles-and-azure-accounts"></a>Role a účty Azure

Jak je každá role mapována k účtu Azure závisí na scénáři a několik scénářů, které mohou být zahrnuty. Běžné vzory níže by měly pomoci poskytují obecné znalosti týkající se způsobu role obecně mapování na účet Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Výrobce čipu poskytuje zabezpečení služby

V tomto scénáři výrobce spravuje zabezpečení pro zákazníky, úroveň: 1. Tento scénář může být upřednostňuje tito zákazníci první úrovně, která si uživatelé nebudou muset spravovat podrobné zabezpečení. 

Výrobce zavádí zabezpečení do modulů hardwarového zabezpečení (HSM). Toto zabezpečení mohou zahrnovat výrobce získání klíče, certifikáty a další z potenciálních zákazníků, kteří už mají instance Device Provisioning a registrace skupiny nastavení. Výrobce může také generovat bezpečnostních informací pro své zákazníky.

V tomto scénáři může existovat dva účty Azure zahrnuté:

- **Účet #1**: pravděpodobně sdílen napříč role operátor a pro vývojáře do určité míry. Tuto stranu koupit od výrobce čipů po modulu hardwarového zabezpečení. Tyto čipy jsou odkazoval instance distribučních bodů přidružené k 1. účet. S registrací distribučních bodů můžete tuto stranu zapůjčení zařízení více zákazníkům úrovně dvě překonfigurováním nastavení registrace zařízení ve službě DPS. Tuto stranu mohou mít i centra IoT hub přidělené pro back-endových systémůf koncových uživatelů k vytvoření rozhraní pro přístup k telemetrii zařízení atd. V takovém případě se nemusí být potřeba druhého účtu.

- **Účet #2**: koncoví uživatelé, druhé úrovně zákazníci můžou mít vlastní centra IoT hub. Strana přidružená k účtu č. 1 pouze body zapůjčena správná zařízení v rámci tohoto účtu. Tato konfigurace vyžaduje propojení služby Device Provisioning a IoT hubs mezi účty Azure, které lze provést pomocí šablon Azure Resource Manageru.

#### <a name="all-in-one-oem"></a>Vše v jednom OEM

Výrobce může být "Výrobce OEM vše v jednom", kde pouze jednoho výrobce účtu, bylo by potřeba. Výrobce zajišťuje zabezpečení a komplexní zřizování.

Výrobce může poskytovat cloudové aplikace pro zákazníky, kteří si koupí zařízení. Tato aplikace by rozhraní službou IoT Hub přidělené výrobce.

Automaty nebo automatizované kávy počítače představují příklady v tomto scénáři.




## <a name="next-steps"></a>Další postup

Vám může být užitečné k oblíbeným položkám v tomto článku jako bod odkazu, při práci projděte odpovídající rychlých startů automatického zřizování. 

Začněte tím, že dokončení rychlého startu "Nastavení automatického zřizování", který nejlépe vyhovuje stylu vaší preferovaný nástroj pro správu, který vás provede fáze "Konfigurace služby":

- [Nastavení automatického zřizování pomocí Azure CLI](quick-setup-auto-provision-cli.md)
- [Nastavení automatického zřizování na webu Azure portal](quick-setup-auto-provision.md)
- [Nastavení automatického zřizování pomocí šablony Resource Manageru](quick-setup-auto-provision-rm.md)

Potom pokračujte "Automatické zřízení simulovaného zařízení" rychlý start, která vyhovuje mechanismu ověřování zařízení a sady SDK služby Device Provisioning Service/jazykové předvolby. V tomto rychlém startu si projdete fáze "Registrace zařízení" a "registraci zařízení a konfigurace": 

|  | Mechanismus ověřování simulovaného zařízení | Rychlý start SDK/jazyka |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certifikát X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




