---
title: 'Scénář: vlastní izolace pro virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování – zabrání vybraným virtuální sítěům v navzájem vzájemnému dosahování
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0a3665f1719c7a5f8ed9bd6acf518b642e06320d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400054"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scénář: vlastní izolace pro virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. Ve vlastním scénáři izolace pro virtuální sítě je cílem zabránit tomu, aby specifická sada virtuální sítě mohla být schopná oslovit jinou konkrétní sadu virtuální sítě. Virtuální sítě ale vyžaduje, aby se dosáhlo všech větví (VPN/ER/User VPN). Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

Aby bylo možné zjistit, kolik směrovacích tabulek bude potřeba, můžete vytvořit matici připojení. V tomto scénáři bude vypadat podobně jako v následujícím příkladu, kde každá buňka představuje, zda zdroj (řádek) může komunikovat s cílem (sloupec):

| Z | Do:| *Modrý virtuální sítě* | *Červené virtuální sítě* | *Větve*|
|---|---|---|---|---|
| **Modrý virtuální sítě** |   &#8594;|      X        |               |       X      |
| **Červené virtuální sítě**  |   &#8594;|              |       X       |       X      |
| **Větve**   |   &#8594;|     X        |       X       |       X      |

Každá z buněk v předchozí tabulce popisuje, zda se připojení k virtuální síti WAN (strana "od" na straně toku, záhlaví řádků v tabulce) učí předpona cíle (strana "do" toku, záhlaví sloupců v tabulce kurzívou) pro konkrétní tok přenosů, kde "X" znamená, že připojení je zajištěno službou Virtual WAN.

Počet různých vzorů řádků bude počet směrovacích tabulek, které v tomto scénáři budete potřebovat. V tomto případě tři směrovací tabulky tras, které budeme volat **RT_BLUE** a **RT_RED** pro virtuální sítě a **výchozí** pro větve. Nezapomeňte, že větve vždy musí být přidruženy k výchozí směrovací tabulce.

Větve budou muset naučit předpony od červené i modré virtuální sítě, takže všechny virtuální sítě se musí rozšířit na výchozí hodnotu (kromě **RT_BLUE** nebo **RT_RED**). Modré a červené virtuální sítě se budou muset naučit předpony větví, takže větve se rozšíří do obou směrovacích tabulek **RT_BLUE** i **RT_RED** . V důsledku toho je to konečný návrh:

* Modré virtuální sítě:
  * Přidružená směrovací tabulka: **RT_BLUE**
  * Rozšiřování do směrovacích tabulek: **RT_BLUE** a **výchozí**
* Červené virtuální sítě:
  * Přidružená směrovací tabulka: **RT_RED**
  * Rozšiřování do směrovacích tabulek: **RT_RED** a **výchozí**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **RT_BLUE**, **RT_RED** a **výchozí**

> [!NOTE]
> Vzhledem k tomu, že jsou všechny větve nutné přidružit k výchozí směrovací tabulce a rozšířit na stejnou sadu směrovacích tabulek, budou mít všechny větve stejný profil připojení. Jinými slovy: koncept červeného/modrého pro virtuální sítě nelze použít na větve.

> [!NOTE]
> Pokud je vaše virtuální síť WAN nasazená v několika oblastech, bude potřeba vytvořit směrovací tabulky **RT_BLUE** a **RT_RED** v každém centru. trasy od každého připojení virtuální sítě je potřeba rozšířit do směrovacích tabulek v každém virtuálním centru pomocí popisků šíření.

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Pracovní postup

Na **obrázku 1**jsou modrá a červená připojení k virtuální síti.

* Modrou připojená virtuální sítě se může vzájemně navázat a získat přístup ke všem větvím připojení (VPN/ER/P2S).
* Červené virtuální sítě se může vzájemně navázat a získat přístup ke všem větvím připojení (VPN/ER/P2S).

Při nastavování směrování Vezměte v úvahu následující kroky.

1. Vytvořte dvě vlastní směrovací tabulky v Azure Portal **RT_BLUE** a **RT_RED**.
2. Pro **RT_BLUE**směrovací tabulky pro následující nastavení:
   * **Asociace**: vyberte všechny modré virtuální sítě.
   * **Šíření**: pro větve vyberte možnost pro větvení, což znamená, že připojení k síti (VPN/ER/P2S) šíří trasy do této směrovací tabulky.
3. Opakujte stejný postup u **RT_RED** směrovací tabulky pro Red virtuální sítě a větví (VPN/ER/P2S).

Výsledkem bude, že se změní konfigurace směrování, jak je vidět na následujícím obrázku.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Obrázek 1":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
