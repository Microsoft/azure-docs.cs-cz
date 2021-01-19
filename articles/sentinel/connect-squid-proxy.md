---
title: Připojit data proxy serveru Squid ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor proxy serveru Squid k získání protokolů proxy Squid do Azure Sentinel. Zobrazení Squidch dat proxy serveru v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.openlocfilehash: b183abf8d42e6f4b1c43db2d87b2650721e0c2a9
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567899"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Připojení proxy serveru Squid ke službě Azure Sentinel

> [!IMPORTANT]
> Konektor proxy serveru Squid je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení proxy Squid ke službě Azure Sentinel. Konektor pro data proxy serveru Squid vám umožňuje snadno připojit protokoly Squid ke službě Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je k vylepšení šetření. Integrace mezi proxy Squid a službou Azure Sentinel využívá protokol syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

## <a name="forward-squid-proxy-logs-to-the-syslog-agent"></a>Přeposílání protokolů proxy serveru do agenta syslog Squid  

Nakonfigurujte proxy server Squid pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **proxy Squid (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na stránce konektor **proxy serveru Squid** :

    1. Instalace a zprovoznění agenta pro Linux

        - Vyberte virtuální počítač Azure Linux nebo počítač se systémem Linux bez platformy Azure (fyzický nebo virtuální).

    1. Konfigurovat protokoly, které se mají shromažďovat

        - V části Rozšířené nastavení pracovního prostoru přidejte vlastní typ protokolu, nahrajte do něj ukázkový soubor a nakonfigurujte ho jako směrovaný.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **vlastní protokoly** v `SquidProxy_CL` tabulce.

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit proxy Squid ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
