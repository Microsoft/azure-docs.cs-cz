---
title: Koncepty zařízení zřizování zařízení v Azure | Dokumentace Microsoftu
description: Popisuje koncepty specifické pro zařízení pomocí služby Azure Device Provisioning a centrem IoT zřizování zařízení
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 354ef48f7935536864cde9dc0d9a130fa5aeb865
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972857"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Koncepty zařízení IoT Hub Device Provisioning Service

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, který použijete ke konfiguraci plně automatizované zřizování zařízení pro určité Centrum IoT. Se službou Device Provisioning můžete bezpečně a škálovatelně zřizovat miliony zařízení.

Tento článek obsahuje přehled *zařízení* součástí koncepty zřizování zařízení. Tento článek je relevantní osobám, které jsou součástí [výrobní krok](about-iot-dps.md#manufacturing-step) získání zařízení připravené na nasazení.

## <a name="attestation-mechanism"></a>Mechanismus ověřování

Mechanismus ověřování použije právě tato metoda za potvrzení identity zařízení. Mechanismus ověřování se taky hodí pro seznamu registrací, který informuje službu zřizování, jakou metodu ověření pro použití s daným zařízením.

> [!NOTE]
> IoT Hub "schéma ověřování" používá pro podobný koncept dané služby.

Služby Device Provisioning podporuje následující formy ověření identity:
* **Certifikáty X.509** založen na standardní tok ověřování certifikátu X.509.
* **Trusted Platform Module (TPM)** podle hodnoty nonce výzvu, pomocí čipu TPM standard pro klíče zobrazíte podepsaný token sdíleného přístupového podpisu (SAS). Tato akce nevyžaduje fyzický čip TPM na zařízení, ale služba očekává, že ověřit pomocí ověřovacího klíče za [specifikace TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symetrický klíč** podle sdíleného přístupového podpisu (SAS) [tokeny zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-tokens), která zahrnuje hash podpisu a vložené vypršení platnosti. Další informace najdete v tématu [symetrického klíče ověření](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení nebo HSM, se používá pro zabezpečené hardwarové úložiště tajných kódů zařízení a je nejbezpečnější způsob úložiště tajných kódů. Certifikáty X.509 a tokeny SAS, mohou být uloženy v modulu HSM. Moduly hardwarového zabezpečení je možné s oběma mechanismů ověřování zřizovací služba podporuje.

> [!TIP]
> Důrazně doporučujeme pomocí modulu hardwarového zabezpečení zařízení pro bezpečné ukládání tajných klíčů v zařízeních.

Tajné kódy zařízení můžou být uložené taky v softwaru (paměť), ale je méně bezpečné forma úložiště než modulu hardwarového zabezpečení.

## <a name="registration-id"></a>ID registrace

ID registrace slouží k jednoznačné identifikaci zařízení ve službě Device Provisioning. ID zařízení musí být jedinečný ve zřizovací službě [rozsah ID](#id-scope). Každé zařízení musí mít ID registrace ID registrace je alfanumerický, malá písmena a může obsahovat pomlčky.

* V případě čipu TPM ID registrace poskytuje samotném čipu TPM.
* V případě ověření založené na X.509 se ID registrace poskytuje jako název subjektu certifikátu.

## <a name="device-id"></a>ID zařízení

ID zařízení je ID, jak se zobrazuje ve službě IoT Hub. ID požadované zařízení může být nastaven v položce registrace, ale není nutné nastavit. Pokud ID požadované zařízení určena v seznamu registrací, ID registrace se používá jako ID zařízení při registraci zařízení. Další informace o [ID ve službě IoT Hub zařízení](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Obor ID

Rozsah ID je přiřazen do služby Device Provisioning Service, když je vytvořen uživatelem a slouží k jednoznačné identifikaci konkrétní službu zřizování, který zařízení k registraci prostřednictvím. Rozsah ID je generovaný službou a je neměnný, což zaručuje jedinečnost.

> [!NOTE]
> Je důležité pro dlouho běžící operace nasazení a fúzí a akvizic scénáře jedinečnost.
