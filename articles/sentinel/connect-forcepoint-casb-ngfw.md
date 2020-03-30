---
title: Připojení produktů Forcepoint k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit produkty Forcepoint k Azure Sentinelu.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588225"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Připojení produktů Forcepoint k Azure Sentinelu

> [!IMPORTANT]
> Datový konektor produktů Forcepoint v Azure Sentinelu je aktuálně ve verzi Public Preview. Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Tento článek vysvětluje, jak připojit vaše produkty Forcepoint k Azure Sentinelu. 

Datové konektory Forcepoint umožňují připojit protokoly Forcepoint Cloud Access Security Broker a Forcepoint Next Generation Firewall s Azure Sentinelem. Tímto způsobem můžete automaticky exportovat uživatelem definované protokoly do Azure Sentinelu v reálném čase. Konektor poskytuje obohacený přehled o uživatelských aktivitách zaznamenaných produkty Forcepoint. Umožňuje také další korelaci s daty z úloh Azure a dalších informačních kanálů a zlepšuje možnosti monitorování pomocí sešitů v Azure Sentinelu.

> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forward Forcepoint protokoly produktů agentovi Syslog 

Nakonfigurujte produkt Forcepoint tak, aby přesměrovává zprávy Syslogve formátu CEF do pracovního prostoru Azure prostřednictvím agenta Syslog.

1. Nastavte produkt Forcepoint na integraci Azure Sentinelu, jak je popsáno v následujících instalačních průvodcích:
 - [Forcepoint CASB Průvodce integrací](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW Integrace Průvodce](https://frcpnt.com/ngfw-sentinel)

2. Vyhledejte CommonSecurityLog použít příslušné schéma v Log Analytics s DeviceVendor název obsahuje 'Forcepoint'. 

3. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).



## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit produkty Forcepoint k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:

- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).

- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).

- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)