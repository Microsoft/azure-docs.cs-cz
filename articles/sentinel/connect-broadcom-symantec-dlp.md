---
title: Připojit data prevence a ochrany před únikem informací z Symantecu na Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor pro ochranu před únikem informací Symantec k získání protokolů ochrany před únikem informací Symantec do Azure Sentinel. Zobrazte si data o ochraně před únikem informací Symantec v sešitech, vytvářejte výstrahy a vylepšete šetření.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 7f89780f2ed440898f5a28d78ec541c48a958b90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700826"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Připojení ochrany před únikem informací ze služby Broadcom Symantec k Azure Sentinel

> [!IMPORTANT]
> Konektor Broadcom Symantec DLP je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení s ochranou před únikem informací Symantec do Azure Sentinel. Datový konektor Broadcom Symantec pro ochranu před únikem informací vám umožní snadno připojit protokoly ochrany před únikem informací od společnosti Symantec ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Tato funkce poskytuje lepší přehled o informacích vaší organizace, jejich přenosu a vylepšuje možnosti vaší operace zabezpečení. Integrace mezi nástroji Broadcom Symantec DLP a Azure Sentinel využívá CEF syslog, službu pro přeposílání protokolů na bázi Linux a agenta Log Analytics. Používá také uživatelsky sestavený analyzátor protokolů založený na funkci Kusto.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Odeslání protokolů ochrany před únikem informací z Broadcom Symantec do Azure Sentinel

Pokud se chcete přihlásit do Azure Sentinel, nakonfigurujte zařízení s technologií ochrany před únikem informací od společnosti Symantec, aby odesílala zprávy syslog ve formátu CEF do serveru pro předávání protokolu založeného na systému Linux (se systémem rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **Broadcom Symantec DLP (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. V části **1. Konfigurace agenta syslog pro Linux** – tento krok proveďte, pokud ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud ho potřebujete. Podrobnější pokyny a vysvětlení najdete v části [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím službám Azure.

    1. V části **2. Přeposílání protokolů ochrany před únikem informací Symantec do agenta SYSLOG** – podle pokynů v článku pro [konfiguraci ochrany před únikem informací společnosti Symantec](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) Tato konfigurace by měla obsahovat následující prvky:
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – TCP 514 (Pokud je to možné jinak, nezapomeňte provést paralelní změnu v procesu démona syslog na serveru pro předávání protokolů)
        - Formát protokolu – CEF
        - Typy protokolů – všechny dostupné nebo všechny příslušné

    1. V části **3. Ověřit připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. [Pomocí těchto kroků](https://aka.ms/sentinel-symantecdlp-parser) vytvořte alias funkce **SymantecDLP** Kusto.

Potom do pole dotaz zadejte v horní části okna dotazu dotaz na data o ochraně před únikem informací od společnosti Broadcom Symantec v Log Analytics `SymantecDLP` .

Několik užitečných ukázek dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit ochranu před únikem informací od Symantec k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
