---
title: Vytváření sestav vektorů útoku
description: Vektorové sestavy útoku poskytují grafické znázornění řetězce ohrožení zabezpečení zneužití zařízení.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784369"
---
# <a name="attack-vector-reporting"></a>Vytváření sestav vektorů útoku

## <a name="about-attack-vector-reports"></a>O vektorových sestavách útoků

Vektorové sestavy útoku poskytují grafické znázornění řetězce ohrožení zabezpečení zneužití zařízení. Tato ohrožení zabezpečení můžou útočníkovi umožnit přístup k klíčovým síťovým zařízením. Simulátor vektoru útoku vypočítá vektory útoku v reálném čase a analyzuje všechny vektory útoku pro konkrétní cíl.

Práce s vektorem útoku umožňuje vyhodnotit účinek zmírňující aktivity v posloupnosti útoků. Pak se můžete rozhodnout, například pokud upgrade systému narušuje cestu útočníka tím, že dojde k přerušení řetězce útoku nebo pokud zůstane jiná cesta k útoku. Tyto informace vám pomůžou určit prioritu oprav a rizikových aktivit.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Zobrazení výstrah v řídicím centru.":::

> [!NOTE]
> Správci a analytici zabezpečení mohou provádět postupy popsané v této části.

## <a name="create-an-attack-vector-report"></a>Vytvoření sestavy vektoru útoku

Postup vytvoření simulace vektoru útoku:

1. Kliknutím na :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="znaménko plus":::v nabídce na straně přidejte simulaci.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simulace vektoru útoku.":::

2. Zadejte vlastnosti simulace:

   - **Název**: název simulace.

   - **Maximum vektorů**: maximální počet vektorů v jedné simulaci.

   - **Zobrazit v mapě zařízení**: zobrazí vektor útoku jako filtr na mapě zařízení.

   - **Všechna zdrojová zařízení**: vektor útoku bude považovat všechna zařízení za zdroj útoku.

   - **Zdroj útoku**: vektor útoku bude zvážit pouze určená zařízení jako zdroj útoku.

   - **Všechna cílová zařízení**: vektor útoku bude považovat všechna zařízení za cíl útoku.

   - **Cíl útoku**: vektor útoku bude zvážit pouze určená zařízení jako cíl útoku.

   - **Vyloučit zařízení**: zadaná zařízení budou vyloučena z simulace vektoru útoku.

   - **Vyloučit podsítě**: zadané podsítě budou vyloučeny z simulace vektoru útoku.

3. Vyberte **Přidat simulaci**. Simulace bude přidána do seznamu simulace.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Přidejte novou simulaci.":::

4. Tuto možnost vyberte, :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: Pokud chcete upravit simulaci.

   Tuto možnost vyberte, :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: Pokud chcete odstranit simulaci.

   Tuto možnost vyberte, :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: Pokud chcete označit simulaci jako oblíbenou.

5. Zobrazí se seznam vektorů útoku, včetně vektorového skóre (od 100), zdrojového zařízení útoku a cílového zařízení útoku. Vyberte konkrétní útok pro grafické znázornění vektorů útoku.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vektory útoku.":::

## <a name="see-also"></a>Viz také

[Vytváření sestav vektorů útoku](how-to-create-attack-vector-reports.md)


