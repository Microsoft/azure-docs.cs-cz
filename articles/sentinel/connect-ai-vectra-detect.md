---
title: Připojení Vectra AI k detekci dat do Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit AI Vectra k detekci dat do Azure Sentinel.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038218"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Připojit AI Vectra rozpoznat do Azure Sentinel

> [!IMPORTANT]
> Datový konektor pro rozpoznávání Vectra AI ve službě Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto článku se dozvíte, jak připojit zařízení [Vectra pro rozpoznávání AI](https://www.vectra.ai/product/cognito-detect) ke službě Azure Sentinel. Datový konektor pro rozpoznávání souborů AI Vectra umožňuje snadno převést data z AI Vectra do služby Azure Sentinel, abyste je mohli zobrazit v sešitech, používat je k vytváření vlastních výstrah a začlenit je k vylepšení šetření.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Konfigurace zařízení Vectra pro rozpoznávání AI k odesílání zpráv CEF  

Nakonfigurujte Vectra AI k přeposílání zpráv syslog ve formátu CEF do vašeho pracovního prostoru Log Analytics pomocí služby pro nastavení protokolu syslog, kterou jste nastavili v [kroku 1: nasazení služby pro přeposílání protokolů](connect-cef-agent.md).

1. V rozhraní Vectra přejděte do nastavení > oznámení a vyberte upravit konfiguraci syslogu. Pokud chcete nastavit připojení, postupujte podle níže uvedených pokynů:

    - Přidat nový cíl (název hostitele [serveru pro](connect-cef-agent.md)překládání protokolů)
    - Nastavte port jako **514** .
    - Nastavit protokol jako **UDP**
    - Nastavte formát na **CEF**
    - Nastavit typy protokolů (vybrat všechny typy protokolů k dispozici)
    - Klikněte na **Uložit**.

2. Kliknutím na tlačítko **test** můžete vynutit odeslání některých událostí testu do služby pro přeposílání protokolů.

3. Pokud chcete použít příslušné schéma v Log Analytics pro události rozpoznávání Vectra AI, vyhledejte **CommonSecurityLog**.

4. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit zařízení Vectra AI ke službě Azure Sentinel. Pokud chcete plně využít možnosti integrované s tímto datovým konektorem, klikněte na kartě **Další kroky** na stránce datový konektor. Najdete tu předem připravené Ukázkové dotazy, abyste mohli začít vyhledávat užitečné informace.

Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
