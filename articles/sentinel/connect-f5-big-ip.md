---
title: Připojení dat F5 BIG-IP k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data F5 BIG-IP k Azure Sentinelu.
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
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588276"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Připojte zařízení F5 BIG-IP 

> [!IMPORTANT]
> Datový konektor F5 BIG-IP v Azure Sentinelu je momentálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Konektor F5 BIG-IP umožňuje snadno připojit všechny protokoly F5 BIG-IP pomocí Azure Sentinelu, zobrazit sešity, vytvořit vlastní výstrahy a zlepšit vyšetřování. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti operací zabezpečení. Integrace mezi F5 BIG-IP a Azure Sentinel využívá rozhraní REST API.


> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Konfigurace a připojení F5 BIG-IP 

F5 BIG-IP můžete integrovat a exportovat protokoly přímo do Azure Sentinelu.

1. Na portálu Azure Sentinel klikněte na **Datové konektory** a vyberte **F5 BIG-IP** a pak **otevřít stránku konektoru**. 
1. Chcete-li připojit F5 BIG-IP, musíte zaúčtovat deklaraci JSON do koncového bodu rozhraní API systému. Pokyny k tomu, jak to provést, naleznete [v tématu integrace F5 BIG-IP s Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Na stránce konektoru F5 BIG-IP zkopírujte ID pracovního prostoru a primární klíč a vložte je podle pokynů v části [Streamování dat do Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Po dokončení F5 BIG-IP pokyny, na stránce konektor Azure Sentinel uvidíte připojené datové typy.
1. Chcete-li použít příslušné schéma v Log Analytics pro události F5 BIG-IP, vyhledejte **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**a **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit F5 BIG-IP k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


