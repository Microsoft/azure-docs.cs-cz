---
title: IoT Hub Device Provisioning Service – role a operace
description: Tento článek obsahuje koncepční přehled rolí a operací, které se týkají vývoje a řešení IoT pomocí služby IoT Device Provisioning (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 52c0dcf4bd9f7b2ca8928eab5272a6500cbb379d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533048"
---
# <a name="roles-and-operations"></a>Role a operace

Fáze vývoje řešení IoT můžou být v průběhu týdnů nebo měsíců v důsledku produkčních realit, jako je výrobní čas, dodávka, celní proces atd. Kromě toho mohou rozbírat aktivity napříč více rolemi, které jsou dány různým subjektům. V tomto tématu se podíváme na různé role a operace související s jednotlivými fázemi a pak tento tok znázorňuje v sekvenčním diagramu. 

Zřizování také umístí požadavky na výrobce zařízení, které jsou specifické pro povolení [mechanismu ověřování](concepts-service.md#attestation-mechanism). Výrobní operace mohou také nastat nezávisle na načasování fází automatického zřizování, zejména v případech, kdy jsou nová zařízení zavedena po vytvoření automatického zřizování.

Řada rychlých startů je k dispozici v obsahu vlevo, což vám umožní vysvětlit Automatické zřizování prostřednictvím praktických zkušeností. Aby bylo možné zjednodušit a zjednodušit proces učení, používá se software k simulaci fyzického zařízení pro registraci a registraci. Některé rychlé starty vyžadují, abyste splnili operace pro více rolí, včetně operací pro neexistující role, a to kvůli simulované povaze rychlých startů.

| Role | Operace | Description |
|------| --------- | ------------|
| Manufacturer | Adresa URL pro kódování identity a registrace | Na základě použitého mechanismu ověřování je výrobce zodpovědný za kódování informací o identitě zařízení a adresu URL pro registraci služby Device Provisioning.<br><br>**Rychlé starty**: vzhledem k tomu, že zařízení je simulované, není k dispozici žádná role výrobce. Podrobnosti o tom, jak získáte tyto informace, které se používají při kódování ukázkové registrační aplikace, najdete v tématu role vývojáře. |
| | Zadejte identitu zařízení. | Jako původce informace o identitě zařízení je výrobce zodpovědný za jeho komunikaci s operátorem (nebo určeným agentem) nebo jeho přímým zápisem do služby Device Provisioning prostřednictvím rozhraní API.<br><br>**Rychlé starty**: vzhledem k tomu, že zařízení je simulované, není k dispozici žádná role výrobce. Podrobnosti o tom, jak získat identitu zařízení, která se používá k registraci simulovaného zařízení v instanci služby Device Provisioning, najdete v tématu role operátora. |
| Operátor | Konfigurace automatického zřizování | Tato operace odpovídá první fázi automatického zřizování.<br><br>**Rychlé starty**: provádíte roli operátora a nakonfigurujete službu Device Provisioning a instance IoT Hub v předplatném Azure. |
|  | Registrovat identitu zařízení | Tato operace odpovídá druhé fázi automatického zřizování.<br><br>**Rychlé starty**: provádíte roli operátora a zaregistrujete simulované zařízení do instance služby Device Provisioning. Identitu zařízení určuje metoda ověření identity, která se simuluje v rychlém startu (TPM nebo X. 509). Podrobnosti o ověření najdete v tématu role vývojáře. |
| Služba Device Provisioning<br>IoT Hub | \<all operations\> | Pro produkční implementaci s fyzickými zařízeními a rychlé starty se simulovanými zařízeními jsou tyto role splněné prostřednictvím služeb IoT, které nakonfigurujete ve svém předplatném Azure. Role/operace fungují přesně stejně, protože služby IoT se neliší od zřizování fyzických a simulovaných zařízení. |
| Vývojář | Sestavit/nasadit registrační software | Tato operace odpovídá třetí fázi automatického zřizování. Vývojář zodpovídá za sestavení a nasazení registračního softwaru do zařízení pomocí příslušné sady SDK.<br><br>**Rychlé starty**: Ukázková registrační aplikace, kterou sestavíte, simuluje reálné zařízení, pro zvolenou platformu nebo jazyk, která se spouští na vaší pracovní stanici (místo nasazení na fyzické zařízení). Registrační aplikace provádí stejné operace jako jeden nasazený na fyzickém zařízení. Pro instanci služby Device Provisioning můžete zadat metodu ověření identity (TPM nebo X. 509 Certificate) a adresu URL pro registraci a obor ID. Identita zařízení je určena logikou ověření sady SDK za běhu na základě vámi zadané metody: <ul><li>**Ověření identity čipem TPM** – vaše vývojová pracovní stanice spouští [aplikaci simulátoru TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po spuštění se k extrakci "ověřovacího klíče TPM" a "ID registrace" použije samostatná aplikace pro použití při registraci identity zařízení. Logika ověření sady SDK také používá simulátor během registrace k zobrazení podepsaného tokenu SAS pro ověřování a ověření registrace.</li><li>**Ověření identity x509** – pomocí nástroje [Vygenerujte certifikát](how-to-use-sdk-tools.md#x509-certificate-generator). Po vygenerování se vytvoří soubor s certifikátem, který se vyžaduje pro použití při registraci. Logika ověření sady SDK také používá certifikát při registraci, aby bylo možné provést ověření ověřování a registrace.</li></ul> |
| Zařízení | Spouštění a registrovat | Tato operace odpovídá třetí fázi automatického zřizování, kterou splňuje software pro registraci zařízení sestavený vývojářem. Podrobnosti najdete v tématu role vývojáře. Při prvním spuštění: <ol><li>Aplikace se připojí k instanci služby Device Provisioning pro každou globální adresu URL a službu "obor ID", která se zadala během vývoje.</li><li>Po připojení se zařízení ověří metodou ověření identity a identitou zadanou během registrace.</li><li>Po ověření se zařízení zaregistruje s instancí IoT Hub určenou instancí služby zřizování.</li><li>Po úspěšné registraci se do registrační aplikace vrátí jedinečné ID zařízení a IoT Hub koncový bod pro komunikaci s IoT Hub.</li><li> Odtud může zařízení načítat počáteční stav nedokončeného [zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) pro konfiguraci a zahájit proces vytváření sestav dat telemetrie.</li></ol>**Rychlé starty**: vzhledem k tomu, že se zařízení simuluje, je registrační software spuštěný na vaší pracovní stanici pro vývoj.|

Následující diagram shrnuje role a sekvencování operací při automatickém zřizování zařízení:
<br><br>
[![Automatické zřizování sekvence pro zařízení](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> V případě potřeby může výrobce také provést operaci registrovat identitu zařízení pomocí rozhraní API služby Device Provisioning (místo přes operátor). Podrobnou diskuzi o tomto sekvencování a další informace najdete v tématu [registrace zařízení s nulovou dotykovou registrací pomocí Azure IoT video](https://youtu.be/cSbDRNg72cU?t=2460) (počínaje značkou 41:00).

## <a name="roles-and-azure-accounts"></a>Role a účty Azure

Způsob mapování jednotlivých rolí na účet Azure závisí na scénáři a existuje poměrně několik scénářů, které mohou být zahrnuty. Níže uvedené běžné vzory by vám měly poskytnout obecné informace o tom, jak jsou role všeobecně mapované na účet Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Výrobce čipu poskytuje bezpečnostní služby

V tomto scénáři výrobce spravuje zabezpečení pro zákazníky úrovně One. Tento scénář můžou být upřednostňovány těmito zákazníky úrovně, protože nemusejí spravovat podrobné zabezpečení. 

Výrobce zavádí zabezpečení v modulech hardwarového zabezpečení (HSM). Toto zabezpečení může zahrnovat výrobce získávající klíče, certifikáty atd. od potenciálních zákazníků, kteří už mají nastavování instancí DPS a skupin registrace. Výrobce může také vygenerovat tyto informace o zabezpečení pro své zákazníky.

V tomto scénáři můžou být zapojené dva účty Azure:

- **#1 účtu**: v některém stupni se nejspíš sdílí mezi operátorem a rolemi vývojářů. Tato strana může koupit čipy HSM od výrobce. Tyto čipy odkazují na instance DPS přidružené k účtu #1. V případě registrace DPS může tato strana zapůjčit zařízení několika zákazníkům na úrovni dvou úrovní tím, že překonfiguruje nastavení registrace zařízení v DPS. Tato strana může také mít k rozhraní back-end koncových uživatelů přidělená zařízení, aby bylo možné získat přístup k telemetrie zařízení atd. V takovém případě nemusí být potřeba druhý účet.

- **#2 účtu**: koncoví uživatelé, úroveň-dva zákazníci můžou mít svoje vlastní centra IoT. Strana přidružená k účtu #1 v tomto účtu jenom zařízení pronajatá na správné centrum. Tato konfigurace vyžaduje propojování rozbočovačů DPS a IoT v rámci účtů Azure, které je možné provádět pomocí Azure Resource Manager šablon.

#### <a name="all-in-one-oem"></a>Vše v jednom výrobci OEM

Výrobcem může být "All-in-One OEM", kde bude potřeba jenom jeden účet výrobce. Výrobce zpracovává zabezpečení a zřizování na konci.

Výrobce může poskytnout cloudovou aplikaci zákazníkům, kteří si zakoupí zařízení. Tato aplikace by měla být v rozhraní IoT Hub přidělená výrobcem.

Příklady pro tento scénář představuje prodejní počítače nebo automatizované kávové počítače.




## <a name="next-steps"></a>Další kroky

Může být užitečné, abyste tento článek mohli označit jako bod odkazu, jak budete pracovat prostřednictvím odpovídajících rychlých startů pro Automatické zřizování. 

Začněte tím, že vyplníte "nastavení automatického zřizování", které nejlépe vyhovuje předvolbám nástrojů pro správu, které vás provede fází konfigurace služby:

- [Nastavení automatického zřizování pomocí Azure CLI](quick-setup-auto-provision-cli.md)
- [Nastavení automatického zřizování pomocí Azure Portal](quick-setup-auto-provision.md)
- [Nastavení automatického zřizování pomocí šablony Správce prostředků](quick-setup-auto-provision-rm.md)

Pak pokračujte v rychlém startu "zřízení zařízení", které vyhovuje mechanismu ověřování zařízení a předvolbu sady SDK/jazyka služby Device Provisioning. V tomto rychlém startu si projdete fáze "registrace zařízení" a "registrace zařízení a konfigurace": 

| Mechanismus ověřování zařízení | Rychlý Start SDK/jazyk | 
| ------------------------------- | -------------------- |
| Symetrický klíč | [R](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| Certifikát X. 509 | [R](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Simulovaný čip TPM (Trusted Platform Module) | [R](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




