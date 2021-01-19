---
title: Připojit data Cisco Unified Computing System (UCS) do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor Cisco UCS k vyžádání protokolů Cisco UCS do Azure Sentinel. Zobrazení dat Cisco UCS v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: caa83b9149f39f69d0cbf44a2d6cb01fdaf29721
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567885"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Připojte systém Cisco Unified Computing System (UCS) k Azure Sentinel

> [!IMPORTANT]
> Konektor Cisco UCS je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení Cisco Unified Computing System (UCS) k Azure Sentinel. Datový konektor Cisco UCS vám umožňuje snadno připojit protokoly UCS ke službě Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření. Integrace mezi Cisco UCS a Azure Sentinel využívá protokol syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Vaše řešení Cisco UCS musí být nakonfigurované tak, aby se protokoly exportovali přes syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Předejte protokoly Cisco UCS do agenta syslog.  

Nakonfigurujte Cisco UCS pro přeposílání zpráv syslog do pracovního prostoru Azure Sentinel prostřednictvím agenta syslog.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **Cisco UCS (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na stránce konektor **Cisco UCS** :

    1. Instalace a zprovoznění agenta pro Linux

        - Vyberte virtuální počítač Azure Linux nebo počítač se systémem Linux bez platformy Azure (fyzický nebo virtuální).

    1. Konfigurovat protokoly, které se mají shromažďovat

        - Vyberte zařízení a závažnost v konfiguraci rozšířených nastavení pracovního prostoru.

    1. Konfigurace a připojení Cisco UCS

        - Postupujte podle [těchto pokynů](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) , abyste nakonfigurovali Cisco UCS na předávací syslog. Pro vzdálený server použijte IP adresu počítače se systémem Linux, na který jste nainstalovali agenta pro Linux.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části syslog.

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Cisco UCS k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
