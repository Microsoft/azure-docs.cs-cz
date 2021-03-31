---
title: Připojení platformy NFP (WireX Network forenzní Platform) do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor NFP Systems WireX k vyžádání protokolu WireX NFP do Azure Sentinel. Zobrazení WireXch dat NFP v sešitech, vytváření výstrah a zlepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541547"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Připojení zařízení WireX Network forenzní Platform (NFP) k Azure Sentinel

> [!IMPORTANT]
> Konektor NFP Systems WireX je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení WireX Systems forenzní Platform (NFP) ke službě Azure Sentinel. Datový konektor WireX NFP vám umožňuje snadno připojit protokoly NFP pomocí funkce Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření. 

> [!NOTE] 
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Odeslat protokoly NFP WireX do Azure Sentinel

Pokud se chcete přihlásit do Azure Sentinel, nakonfigurujte zařízení WireX Systems NFP tak, aby odesílala zprávy syslog ve formátu CEF do serveru pro předávání protokolů zacházejícího se systémem Linux (se spuštěným rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **WireX Network forenzní Platform (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. **1. Linux konfigurace agenta SYSLOG** – tento krok proveďte v případě, že ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud potřebujete nějaký jiný. Podrobnější pokyny a vysvětlení najdete v části [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím službám Azure.

    1. **2. předejte protokoly CEF (Common Event Format) do agenta syslog** – kontaktujte [podporu WireX](https://wirexsystems.com/contact-us/) pro správnou konfiguraci řešení WireX NFP. Tato konfigurace by měla obsahovat následující prvky:
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – TCP 514 (Pokud je to možné jinak, nezapomeňte provést paralelní změnu v procesu démona syslog na serveru pro předávání protokolů)
        - Formát protokolu – CEF
        - Typy protokolů – všechny doporučené podle WireX

    1. **3. ověření připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Pokud chcete zadat dotaz na WireX data NFP v Log Analytics, zkopírujte do okna dotazu následující příkaz a použijte přitom jiné filtry, když zvolíte:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Další ukázky dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit WireX Systems NFP k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.