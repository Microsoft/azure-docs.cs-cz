---
title: Připojení dat Cisco Meraki k Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor Cisco Meraki k vyžádání protokolu Cisco Meraki do služby Azure Sentinel. Zobrazení dat Cisco Meraki v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745028"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Připojení Cisco Meraki k Azure Sentinel

> [!IMPORTANT]
> Konektor Cisco Meraki je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení Cisco Meraki (MX/MS/MR) k Azure Sentinel. Konektor Cisco Meraki data Connector vám umožní snadno připojit protokoly Cisco Meraki ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi Cisco Meraki a službou Azure Sentinel používá server syslog s nainstalovaným agentem Log Analytics. Používá také uživatelsky sestavený analyzátor protokolů založený na funkci Kusto.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Vaše řešení Cisco Meraki musí být nakonfigurované tak, aby se protokoly exportovali přes syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Odeslání protokolů Cisco Meraki do Azure Sentinel prostřednictvím agenta syslog  

Nakonfigurujte Cisco Meraki pro přeposílání zpráv syslog do vašeho pracovního prostoru Azure Sentinel prostřednictvím agenta syslog.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **Cisco Meraki (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na stránce konektoru **Cisco Meraki** :

    1. Instalace a zprovoznění agenta pro Linux

        - Vyberte virtuální počítač Azure Linux nebo počítač se systémem Linux bez platformy Azure (fyzický nebo virtuální).

    1. Konfigurovat protokoly, které se mají shromažďovat

        - V **konfiguraci agentů pracovního prostoru** vyberte zařízení a závažnost.

    1. Konfigurace a připojení zařízení s Cisco Meraki

        - Postupujte podle [těchto pokynů](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) a nakonfigurujte zařízení Cisco Meraki na předávací protokol syslog. Pro vzdálený server použijte IP adresu počítače se systémem Linux, na který jste nainstalovali agenta pro Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Ověření připojení a hledání dat

Může trvat až 20 minut, než se protokoly začnou zobrazovat v Azure Sentinel. 

Po navázání úspěšného připojení se data objeví v **protokolech** v části *Správa protokolu* v tabulce *syslog* .

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. [Pomocí těchto kroků](https://aka.ms/sentinel-ciscomeraki-parser) vytvořte alias funkce **CiscoMeraki** Kusto. Pak můžete zadat `CiscoMeraki` do okna dotazu dotaz na data.

Několik užitečných ukázek dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Cisco Meraki ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
