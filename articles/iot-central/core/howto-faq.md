---
title: Nejčastější dotazy k Azure IoT Central | Microsoft Docs
description: Nejčastější dotazy k Azure IoT Centralu (FAQ) a odpovědi
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796036"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Nejčastější dotazy k IoT Central

**Návody zjistit problémy s přihlašovacími údaji, pokud se zařízení nepřipojuje k mé IoT Central aplikaci?**

[Řešení potíží s tím, proč se data ze zařízení nezobrazuje v Azure IoT Central](troubleshoot-connection.md) zahrnuje kroky pro diagnostiku problémů s připojením pro zařízení.

**Návody odeslat lístky se zákaznickou podporou?**

Pokud potřebujete pomoc, můžete si zasouborovat [lístek podpory Azure](https://portal.azure.com/#create/Microsoft.Support).

Další informace, včetně dalších možností podpory, najdete v tématu [Podpora a možnosti pomoci Azure IoT](../../iot-fundamentals/iot-support-help.md).

**Návody odblokovat zařízení?**

Když je zařízení blokované, nemůže odesílat data do vaší aplikace IoT Central. Blokovaná zařízení mají na stránce **zařízení** ve vaší aplikaci stav **blokováno** . Operátor musí zařízení odblokovat, aby mohl pokračovat v odesílání dat:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Snímek obrazovky znázorňující blokované zařízení":::

Když operátor odblokuje zařízení, stav se vrátí do předchozí hodnoty, **zaregistrováno** nebo **zřízeno**.

**Návody schválit zařízení?**

Pokud stav zařízení čeká na **schválení** na stránce **zařízení** , znamená to, že je možnost **automatického schvalování** zakázaná:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Snímek obrazovky znázorňující možnost automatického schvalování zakázané aplikace":::

Operátor musí explicitně schválit zařízení před tím, než začne zasílat data. Zařízení, která nejsou zaregistrovaná ručně na stránce **zařízení** , ale jsou připojená s platnými přihlašovacími údaji, budou mít stav zařízení **čekající na schválení**. Operátoři mohou schválit tato zařízení ze stránky **zařízení** pomocí tlačítka **schválit** :

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Snímek obrazovky ukazující, jak schválit zařízení":::

**Návody přidružit zařízení k šabloně zařízení?**

Pokud je stav zařízení **Nepřidruženo**, znamená to, že zařízení připojující se k IoT Central nemá přidruženou šablonu zařízení. K této situaci obvykle dochází v následujících situacích:

- Sada zařízení se přidá na stránce **zařízení** pomocí **importu** bez zadání šablony zařízení.
- Zařízení se zaregistrovalo ručně na stránce **zařízení** bez zadání šablony zařízení. Zařízení se pak připojí s platnými přihlašovacími údaji.  

Operátor může přidružit zařízení k šabloně zařízení ze stránky **zařízení** pomocí tlačítka **migrovat** . Další informace najdete v tématu [Správa zařízení ve vaší aplikaci Azure IoT Central > migrace zařízení do šablony](howto-manage-devices.md).

**Kde se mohu dozvědět více o IoT Hub?**

Azure IoT Central používá Azure IoT Hub jako cloudovou bránu, která umožňuje připojení zařízení. IoT Hub povoluje:

- Přijímání dat v cloudu se škálováním.
- Správa zařízení.
- Zabezpečte připojení zařízení.

Další informace o IoT Hub najdete v tématu [Azure IoT Hub](../../iot-hub/index.yml).

**Kde se mohu dozvědět více o službě Device Provisioning Service (DPS)?**

Azure IoT Central používá DPS k tomu, aby se zařízení mohla připojovat k vaší aplikaci. Další informace o tom, jak se role DPS hraje v tématu připojení zařízení k IoT Central, najdete v tématu věnovaném [připojení k Azure IoT Central](concepts-get-connected.md). Další informace o DPS najdete v tématu [zřizování zařízení pomocí Azure IoT Hub Device Provisioning Service](../../iot-dps/about-iot-dps.md).