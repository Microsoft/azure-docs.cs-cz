---
title: Zaregistrování dat pro Azure Network Watcher | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak zadáte data pro službu Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 2e6a92a4d08f1603f480a990ad437a90302a8189
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966084"
---
# <a name="data-residency-for-azure-network-watcher"></a>Zaregistrování dat pro Azure Network Watcher
S výjimkou služby monitorování připojení (Preview) neukládá Azure Network Watcher data zákazníků.


## <a name="connection-monitor-preview-data-residency"></a>Data-monitorování připojení (Preview) zasídlí dat
Služba monitorování připojení (Preview) ukládá zákaznická data. Tato data se automaticky ukládají pomocí Network Watcher v jedné oblasti. Monitorování připojení (Preview) automaticky splňuje požadavky na umístění dat v oblasti, včetně požadavků zadaných v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Rezidence dat
Funkce, která umožňuje ukládání zákaznických dat v jedné oblasti v Azure, je v tuto chvíli dostupná jenom v oblasti jihovýchodní Asie (Singapur) oblasti Asie a Tichomoří geografické a Brazílie – jih (stát Svatý Paulo) Brazílie geograficky. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky. Další informace najdete v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Další kroky

* Přečtěte si přehled [Network Watcher](./network-watcher-monitoring-overview.md).