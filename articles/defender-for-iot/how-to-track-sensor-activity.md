---
title: Sledování aktivity senzorů
description: Časová osa události prezentuje časovou osu aktivity zjištěné v síti, včetně výstrah a akcí správy výstrah, událostí sítě a uživatelských operací, jako je přihlášení uživatele a odstranění uživatele.
ms.date: 12/10/2020
ms.topic: article
ms.openlocfilehash: 195908001fbd247ed2e0fa007bc8dcd5ebf28e60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781615"
---
# <a name="track-sensor-activity"></a>Sledování aktivity senzorů

## <a name="event-timeline"></a>Časová osa události

Časová osa události prezentuje časovou osu aktivity, kterou senzor rozpoznal. Například:

  - Akce správy výstrah a výstrah

  - Události sítě

  - Uživatelské operace jako přihlašování a odstraňování uživatelů

Časová osa události poskytuje chronologické zobrazení událostí, ke kterým došlo v síti. Časová osa události umožňuje porozumění a analýzám řetězce událostí, které předcházejí a následovaly útok nebo incident, což pomáhá při šetření a forenzní.

> [!NOTE]
> *Správci* a *analytici zabezpečení* mohou provádět postupy popsané v této části.

Zobrazení protokolů událostí:

- V postranní nabídce vyberte možnost **Časová osa události**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Zobrazit události v časové ose události.":::

Kromě zobrazení událostí, které senzor zjistil, můžete ručně přidat události do časové osy. Tento proces je užitečný v případě, že událost nastala v externím systému, ale má dopad na vaši síť a je důležité ji zaznamenat a prezentovat jako součást časové osy.

Postup ručního přidání událostí:

- Vyberte **vytvořit událost**.

Export informací protokolu událostí do souboru CSV:

- Vyberte **Exportovat**.

## <a name="filter-the-event-timeline"></a>Filtrovat časovou osu události

Vyfiltrujte časovou osu a zobrazte si zařízení a události, které vás zajímají.

Postup filtrování časové osy:

1. Vyberte **Rozšířené filtry**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="K filtrování událostí použijte okno Pokročilé filtry událostí.":::

2. Nastavte filtry událostí následujícím způsobem:

   - **Zahrnout adresu**: Zobrazit události pro konkrétní zařízení.

   - **Vyloučit adresu**: skrýt události pro konkrétní zařízení.

   - **Zahrnout typy událostí**: zobrazí konkrétní typy událostí.

   - **Vyloučit typy událostí**: skrýt určité typy událostí.

   - **Skupina zařízení**: vyberte skupinu zařízení, která byla definována v mapě zařízení. Prezentují se jenom události z této skupiny.

3. Výběrem **Zrušit vše** vymažete všechny vybrané filtry.

4. Hledejte **jenom výstrahy**, **Upozornění a oznámení** nebo **všechny události**.

5. Vyberte **vybrat datum** a zvolte konkrétní datum. Vyberte den, hodinu a minutu. Zobrazí se události z vybraného časového rámce.

6.  Vyberte **operace uživatele** , které chcete zahrnout nebo vyloučit události operace uživatele.

7.  Výběrem šipky (**V**) zobrazíte další informace o události:

    - Vyberte související výstrahy (pokud existují), chcete-li zobrazit podrobný popis výstrahy.

    - Vyberte zařízení, na které se má zařízení zobrazit na mapě.

    - Pokud chcete filtrovat podle souvisejících zařízení, vyberte možnost **Filtrovat události podle souvisejících zařízení** .

    - Vyberte **soubor PCAP** a Stáhněte si soubor PCAP (pokud existuje), který obsahuje zachytávání paketů celé sítě v určitou dobu. 
    
      Soubor PCAP obsahuje technické informace, které mohou pomoci síťovým inženýrům určit přesné parametry události. Soubor PCAP můžete analyzovat pomocí analyzátoru síťových protokolů, jako je třeba Wireshark, open source aplikace.

## <a name="see-also"></a>Viz také

[Zobrazení upozornění](how-to-view-alerts.md)
