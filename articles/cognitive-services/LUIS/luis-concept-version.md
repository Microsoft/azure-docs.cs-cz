---
title: Vysvětlení správy verzí v LUIS – Azure | Dokumentace Microsoftu
description: Další informace o použití verze správě změn v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/13/2018
ms.author: diberry
ms.openlocfilehash: 17abe383d3074d636605c3b1b91927f89f7dd896
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225723"
---
# <a name="versions"></a>Verze
Vytvořit stejnou aplikaci pomocí různých modelů [verze](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID verze
ID verze se skládá ze znaků, číslic nebo '.' a nesmí být delší než 10 znaků.

## <a name="initial-version"></a>Počáteční verze
Počáteční verze (0,1) je výchozí verze aktivní. 

## <a name="active-version"></a>Aktivní verze
K [nastavit verzi](luis-how-to-manage-versions.md#set-active-version) jako aktivní znamená, že je aktuálně upravovat a testovat v [LUIS](luis-reference-regions.md) webu. Nastavte verzi jako aktivní, aby přístup k jeho datům, proveďte aktualizace, a jde o test a publikujete ji.

Název aktuálně aktivní verze se zobrazí v horní, levý panel za názvem aplikace. 

[ ![Změnit verzi rozhraní active](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Verze a publikování sloty
Publikujete do fáze a produktu sloty. Každý slot může mít jinou verzi nebo stejnou verzi. To je užitečné pro ověření změn mezi verzemi modelu přes koncový bod, který je k dispozici pro roboty nebo jiné služby LUIS volání aplikace. 

## <a name="clone-a-version"></a>Klonovat verze
Klonovat verzi vytvoří kopii stávající verzi a uložte ho jako novou verzi. Klonovat verze se má použít stejný obsah stávající verze jako výchozí bod pro novou verzi. Po klonování verze stane novou verzi **aktivní** verze. 

## <a name="import-and-export-a-version"></a>Import a export verze
Můžete importovat verze na úrovni aplikace. Tato verze se stane aktivní verze a použít ID verze ve vlastnosti "versionId" soubor aplikace. Na úrovni verze můžete také importovat do stávající aplikace. Nová verze stane aktivní verze. 

Můžete to taky verze na úrovni aplikace nebo exportujete verze na úrovni verze. Jediným rozdílem je, že verze exportované úroveň aplikace je aktuálně aktivní verze v úrovni verze, můžete použít libovolnou verzi pro export na **[nastavení](luis-how-to-manage-versions.md)** stránky. 

Exportovaný soubor neobsahuje informace o zjištěné počítače, protože aplikace je retrained až po importu. Exportovaný soubor neobsahuje spolupracovníci – je potřeba přidat tyto zpět po verzi, se importují do nové aplikace.

## <a name="export-each-version-as-app-backup"></a>Exportovat všechny verze jako záloha aplikace
Aby bylo možné zálohovat aplikace LUIS, exportovat jednotlivých verzí na **[nastavení](luis-how-to-manage-versions.md)** stránky.

## <a name="delete-a-version"></a>Odstranit verzi
Všechny verze, s výjimkou aktivní verze můžete odstranit ze seznamu verze na stránce nastavení. 

## <a name="version-availability-at-the-endpoint"></a>Dostupnost verze v koncovém bodě
Trénované verze nejsou automaticky k dispozici v aplikaci [koncový bod](luis-glossary.md#endpoint). Je nutné [publikovat](luis-how-to-publish-app.md) nebo znovu publikovat verzi v pořadí pro ni k dispozici na váš koncový bod aplikace. Můžete publikovat do **pracovní** a **produkční**, získáte až dvě verze aplikace, které jsou k dispozici v koncovém bodě. Pokud potřebujete další verze aplikace, které jsou k dispozici na koncový bod, by měl exportovat na verzi a znovu importovat do nové aplikace. Nová aplikace má ID jiné aplikace.

## <a name="collaborators"></a>Spolupracovníci
Vlastníka a všechny [spolupracovníci](luis-how-to-collaborate.md) úplný přístup pro všechny verze aplikace.

## <a name="next-steps"></a>Další postup

Zjistěte, jak přidat [správy verzí](luis-how-to-manage-versions.md) na stránce nastavení aplikace. 

Zjistěte, jak navrhnout [záměry](luis-concept-intent.md) do modelu.