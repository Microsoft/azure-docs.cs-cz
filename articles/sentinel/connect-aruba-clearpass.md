---
title: Připojení dat Aruba ClearPass k Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor Aruba ClearPass k vyžádání protokolu Aruba ClearPass do Azure Sentinel. Zobrazení Arubach dat ClearPass v sešitech, vytváření výstrah a zlepšení šetření.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 1b03e2d8715b3d80425ef181dd908978d5df4cf2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744884"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Připojení Aruba ClearPass k Azure Sentinel

> [!IMPORTANT]
> Konektor Aruba ClearPass je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení ClearPass Aruba ke službě Azure Sentinel. Datový konektor Aruba ClearPass vám umožňuje snadno připojit protokoly Aruba ClearPass k Sentinel Azure, abyste mohli zobrazit data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je ke zlepšení šetření. Integrace mezi Aruba ClearPass a službou Azure Sentinel využívá CEF zprávy syslog, server pro přeposílání protokolů a agenta Log Analytics, které jsou v systému Linux. Používá také uživatelsky sestavený analyzátor protokolů založený na funkci Kusto.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Odeslat protokoly ClearPass Aruba do Azure Sentinel

Pokud chcete získat své protokoly do Azure Sentinel, nakonfigurujte zařízení Aruba ClearPass tak, aby odesílalo zprávy syslog ve formátu CEF do serveru pro předávání protokolu založeného na systému Linux (se spuštěným rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Aruba ClearPass (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. V části **1. Konfigurace agenta syslog pro Linux** – tento krok proveďte, pokud ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud ho potřebujete. Podrobnější pokyny a vysvětlení najdete v části [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím službám Azure.

    1. V části **2. Přeposílání protokolů Aruba ClearPass na agenta SYSLOG** – postupujte podle pokynů Aruba pro [konfiguraci ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Tato konfigurace by měla obsahovat následující prvky:
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – TCP 514 (Pokud je to možné jinak, nezapomeňte provést paralelní změnu v procesu démona syslog na serveru pro předávání protokolů)
        - Formát protokolu – CEF
        - Typy protokolů – všechny dostupné nebo všechny příslušné

    1. V části **3. Ověřit připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. [Pomocí těchto kroků](https://aka.ms/sentinel-arubaclearpass-parser) vytvořte alias funkce **ArubaClearPass** Kusto.

Potom pro dotazování na data Aruba ClearPass v Log Analytics zadejte `ArubaClearPass` v horní části okna dotazu.

Několik užitečných ukázek dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Aruba ClearPass ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
