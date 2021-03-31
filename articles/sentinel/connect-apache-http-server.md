---
title: Připojení serveru Apache HTTP k Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor serveru Apache HTTP k vyžádání protokolů Apache do Azure Sentinel. Zobrazení dat Apache v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096850"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Připojení serveru Apache HTTP k Azure Sentinel

> [!IMPORTANT]
> Konektor serveru Apache HTTP je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit server Apache HTTP k Azure Sentinel. Konektor serveru Apache HTTP umožňuje snadno ingestovat protokoly serveru Apache HTTP do Azure Sentinel, takže můžete zobrazit data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je k vylepšení šetření. Integrace mezi serverem Apache HTTP a službou Azure Sentinel využívá pro Log Analyticsho agenta místní zpracování souborů.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Sentinel Azure musíte mít oprávnění k zápisu.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Konfigurace a integrace protokolů serveru Apache HTTP prostřednictvím agenta Log Analytics

Nakonfigurujte server Apache HTTP tak, aby odesílal soubory protokolu do pracovního prostoru Azure prostřednictvím agenta Log Analytics.
Nakonfigurujte agenta Log Analytics pro čtení souborů protokolu serveru Apache HTTP.

1. Postupujte podle pokynů v tématu https://httpd.apache.org/docs/2.4/logs.html Nastavení umístění souborů protokolu na serveru Apache HTTP.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory** a pak vyberte **Apache HTTP Server (Preview)**.

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce serveru protokolu HTTP Apache.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit server Apache HTTP k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
