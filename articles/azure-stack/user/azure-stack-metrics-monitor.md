---
title: Používat data monitorování z Azure Stack | Dokumentace Microsoftu
description: Další informace o možnosti, jak využívat data monitorování z Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 949dee06089ed381010a03ac2fae73aaf4a00c54
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889858"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Jak používat data monitorování z Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete najít data monitorování na jednom místě s kanálem monitorování Azure, stejně, jako je Azure Monitor v globální Azure. Ale ne všechny data monitorování, které jsou součástí globální Azure je k dispozici ve službě Azure Stack. V tomto článku najdete přehled různých způsobů, jak může prostřednictvím kódu programu ingestovat data monitorování ze služby.
 
## <a name="options-for-data-consumption"></a>Možnosti využití dat

| Typ dat | Kategorie | Podporované služby | Metody přístupu |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Metriky Azure Monitor úrovni platformy | Metriky | [Podporované metriky ve službě Azure Monitor ve službě Azure Stack](azure-stack-metrics-supported.md) | REST API |
| Vypočítat metriky hostovaného operačního systému (například počet výk.) | Metriky | Windows a virtuální počítače s Linuxem | Storage table nebo blob:<br>Windows nebo Linux Azure Diagnostics <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Metriky úložiště | Metriky | Azure Storage | Tabulka úložiště:<br>Storage Analytics |
| Protokol aktivit | Události | Všechny služby Azure | ROZHRANÍ REST API:<br>Azure Monitor událostí rozhraní API |
| COMPUTE protokoly operačního systému hosta (například služby IIS, trasování událostí pro Windows, syslogy) | Události | Windows a virtuální počítače s Linuxem | Storage table nebo blob:<br>Windows nebo Linux Azure Diagnostics <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Protokoly úložiště | Události | Azure Storage | Tabulka úložiště:<br>Storage Analytics<br>`Vita: how about hybrid OMS/AppInsights, shall we mention?` |

## <a name="next-steps"></a>Další postup

Další informace o [monitorování Azure ve službě Azure Stack](azure-stack-metrics-azure-data.md).