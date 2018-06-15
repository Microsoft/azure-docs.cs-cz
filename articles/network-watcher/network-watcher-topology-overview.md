---
title: Úvod do topologii sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka obsahuje přehled možností topologie sledovací proces sítě
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: d5cb5ba431eeae1956a9dbf1d48561c66faef9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23863941"
---
# <a name="introduction-to-topology-in-azure-network-watcher"></a>Úvod do topologii sledovací proces sítě Azure

Topologie vrátí graf síťovým prostředkům ve virtuální síti. Graf znázorňuje propojení mezi prostředky představující koncové síťové připojení.

![Přehled topologie][1]

Na portálu, vrátí topologie objektů prostředků v na základě virtuální sítě. Vztahy, jsou použité v ukázkách linkami mezi prostředky prostředky mimo oblast sledovací proces sítě, i když v prostředku skupiny se nezobrazí. Prostředky, vrátí se v zobrazení portálu, jsou podmnožinou síťové součásti, které vykreslovacích. Pokud chcete zobrazit úplný seznam síťových prostředků, můžete použít [prostředí PowerShell](network-watcher-topology-powershell.md) nebo [REST](network-watcher-topology-rest.md)

> [!NOTE]
> V každé oblasti, kterou chcete spustit topologie na je vyžadován instanci sledovací proces sítě.

Prostředky jsou vrácený připojení mezi nimi jsou modelovat v dva vztahy.

- **Členství ve skupině** – příklad: virtuální síť obsahuje podsítě, který obsahuje síťový adaptér
- **Související** – příklad: síťový adaptér A je přidružený virtuální počítač

### <a name="next-steps"></a>Další kroky

Další informace o použití prostředí PowerShell k načtení zobrazení topologie navštivte stránky [sledovací proces sítě topologie pomocí prostředí PowerShell](network-watcher-topology-powershell.md)

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png
