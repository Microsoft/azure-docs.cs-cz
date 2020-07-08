---
title: 'Scénář: vlastní izolace pro virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování – zabrání vybraným virtuální sítěům v navzájem vzájemnému dosahování
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568399"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scénář: vlastní izolace pro virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. Ve vlastním scénáři izolace pro virtuální sítě je cílem zabránit tomu, aby specifická sada virtuální sítě mohla být schopná oslovit jinou konkrétní sadu virtuální sítě. Virtuální sítě ale vyžaduje, aby se dosáhlo všech větví (VPN/ER/User VPN).

V tomto scénáři jsou připojení VPN, ExpressRoute a User VPN (společně označované jako větve) přidružená ke stejné směrovací tabulce (výchozí směrovací tabulka). Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek. Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Pracovní postup scénáře

Na **obrázku 1**jsou modrá a červená připojení k virtuální síti.

* Modrou připojená virtuální sítě se může vzájemně navázat a získat přístup ke všem větvím připojení (VPN/ER/P2S).
* Červené virtuální sítě se může vzájemně navázat a získat přístup ke všem větvím připojení (VPN/ER/P2S).

Při nastavování směrování Vezměte v úvahu následující kroky.

1. Vytvořte dvě vlastní směrovací tabulky v Azure Portal **RT_BLUE** a **RT_RED**.
2. Pro směrovací tabulku **RT_BLUE**v části:
   * **Asociace**: výběr všech modrých virtuální sítě
   * **Šíření**: pro větve vyberte možnost pro větvení, což znamená, že připojení k síti (VPN/ER/P2S) šíří trasy do této směrovací tabulky.
3. Opakujte stejný postup u **RT_RED** směrovací tabulky pro Red virtuální sítě a větví (VPN/ER/P2S).

Výsledkem bude, že se změní konfigurace směrování, jak je vidět na následujícím obrázku.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Obrázek 1":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
