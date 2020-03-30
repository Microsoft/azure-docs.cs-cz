---
title: Koncepty zařízení v zřizování zařízení Azure | Dokumenty společnosti Microsoft
description: Popisuje koncepty zřizování zařízení specifické pro zařízení se službou Device Provisioning Service (DPS) a službou IoT Hub.
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285159"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Koncepty zařízení pro službu Azure IoT Hub Device Provisioning

Služba zřizování zařízení služby IoT Hub je pomocná služba pro službu IoT Hub, kterou používáte ke konfiguraci zřizování zařízení bez dotykového ovládání do určeného centra IoT Hub. Se službou Device Provisioning můžete bezpečně a škálovatelně zřizovat miliony zařízení.

Tento článek poskytuje přehled konceptů *zařízení* zapojených do zřizování zařízení. Tento článek je nejdůležitější pro osoby zapojené do [výrobního kroku](about-iot-dps.md#manufacturing-step) získání zařízení připraveného k nasazení.

## <a name="attestation-mechanism"></a>Atestační mechanismus

Mechanismus ověřování je metoda používaná k potvrzení identity zařízení. Mechanismus ověřování je také relevantní pro seznam zápisu, který říká zřizovací službě, kterou metodu ověřování použít s daným zařízením.

> [!NOTE]
> IoT Hub používá "schéma ověřování" pro podobný koncept v této službě.

Služba device provisioning podporuje následující formy atestace:
* **Certifikáty X.509** založené na standardním toku ověřování certifikátu X.509.
* **Trusted Platform Module (TPM)** na základě výzvy nonce, pomocí standardu TPM pro klíče prezentovat podepsaný token sdíleného přístupového podpisu (SAS). To nevyžaduje fyzické čipu TPM v zařízení, ale služba očekává, že poznamená pomocí ověřovacího klíče podle [specifikace čipu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symetrický klíč** založený na [tokenech zabezpečení sdíleného](../iot-hub/iot-hub-devguide-security.md#security-tokens)přístupového podpisu (SAS) , které obsahují zahashovaný podpis a vložené vypršení platnosti. Další informace naleznete v tématu [Symetrické atestace klíče](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení nebo modul hardwarového zabezpečení se používá pro zabezpečené hardwarové úložiště tajných kódů zařízení a je nejbezpečnější formou tajného úložiště. Certifikáty X.509 a tokeny SAS mohou být uloženy v souboru hesm. HSM lze použít s oběma mechanismy ověřování zřizovací služba podporuje.

> [!TIP]
> Důrazně doporučujeme používat bezpečnostní počítač se zařízeními k bezpečnému ukládání tajných kódů na vaše zařízení.

Tajné klíče zařízení mohou být také uloženy v softwaru (paměti), ale je to méně bezpečná forma úložiště než hsm.

## <a name="registration-id"></a>ID registrace

ID registrace se používá k jednoznačné identifikaci zařízení ve službě Zřizování zařízení. ID zařízení musí být jedinečné v [oboru ID](#id-scope)služby zřizování . Každé zařízení musí mít ID registrace. ID registrace je alfanumerické, malá a velká písmena a může obsahovat speciální znaky včetně dvojtečky, tečky, podtržítka a pomlčky.

* V případě čipu TPM poskytuje ID registrace samotný čip TPM.
* V případě osvědčení založeného na X.509 je id registrace uvedeno jako název subjektu certifikátu.

## <a name="device-id"></a>ID zařízení

ID zařízení je ID, jak se zobrazuje v centru IoT Hub. Požadované ID zařízení může být nastaveno v položce registrace, ale není nutné jej nastavit. Nastavení požadovaného ID zařízení je podporováno pouze v jednotlivých registracích. Pokud v seznamu registrací není zadáno žádné požadované ID zařízení, použije se ID registrace jako ID zařízení při registraci zařízení. Další informace o [ID zařízení v Centru IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Obor ID

Obor ID je přiřazen službě zřizování zařízení, když je vytvořen uživatelem a slouží k jednoznačné identifikaci konkrétní zřizovací služby, kterou zařízení zaregistruje. Obor ID je generován službou a je neměnný, což zaručuje jedinečnost.

> [!NOTE]
> Jedinečnost je důležitá pro dlouhodobé operace nasazení a scénáře fúzí a akvizic.
