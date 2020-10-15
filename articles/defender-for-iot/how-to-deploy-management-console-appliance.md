---
title: Nasazení konzoly pro správu v Azure Defenderu pro IoT
description: Přečtěte si, jak nasadit konzolu pro správu v Azure Defenderu pro IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094319"
---
# <a name="deploy-the-management-console"></a>Nasazení konzoly pro správu
Tento článek popisuje, jak nasadit místní konzolu pro správu Azure Defenderu pro IoT.

## <a name="the-on-premises-management-console"></a>Místní Konzola pro správu

Místní Konzola pro správu nabízí ucelený přehled o všech síťových prostředcích a poskytuje přehled o klíčových sítích IoT a rizikových indikátorech a výstrahách v reálném čase napříč všemi vašimi zařízeními. Integrovaná integrace s pracovními postupy SOC a spouštěním knih umožňuje snadnou prioritu aktivit zmírňování a korelace mezi různými pracovišti hrozeb.

- Holistický – zmenšete složitost s jednou sjednocenou platformou pro správu prostředků, riziku a správou ohrožení zabezpečení a také monitorováním hrozeb pomocí reakce na incidenty.

- Agregace a korelace – zobrazí, agreguje a analyzuje data a výstrahy shromážděné ze všech lokalit.

- Řízení všech senzorů – nakonfigurujte a monitorujte všechny snímače z jednoho místa.

   ![Azure Defender pro zobrazení správy webů v IoT](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Nasazení místního zařízení konzoly pro správu

Tento proces vyžaduje získání vlastního hardwaru a instalaci softwaru. Řešení se spouští na certifikovaných zařízeních. Pokud si koupíte certifikované zařízení, přečtěte si [Průvodce specifikacemi hardwaru pro Azure Defender pro IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .

Podrobnosti o stažení bitové kopie ISO a instalaci softwaru snímače najdete v [Průvodci instalací Azure Defenderu pro IoT](https://aka.ms/AzureDefenderforIoTInstallSensorISO) .

**Nasazení místní konzoly pro správu:**

1. Přejděte na Microsoft Azure Defender pro IoT.

2. Vyberte **místní konzolu pro správu**.

   ![Zobrazení místní konzoly pro správu Azure Defenderu pro IoT](media/updates/image15.png)

3. V nabídce **vybrat verzi** vyberte verze 3,1.

4. Vyberte **Stáhnout** a uložte soubor.

5. Po instalaci softwaru provede úlohy nastavení sítě. Podrobnosti najdete v [příručce k instalaci sítě v Azure Defenderu pro IoT](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o možnostech konfigurace, přejděte k příručce Průvodce konfigurací modulu.
> [!div class="nextstepaction"]
> [Průvodce konfigurací modulů](./how-to-agent-configuration.md)
