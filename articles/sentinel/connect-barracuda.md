---
title: Připojení dat Barracuda ke službě Azure Sentinel | Microsoft Docs
description: Naučte se, jak pomocí konektoru WAF (Barracuda Web Application firewall) připojit protokoly Barracuda s použitím funkce Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e1462246b95da67591cbdfd1f9ed819220de5764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633057"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Připojení zařízení Barracuda WAF 

Konektor firewallu webových aplikací Barracuda (WAF) umožňuje snadno připojit protokoly Barracuda ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti vaší operace zabezpečení. Azure Sentinel využívá k zajištění bezproblémové integrace nativní integraci mezi **Barracuda** a agentem Log Analytics. 

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurace a připojení Barracuda WAF

Firewall webových aplikací Barracuda může integrovat a exportovat protokoly přímo do Azure Sentinel prostřednictvím agenta Log Analytics.

1. Přejděte na [tok konfigurace Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)a podle pokynů nastavte připojení pomocí těchto parametrů:

    - **ID pracovního prostoru**: na stránce konektoru služby Azure Sentinel Barracuda ZKOPÍRUJTE hodnotu ID vašeho pracovního prostoru.

    - **Primární klíč**: na stránce konektoru služby Azure Sentinel Barracuda Zkopírujte hodnotu vašeho primárního klíče.

1. Pokud chcete použít příslušné schéma v Log Analytics pro události Barracuda, vyhledejte **CommonSecurityLog** a **barracuda_CL**.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení Barracuda ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


