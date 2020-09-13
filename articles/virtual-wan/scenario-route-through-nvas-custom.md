---
title: 'Scénář: směrování provozu prostřednictvím síťová virtuální zařízení pomocí vlastního nastavení'
titleSuffix: Azure Virtual WAN
description: Tento scénář vám pomůže směrovat provoz prostřednictvím síťová virtuální zařízení pomocí různých síťové virtuální zařízení pro přenosy vázané na Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f233aedf8b51967264994f5a4081f8f4cd99df01
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400003"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scénář: směrování provozu prostřednictvím síťová virtuální zařízení-Custom (Preview)

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři síťové virtuální zařízení (síťové virtuální zařízení) je cílem směrovat provoz prostřednictvím síťové virtuální zařízení pro komunikaci mezi virtuální sítě a větvemi a použít jiný síťové virtuální zařízení pro přenosy vázané na Internet. Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

V tomto scénáři použijeme konvenci pojmenování:

* "Síť VNet" pro virtuální sítě, ve které uživatelé nasadili síťové virtuální zařízení (virtuální síť 4 na **obrázku 1**), aby zkontrolovaly provoz, který není v Internetu.
* "DMZ VNet" pro virtuální sítě, ve kterých si uživatelé nasadili síťové virtuální zařízení, které se mají použít ke kontrole provozu vázaného na Internet (virtuální síť 5 na **obrázku 1**).
* "Síťové virtuální zařízení paprsky" pro virtuální sítě připojené k virtuální síti síťové virtuální zařízení (virtuální síť 1, virtuální síť 2 a virtuální síť 3 na **obrázku 1**).
* "Centra" pro virtuální sítě WAN spravovaná Microsoftem.

Následující matice připojení shrnuje toky podporované v tomto scénáři:

**Matice připojení**

| Z          | Do:|*SÍŤOVÉ virtuální zařízení paprsky*|*Virtuální síť služby*|*Virtuální síť DMZ*|*Statické větve*|
|---|---|---|---|---|---|
| **SÍŤOVÉ virtuální zařízení paprsky**| &#8594;|      X |            X |   Partnerské vztahy |    Static    |
| **Virtuální síť služby**| &#8594;|    X |            X |      X    |      X       |
| **Virtuální síť DMZ** | &#8594;|       X |            X |      X    |      X       |
| **Větve** | &#8594;|  Static |            X |      X    |      X       |

Každá z buněk v matici připojení popisuje, jestli připojení k virtuální síti WAN (strana "od", záhlaví řádků) obsahuje předponu cíle (stranu "do" toku, záhlaví sloupců v kurzívě) pro konkrétní tok přenosů. "X" znamená, že připojení je nativně zajištěno službou Virtual WAN a "static" znamená, že připojení je zajištěno přes virtuální síť WAN pomocí statických tras. Podívejme se na podrobnosti na různé řádky:

* SÍŤOVÉ virtuální zařízení paprsky:
  * Paprsky dostanou k ostatním paprskům přímý přístup k virtuálním rozbočovačům WAN.
  * Paprsky získají připojení ke větvím přes statickou trasu ukazující na virtuální síť služby. Neměly by se učit konkrétní předpony z větví (jinak by byly konkrétnější a potlačit souhrn).
  * Paprsky budou odesílat internetový provoz do virtuální sítě DMZ prostřednictvím přímého partnerského vztahu virtuálních sítí.
* Zřizování
  * Větve budou dostávat na paprsky prostřednictvím statického směrování směřujícího k virtuální síti služby. Neměly by se učit konkrétní předpony z virtuální sítě, které přepisují souhrnnou statickou trasu.
* Virtuální síť služby bude podobná virtuální síti služby Shared Services, která musí být dosažitelná z každé virtuální sítě a každé větve.
* Virtuální síť DMZ ve skutečnosti nemusí mít připojení přes virtuální síť WAN, protože jediný provoz, který bude podporovat, se bude nacházet prostřednictvím přímých partnerských vztahů virtuální sítě. Pro zjednodušení konfigurace ale použijeme stejný model připojení jako u virtuální sítě DMZ.

Naše matrice připojení proto nabízí tři rozdílné vzory připojení, které se přeloží na tři směrovací tabulky. Přidružení k různým virtuální sítě budou následující:

* SÍŤOVÉ virtuální zařízení paprsky:
  * Přidružená směrovací tabulka: **RT_V2B**
  * Rozšiřování do směrovacích tabulek: **RT_V2B** a **RT_SHARED**
* SÍŤOVÉ virtuální zařízení virtuální sítě (interní a Internet):
  * Přidružená směrovací tabulka: **RT_SHARED**
  * Rozšiřování do směrovacích tabulek: **RT_SHARED**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **RT_SHARED** a **výchozí**

Tyto statické trasy potřebujeme, abychom zajistili, že provoz VNet-to-VNet a propojení mezi virtuálními sítěmi prochází síťové virtuální zařízení ve virtuální síti služby (virtuální síť 4):

| Popis | Tabulka směrování | Statická trasa              |
| ----------- | ----------- | ------------------------- |
| Větve    | RT_V2B      | 10.2.0.0/16 – > vnet4conn  |
| SÍŤOVÉ virtuální zařízení paprsky  | Výchozí     | 10.1.0.0/16 – > vnet4conn  |

Virtuální síť WAN teď ví, ke kterému připojení se mají odesílat pakety, ale připojení potřebuje vědět, co dělat při přijímání těchto paketů: v tomto umístění se používají tabulky směrování připojení.

| Popis | Připojení | Statická trasa            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 – > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 – > 10.4.0.5 |

V tomto okamžiku by mělo probíhat vše.

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **obrázku 1**je jeden rozbočovač, **centrum 1**.

* **Centrum 1** je přímo připojené k síťové virtuální zařízení virtuální sítě **VNet 4** a **VNet 5**.

* U provozu mezi virtuální sítě 1, 2 a 3 a větvemi (VPN/ER/P2S) se očekává, že se bude přecházet přes **virtuální síť 4 síťové virtuální zařízení** 10.4.0.5.

* Každý internetový provoz vázaný z virtuální sítě 1, 2 a 3 se očekává přes **virtuální síť 5 síťové virtuální zařízení** 10.5.0.5.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Obrázek 1":::

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Pokud chcete nastavit směrování přes síťové virtuální zařízení, tady je postup, který je potřeba vzít v úvahu:

1. Aby přenosy vázané na Internet byly přes virtuální síť 5, budete k přímému připojení prostřednictvím partnerského vztahu virtuálních sítí k virtuální síti 5 potřebovat virtuální sítě 1, 2 a 3. Budete také potřebovat UDR sadu v virtuální sítě pro 0.0.0.0/0 a další segment směrování 10.5.0.5. Virtuální síť WAN v současné době neumožňuje síťové virtuální zařízení dalšího směrování ve virtuálním rozbočovači pro 0.0.0.0/0.

1. V Azure Portal přejděte do svého virtuálního centra a vytvořte vlastní směrovací tabulku **RT_Shared** , která se bude naučit trasy prostřednictvím šíření ze všech připojení virtuální sítě a větví. Na **obrázku 2**je znázorněno jako prázdná vlastní směrovací tabulka **RT_Shared**.

   * **Trasy:** Nemusíte přidávat žádné statické trasy.

   * **Přidružení:** Vyberte virtuální sítě 4 a 5, což znamená, že připojení virtuální sítě 4 a 5 se přidruží ke směrovací tabulce **RT_Shared**.

   * **Šíření:** Vzhledem k tomu, že chcete, aby všechny větve a připojení virtuální sítě dynamicky rozšířily své trasy do této směrovací tabulky, vyberte možnost větve a všechny virtuální sítě.

1. Vytvořte vlastní směrovací tabulku **RT_V2B** pro směrování provozu z virtuální sítě 1, 2 a 3 do větví.

   * **Trasy:** Přidat agregovanou položku statického směrování pro větve (VPN/ER/P2S) (10.2.0.0/16 na **obrázku 2**) s dalším segmentem směrování jako připojení virtuální sítě 4. Také je potřeba nakonfigurovat statickou trasu v připojení virtuální sítě 4 pro předpony větví a označit další směrování jako konkrétní IP adresu síťové virtuální zařízení ve virtuální síti 4.

   * **Přidružení:** Vyberte všechny virtuální sítě 1, 2 a 3. To znamená, že připojení k virtuální síti 1, 2 a 3 se přidruží k této tabulce směrování a budou schopna učit se trasy (statické a dynamické prostřednictvím šíření) v této směrovací tabulce.

   * **Šíření:** Připojení šíří trasy do směrovacích tabulek. Když vyberete virtuální sítě 1, 2 a 3, umožníte šíření tras z virtuální sítě 1, 2 a 3 do této směrovací tabulky. Nemusíte šířit trasy z připojení větví do RT_V2B, protože provoz virtuální sítě VNet přechází přes síťové virtuální zařízení ve virtuální síti 4.
  
1. Upravte výchozí směrovací tabulku **DefaultRouteTable**.

   Všechna připojení VPN, ExpressRoute a User VPN jsou přidružena k výchozí směrovací tabulce. Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek.

   * **Trasy:** Přidejte agregovanou položku statické trasy pro virtuální sítě 1, 2 a 3 (10.1.0.0/16 na **obrázku 2**) s dalším segmentem směrování jako připojení virtuální sítě 4. Pro virtuální síť 1, 2 a 3 agregované předpony musíte také nakonfigurovat statickou trasu v připojení virtuální sítě 4 a označit další směrování jako konkrétní IP adresu síťové virtuální zařízení ve virtuální síti 4.

   * **Přidružení:** Ujistěte se, že je vybraná možnost pro větve (VPN/ER/P2S), což zajišťuje, že připojení místních větví k *defaultroutetable*se přidružit.

   * **Šíření z:** Ujistěte se, že je vybraná možnost pro větve (VPN/ER/P2S), což zajistí, že místní připojení šíří trasy do *defaultroutetable*.

**Obrázek 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Obrázek 2" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
