---
title: Správa verzí – LUIS
titleSuffix: Azure Cognitive Services
description: Verze v LUIS se podobají verzím v tradičním programování. Každá verze je snímkem v čase aplikace. Než v aplikaci provedete změny, vytvořte novou verzi. Je snazší přejít zpět na přesnou aplikaci a pak se pokusit neloupaný záměr a projevy aplikace a vrátit se k předchozímu stavu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2e13efa70d0344defeb306a92ac405439635e929
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619704"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Vysvětlení, jak a kdy použít verzi LUIS

Verze v LUIS se podobají verzím v tradičním programování. Každá verze je snímkem v čase aplikace. Než v aplikaci provedete změny, vytvořte novou verzi. Je snazší přejít zpět na přesnou verzi a pak se pokusit odebrat záměry a projevy do předchozího stavu.

Vytvořte různé modely stejné aplikace s [verzemi](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID verze
ID verze se skládá ze znaků, číslic nebo "." a nemůže být delší než 10 znaků.

## <a name="initial-version"></a>Počáteční verze
Počáteční verze (0,1) je výchozí aktivní verze. 

## <a name="active-version"></a>Aktivní verze
Pokud chcete [nastavit verzi](luis-how-to-manage-versions.md#set-active-version) jako aktivní, znamená to, že je aktuálně upravována a testována na webu [Luis](luis-reference-regions.md) . Nastavte verzi jako aktivní pro přístup k datům, proveďte aktualizace a otestujte a publikujte ji.

Název aktuálně aktivní verze se zobrazí v horním a levém panelu za názvem aplikace. 

[![Změna aktivní verze](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Verze a sloty publikování
Publikujete do těchto fází a produktových slotů. Každá patice může mít jinou verzi nebo stejnou verzi. To je užitečné pro ověření změn mezi verzemi modelu prostřednictvím koncového bodu, který je k dispozici pro roboty nebo jiné LUIS volání aplikací. 

## <a name="clone-a-version"></a>Klonovat verze
Naklonujte verzi, aby se vytvořila kopie existující verze, a uložte ji jako novou verzi. Naklonujte verzi tak, aby používala stejný obsah existující verze jako výchozí bod pro novou verzi. Po naklonování verze se nová verze zobrazí jako **aktivní** verze. 

## <a name="import-and-export-a-version"></a>Import a export verze
Verzi můžete importovat na úrovni aplikace. Tato verze se zobrazí jako aktivní verze a v souboru aplikace se použije ID verze ve vlastnosti "versionId". Můžete také naimportovat na úrovni verze do existující aplikace. Nová verze se zobrazí jako aktivní verze. 

Verzi můžete exportovat na úrovni aplikace nebo můžete exportovat verzi na úrovni verze. Jediným rozdílem je, že vyexportovaná verze na úrovni aplikace je aktuálně aktivní verze, zatímco na úrovni verze můžete na stránce **[Nastavení](luis-how-to-manage-versions.md)** zvolit libovolnou verzi, kterou chcete exportovat. 

Exportovaný soubor neobsahuje informace zjištěné počítačem, protože aplikace je po importu znovu přeučena. Exportovaný soubor neobsahuje spolupracovníky – po importu verze do nové aplikace je budete muset přidat zpátky.

## <a name="export-each-version-as-app-backup"></a>Exportovat každou verzi jako zálohu aplikace
Pokud chcete zálohovat aplikaci LUIS, exportujte každou verzi na stránce **[Nastavení](luis-how-to-manage-versions.md)** .

## <a name="delete-a-version"></a>Odstranění verze
Všechny verze kromě aktivní verze můžete odstranit ze stránky seznam verzí na stránce nastavení. 

## <a name="version-availability-at-the-endpoint"></a>Dostupnost verze na koncovém bodu
Vyškolené verze nejsou automaticky dostupné ve vašem [koncovém bodě](luis-glossary.md#endpoint)aplikace. Je nutné [publikovat](luis-how-to-publish-app.md) nebo znovu publikovat verzi, aby byla k dispozici ve vašem koncovém bodě aplikace. Můžete publikovat do **přípravy** a **výroby**, což vám poskytne až dvě verze aplikace, která je k dispozici na koncovém bodu. Pokud potřebujete další verze aplikace, kterou máte k dispozici na koncovém bodu, měli byste verzi exportovat a znovu importovat do nové aplikace. Nová aplikace má jiné ID aplikace.

## <a name="collaborators"></a>Spolupracovníci
Vlastník a všichni spolupracovníci mají plný přístup ke všem verzím aplikace. [](luis-how-to-collaborate.md)

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu Postup přidání [správy verzí](luis-how-to-manage-versions.md) na stránce nastavení aplikace. 

Naučte se navrhovat [záměry](luis-concept-intent.md) do modelu.
