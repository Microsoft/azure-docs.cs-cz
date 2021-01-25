---
title: Monitorování stavu aplikace IoT Central v Azure | Microsoft Docs
description: Jako operátor nebo správce monitorujte celkový stav zařízení připojených k vaší IoT Central aplikaci.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 12485e07bc3cad9d420141ecc6ee66763ac010b7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760767"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>Monitorování celkového stavu zařízení připojených k aplikaci IoT Central

> [!NOTE]
> Metriky jsou k dispozici pouze pro aplikace IoT Central verze 3. Informace o tom, jak zjistit verzi vaší aplikace, najdete v tématu [o vaší aplikaci](./howto-get-app-info.md).

*Tento článek se týká operátorů a správců.*

V tomto článku se naučíte, jak pomocí sady metrik poskytovaných pomocí IoT Central posoudit celkový stav zařízení připojených k vaší IoT Central aplikaci.

Metriky jsou ve výchozím nastavení povolené pro vaši aplikaci IoT Central a vy k nim přistupujete z [Azure Portal](https://portal.azure.com/). [Azure monitor datovou platformu zpřístupňuje tyto metriky](../../azure-monitor/platform/data-platform-metrics.md) a nabízí několik způsobů, jak s nimi pracovat. Například můžete použít grafy v Azure Portal, REST API nebo dotazech v prostředí PowerShell nebo v rozhraní příkazového řádku Azure CLI.

### <a name="trial-applications"></a>Zkušební verze aplikací

Aplikace, které používají bezplatný zkušební plán, nemají přidružené předplatné Azure, a proto nepodporují Azure Monitor metriky. Můžete [převést aplikaci na cenový tarif Standard](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) a získat přístup k těmto metrikám.

## <a name="view-metrics-in-the-azure-portal"></a>Zobrazit metriky v Azure Portal

V následujících krocích se předpokládá, že máte [aplikaci IoT Central](./quick-deploy-iot-central.md) s některými [připojenými zařízeními](./tutorial-connect-device.md).

Postup zobrazení IoT Centralch metrik na portálu:

1. Na portálu přejděte na prostředek aplikace IoT Central. Ve výchozím nastavení se IoT Central prostředky nacházejí ve skupině prostředků s názvem **IOTC**.
1. Pokud chcete vytvořit graf z metrik vaší aplikace, vyberte v části **monitorování** možnost **metriky** .

![Metriky Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal oprávnění

Přístup k metrikám v Azure Portal spravuje [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Pomocí Azure Portal můžete přidat uživatele do IoT Central aplikace/skupiny prostředků nebo předplatného, abyste jim udělili přístup. Na portálu je nutné přidat uživatele, i když jsou již přidány do aplikace IoT Central. Používejte [předdefinované role Azure](../../role-based-access-control/built-in-roles.md) pro jemnější řízení přístupu.

## <a name="iot-central-metrics"></a>IoT Central metriky

Seznam metrik, které jsou aktuálně k dispozici pro IoT Central, najdete v tématu [podporované metriky s Azure monitor](../../azure-monitor/platform/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Metriky a faktury

Metriky se můžou lišit od čísel zobrazených na faktuře Azure IoT Central. K této situaci dochází z několika důvodů:

- [Cenové tarify IoT Central Standard](https://azure.microsoft.com/pricing/details/iot-central/) zahrnují dvě zařízení a různé kvóty zpráv zdarma. I když jsou bezplatné položky vyloučené z fakturace, pořád se započítávají do metrik.

- IoT Central pro každou šablonu zařízení v aplikaci automaticky vygeneruje jedno ID testovacího zařízení. Toto ID zařízení je viditelné na stránce **Spravovat testovací zařízení** pro šablonu zařízení. Tvůrci řešení se můžou rozhodnout [ověřit své šablony zařízení](./overview-iot-central.md#create-device-templates) před jejich publikováním, a to tak, že generují kód, který používá tato ID testovacích zařízení. I když se tato zařízení z fakturace vyloučí, pořád se započítávají do metrik.

- I když se metriky můžou ukázat jako podmnožina komunikace typu zařízení-Cloud, veškerá komunikace mezi zařízením a cloudem se [vypočítává jako zpráva k fakturaci](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s použitím šablon aplikací, je navržený další krok, kde se dozvíte, jak [spravovat IoT Central z Azure Portal](howto-manage-iot-central-from-portal.md).