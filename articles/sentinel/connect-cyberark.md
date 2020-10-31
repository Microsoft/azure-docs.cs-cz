---
title: Připojení dat CyberArk EPV k Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor EPV (CyberArk Enterprise Password) k vyžádání svých protokolů do služby Azure Sentinel. Zobrazení CyberArkch dat EPV v sešitech, vytváření výstrah a zlepšení šetření.
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
ms.openlocfilehash: c375595951eb760d5341db424c5572719b97046a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102875"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Připojení EPV (CyberArk Enterprise Password) do Azure Sentinel

> [!IMPORTANT]
> Datový konektor EPV (CyberArk Enterprise Password) ve službě Azure Sentinel je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Konektor syslog CyberArk vám umožňuje snadno propojit všechna vaše protokolová řešení zabezpečení CyberArk s vaší službou Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi CyberArk a službou Azure Sentinel využívá datový konektor CEF ke správné analýze a zobrazení zpráv syslogu CyberArk.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-cyberark-epv"></a>Konfigurace a připojení CyberArk EPV

Protokoly EPV CyberArk jsou odesílány z trezoru na server pro předávání protokolů založený na systému Linux (se spuštěným rsyslog nebo syslog-ng) s nainstalovaným agentem Log Analytics, který exportuje protokoly do Azure Sentinel. Pokud nemáte takový server pro předávání protokolů, přečtěte si [tyto pokyny](connect-cef-agent.md) , abyste si ho mohli stáhnout a spustit.

1. Na portálu Sentinel Azure klikněte na **datové konektory** , vyberte **CyberArk (Enterprise Password Password) (EPV) události (Preview)** a pak **otevřete stránku konektor** .

1. Postupujte podle pokynů CyberArk EPV a nakonfigurujte odesílání dat syslog na server pro předávání protokolů.

1. Ověřte připojení a ověřte příjem dat pomocí [těchto pokynů](connect-cef-verify.md). Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Pokud chcete zadat dotaz na protokoly CyberArk EPV v Log Analytics, zadejte `CommonSecurityLog` v horní části okna dotazu.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak propojit protokoly CyberArk Enterprise pro podnikové hesla do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
