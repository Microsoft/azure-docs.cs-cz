---
title: Mapování datových polí na entity Azure Sentinel | Microsoft Docs
description: Mapování datových polí v tabulkách do entit Azure Sentinel v pravidlech analýz pro lepší informace o incidentech
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456242"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Mapování datových polí na entity v Azure Sentinel 

> [!IMPORTANT]
>
> - Nová verze funkce mapování entit je ve verzi **Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

> [!IMPORTANT]
>
> - Důležité informace o zpětné kompatibilitě a rozdílech mezi novými a starými verzemi mapování entit najdete v [poznámkách k nové verzi](#notes-on-the-new-version) na konci tohoto dokumentu.

## <a name="introduction"></a>Úvod

Mapování entit je nedílnou součástí konfigurace [naplánovaných pravidel analýzy dotazů](tutorial-detect-threats-custom.md). Vylepšuje výstup pravidel (výstrahy a incidenty) základními informacemi, které slouží jako stavební kameny všech vyšetřovacích procesů a nápravných akcí, které následují.

Níže uvedený postup je součástí Průvodce vytvořením pravidla Analytics. Zpracovává se tady nezávisle na řešení scénáře přidávání nebo změny mapování entit v existujícím analytickém pravidle.

## <a name="how-to-map-entities"></a>Postup mapování entit

1. V navigační nabídce Azure Sentinel vyberte **Analytics**.

1. Vyberte pravidlo naplánovaného dotazu a klikněte na **Upravit**. Nebo vytvořte nové pravidlo kliknutím na **vytvořit &#10132; pravidlo plánovaného dotazu** v horní části obrazovky.

1. Klikněte na kartu **nastavit logiku pravidla** .

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Mapování polí na entity":::

1. V části **vylepšení výstrah** v části **mapování entit** vyberte typ entity z rozevíracího seznamu **typ entity** .

1. Vyberte **identifikátor** entity. Identifikátory jsou atributy entity, které ji mohou dostatečně identifikovat. Zvolte jednu z rozevíracího seznamu **identifikátor** a pak v rozevíracím seznamu **hodnota** zvolte datové pole, které bude odpovídat identifikátoru. V některých výjimkách se seznam **hodnot** vyplní datovými poli v tabulce definované jako předmět dotazu pravidla.

    Pro danou entitu můžete definovat **až tři identifikátory** . Některé identifikátory jsou povinné, jiné jsou volitelné. Musíte zvolit alespoň jeden požadovaný identifikátor. Pokud to neuděláte, zobrazí se varovná zpráva s pokynem, které identifikátory se vyžadují. Nejlepší výsledky – pro maximální jedinečnou identifikaci – Pokud je to možné, používejte **silné identifikátory** , kdykoli je to možné, a použití více silných identifikátorů umožníte větší korelaci mezi zdroji dat. Podívejte se na úplný seznam dostupných [entit a identifikátorů](entities-reference.md).

1. Klikněte na **Přidat novou entitu** a namapujte další entity. V rámci jednoho analytického pravidla můžete mapovat **až pět entit** . Můžete také namapovat více než jeden ze stejného typu. Můžete například mapovat dvě entity **IP** adres, jednu z pole *zdrojové IP adresy* a jednu z pole *cílové IP adresy* . Tímto způsobem je můžete sledovat.

    Pokud změníte své rozhodnutí nebo jste udělali chybu, můžete mapování entit odebrat kliknutím na ikonu koše vedle rozevíracího seznamu entita.

1. Po dokončení mapování entit klikněte na kartu **Kontrola a vytvoření** . Po úspěšném ověření pravidla klikněte na **Uložit**.

## <a name="notes-on-the-new-version"></a>Poznámky k nové verzi

- Pokud jste dříve definovali mapování entit pro toto pravidlo analýzy pomocí staré verze, tyto mapování se zobrazí v kódu dotazu. Mapování entit definované v nové verzi se nezobrazí **v kódu dotazu**. Pravidla analýzy můžou podporovat jenom jednu verzi mapování entit současně a má přednost nová verze. Jakékoli mapování, které zde definujete, **tak způsobí, že všechna mapování** definovaná v kódu dotazu budou při spuštění dotazu **ignorována** . 

- Pokud stále potřebujete použít **starou verzi** mapování entit (Pokud je nová verze stále ve verzi Preview), můžete k ní i nadále přistupovat pomocí příznaku funkce v adrese URL. Umístěte kurzor mezi `https://portal.azure.com/` a a `#blade` vložte text `?feature.EntityMapping=false` .

  - Omezení staré verze budou platit i nadále. Můžete mapovat pouze uživatele, hostitele, IP adresu, adresu URL a entity hash souborů a pouze jeden z nich.

  - **Předtím, než** se vrátíte do původní verze, musíte **Odebrat** všechna mapování entit vytvořená pomocí nové verze, jinak nebudou **fungovat** všechna mapování entit, která používají starou verzi.

- Jakmile je nová verze mapování entit obecně dostupná, nebude již možné použít starou verzi. Důrazně doporučujeme migrovat staré mapování entit na novou verzi.


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak mapovat datová pole k entitám v pravidlech Azure Sentinel Analytics. Další informace o Sentinel Azure najdete v následujících článcích:
- Získejte úplný přehled o [plánovaných pravidlech dotaz Analytics](tutorial-detect-threats-custom.md).
- Přečtěte si další informace o [entitách v Sentinel Azure](entities-in-azure-sentinel.md).
