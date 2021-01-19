---
title: Připojit Trend Micro TippingPoint do Azure Sentinel | Microsoft Docs
description: Naučte se, jak používat datový konektor Trend Micro TippingPoint k získání obsahu v rámci služby Azure Sentinel z TippingPoint. Zobrazení dat TippingPoint v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.openlocfilehash: 989520e079988e1821d8bb9a936f857e1f62c11a
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567760"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Připojení řešení Trend Micro TippingPoint k Azure Sentinel

> [!IMPORTANT]
> Konektor Trend Micro TippingPoint je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

V tomto článku se dozvíte, jak připojit řešení System pro ochranu před hrozbami trendu z ochrany před internetovými útoky do Azure Sentinel Datový konektor Trend Micro TippingPoint vám umožňuje snadno připojit protokoly zabezpečení TippingPoint (SMS) do systému Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je k vylepšení šetření. 

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Odeslání protokolů trendu pro záznamy o netippingpoint do Azure Sentinel

Pokud chcete získat své protokoly do služby Azure Sentinel, nakonfigurujte své řešení TPS, aby odesílalo zprávy syslog ve formátu CEF na server pro předávání protokolů založený na systému Linux (se spuštěným rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Trend Micro TippingPoint (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. V části **1. Konfigurace agenta syslog pro Linux** – tento krok proveďte, pokud ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud ho potřebujete. Podrobnější pokyny a vysvětlení najdete v části [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím službám Azure.

    1. V části **2. Přeposílání protokolu SMS Trend Micro TippingPoint do agenta SYSLOG** – Tato konfigurace by měla zahrnovat tyto prvky:
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – **TCP 514** (Pokud je to možné jinak, nezapomeňte provést paralelní změnu v procesu démona syslog na serveru pro předávání protokolů)
        - Formát protokolu – **ARCSIGHT CEF Format v 4.2**
        - Typy protokolů – všechny dostupné

    1. V části **3. Ověřit připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Pokud chcete v Log Analytics dotazovat data TrendMicro TippingPoint, zkopírujte do okna dotazu následující příkaz a použijte přitom jiné filtry, když zvolíte:

```kusto
CommonSecurityLog 
| where DeviceVendor == "TrendMicroTippingPoint"
```

Další ukázky dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak propojit Trend Micro TippingPoint s Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.