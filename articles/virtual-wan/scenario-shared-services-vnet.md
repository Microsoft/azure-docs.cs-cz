---
title: 'Scénář: Route to Shared Services virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře směrování – nastavení tras pro přístup ke sdílené virtuální síti služby s úlohou, ke které má každá virtuální síť a větev přístup.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: b8cc59b805cd757edce79a14d124ea244b4652a4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267478"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scénář: Route to Shared Services virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem nastavit směrování pro přístup ke sdílené virtuální síti **služby** s úlohami, které chcete použít pro každou virtuální síť a větev (VPN/ER/P2S). Příklady těchto sdílených úloh můžou zahrnovat Virtual Machines se službami, jako jsou řadiče domény nebo sdílené složky, nebo služby Azure exponované interně prostřednictvím [privátních koncových bodů Azure](../private-link/private-endpoint-overview.md).

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

K sumarizaci požadavků tohoto scénáře můžeme použít matrici připojení. V matici každá buňka popisuje, zda se připojení k virtuální síti WAN (strana "od" na straně toku, záhlaví řádků v tabulce) učí předpona cíle (na straně toku, záhlaví sloupců v tabulce kurzíva) pro konkrétní tok přenosů. "X" znamená, že připojení poskytuje virtuální síť WAN:

**Matice připojení**

| Z             | Do:   |*Izolované virtuální sítě*|*Sdílená virtuální síť*|*Větve*|
|---|---|---|---|---|
|**Izolované virtuální sítě**|&#8594;|                |        X        |       X      |
|**Sdílené virtuální sítě**  |&#8594;|       X        |        X        |       X      |
|**Větve**      |&#8594;|       X        |        X        |       X      |

Podobně jako u [scénáře izolované virtuální](scenario-isolate-vnets.md)sítě poskytuje tato matice připojení dva různé vzory řádků, které se převádějí do dvou směrovacích tabulek (virtuální sítě sdílených služeb a větve mají stejné požadavky na připojení). Virtuální síť WAN už má výchozí směrovací tabulku, takže budeme potřebovat další vlastní směrovací tabulku, která v tomto příkladu budeme volat **RT_SHARED** .

Virtuální sítě se bude přidružit k směrovací tabulce **RT_SHARED** . Vzhledem k tomu, že potřebují připojení k větvím a ke sdílené službě virtuální sítě, musí být virtuální síť a větve sdílené služby předávány na **RT_SHARED** (jinak virtuální sítě se neučí pro větve a sdílené předpony virtuální sítě). Vzhledem k tomu, že jsou větve vždy přidružené k výchozí směrovací tabulce a požadavky na připojení jsou pro sdílené služby virtuální sítě stejné, přidružíme virtuální sítě sdílené služby k výchozí tabulce směrování.

V důsledku toho je to konečný návrh:

* Izolované virtuální sítě:
  * Přidružená směrovací tabulka: **RT_SHARED**
  * Rozšiřování do směrovacích tabulek: **výchozí**
* Virtuální sítě sdílených služeb:
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **RT_SHARED** a **výchozí**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **RT_SHARED** a **výchozí**

> [!NOTE]
> Pokud je vaše virtuální síť WAN nasazená v několika oblastech, bude potřeba vytvořit směrovací tabulku **RT_SHARED** v každém centru. trasy od každé virtuální sítě a připojení k síti sdílené služby je potřeba rozšířit do směrovacích tabulek v každém virtuálním centru pomocí popisků šíření.

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Při konfiguraci scénáře Vezměte v úvahu následující postup:

1. Identifikujte virtuální síť **sdílených služeb** .
2. Vytvořte vlastní směrovací tabulku. V tomto příkladu odkazujeme na tabulku směrování jako na **RT_SHARED**. Postup vytvoření směrovací tabulky najdete v tématu [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md). Jako vodítko použijte následující hodnoty:

   * **Řídí**
     * Pro **virtuální sítě *s výjimkou* virtuální sítě sdílených služeb**vyberte virtuální sítě, který chcete izolovat. To znamená, že všechny tyto virtuální sítě (s výjimkou virtuální sítě sdílené služby) budou mít přístup k cíli na základě tras RT_SHARED směrovací tabulky.

   * **Šíření**
      * V případě **větví**šíří trasy do této směrovací tabulky kromě dalších směrovacích tabulek, které jste už vybrali. Z důvodu tohoto kroku se v tabulce směrování v RT_SHARED dozvíte o trasách ze všech připojení větví (VPN/ER/User VPN).
      * Pro **virtuální sítě**vyberte **virtuální síť sdílených služeb**. Z důvodu tohoto kroku se RT_SHARED směrovací tabulce dozvíte o trasách z připojení virtuální sítě Shared Services.

Výsledkem bude konfigurace směrování, která je znázorněna na následujícím obrázku:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Virtuální síť sdílených služeb" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
