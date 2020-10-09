---
title: Připojení produktů Forcepoint ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit produkty Forcepoint k Sentinel Azure.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588225"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Připojení produktů Forcepoint ke službě Azure Sentinel

> [!IMPORTANT]
> Datový konektor pro Forcepoint Products v Azure Sentinel je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Tento článek vysvětluje, jak připojit produkty Forcepoint ke službě Azure Sentinel. 

Konektory Forcepoint data vám umožňují propojit protokoly brány firewall pro Forcepoint Cloud Access a Forcepoint nové generace pomocí služby Azure Sentinel. Tímto způsobem můžete automaticky exportovat uživatelsky definované protokoly do Azure Sentinel v reálném čase. Konektor poskytuje obohacený přehled o aktivitách uživatelů zaznamenaných produkty Forcepoint. Umožňuje taky další korelaci s daty z úloh Azure a dalších informačních kanálů a vylepšuje možnosti monitorování pomocí sešitů v rámci Azure Sentinel.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Dopředné protokoly produktů do agenta syslog Forcepoint 

Nakonfigurujte Forcepoint produkt tak, aby předal zprávy syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Nastavte Forcepoint produkt na integraci služby Azure Sentinel, jak je popsáno v následujících příručkách pro instalaci:
 - [Průvodce integrací CASB pro Forcepoint](https://frcpnt.com/casb-sentinel)
 - [Průvodce integrací NGFW pro Forcepoint](https://frcpnt.com/ngfw-sentinel)

2. Vyhledejte CommonSecurityLog a použijte příslušné schéma v Log Analytics s DeviceVendor názvem Forcepoint. 

3. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).



## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit produkty Forcepoint ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).

- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.