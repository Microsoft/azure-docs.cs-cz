---
title: Koncepty zařízení v Azure Device Provisioning | Microsoft Docs
description: Popisuje koncepty zřizování zařízení, které jsou specifické pro zařízení se službou Device Provisioning Service (DPS) a IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389777"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service koncepty zařízení

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, která slouží ke konfiguraci zařízení s nulovým dotykem pro zadané centrum IoT. Se službou Device Provisioning můžete bezpečně a škálovatelně zřizovat miliony zařízení.

Tento článek obsahuje přehled konceptů *zařízení* , které se týkají zřizování zařízení. Tento článek je nejdůležitější pro osoby zapojení do [výrobního kroku](about-iot-dps.md#manufacturing-step) , kdy je zařízení připravené k nasazení.

## <a name="attestation-mechanism"></a>Mechanismus ověřování

Mechanismus ověřování je metoda, která se používá k potvrzení identity zařízení. Mechanismus ověřování je také relevantní pro seznam registrací, který oznamuje službě zřizování, kterou metodu ověřování použít s daným zařízením.

> [!NOTE]
> IoT Hub používá schéma ověřování pro podobný koncept v dané službě.

Služba Device Provisioning podporuje následující formy ověření identity:
* **Certifikáty x. 509** založené na standardním toku ověřování certifikátu x. 509.
* **Trusted Platform Module (TPM)** na základě výzvy s identifikátorem nonce, který používá standard TPM pro klíče k prezentaci podepsaného tokenu sdíleného přístupového podpisu (SAS). To nevyžaduje fyzického čipu TPM v zařízení, ale služba očekává ověření pomocí ověřovacího klíče podle [specifikace čipu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symetrický klíč** založený na [tokenech zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-tokens)sdíleného přístupového podpisu (SAS), které zahrnují signaturu s hodnotou hash a vložené vypršení platnosti. Další informace najdete v tématu [ověření identity symetrického klíče](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení (HSM) se používá k zabezpečenému hardwarovému úložišti tajných kódů zařízení a jedná se o nejbezpečnější formu tajného úložiště. Certifikáty X. 509 i tokeny SAS můžou být uložené v modulu HSM. HSM je možné použít s mechanismem ověřování, který služba zřizování podporuje.

> [!TIP]
> Důrazně doporučujeme používat modul HARDWAROVÉho zabezpečení a zařízení k bezpečnému ukládání tajných kódů do vašich zařízení.

Tajné kódy zařízení je také možné ukládat v softwaru (paměti), ale jedná se o méně bezpečnou podobu úložiště než modul hardwarového zabezpečení (HSM).

## <a name="registration-id"></a>ID registrace

ID registrace slouží k jednoznačné identifikaci zařízení ve službě Device Provisioning. ID zařízení musí být v [oboru ID](#id-scope)služby zřizování jedinečné. Každé zařízení musí mít ID registrace. ID registrace je alfanumerické, rozlišuje velká a malá písmena a může obsahovat speciální znaky, včetně dvojtečky, tečky, podtržítka a spojovníku.

* V případě čipu TPM je ID registrace zajištěno samotným čipem TPM.
* V případě ověřování na základě X. 509 se ID registrace poskytuje jako název subjektu certifikátu.

## <a name="device-id"></a>ID zařízení

ID zařízení je ID tak, jak se zobrazuje v IoT Hub. V položce registrace může být nastaveno požadované ID zařízení, není však nutné jej nastavit. Nastavení požadovaného ID zařízení se podporuje jenom v jednotlivých registrech. Pokud v seznamu registrací není požadované ID zařízení, jako ID zařízení se při registraci zařízení použije ID registrace. Přečtěte si další informace o [ID zařízení v IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Rozsah ID

Obor ID se přiřadí službě Device Provisioning, když ji vytvoří uživatel a používá se k jednoznačné identifikaci konkrétní služby zřizování, přes kterou se bude zařízení registrovat. Rozsah ID je vygenerován službou a je neměnný, což zaručuje jedinečnost.

> [!NOTE]
> Jedinečnost je důležitá pro dlouhotrvající operace nasazení a scénáře fúze a akvizice.
