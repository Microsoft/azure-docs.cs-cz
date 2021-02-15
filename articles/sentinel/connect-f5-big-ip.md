---
title: Připojit data o velkém IP adrese F5 do Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit data o velkém IP adresy F5 do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092787"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Připojení zařízení se systémem F5 BIG-IP 

Konektor pro BIG-IP s nástrojem F5 umožňuje snadno propojit všechny protokoly BIG-IP s protokolem Azure Sentinel, zobrazit sešity, vytvářet vlastní výstrahy a vylepšit šetření. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti vaší operace zabezpečení. Integrace mezi službami F5 BIG-IP a Sentinel Azure využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Konfigurace a připojení F5 BIG-IP 

Možnost F5 BIG-IP může integrovat a exportovat protokoly přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **F5 Big-IP** a pak **otevřete stránku konektor**. 
1. Pro připojení ke službě F5 BIG-IP musíte odeslat deklaraci JSON do koncového bodu rozhraní API systému. Pokyny k tomu, jak to provést, najdete v tématu [integrování Big-IP adresy F5 pomocí funkce Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Na stránce konektoru nástroje F5 BIG-IP zkopírujte ID a primární klíč pracovního prostoru a vložte je podle pokynů v části [streamovaná data do Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Po dokončení kroků F5 BIG-IP se na stránce konektoru služby Azure Sentinel zobrazí propojené datové typy.
1. Pokud chcete použít příslušné schéma v Log Analytics pro události F5 BIG-IP, vyhledejte **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL** a **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit F5 BIG-IP ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


