---
title: Segmenty sítě VMware HCX
description: Pro VMware HCX jsou potřeba čtyři sítě.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462286"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Pro VMware HCX jsou potřeba čtyři sítě:

- **Síť pro správu:** Obvykle se jedná o stejnou síť pro správu, kterou používá cluster řešení Azure VMware. Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.

   > [!NOTE]
   > Doporučenou metodou je vytvořit síť a/26, protože můžete použít až 10 sítí pro služby a 60 síťových zařízení (-1 na síť služby). Můžete roztáhnout **osm** sítí na rozšíření sítě pomocí privátních cloudů řešení Azure VMware.
   >
   
- **vMotion síť:** Obvykle se jedná o stejnou síť, která se používá pro vMotion v clusteru řešení Azure VMware.  Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.  

   Síť vMotion musí být vystavena v distribuovaném virtuálním přepínači nebo v vSwitch0. Pokud není, upravte prostředí.

   > [!NOTE]
   > Tato síť může být soukromá (nesměrována).

- **Síť pro odesílání:** Chcete vytvořit novou síť pro HCX pro odchozí připojení VMware a roztáhnout ji do svého clusteru vSphere přes skupinu portů. Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.  

   > [!NOTE]
   > Doporučenou metodou je vytvořit síť a/26, protože můžete použít až 10 sítí pro služby a 60 síťových zařízení (-1 na síť služby). Můžete roztáhnout **osm** sítí na rozšíření sítě pomocí privátních cloudů řešení Azure VMware.
   >
   
- **Síť replikace:** To je volitelné. Chcete vytvořit novou síť pro replikaci VMware HCX a tuto síť do svého clusteru vSphere rozšíříte přes skupinu portů. Minimálně Identifikujte **dvě** IP adresy v tomto segmentu sítě pro VMware HCX. V závislosti na vašem nasazení budete možná potřebovat větší čísla.

   > [!NOTE]
   > Tato konfigurace je možná jenom v případě, že hostitelé místního clusteru používají vyhrazenou replikaci VMkernel Network.  Pokud Váš místní cluster nemá definovanou vyhrazenou VMkernel síť pro replikaci, nemusíte tuto síť vytvářet.
