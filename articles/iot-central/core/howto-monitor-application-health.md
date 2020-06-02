---
title: Monitorování stavu aplikace IoT Central v Azure | Microsoft Docs
description: Jako operátor nebo správce monitorujte celkový stav zařízení připojených k vaší IoT Central aplikaci.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 664819b209aeb09093ce8711456b86ff4d3e8949
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84249575"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>Monitorování celkového stavu zařízení připojených k aplikaci IoT Central

*Tento článek se týká operátorů a správců.*

V tomto článku se naučíte, jak pomocí sady metrik poskytovaných pomocí IoT Central posoudit celkový stav zařízení připojených k vaší IoT Central aplikaci.

Metriky jsou ve výchozím nastavení povolené pro vaši aplikaci IoT Central a vy k nim přistupujete z [Azure Portal](https://portal.azure.com/). [Azure monitor datovou platformu zpřístupňuje tyto metriky](../../azure-monitor/platform/data-platform-metrics.md) a nabízí několik způsobů, jak s nimi pracovat. Například můžete použít grafy v Azure Portal, REST API nebo dotazech v prostředí PowerShell nebo v rozhraní příkazového řádku Azure CLI.

### <a name="trial-applications"></a>Zkušební verze aplikací

Aplikace, které používají bezplatný zkušební plán, nemají přidružené předplatné Azure, a proto nepodporují Azure Monitor metriky. Můžete [převést aplikaci na cenový tarif Standard](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) a získat přístup k těmto metrikám.

## <a name="view-metrics-in-the-azure-portal"></a>Zobrazit metriky v Azure Portal

V následujících krocích se předpokládá, že máte [aplikaci IoT Central](./quick-deploy-iot-central.md) s některými [připojenými zařízeními](./tutorial-connect-device-nodejs.md).

Postup zobrazení IoT Centralch metrik na portálu:

1. Na portálu přejděte na prostředek aplikace IoT Central. Ve výchozím nastavení se IoT Central prostředky nacházejí ve skupině prostředků s názvem **IOTC**.
1. Pokud chcete vytvořit graf z metrik vaší aplikace, vyberte v části **monitorování** možnost **metriky** .

### <a name="azure-portal-permissions"></a>Azure Portal oprávnění

Přístup k metrikám v Azure Portal spravuje [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Pomocí Azure Portal můžete přidat uživatele do IoT Central aplikace/skupiny prostředků nebo předplatného, abyste jim udělili přístup. Na portálu je nutné přidat uživatele, i když jsou již přidány do aplikace IoT Central. Používejte [předdefinované role Azure](../../role-based-access-control/built-in-roles.md) pro jemnější řízení přístupu.

## <a name="iot-central-metrics"></a>IoT Central metriky

Následující tabulka popisuje metriky, které jsou aktuálně k dispozici pro IoT Central:

| Metrika | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis |
|--------|---------------------|------|------------------|-------------|
| connectedDeviceCount         | Celkem připojených zařízení                              | Počet  | Celkem             | Počet zařízení připojených k IoT Central                               |
| C2D. Property. Read. Success    | Úspěšné čtení vlastností zařízení z IoT Central    | Počet  | Celkem             | Počet všech úspěšných čtení vlastností zahájených z IoT Central    |
| C2D. Property. Read. Failure    | Neúspěšná čtení vlastností zařízení z IoT Central        | Počet  | Celkem             | Počet všech neúspěšných čtení vlastností zahájených z IoT Central        |
| D2C. Property. Read. Success    | Úspěšná čtení vlastností zařízení ze zařízení        | Počet  | Celkem             | Počet všech úspěšných čtení vlastností inicializovaných ze zařízení        |
| D2C. Property. Read. Failure    | Neúspěšná čtení vlastností zařízení ze zařízení            | Počet  | Celkem             | Počet všech neúspěšných čtení vlastností inicializovaných ze zařízení            |
| C2D. Property. Update. Success  | Úspěšná aktualizace vlastností zařízení z IoT Central  | Počet  | Celkem             | Počet všech úspěšných aktualizací vlastností zahájených z IoT Central  |
| C2D. Property. Update. Failure  | Neúspěšné aktualizace vlastností zařízení z IoT Central      | Počet  | Celkem             | Počet všech neúspěšných aktualizací vlastností iniciované z IoT Central      |
| D2C. Property. Update. Success  | Úspěšná aktualizace vlastností zařízení ze zařízení      | Počet  | Celkem             | Počet všech úspěšných aktualizací vlastností inicializovaných ze zařízení      |
| D2C. Property. Update. Failure  | Neúspěšné aktualizace vlastností zařízení ze zařízení          | Počet  | Celkem             | Počet všech neúspěšných aktualizací vlastností inicializovaných ze zařízení          |

### <a name="metrics-and-invoices"></a>Metriky a faktury

Metriky se můžou lišit od čísel zobrazených na faktuře Azure IoT Central. K této situaci dochází z několika důvodů:

- [Cenové tarify IoT Central Standard](https://azure.microsoft.com/pricing/details/iot-central/) zahrnují dvě zařízení a různé kvóty zpráv zdarma. I když jsou bezplatné položky vyloučené z fakturace, pořád se započítávají do metrik.

- IoT Central pro každou šablonu zařízení v aplikaci automaticky vygeneruje jedno ID testovacího zařízení. Toto ID zařízení je viditelné na stránce **Spravovat testovací zařízení** pro šablonu zařízení. Tvůrci řešení se můžou rozhodnout [ověřit své šablony zařízení](./overview-iot-central.md#create-device-templates) před jejich publikováním, a to tak, že generují kód, který používá tato ID testovacích zařízení. I když se tato zařízení z fakturace vyloučí, pořád se započítávají do metrik.

- I když se metriky můžou ukázat jako podmnožina komunikace typu zařízení-Cloud, veškerá komunikace mezi zařízením a cloudem se [vypočítává jako zpráva k fakturaci](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s použitím šablon aplikací, je navržený další krok, kde se dozvíte, jak [spravovat IoT Central z Azure Portal](howto-manage-iot-central-from-portal.md)
