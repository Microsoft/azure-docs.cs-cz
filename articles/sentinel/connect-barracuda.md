---
title: Připojení dat Barracuda k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data Barracuda k Azure Sentinelu.
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
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588480"
---
# <a name="connect-your-barracuda-appliance"></a>Připojte zařízení Barracuda 



Konektor Barracuda Web Application Firewall (WAF) umožňuje snadno připojit protokoly Barracuda s Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti operací zabezpečení. Azure Sentinel využívá nativní integraci mezi **Barracuda** a Log Analytics agent a poskytuje bezproblémovou integraci. 


> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurace a připojení Barracuda WAF
Barracuda Web Application Firewall můžete integrovat a exportovat protokoly přímo do Azure Sentinelu prostřednictvím agenta Log Analytics.
1. Přejděte na [tok konfigurace Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)a podle pokynů nastavte připojení pomocí těchto parametrů:
    - **ID pracovního prostoru:** zkopírujte hodnotu ID pracovního prostoru ze stránky konektoru Azure Sentinel Barracuda.
    - **Primární klíč**: zkopírujte hodnotu primárního klíče ze stránky konektoru Azure Sentinel Barracuda.
1. Chcete-li použít příslušné schéma v Log Analytics pro události Barracuda, vyhledejte **CommonSecurityLog** a **barracuda_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení Barracuda k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


