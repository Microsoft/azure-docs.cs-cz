---
title: Připojení Juniper Networks SRX data do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor Juniper SRX k získání protokolu Juniper SRX do služby Azure Sentinel. Prohlédněte si data Juniper SRX v sešitech, vytvářejte výstrahy a vylepšete šetření.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530633"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Připojení brány Juniper SRX firewall k Azure Sentinel

> [!IMPORTANT]
> Konektor Juniper SRX je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení brány firewall Juniper SRX ke službě Azure Sentinel. Datový konektor Juniper SRX vám umožňuje snadno připojit protokoly SRX ke službě Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření. Integrace mezi Juniper SRX a Azure Sentinel využívá syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Vaše řešení Juniper SRX je potřeba nakonfigurovat tak, aby se protokoly exportovali přes syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Dopředné protokoly Juniper SRX do agenta syslog  

Nakonfigurujte Juniper SRX pro přeposílání zpráv syslog do vašeho pracovního prostoru Azure Sentinel prostřednictvím agenta syslog.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **Juniper SRX (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na stránce konektoru **JUNIPER SRX** :

    1. Instalace a zprovoznění agenta pro Linux

        - Vyberte virtuální počítač Azure Linux nebo počítač se systémem Linux bez platformy Azure (fyzický nebo virtuální).

    1. Konfigurovat protokoly, které se mají shromažďovat

        - V konfiguraci agentů pracovního prostoru vyberte zařízení a závažnost.

    1. Konfigurace a připojení Juniper SRX

        - Podle těchto pokynů nakonfigurujte protokol Juniper SRX na předávací protokol syslog.
            - [Protokoly přenosů (protokoly zásad zabezpečení)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Systémové protokoly](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Pro vzdálený server použijte IP adresu počítače se systémem Linux, na který jste nainstalovali agenta pro Linux.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části syslog.

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Juniper SRX ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
