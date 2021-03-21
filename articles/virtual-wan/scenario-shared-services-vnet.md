---
title: 'Scénář: Route to Shared Services virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře směrování – nastavení tras pro přístup ke sdílené virtuální síti služby s úlohou, ke které má každá virtuální síť a větev přístup.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e0d05d2cb960e760809ab35a8f9e4ca04acf250
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442957"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scénář: Route to Shared Services virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem nastavit směrování pro přístup ke sdílené virtuální síti **služby** s úlohami, které chcete použít pro každou virtuální síť a větev (VPN/ER/P2S). Příklady těchto sdílených úloh můžou zahrnovat Virtual Machines se službami, jako jsou řadiče domény nebo sdílené složky, nebo služby Azure exponované interně prostřednictvím [privátních koncových bodů Azure](../private-link/private-endpoint-overview.md).

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

K sumarizaci požadavků tohoto scénáře můžeme použít matrici připojení:

**Matice připojení**

| Z             | Do:   |*Izolované virtuální sítě*|*Sdílená virtuální síť*|*Větve*|
|---|---|---|---|---|
|**Izolované virtuální sítě**| ->|        | Direct | Direct |
|**Sdílené virtuální sítě**  |->| Direct | Direct | Direct |
|**Větve**      |->| Direct | Direct | Direct |

Každá z buněk v předchozí tabulce popisuje, jestli připojení k virtuální síti WAN (strana "od", záhlaví řádků) komunikuje s cílem (strana "do" toku, záhlaví sloupců jsou kurzívou). V tomto scénáři nejsou k dispozici žádné brány firewall ani síťová virtuální zařízení, takže komunikace toků přímo přes virtuální síť WAN (tedy slovo "Direct" v tabulce).

Podobně jako u [scénáře izolované virtuální](scenario-isolate-vnets.md)sítě poskytuje tato matice připojení dva různé vzory řádků, které se převádějí do dvou směrovacích tabulek (sdílené služby virtuální sítě a větve mají stejné požadavky na připojení). Virtuální síť WAN už má výchozí směrovací tabulku, takže budeme potřebovat další vlastní směrovací tabulku, která v tomto příkladu budeme volat **RT_SHARED** .

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
     * Pro **virtuální sítě *s výjimkou* virtuální sítě sdílených služeb** vyberte virtuální sítě, který chcete izolovat. To znamená, že všechny tyto virtuální sítě (s výjimkou virtuální sítě sdílené služby) budou mít přístup k cíli na základě tras RT_SHARED směrovací tabulky.

   * **Šíření**
      * V případě **větví** šíří trasy do této směrovací tabulky kromě dalších směrovacích tabulek, které jste už vybrali. Z důvodu tohoto kroku se v tabulce směrování v RT_SHARED dozvíte o trasách ze všech připojení větví (VPN/ER/User VPN).
      * Pro **virtuální sítě** vyberte **virtuální síť sdílených služeb**. Z důvodu tohoto kroku se RT_SHARED směrovací tabulce dozvíte o trasách z připojení virtuální sítě Shared Services.

Výsledkem bude konfigurace směrování, která je znázorněna na následujícím obrázku:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Diagram virtuální sítě sdílených služeb" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci pomocí šablony ARM najdete v tématu [rychlý Start: směrování na sdílené služby virtuální sítě pomocí šablony ARM](quickstart-route-shared-services-vnet-template.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
