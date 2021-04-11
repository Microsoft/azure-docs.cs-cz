---
title: Segmenty sítě VMware HCX
description: Pro VMware HCX jsou potřeba čtyři sítě.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251355"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Existují různé způsoby, jak nakonfigurovat segmenty sítě VMware HCX v místním prostředí. Následující text popisuje jednoduchou konfiguraci, která podporuje pilotní nebo malý případ použití v produkčním prostředí.  Při navrhování pro stovky nebo tisíce úloh se může tato konfigurace v závislosti na potřebách migrace změnit.  

Při přípravě na nasazení VMware HCX pro podporu pilotního nebo malého produkčního případu určete následující:

- **Síť pro správu:** Při nasazování VMware HCX v místním prostředí budete muset pro VMware HCX identifikovat síť pro správu.  Obvykle je to stejná síť pro správu, kterou používá váš místní cluster VMware.  Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. Je možné, že budete potřebovat větší počet čísel v závislosti na rozsahu nasazení mimo pilotní nebo malý případ použití.

  > [!NOTE]
  > Příprava pro velká prostředí místo používání sítě pro správu, která se používá pro místní cluster VMware, vytvořte novou síť/26 a tuto síť přihlaste jako skupinu portů pro místní cluster VMware.  Pak můžete vytvořit až 10 sítí pro služby a 60 síťových zařízení (-1 na síť služby). Můžete roztáhnout **osm** sítí na rozšíření sítě pomocí privátních cloudů řešení Azure VMware.
  >

- **Síť pro odesílání:** Při nasazování služby VMware HCX v místním prostředí budete muset určit síť pro odesílání pro VMware HCX. Použijte stejnou síť, kterou budete používat pro síť pro správu. 

- **vMotion síť:** Při nasazování VMware HCX v místním prostředí budete muset pro VMware HCX identifikovat síť vMotion.  Obvykle se jedná o stejnou síť, jakou používá vMotion místní cluster VMware.  Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. Je možné, že budete potřebovat větší počet čísel v závislosti na rozsahu nasazení mimo pilotní nebo malý případ použití.

   Síť vMotion musí být vystavena v distribuovaném virtuálním přepínači nebo v vSwitch0. Pokud není, upravte prostředí tak, aby odpovídalo.

   > [!NOTE]
   > Mnoho prostředí VMware používá nesměrované síťové segmenty pro vMotion, což nepřináší žádné problémy.
  
- **Síť replikace:** Při nasazování VMware HCX v místním prostředí budete muset definovat síť replikace. Použijte stejnou síť, jakou používáte pro sítě pro správu a odesílání.  Pokud hostitelé místního clusteru používají vyhrazenou VMkernel síť pro replikaci, vyhradí v tomto segmentu sítě **dvě** IP adresy a pro replikaci sítě použijte replikační VMkernel síť.
