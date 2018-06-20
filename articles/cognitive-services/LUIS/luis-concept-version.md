---
title: Pochopení správy verzí v LEOŠ - Azure | Microsoft Docs
description: Naučte se používat k správě změn znalosti jazyka (LEOŠ) verze
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/13/2018
ms.author: v-geberr
ms.openlocfilehash: dabe7def2766770b686be3c43d4af4f331dd9577
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266069"
---
# <a name="versions"></a>Verze
Vytvoření stejné aplikace s odlišnými modely [verze](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID verze
ID verze obsahuje znaky, číslice nebo '.' a nesmí být delší než 10 znaků.

## <a name="initial-version"></a>Počáteční verze
Počáteční verze (0,1) je výchozí verze aktivní. 

## <a name="active-version"></a>Aktivní verze
K [nastavit na verzi](luis-how-to-manage-versions.md#set-active-version) jako aktivní znamená je v současné době upravovat a testovat v [LEOŠ] [ LUIS] webu. Verze nastavená jako aktivní, aby přístup k jeho data, zkontrolujte aktualizace, také, testování a publikujete ji.

Název aktuálně aktivní verze se zobrazí v horní, levém panelu po název aplikace. 

[ ![Změnit active verzi](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Verze a publikování sloty
Můžete publikovat na fázi a produkt sloty. Každý slot může mít různé verze nebo se stejnou verzí. To je užitečné pro ověření změn mezi verzemi modelu prostřednictvím koncového bodu, který je k dispozici robotů nebo jiných LEOŠ volání aplikace. 

## <a name="clone-a-version"></a>Klonování verze
Clone – verze pro vytvoření kopie stávající verzi a uložte ho jako novou verzi. Klonování verze se má použít stejný obsah stávající verze jako výchozí bod pro novou verzi. Jakmile naklonovat na verzi, se změní na novou verzi **active** verze. 

## <a name="import-and-export-a-version"></a>Import a export verze
Můžete importovat verze na úrovni aplikace. Tuto verzi stane aktivní verze a použít ID verze ve vlastnosti "versionId" souboru aplikace. Na úrovni verze můžete také importovat do stávající aplikace. Nová verze stane aktivní verze. 

Můžete exportovat na verzi na úrovni aplikace, nebo můžete exportovat na verzi na úrovni verze. Jediným rozdílem je, že verze exportovaný úrovni aplikace je aktuálně aktivní verze, zatímco na úrovni verze, můžete exportovat na všechny verze **[nastavení](luis-how-to-manage-versions.md)** stránky. 

Exportovaný soubor neobsahuje naučili počítače informace, protože aplikace je retrained po dokončení importu. Exportovaný soubor neobsahuje spolupracovníci – je potřeba přidat tyto zpět po verze je importovat do nové aplikace.

## <a name="export-each-version-as-app-backup"></a>Exportovat jednotlivých verzí jako zálohování aplikace
Chcete-li zálohovat aplikace LEOŠ, exportovat na jednotlivých verzí **[nastavení](luis-how-to-manage-versions.md)** stránky.

## <a name="delete-a-version"></a>Odstranění verze
Všechny verze kromě aktivní verze můžete odstranit ze seznamu verze na stránce nastavení. 

## <a name="version-availability-at-the-endpoint"></a>Dostupnost verze na koncový bod
Vyškolení verze nejsou automaticky k dispozici v aplikaci [koncový bod](luis-glossary.md#endpoint). Je nutné [publikování](PublishApp.md) nebo znovu publikovat na verzi, aby byla k dispozici na váš koncový bod aplikace. Můžete publikovat **pracovní** a **produkční**, která poskytuje až dvě verze aplikace, které jsou dostupné na koncový bod. Pokud potřebujete další verze aplikace, které jsou k dispozici na koncový bod, musí exportovat na verzi a znovu naimportovat do nové aplikace. Nové aplikace má ID. jiné aplikace.

## <a name="collaborators"></a>Spolupracovníci
Vlastník a všechny [spolupracovníci](luis-how-to-collaborate.md) mají plný přístup ke všechny verze aplikace.

## <a name="next-steps"></a>Další postup

Zjistit, jak přidat [Správa verzí](luis-how-to-manage-versions.md) na stránce nastavení aplikace. 

Získat informace o vytváření [záměry](luis-concept-intent.md) do modelu.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions