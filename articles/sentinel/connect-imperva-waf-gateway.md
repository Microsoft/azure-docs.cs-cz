---
title: Připojte zařízení brány Imperva WAF ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor brány Imperva WAF k vyžádání protokolu Imperva WAF do Azure Sentinel. Zobrazení Impervach dat WAF v sešitech, vytváření výstrah a zlepšení šetření.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698429"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Připojení zařízení brány Imperva WAF k Azure Sentinel

> [!IMPORTANT]
> Konektor brány Imperva WAF je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení brány Imperva WAF ke službě Azure Sentinel. Konektor dat brány Imperva WAF umožňuje snadno připojit protokoly Imperva WAF Gateway k Azure Sentinel, abyste mohli zobrazit data v sešitech, použít je k vytváření vlastních výstrah a začlenit je k vylepšení šetření. Integrace mezi bránou Imperva WAF a službou Azure Sentinel využívá CEF zprávy syslog, server pro přeposílání protokolů a agenta Log Analytics.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Odeslat protokoly brány Imperva WAF do Azure Sentinel

Pokud se chcete přihlásit do Azure Sentinel, nakonfigurujte zařízení brány Imperva WAF tak, aby odesílalo zprávy syslog ve formátu CEF do serveru pro předávání protokolů zacházejícího se systémem Linux (se spuštěným rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Imperva WAF Gateway (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. V části **1. Konfigurace agenta syslog pro Linux** – tento krok proveďte, pokud ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud ho potřebujete. Podrobnější pokyny a vysvětlení najdete v části [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím službám Azure.

    1. V části **2. Protokol CEF (Common Event Format) do agenta SYSLOG** – tento konektor vyžaduje, aby se v konzole pro správu **IMPERVA SecureSphere MX** vytvořilo **rozhraní akcí** a **Akce** . Podle pokynů Imperva [Povolte protokolování výstrah brány IMPERVA WAF do Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Tato konfigurace by měla obsahovat následující prvky:
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – TCP 514
        - Formát protokolu – CEF
        - Typy protokolů – všechny dostupné

    1. V části **3. Ověřit připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Chcete-li zadat dotaz na data brány Imperva WAF v Log Analytics, zkopírujte následující příkaz do okna dotazu a použijte přitom další filtry:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Další užitečné Ukázky dotazů najdete na kartě **Další kroky** na stránce konektoru.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit bránu Imperva WAF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.