---
title: 'Scénář: Route to Shared Services virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře směrování – nastavení tras pro přístup ke sdílené virtuální síti služby s úlohou, ke které má každá virtuální síť a větev přístup.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568468"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scénář: Route to Shared Services virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem nastavit směrování pro přístup k virtuální síti **sdílené služby** s úlohou, kterou chcete použít pro každou virtuální síť a větev (VPN/ER/P2S). Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

1. Identifikujte virtuální síť **sdílených služeb** .
2. Vytvořte vlastní směrovací tabulku. V tomto příkladu odkazujeme na tuto směrovací tabulku jako na **RT_SHARED**. Postup vytvoření směrovací tabulky najdete v tématu [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md). Jako vodítko použijte následující hodnoty:

   * **Řídí**
     * Pro **virtuální sítě *s výjimkou* virtuální sítě sdílených služeb**vyberte virtuální sítě, který chcete izolovat. To znamená, že všechny tyto virtuální sítě (s výjimkou virtuální sítě sdílené služby) budou mít přístup k cíli na základě tras RT_SHARED směrovací tabulky.

   * **Šíření**
      * V případě **větví**šíří trasy do této směrovací tabulky kromě dalších směrovacích tabulek, které jste už vybrali. V důsledku tohoto kroku se RT_SHARED směrovací tabulce dozvíte o trasách ze všech připojení větví (VPN/ER/User VPN).
      * Pro **virtuální sítě**vyberte **virtuální síť sdílených služeb**. V důsledku tohoto kroku se RT_SHARED směrovací tabulce seznámí s trasami z připojení virtuální sítě Shared Services.

     Výsledkem bude, že se změní konfigurace směrování, jak je vidět na následujícím obrázku.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Virtuální síť sdílených služeb":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
