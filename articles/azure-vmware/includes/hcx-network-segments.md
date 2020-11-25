---
title: Segmenty sítě VMware HCX
description: Pro VMware HCX jsou potřeba čtyři sítě.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999620"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Pro VMware HCX jsou potřeba čtyři sítě:

- **Síť pro správu:** Obvykle se jedná o stejnou síť pro správu, která se používá v clusteru vSphere. Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.

   > [!NOTE]
   > Metoda, kterou doporučujeme vytvořit síť/26. V síti/26 můžete použít až 10 sítí a 60 síťových zařízení (-1 na síť služby). Můžete roztáhnout osm sítí na rozšíření sítě pomocí privátních cloudů řešení Azure VMware.
   >
   
- **vMotion síť:** Obvykle se jedná o stejnou síť, která se používá pro vMotion v clusteru vSphere.  Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.  

   Síť vMotion musí být vystavena v distribuovaném virtuálním přepínači nebo v vSwitch0. Pokud není, upravte prostředí.

   > [!NOTE]
   > Tato síť může být soukromá (nesměrována).

- **Síť pro odesílání:** Chcete vytvořit novou síť pro HCX pro odchozí připojení VMware a roztáhnout ji do svého clusteru vSphere přes skupinu portů. Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.  

   > [!NOTE]
   > Metoda, kterou doporučujeme vytvořit síť/26. V síti/26 můžete použít až 10 sítí a 60 síťových zařízení (-1 na síť služby). Můžete roztáhnout osm sítí na rozšíření sítě pomocí privátních cloudů řešení Azure VMware.
   >
   
- **Síť replikace:** To je volitelné. Chcete vytvořit novou síť pro replikaci VMware HCX a tuto síť do svého clusteru vSphere rozšíříte přes skupinu portů. Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.

   > [!NOTE]
   > Tato konfigurace je možná jenom v případě, že hostitelé místního clusteru používají vyhrazenou replikaci VMkernel Network.  Pokud Váš místní cluster nemá definovanou vyhrazenou VMkernel síť pro replikaci, nemusíte tuto síť vytvářet.
