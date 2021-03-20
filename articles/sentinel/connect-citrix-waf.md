---
title: Připojení dat Citrix WAF ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor Citrix WAF k získání svých protokolů do služby Azure Sentinel. Zobrazení dat Citrix WAF v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102874"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Připojení WAF Citrix k Azure Sentinel

> [!IMPORTANT]
> Datový konektor firewallu webových aplikací Citrix (WAF) ve službě Azure Sentinel je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit zařízení s bránou firewall webových aplikací (WAF) pro Citrix do Azure Sentinel. Datový konektor Citrix WAF umožňuje snadno připojit protokoly Citrix WAF ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Připojením protokolu Citrix WAF CEF do Azure Sentinel můžete využít výhod vyhledávání a korelace, upozorňování a rozšíření analýzy hrozeb pro každý protokol.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Dopředné protokoly Citrix WAF do agenta syslog  

Citrix WAF posílá zprávy syslog ve formátu CEF na server pro předávání protokolů založený na systému Linux (se spuštěným rsyslog nebo syslogem-NG) s nainstalovaným agentem Log Analytics, který předává protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. Pokud nemáte takový server pro předávání protokolů, přečtěte si [tyto pokyny](connect-cef-agent.md) , abyste si ho mohli stáhnout a spustit.

1. Postupujte podle pokynů dodaných od společnosti Citrix a [NAKONFIGURUJTE WAF](https://support.citrix.com/article/CTX234174), [nakonfigurujte protokolování CEF](https://support.citrix.com/article/CTX136146)a [nakonfigurujte odesílání protokolů do služby pro přeposílání protokolů](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Ujistěte se, že jste protokoly odesílali na port TCP 514 na IP adrese počítače pro přeposílání protokolů.

1. Ověřte připojení a ověřte příjem dat pomocí [těchto pokynů](connect-cef-verify.md). Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Pokud chcete zadat dotaz na protokoly Citrix WAF v Log Analytics, zadejte `CommonSecurityLog` v horní části okna dotazu.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Citrix WAF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.