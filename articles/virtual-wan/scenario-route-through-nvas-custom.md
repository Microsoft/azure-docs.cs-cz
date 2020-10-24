---
title: Směrování provozu prostřednictvím síťová virtuální zařízení pomocí vlastního nastavení
titleSuffix: Azure Virtual WAN
description: Tento scénář vám pomůže směrovat provoz prostřednictvím síťová virtuální zařízení pomocí různých síťové virtuální zařízení pro přenosy vázané na Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 122e76e4bde96823ff18207bc24df4a8e91afb1c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517964"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Scénář: směrování provozu prostřednictvím síťová virtuální zařízení pomocí vlastního nastavení

Při práci s směrováním virtuálního rozbočovače Azure Virtual WAN máte k dispozici řadu možností. Tento článek se zaměřuje na směrování provozu přes síťové virtuální zařízení (síťové virtuální zařízení) pro komunikaci mezi virtuálními sítěmi a větvemi a používání různých síťové virtuální zařízení pro přenosy vázané na Internet. Další informace najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a>Návrh

* **Paprsky** pro virtuální sítě připojené k virtuálnímu rozbočovači. (Například virtuální síť 1, virtuální síť 2 a virtuální síť 3 v diagramu dále v tomto článku.)
* Virtuální **síť** pro virtuální sítě, kde uživatelé nasadili síťové virtuální zařízení pro kontrolu internetového provozu a případně i se společnými službami, ke kterým se přistupovali pomocí paprsků. (Například virtuální síť 4 v diagramu dále v tomto článku.) 
* **Hraniční síť** pro virtuální sítě, ve které uživatelé nasadili síťové virtuální zařízení, která se má použít ke kontrole provozu vázaného na Internet. (Například virtuální síť 5 v diagramu dále v tomto článku.)
* **Centra** pro virtuální rozbočovače WAN spravovaná Microsoftem

Následující tabulka shrnuje připojení podporovaná v tomto scénáři:

| Z          | Záměr|Paprsky|Virtuální síť služby|Větve|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Paprsky**| ->| přímých |přímých | přes virtuální síť služby |přes hraniční síť VNet |
| **Virtuální síť služby**| ->| přímých |neuvedeno| přímých | |
| **Větve** | ->| přes virtuální síť služby |přímých| přímých |  |

Každá z buněk v matici připojení popisuje, jestli jsou toky připojení přímo přes virtuální síť WAN nebo přes jednu z virtuálních sítí s síťové virtuální zařízení. 

Všimněte si těchto podrobností:
* Paprsky
  * Paprsky dostanou k ostatním paprskům přímý přístup k virtuálním rozbočovačům WAN.
  * Paprsky získají připojení ke větvím přes statickou trasu ukazující na virtuální síť služby. Nevztahují se na konkrétní předpony z větví, protože jsou konkrétnější a přepisují souhrn.
  * Paprsky odesílají internetový provoz do hraniční sítě VNet prostřednictvím přímého partnerského vztahu virtuální sítě.
* Větve budou dostávat na paprsky prostřednictvím statického směrování směřujícího k virtuální síti služby. Nezpůsobují konkrétní předpony z virtuálních sítí, které přepíší souhrnnou statickou trasu.
* Virtuální síť služby bude podobná virtuální síti služby Shared Services, která musí být dosažitelná z každé virtuální sítě a každé větve.
* Síť VNet hraniční sítě nemusí mít připojení přes virtuální síť WAN, protože jediný provoz, který bude podporovat, prochází přes přímé partnerské vztahy virtuálních sítí. Pro zjednodušení konfigurace ale použijte stejný model připojení jako virtuální síť hraniční sítě.

Existují tři rozdílné vzory připojení, které se přeloží na tři směrovací tabulky. Přidružení k různým virtuálním sítím jsou:

* Paprsky
  * Přidružená směrovací tabulka: **RT_V2B**
  * Rozšiřování do směrovacích tabulek: **RT_V2B** a **RT_SHARED**
* SÍŤOVÉ virtuální zařízení virtuální sítě (virtuální síť služby a virtuální síť DMZ):
  * Přidružená směrovací tabulka: **RT_SHARED**
  * Rozšiřování do směrovacích tabulek: **RT_SHARED**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **RT_SHARED** a **výchozí**

Tyto statické trasy zajišťují, aby přenosy dat do a z virtuální sítě a větve procházely přes síťové virtuální zařízení ve virtuální síti služby (virtuální síť 4):

| Popis | Tabulka směrování | Statická trasa              |
| ----------- | ----------- | ------------------------- |
| Větve    | RT_V2B      | 10.2.0.0/16 – > vnet4conn  |
| SÍŤOVÉ virtuální zařízení paprsky  | Výchozí     | 10.1.0.0/16 – > vnet4conn  |

Nyní můžete virtuální síť WAN použít k výběru správného připojení pro odesílání paketů. K výběru správné akce, která se má provést při přijímání těchto paketů, je také nutné použít virtuální síť WAN. Pro tento postup použijete tabulky směrování připojení následujícím způsobem:

| Popis | Připojení | Statická trasa            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 – > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 – > 10.4.0.5 |

Další informace najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="architecture"></a>Architektura

Zde je diagram architektury popsané dříve v článku.

Existuje jedno centrum s názvem **hub 1**.

* **Centrum 1** je přímo připojené k síťové virtuální zařízení virtuální sítě **VNet 4** a **VNet 5**.

* Provoz mezi virtuální sítě 1, 2 a 3 a větvemi by měl jít přes **virtuální síť 4 síťové virtuální zařízení** 10.4.0.5.

* Každý internetový provoz vázaný z virtuální sítě 1, 2 a 3 se očekává přes **virtuální síť 5 síťové virtuální zařízení** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Diagram síťové architektury":::

## <a name="workflow"></a>Pracovní postup

Pokud chcete nastavit směrování přes síťové virtuální zařízení, tady je postup, který je potřeba vzít v úvahu:

1. Pro přenos internetového provozu přes virtuální síť 5 potřebujete virtuální sítě 1, 2 a 3, abyste se mohli přímo připojit prostřednictvím partnerského vztahu virtuální sítě k virtuální síti 5. Budete také potřebovat uživatelsky definovanou trasu nastavenou ve virtuálních sítích pro 0.0.0.0/0 a další segment směrování 10.5.0.5. Virtuální síť WAN v současné době neumožňuje síťové virtuální zařízení dalšího segmentu směrování ve virtuálním rozbočovači pro 0.0.0.0/0.

1. V Azure Portal přejdete do svého virtuálního centra a vytvoříte vlastní směrovací tabulku s názvem **RT_Shared**. V této tabulce se dozvíte o trasách prostřednictvím šíření ze všech virtuálních sítí a připojení větví. Tuto prázdnou tabulku můžete zobrazit v následujícím diagramu.

   * **Trasy:** Nemusíte přidávat žádné statické trasy.

   * **Přidružení:** Vyberte virtuální sítě 4 a 5, což znamená, že připojení těchto virtuálních sítí se přidruží k směrovací tabulce **RT_Shared**.

   * **Šíření:** Vzhledem k tomu, že chcete, aby všechny větve a virtuální síťová připojení dynamicky rozšířily své trasy do této směrovací tabulky, vyberte možnost větve a všechny virtuální sítě.

1. Vytvořte vlastní směrovací tabulku s názvem **RT_V2B** pro směrování provozu z virtuální sítě 1, 2 a 3 do větví.

   * **Trasy:** Přidejte agregovanou položku statického směrování pro větve s dalším segmentem směrování jako připojení virtuální sítě 4. Nakonfigurujete statickou trasu v připojení virtuální sítě 4 pro předpony větví a označíte další směrování jako konkrétní IP adresu síťové virtuální zařízení ve virtuální síti 4.

   * **Přidružení:** Vyberte všechny virtuální sítě 1, 2 a 3. To znamená, že připojení k virtuální síti 1, 2 a 3 se přidruží k této tabulce směrování a budou schopna učit se trasy (statické a dynamické prostřednictvím šíření) v této směrovací tabulce.

   * **Šíření:** Připojení šíří trasy do směrovacích tabulek. Výběr virtuální sítě 1, 2 a 3 povolí šíření tras z virtuální sítě 1, 2 a 3 do této směrovací tabulky. Nemusíte šířit trasy z připojení větví do **RT_V2B**, protože provoz virtuální sítě větve přechází přes síťové virtuální zařízení ve virtuální síti 4.
  
1. Upravte výchozí směrovací tabulku **DefaultRouteTable**.

   Všechna připojení VPN, Azure ExpressRoute a User VPN jsou přidružená k výchozí směrovací tabulce. Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek.

   * **Trasy:** Přidejte agregovanou položku statické trasy pro virtuální sítě 1, 2 a 3 s dalším segmentem směrování jako připojení virtuální sítě 4. Nakonfigurujte statickou trasu v připojení virtuální sítě 4 pro virtuální síť 1, 2 a 3 agregované předpony a určete další směrování, které bude mít konkrétní IP adresu síťové virtuální zařízení ve virtuální síti 4.

   * **Přidružení:** Ujistěte se, že je vybrána možnost pro větve (VPN/ER/P2S), což zajistí, že připojení k místní větvi jsou přidružena k výchozí směrovací tabulce.

   * **Šíření z:** Ujistěte se, že je vybraná možnost pro větve (VPN/ER/P2S), aby se zajistilo, že místní připojení šíří trasy do výchozí směrovací tabulky.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Diagram síťové architektury" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
