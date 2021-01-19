---
title: Připojení serveru tajného klíče Thycotic ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor serveru Thycotic Secret k vyžádání protokolu Thycotic tajného serveru do služby Azure Sentinel. Zobrazení Thycoticch dat serveru tajného serveru v sešitech, vytváření výstrah a zlepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567894"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Připojení serveru tajného klíče Thycotic k Azure Sentinel

> [!IMPORTANT]
> Konektor serveru Thycotic Secret je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit své zařízení tajného serveru Thycotic ke službě Azure Sentinel. Datový konektor Thycotic tajného serveru umožňuje snadno připojit protokoly serveru Thycotic tajných klíčů ke službě Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření. Integrace mezi Thycotic a službou Azure Sentinel využívá datový konektor CEF ke správné analýze a zobrazení zpráv protokolu syslog serveru tajných klíčů.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů.

- Váš Thycotic tajný server musí být nakonfigurovaný tak, aby se protokoly exportovali přes syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Odeslání protokolů Thycotic tajného serveru do Azure Sentinel

Pokud chcete získat své protokoly do Azure Sentinel, nakonfigurujte svůj tajný server Thycotic tak, aby odesílal zprávy syslog ve formátu CEF na server pro předávání protokolů založený na systému Linux (se spuštěným rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Thycotic tajný server (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. V části **1. Konfigurace agenta syslog pro Linux** – tento krok proveďte, pokud ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud ho potřebujete. Podrobnější pokyny a vysvětlení najdete v části [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím službám Azure.

    1. V části **2. Protokol CEF (Common Event Format) do agenta SYSLOG** – postupujte podle pokynů Thycotic pro [konfiguraci tajného serveru](https://thy.center/ss/link/syslog). Tato konfigurace by měla obsahovat následující prvky:
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – **TCP 514** (Pokud je to možné jinak, nezapomeňte provést paralelní změnu v procesu démona syslog na serveru pro předávání protokolů)
        - Formát protokolu – CEF
        - Typy protokolů – všechny dostupné

    1. V části **3. Ověřit připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Pokud chcete v Log Analytics dotazovat data tajného serveru Thycotic, zkopírujte následující filtry do okna dotazu a použijte přitom další filtry:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

V některých užitečných sešitech a ukázkách dotazů se podívejte na kartu **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Thycotic tajný server k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.