---
title: 'Scénář: směrování provozu přes síťové virtuální zařízení (síťové virtuální zařízení)'
titleSuffix: Azure Virtual WAN
description: Směrování provozu přes síťové virtuální zařízení
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519792"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scénář: Směrování provozu přes síťové virtuální zařízení

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři síťové virtuální zařízení je cílem směrovat provoz prostřednictvím síťové virtuální zařízení (síťového virtuálního zařízení) pro větev do virtuální sítě a virtuální sítě do větve. Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

> [!NOTE]
> Pokud už máte nastavení s trasami, které se před novými funkcemi, [jak nakonfigurovat směrování virtuálního centra, co](how-to-virtual-hub-routing.md) je k dispozici, použijte prosím postup v těchto verzích těchto článků:
>* [Azure Portal článek](virtual-wan-route-table-nva-portal.md)
>* [Článek o PowerShellu](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Návrh

V tomto scénáři použijeme konvenci pojmenování:

* "Síťové virtuální zařízení virtuální sítě" pro virtuální sítě, ve kterých uživatelé nasadili síťové virtuální zařízení a připojili jiné virtuální sítě jako paprsky (virtuální síť 2 a virtuální síť 4 na **obrázku 2** dále v tomto článku).
* "Síťové virtuální zařízení paprsky" pro virtuální sítě připojené k virtuální síti síťové virtuální zařízení (virtuální síť 5, virtuální síť 6, virtuální síť 7 a virtuální síť 8 na **obrázku 2** v tomto článku).
* "Non-síťové virtuální zařízení virtuální sítě" pro virtuální sítě připojené k virtuální síti WAN, které nemají partnerský vztah síťové virtuální zařízení nebo jiné virtuální sítěy s nimi (virtuální síť 1 a virtuální síť 3 na **obrázku 2** dále v tomto článku).
* "Centra" pro virtuální sítě WAN spravovaná Microsoftem, ke kterým jsou připojené síťové virtuální zařízení virtuální sítě. SÍŤOVÉ virtuální zařízení paprskové virtuální sítě nemusí být připojené k virtuálním rozbočovačům WAN, jenom pro síťové virtuální zařízení virtuální sítě.

Následující matice připojení shrnuje toky podporované v tomto scénáři:

**Matice připojení**

| Z             | Do:|   *SÍŤOVÉ virtuální zařízení paprsky*|*SÍŤOVÉ virtuální zařízení virtuální sítě*|*Virtuální sítě bez síťové virtuální zařízení*|*Větve*|
|---|---|---|---|---|---|
| **SÍŤOVÉ virtuální zařízení paprsky**   | &#8594; | Přes síťové virtuální zařízení VNet | Partnerské vztahy | Přes síťové virtuální zařízení VNet | Přes síťové virtuální zařízení VNet |
| **SÍŤOVÉ virtuální zařízení virtuální sítě**    | &#8594; | Partnerské vztahy | Direct | Direct | Direct |
| **Virtuální sítě bez síťové virtuální zařízení**| &#8594; | Přes síťové virtuální zařízení VNet | Direct | Direct | Direct |
| **Větve**     | &#8594; | Přes síťové virtuální zařízení VNet | Direct | Direct | Direct |

Každá z buněk v matici připojení popisuje, jak virtuální síť nebo větev ("od" na straně toku, záhlaví řádků v tabulce) komunikuje s cílovou virtuální sítí nebo větví (strana "do" toku, záhlaví sloupců jsou kurzívou v tabulce). "Direct" znamená, že připojení je nativně zajištěno pomocí virtuální sítě WAN, "partnerského vztahu" znamená, že připojení je zajištěno User-Defined cestou ve virtuální síti, "přes virtuální síť služby síťové virtuální zařízení" znamená, že připojení projde síťové virtuální zařízení nasazenou ve virtuální síti síťové virtuální zařízení. Zvažte použití těchto zdrojů:

* SÍŤOVÉ virtuální zařízení paprsky nespravuje virtuální síť WAN. V důsledku toho bude uživatel spravovat mechanismy, se kterými budou komunikovat s ostatními virtuální sítě nebo větvemi. Připojení k virtuální síti síťové virtuální zařízení je zajišťováno partnerským vztahem virtuální sítě a výchozí trasa s hodnotou 0.0.0.0/0 ukazující na síťové virtuální zařízení jako další směrování by se měla týkat připojení k Internetu, k ostatním paprskům a k větvím.
* SÍŤOVÉ virtuální zařízení virtuální sítě ví o svých vlastních paprskech síťové virtuální zařízení, ale ne o síťové virtuální zařízení paprsky, které jsou připojené k ostatním síťové virtuální zařízení virtuální sítě. Například na obrázku 2 dále v tomto článku ví virtuální síť 2 o virtuální síti 5 a virtuální síti 6, ale ne o ostatních paprskech, jako jsou virtuální sítě 7 a virtuální síť 8. Pro vložení předpon ostatních paprsků do síťové virtuální zařízení virtuální sítě se vyžaduje statická trasa.
* Podobně větve a jiné než síťové virtuální zařízení virtuální sítě nebudou znát síťové virtuální zařízení paprsky, protože síťové virtuální zařízení paprsky nejsou připojené k virtuálním rozbočovačům sítě WAN. V důsledku toho budou potřeba statické trasy i tady.

S ohledem na to, že síťové virtuální zařízení paprsky nespravuje Virtual WAN, všechny ostatní řádky zobrazí stejný vzor připojení. V důsledku toho bude jedna směrovací tabulka (výchozí) provádět tyto akce:

* Virtuální sítě (bez centra virtuální sítě a virtuální sítě uživatele):
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **výchozí**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **výchozí**

V tomto scénáři si ale musíme představit, které statické trasy se mají nakonfigurovat. Každá statická trasa bude mít dvě komponenty, jedna součást ve virtuálním rozbočovači WAN oznamuje virtuálním komponentám WAN, které se mají použít pro každý paprsk, a druhý v tomto konkrétním připojení ukazující na konkrétní IP adresu přiřazenou k síťové virtuální zařízení (nebo k vyrovnávání zatížení před několika síťová virtuální zařízení), jak ukazuje **Obrázek 1** :

**Obrázek 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Obrázek 1":::

V takovém případě statické trasy, které potřebujeme ve výchozí tabulce k odesílání provozu do síťové virtuální zařízení paprsků za virtuální sítí síťové virtuální zařízení, jsou následující:

| Description | Tabulka směrování | Statická trasa              |
| ----------- | ----------- | ------------------------- |
| Virtuální síť 2       | Výchozí     | 10.2.0.0/16 – > eastusconn |
| Virtuální síť 4       | Výchozí     | 10.4.0.0/16 – > weconn     |

Virtuální síť WAN teď ví, ke kterému připojení se mají odesílat pakety, ale připojení potřebuje vědět, co dělat při přijímání těchto paketů: v tomto umístění se používají tabulky směrování připojení. Tady budeme používat kratší předpony (/24 místo déle než 16), abyste se ujistili, že tyto trasy mají přednost před trasami, které jsou importované z síťové virtuální zařízení virtuální sítě (virtuální síť 2 a virtuální síť 4):

| Description | Připojení | Statická trasa            |
| ----------- | ---------- | ----------------------- |
| Virtuální síť 5       | eastusconn | 10.2.1.0/24 – > 10.2.0.5 |
| Virtuální síť 6       | eastusconn | 10.2.2.0/24 – > 10.2.0.5 |
| Virtuální síť 7       | weconn     | 10.4.1.0/24 – > 10.4.0.5 |
| Virtuální síť 8       | weconn     | 10.4.2.0/24 – > 10.4.0.5 |

Nyní síťové virtuální zařízení virtuální sítě, non-síťové virtuální zařízení virtuální sítě a větve ví, jak dosáhnout všech síťové virtuální zařízení paprsků. Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **obrázku 2** existují dvě centra. **Hub1** a **hub2**.

* **Hub1** a **hub2** jsou přímo připojené k síťové virtuální zařízení virtuální sítě **VNet 2** a **VNet 4**.

* **Virtuální síť 5** a **virtuální síť 6** jsou v partnerském vztahu s **virtuální sítí 2**.

* **Virtuální síť 7** a **virtuální síť 8** jsou v partnerském vztahu s **virtuální sítí 4**.

* **Virtuální sítě 5, 6, 7, 8** jsou nepřímé paprsky, ne přímo připojené k virtuálnímu rozbočovači.

**Obrázek 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Obrázek 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Pokud chcete nastavit směrování přes síťové virtuální zařízení, tady je postup, který je potřeba vzít v úvahu:

1. Identifikujte připojení virtuální sítě síťové virtuální zařízení paprsku. Na **obrázku 2** jsou **připojení VNet 2 (Eastusconn)** a připojení k **virtuální síti 4 (weconn)**.

   Zajistěte, aby byly nastavené udr:
   * Z virtuální sítě 5 a virtuální sítě 6 k virtuální síti 2 síťové virtuální zařízení IP adresy
   * Z virtuální sítě 7 a virtuální sítě 8 na virtuální síť 4 síťové virtuální zařízení IP adresy 
   
   K virtuálním rozbočovačům nemusíte přímo připojovat virtuální sítě 5, 6, 7, 8. Ujistěte se, že skupin zabezpečení sítě v virtuální sítě 5, 6, 7, 8 povoluje provoz pro větev (VPN/ER/P2S) nebo virtuální sítě připojená ke svým vzdáleným virtuální sítě. Například virtuální sítě 5, 6 musí zajistit, aby skupin zabezpečení sítě povolovaly přenosy místních předpon adres a virtuální sítě 7, které jsou připojené ke vzdálenému rozbočovači 2.

Virtuální síť WAN nepodporuje scénář, ve kterém se virtuální sítě 5, 6 připojuje k virtuálnímu rozbočovači a komunikuje prostřednictvím sítě VNet 2 síťové virtuální zařízení IP. Proto je potřeba připojit virtuální sítě 5, 6 k VNet2 a podobně virtuální síť 7, 8 k virtuální síti 4.

2. Přidejte agregovanou položku statické trasy pro virtuální sítě 2, 5, 6 do výchozí směrovací tabulky centra 1.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Příklad":::

3. Nakonfigurujte statickou trasu pro virtuální sítě 5, 6 ve virtuální síti síťového připojení 2. Informace o nastavení konfigurace směrování pro připojení k virtuální síti najdete v tématu [Směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md#routing-configuration).

4. Přidejte agregovanou položku statického směrování pro výchozí směrovací tabulku virtuální sítě 4, 7, 8 do středu 1.

5. Opakujte kroky 2, 3 a 4 pro výchozí směrovací tabulku centra 2.

Výsledkem bude, že se změní konfigurace směrování, jak je znázorněno na **obrázku 3** níže.

**Obrázek 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Obrázek 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
