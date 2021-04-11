---
title: Segmenty sítě VMware HCX
description: Pro VMware HCX jsou potřeba čtyři sítě.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622009"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Existují různé způsoby, jak nakonfigurovat segmenty sítě VMware HCX v místním prostředí. Následující text popisuje jednoduchou konfiguraci, která podporuje pilotní nebo malý případ použití v produkčním prostředí.  Při navrhování pro stovky nebo tisíce úloh se může tato konfigurace v závislosti na potřebách migrace změnit.  

Při přípravě na nasazení VMware HCX pro podporu pilotního nebo malého produkčního případu určete následující:

- **Síť pro správu:** Při nasazování VMware HCX v místním prostředí budete muset definovat síť pro správu.  Obvykle je to stejná síť pro správu, kterou používá váš místní cluster VMware.  Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. Je možné, že budete potřebovat větší počet čísel v závislosti na rozsahu nasazení mimo pilotní nebo malý případ použití.

> [!NOTE]
   > Doporučenou metodou je vytvořit síť a/26, protože můžete použít až 10 sítí pro služby a 60 síťových zařízení (-1 na síť služby). Můžete roztáhnout **osm** sítí na rozšíření sítě pomocí privátních cloudů řešení Azure VMware.
   >
   
- **vMotion síť:** Při nasazování VMware HCX v místním prostředí budete muset definovat síť vMotion.  Obvykle se jedná o stejnou síť, jakou používá vMotion místní cluster VMware.  Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. Je možné, že budete potřebovat větší počet čísel v závislosti na rozsahu nasazení mimo pilotní nebo malý případ použití.

   Síť vMotion musí být vystavena v distribuovaném virtuálním přepínači nebo v vSwitch0. Pokud není, upravte prostředí tak, aby odpovídalo.

   > [!NOTE]
   > Mnoho prostředí VMware používá nesměrované síťové segmenty pro vMotion, což nepřináší žádné problémy.

- **Síť pro odesílání:** Při nasazování služby VMware HCX v místním prostředí budete muset definovat síť pro odesílání. Použijte síť pro správu definovanou jako síť pro odesílání.
   
- **Síť replikace:** Při nasazování VMware HCX v místním prostředí budete muset definovat síť replikace. Použijte síť pro správu definovanou jako síť replikace.  Pokud hostitelé místního clusteru používají vyhrazenou VMkernel síť pro replikaci, vyhradí v tomto segmentu sítě **dvě** IP adresy a pro replikaci sítě použijte replikační VMkernel síť.
