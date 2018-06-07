---
title: Koncepty zařízení v Azure zařízení zřizování | Microsoft Docs
description: Popisuje zřizování koncepty, které jsou specifické pro zařízení s služby zřizování zařízení a služby IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: bd77a56acee948995bb2fcbb5beea60f69cda9ee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630149"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Koncepty služby zřizování zařízení IoT Hub zařízení

Služba zřizování zařízení IoT Hub je služba Pomocník pro IoT Hub, který použijete ke konfiguraci zařízení nula touch zřizování do zadané služby IoT hub. Se službou Device Provisioning můžete bezpečně a škálovatelně zřizovat miliony zařízení.

Tento článek nabízí přehled *zařízení* koncepty, které jsou součástí zřizování zařízení. Tento článek je nejdůležitější osoby, které se účastní [výrobní krok](about-iot-dps.md#manufacturing-step) z Příprava zařízení pro nasazení.

## <a name="attestation-mechanism"></a>Ověření mechanismu

Ověření mechanismu je metoda použitá pro potvrzení identity zařízení. Ověření mechanismu se taky hodí do seznamu registrace, který informuje zřizování služby, kterou metodu ověření pro použití s daným zařízením.

> [!NOTE]
> IoT Hub používá "schéma ověřování" pro podobný koncept v této službě.

Službu zřizování zařízení podporuje dva způsoby ověření:
* **Certifikáty X.509** podle standardní tok ověřování certifikátu X.509.
* **Trusted Platform Module (TPM)** podle nonce výzvy, pomocí čipu TPM standard pro klíče podepsaný token sdíleného přístupového podpisu (SAS) k dispozici. Nevyžaduje fyzické TPM na zařízení, ale služba očekává, že ověřit identitu pomocí ověřovacího klíče na [specifikace TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení nebo modulu hardwarového zabezpečení, se používá pro zabezpečené úložiště hardwarové zařízení tajemství a je nejbezpečnější formu tajný úložiště. X.509 – certifikáty a tokeny SAS může být uložen v modulu hardwarového zabezpečení. Moduly hardwarového zabezpečení lze použít s obou mechanismů ověření zřizování služby podporuje.

> [!TIP]
> Důrazně doporučujeme pomocí modulu hardwarového zabezpečení zařízení na zařízení bezpečně uložit tajných klíčů.

Tajné klíče zařízení může být taky uložené v softwaru (paměť), ale je méně bezpečné formu úložiště než modulu hardwarového zabezpečení.

## <a name="registration-id"></a>ID registrace

ID registrace slouží k jednoznačné identifikaci zařízení ve službě zřizování zařízení. ID zařízení musí být jedinečný v rámci zřizování služby [ID oboru](#id-scope). Každé zařízení musí mít ID registrace ID registrace je alfanumerické znaky a malých písmen a může obsahovat pomlčky.

* V případě TPM je ID registrace poskytovaný čipu TPM, sám sebe.
* V případě ověření na základě X.509 je ID registrace zadat jako název subjektu certifikátu.

## <a name="device-id"></a>ID zařízení

ID zařízení je ID, jak se objevuje v IoT Hub. ID požadovaného zařízení může být nastaven v položce zápisu, ale není nutné nastavit. Pokud žádné ID požadovaného zařízení je uvedený v seznamu registrace, ID registrace se používá jako ID zařízení při registraci zařízení. Další informace o [ID IoT hub zařízení](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID oboru

Zařízení zřizování služby je přiřazen obor ID, když je vytvořen uživatelem a slouží k jednoznačné identifikaci konkrétní zřizování službu, kterou budou zařízení registrovat prostřednictvím. ID oboru je vygenerované službou a se nedá změnit, což zaručuje jedinečnosti.

> [!NOTE]
> Jedinečnost je důležité pro scénáře fúze a akvizice nebo dlouhotrvající operace nasazení.
