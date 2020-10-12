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
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706828"
---
# <a name="data-residency-for-azure-network-watcher"></a>Zaregistrování dat pro Azure Network Watcher
S výjimkou služby monitorování připojení (Preview) neukládá Azure Network Watcher data zákazníků.


## <a name="connection-monitor-preview-data-residency"></a>Data-monitorování připojení (Preview) zasídlí dat
Služba monitorování připojení (Preview) ukládá zákaznická data. Tato data se automaticky ukládají pomocí Network Watcher v jedné oblasti. Monitorování připojení (Preview) automaticky splňuje požadavky na umístění dat v oblasti, včetně požadavků zadaných v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Zasídlí dat Singapuru

Funkce, která umožňuje ukládat zákaznická data v jedné oblasti v Azure, je aktuálně dostupná jenom v oblasti jihovýchodní Asie (Singapur) Asie a Tichomoří geografické oblasti. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky. Další informace najdete v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Další kroky

* Přečtěte si přehled [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
