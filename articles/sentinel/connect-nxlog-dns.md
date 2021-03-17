---
title: Připojit data protokolu DNS systému Windows NXLog do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor NXLog DNS log k vyžádání událostí DNS systému Windows do služby Azure Sentinel. Zobrazení dat DNS systému Windows v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745025"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Připojení protokolů DNS systému Windows NXLog do Azure Sentinel

> [!IMPORTANT]
> Konektor protokolu DNS NXLog je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Konektor [NXLOG DNS](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) vám umožní snadno exportovat všechny události serveru DNS Windows do služby Azure Sentinel v reálném čase, což vám poskytne přehled o aktivitě názvového serveru domény v celé organizaci. Využívá vysoce výkonné [trasování událostí pro Windows (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) pro shromažďování událostí auditování a analýzy serveru DNS v reálném čase a podporuje rozšíření událostí s vlastními poli. Integrace mezi NXLog protokoly DNS a Azure Sentinel se usnadňuje prostřednictvím REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Konfigurace a připojení protokolů DNS NXLog

NXLog je možné nakonfigurovat tak, aby odesílala události ve formátu JSON přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte konektor **NXLog DNS log** .

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle podrobných pokynů v tématu Integrace *uživatelské příručky NXLog* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) ke konfiguraci předávání prostřednictvím REST API.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v části **protokoly** v části  **vlastní protokoly** v tabulce *DNS_Logs_CL* .

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat NXLog k ingestování protokolů DNS Windows do služby Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
