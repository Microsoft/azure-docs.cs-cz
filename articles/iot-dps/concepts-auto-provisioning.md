---
title: Služba pro zřizování zařízení ze služby IoT Hub – automatické zřizování koncepty
description: Tento článek obsahuje koncepční přehled fází auto zřizování zařízení, pomocí služby zřizování zařízení IoT, IoT Hub a klienta sady SDK.
services: iot-dps
keywords: ''
author: BryanLa
ms.author: bryanla
ms.date: 03/27/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: cd458b1f6d26fbd5f5821a04cd01be5c3a4e4514
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="auto-provisioning-concepts"></a>Automatické zřizování koncepty

Jak je popsáno v [přehled](about-iot-dps.md), služba zřizování zařízení je pomocná služba, která umožňuje za běhu zřizování zařízení do služby IoT hub, bez nutnosti lidského zásahu. Po úspěšném zřízení zařízení připojit přímo s jejich určené IoT Hub. Tento proces se označuje jako automatické zřizování a zajišťuje zvýšení registraci se na pole a počáteční konfigurace pro zařízení.

## <a name="overview"></a>Přehled

Azure IoT automatické zřizování možné ho rozdělit do tří fází:

1. **Konfigurace služby** -jednorázovou konfiguraci Azure IoT Hub a služba zřizování zařízení IoT Hub instancí, je vytvoření a vytvoření propojení mezi nimi.

   > [!NOTE]
   > Bez ohledu na velikost řešení IoT, i pokud plánujete podporu miliony zařízení, je to **jednorázovou konfiguraci**.

2. **Registrace zařízení** -proces převedení instance služby zřizování zařízení upozornit zařízení, která se pokusí o registraci v budoucnu. Registrace se provádí konfigurace informací o identitu zařízení v rámci zřizování služby, jako "jednotlivých zápisu" pro jedno zařízení, nebo "registrace skupiny" pro více zařízení. Je na základě identity [ověření mechanismu](concepts-security.md#attestation-mechanism) zařízení slouží k použití, která umožňuje zřizování služby ověřit identitu pro pravosti zařízení během registrace:

   - **Čip TPM**: nakonfigurovaný jako "jednotlivých zápisu", identitu tohoto zařízení je založeno na ID registrace TPM a veřejné ověřovacího klíče. Vzhledem k tomu, že je čip TPM [specifikace]((https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)), služba očekává pouze ověřit identitu podle specifikace, bez ohledu na čipu TPM implementace (hardwaru nebo softwaru). V tématu [zřizování zařízení: ověření Identity s čipem TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) podrobnosti o ověření založené na čipu TPM. 

   - **X509**: nakonfigurovaný jako "jednotlivých registrace" nebo "registrace skupiny", identita zařízení je založena na digitální certifikát X.509, který se nahraje registrace jako soubor .pem nebo .cer.

   > [!IMPORTANT]  
   > I když není předpokladem pro používání službami zřizování zařízení, důrazně doporučujeme, aby vaše zařízení použití hardwarového zabezpečení modulu (HSM) k uložení informací o identitě citlivé zařízení, jako je například klíčů a certifikátů X.509.

3. **Registrace zařízení a konfigurace** – inicializovaná při spuštění registrace softwarem, který je sestaven pomocí služby zřizování zařízení klienta SDK, které jsou vhodné pro zařízení a ověření mechanismu. Software naváže připojení ke službě zřizování pro ověřování zařízení a následné registrace ve službě IoT Hub. Po úspěšné registraci zařízení je součástí jeho IoT Hub jedinečné ID a připojení informace o zařízení, díky kterému jej jeho počáteční konfigurace pro vyžádání obsahu a zahájit proces telemetrie. V produkčním prostředí může docházet k této fáze týdny nebo měsíce po předchozí dvě fáze.

## <a name="roles-and-operations"></a>Role a operace

Fáze popsané v předchozí části může mít rozsah týdny nebo měsíce, z důvodu produkční skutečnosti jako výrobní čas přesouvání celní proces atd. Kromě toho se může zahrnovat aktivity napříč více rolí zadaný různými entitami. V této části se podíváme podrobněji na různých rolí a operace související s každou fázi pak znázorňuje tok v diagramu pořadí. 

Automatické zřizování také uvádí požadavky na výrobce zařízení, specifická pro povolení ověření mechanismu. Úkony, může dojít také nezávislé načasování automatické zřizování fází, obzvláště v případech, kde se nová zařízení zajišťují po automatické zřizování již bylo navázáno.

Řadu – elementy QuickStart jsou uvedeny v obsahu vlevo, vysvětlení automatické zřizování prostřednictvím praktických zkušeností. Chcete-li usnadnit/zjednodušit proces učení, softwaru slouží k simulaci fyzického zařízení pro zápis a registraci. Některé elementy QuickStart vyžadovat splnění operací pro víc rolí, včetně operací pro neexistující role, vzhledem k povaze simulované elementy quickstart.

| Role | Operace | Popis | 
|------| --------- | ------------| 
| Výrobce | Kódování URL identity a registrace | Výrobce, založené na mechanismu ověření použít, je zodpovědná za kódování informace, které zařízení identity a adresu URL pro registraci zařízení zřizovací služby.<br><br>**– Elementy QuickStart**: vzhledem k tomu, že je simulované zařízení, neexistuje žádný atribut role výrobce. O tom, jak získat tyto informace, které se používá v kódování ukázkovou aplikaci registrace najdete v roli vývojáře podrobnosti. |  
| | Zadejte identitu zařízení | Jako původce údaje identity zařízení výrobce zodpovídá za komunikaci operátor (nebo určenou agenta), nebo přímo registraci ke službě zřizování zařízení prostřednictvím rozhraní API.<br><br>**– Elementy QuickStart**: vzhledem k tomu, že je simulované zařízení, neexistuje žádný atribut role výrobce. O tom, jak získat identitu zařízení, který se používá k registraci zařízení s simulované v instanci služby zřizování zařízení najdete v roli operátora podrobnosti. | 
| Operátor | Konfigurace automatického zřizování | Tato operace odpovídá první fáze zřizování se automaticky.<br><br>**– Elementy QuickStart**: provedení roli operátor konfigurace služby zřizování zařízení a instance služby IoT Hub ve vašem předplatném Azure. | Nastavení automatického zřizování |
|  | Registrace identita zařízení. | Tato operace odpovídá druhé fáze automatické zřizování.<br><br>**– Elementy QuickStart**: provedení roli operátor registraci simulované zařízení v instanci služby zřizování zařízení. Identitu tohoto zařízení je určen podle metodu ověření se simulated startu (TPM nebo X.509). Najdete v části role vývojáře podrobnosti ověření. | 
| Služba zřizování zařízení,<br>IoT Hub | \<všechny operace\> | Pro obě provozní implementace s fyzických zařízení a – elementy QuickStart s Simulovaná zařízení jsou tyto role splněna prostřednictvím služeb IoT, které konfigurujete ve vašem předplatném Azure. Role, operations fungovat stejně, jako jsou je mi to k zajišťování fyzických oproti Simulovaná zařízení služby IoT. | 
| Developer | Sestavení nebo nasazení softwaru registrace | Tato operace odpovídá třetí fáze automatické zřizování. Vývojář je odpovědná za vytváření a nasazování softwaru registrace zařízení, pomocí příslušné sady SDK.<br><br>**– Elementy QuickStart**: registrace ukázkovou aplikaci vytvoříte simuluje skutečné zařízení, pro vaši platformu nebo jazyk podle vlastní volby, který běží na pracovní stanici (namísto jeho nasazení na fyzické zařízení). Registrace aplikace provádí stejné operace jako jeden nasadit do fyzického zařízení. Zadejte metodu ověření (TPM nebo X.509 certifikátu), plus URL pro registraci a "ID obor" vaší instance služby zřizování zařízení. Identitu tohoto zařízení je dáno logiku ověření SDK v době běhu založené na metodě, kterou zadáte: <ul><li>**Ověření identity TPM** -spouští stanici vývoje [TPM simulátoru aplikace](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po spuštění samostatné aplikace slouží k extrakci čipu TPM "Ověřovací klíč" a "ID registrace" pro použití v registraci identitu tohoto zařízení. Logiku ověření SDK také používá simulátoru během registrace, k dispozici podepsaný token SAS pro ověřování a ověření registrace.</li><li>**X509 ověření** -použít nástroj, který [vygenerovat certifikát](how-to-use-sdk-tools.md#x509-certificate-generator). Po vygenerování vytvoříte soubor certifikátu, vyžaduje se pro použití v zápisu. Ověření identity logiku SDK také používá certifikát během registrace, k dispozici pro ověřování a ověření registrace.</li></ul> | 
| Zařízení | Spuštění a registrace | Tato operace odpovídá fázi třetí automatické zřizování splnit vytvořené vývojář softwaru registrace zařízení. Najdete v části role vývojáře podrobnosti. Při prvním spuštění počítače: <ol><li>Aplikace se připojí pomocí instance služby zřizování zařízení, na globální adresy URL a služby "ID" zadaný obor během vývoje.</li><li>Po připojení zařízení se ověřují vůči metodu ověření a identita zadaná při registraci.</li><li>Po ověření, je zařízení registrované v instanci služby IoT Hub zadaného zřizování instancí služby.</li><li>Po úspěšné registraci vrátíte se koncový bod zařízení jedinečné ID a IoT Hub registrace aplikace pro komunikaci se službou IoT Hub.</li><li> Odtud můžete zařízení stahují úvodního [dvojče zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) stavu pro konfiguraci a začít s procesem vytváření sestav telemetrická data.</li></ol>**– Elementy QuickStart**: vzhledem k tomu, že je simulované zařízení, registraci softwaru běží na pracovní stanici.| 

Následující diagram shrnuje role a pořadí operací během automatického zřizování zařízení:
<br><br>
![Automatické zřizování pořadí pro zařízení.](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png) 

> [!NOTE]
> Volitelně výrobce můžete také provést operaci "Registrace identity zařízení" pomocí rozhraní API služby pro zřizování zařízení (ne prostřednictvím operátor). Podrobné informace o této sekvencování a další, najdete v článku [nulové touch registrace zařízení s Azure IoT video](https://myignite.microsoft.com/sessions/55087) (počínaje značky 41:00)

## <a name="next-steps"></a>Další postup

Možná bude vhodné bookmark Tento článek jako referenční, při práci procházení odpovídající Quickstarts automatické zřizování. 

Začněte tím, že dokončení rychlý start "Vytvořit automatické zřizování", který nejlépe vyhovuje zúčastníte nástroj pro správu, který vás provede fázi "Konfigurace služby":

- [Nastavení automatického zřizování pomocí rozhraní příkazového řádku Azure](quick-setup-auto-provision-cli.md)
- [Nastavení automatického zřizování pomocí portálu Azure](quick-setup-auto-provision.md)
- [Nastavení automatického zřizování pomocí šablony Resource Manageru](quick-setup-auto-provision-rm.md)

Potom pokračujte "Automatického zřizování simulované zařízení" rychlý start, který vyhovuje mechanismus ověření zařízení a zařízení zřizování služeb SDK/jazykové předvolby. V tento rychlý start provede fáze "Registrace zařízení" a "registrace zařízení a konfigurace": 

|  | Ověření mechanismu simulovaného zařízení | Rychlý start SDK/jazyk |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certifikát X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




