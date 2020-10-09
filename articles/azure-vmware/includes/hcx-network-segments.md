---
title: Segmenty sítě VMware HCX
description: Pro VMware HCX jsou potřeba čtyři sítě.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583270"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Pro VMware HCX jsou potřeba čtyři sítě:

- **Síť pro správu:** Obvykle se jedná o stejnou síť pro správu, která se používá v clusteru vSphere.  Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX (v závislosti na vašem nasazení může být potřeba zadat větší počet čísel).

- **vMotion síť:** Obvykle se jedná o stejnou síť, která se používá pro vMotion v clusteru vSphere.  Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX (v závislosti na vašem nasazení může být potřeba zadat větší počet čísel).  

   Síť vMotion musí být vystavena v distribuovaném virtuálním přepínači nebo v vSwitch0. Pokud není, upravte prostředí.

   > [!NOTE]
   > Pokud tato síť není směrována (soukromá), je to v pořádku.

- **Síť pro odesílání:** Chcete vytvořit novou síť pro HCX pro odchozí připojení VMware a roztáhnout ji do svého clusteru vSphere přes skupinu portů.  Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX (v závislosti na vašem nasazení může být potřeba zadat větší počet čísel).  

   > [!NOTE]
   > Doporučenou metodou je vytvořit síť o velikosti/29, ale bude to mít jakákoli síť.

- **Síť replikace:** Chcete vytvořit novou síť pro replikaci VMware HCX a tuto síť do svého clusteru vSphere rozšíříte přes skupinu portů.  Minimálně Identifikujte dvě IP adresy v tomto segmentu sítě pro VMware HCX (v závislosti na vašem nasazení může být potřeba zadat větší počet čísel).

   > [!NOTE]
   > Doporučenou metodou je vytvořit síť o velikosti/29, ale bude to mít jakákoli síť.