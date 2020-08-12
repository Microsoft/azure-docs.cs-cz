---
title: Zaregistrování dat pro Azure Network Watcher | Microsoft Docs
description: Informace o zaregistrování dat pro službu Network Watcher
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
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117831"
---
# <a name="data-residency-for-azure-network-watcher"></a>Zaregistrování dat pro Azure Network Watcher
Azure Network Watcher neukládá zákaznická data kromě služby monitorování připojení (Preview).


## <a name="connection-monitor-preview-data-residency"></a>Data-monitorování připojení (Preview) zasídlí dat
Služba *monitorování připojení (Preview)* ukládá zákaznická data. Tato data se automaticky ukládají pomocí Network Watcher v jedné oblasti. *Monitorování připojení (Preview)* automaticky splňuje požadavky na umístění dat v oblasti, včetně jak je uvedeno v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Zasídlí dat Singapuru

V Azure je funkce pro ukládání zákaznických dat v jedné oblasti v současnosti dostupná jenom v oblasti jihovýchodní Asie (Singapur) Asie a Tichomoří geografické oblasti. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky. Další informace najdete v tématu [Centrum zabezpečení](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Předchozí replikace** Zákazníci mají možnost ukládat IP adresy koncových uživatelů s jejich Network Watcher instance, aby Network Watcher mohl monitorovat dostupnost, latenci a změny topologie sítě související s IP adresami koncových uživatelů. Tyto IP adresy koncových uživatelů můžou být klasifikované jako zákaznická data. Od 15. července 2020 Network Watcher uložená tato data v jedné oblasti. (Zákaznická data se už nereplikují do HK.) Tato data již nejsou replikována do HK. Tato zákaznická data jsou v klidovém stavu a byla zašifrovaná.
> 
> Pokud by tato zákaznická data měla být přístupná třetí stranou, mohla by třetí strana znát IP adresu, ale neudělí třetí straně přístup k počítači nebo zařízení přidruženému k IP adrese. Network Watcher se domnívá, že tato zákaznická data nezískala žádná třetí strana v HK.

## <a name="next-steps"></a>Další kroky

* Přečtěte si přehled [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
