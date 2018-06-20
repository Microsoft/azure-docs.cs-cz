---
title: Pochopení LEOŠ aplikace spolupráce - Azure | Microsoft Docs
description: LEOŠ aplikace vyžadují jednoho vlastníka a volitelné spolupracovníci.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 51b3958f83cd110ace27f6ee42571c05843f5aa2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265061"
---
# <a name="collaborating"></a>Spolupráce

LEOŠ poskytuje spolupráce umožňující ve skupině uživatelů k vytváření aplikace.

## <a name="luis-account"></a>LEOŠ účtu
Účet LEOŠ souvisí s jedním [Microsoft Live](https://login.live.com/) účtu. Každý účet LEOŠ dostane bezplatný [vytváření klíč](luis-concept-keys.md#authoring-key) sloužící k vytváření všechny aplikace LEOŠ účtu má přístup k. 

Účet LEOŠ může mít velký počet LEOŠ aplikací.

## <a name="luis-app-owner"></a>Vlastník LEOŠ aplikace
Účet, který vytvoří aplikace je vlastníkem. Každá aplikace má jednoho vlastníka. V aplikaci je uvedený vlastník  **[nastavení](luis-how-to-collaborate.md)**. Toto je účet, který může odstranit aplikaci. Toto je účet, který obdrží e-mailem, když se koncový bod kvóty dosáhne 75 % měsíčního limitu. 

## <a name="transfer-ownership"></a>Převod vlastnictví
LEOŠ neposkytuje přenos vlastnictví, ale žádné spolupracovník můžete exportovat aplikaci a pak vytvořit aplikaci metodou importování, se. Mějte na paměti, že nové aplikace má jiné ID aplikace. Nové aplikace potřebám cvičení, publikovat a použít nový koncový bod.

## <a name="luis-app-collaborators"></a>Spolupracovníci LEOŠ aplikace
Vlastníka aplikace můžete přidat spolupracovníci do aplikace. Vlastník musí přidat spolupracovník e-mailovou adresu na aplikaci  **[nastavení](luis-how-to-collaborate.md)**. Spolupracovník má úplný přístup k aplikaci. Pokud spolupracovník odstraní aplikaci, aplikace se odebere z účtu spolupracovník, ale zůstává v vlastníka účtu. 

Pokud chcete sdílet se spolupracovníky víc aplikací, musí každá aplikace přidat e-mailu spolupracovníka. 

## <a name="managing-multiple-authors"></a>Správa více autorů
[LEOŠ] [ LUIS] webu nenabízí aktuálně vytváření úrovni transakcí. Můžete povolit autorům pracovat na nezávislé verze základní verze. Dvě různé metody jsou popsané v následujících částech.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Spravovat více verzí uvnitř stejné aplikaci
Začněte tím, že [klonování](luis-how-to-manage-versions.md#clone-a-version), z základní verze, pro každého autora. 

Každý autor provede změny svou vlastní verzi aplikace. Jakmile každého autora s modelem spokojeni, exportujte soubory JSON nové verze.  

Exportovaný aplikace jsou soubory formátu JSON, které je možné porovnávat změny. Kombinovat soubory a vytvořte soubor JSON jeden nové verze. Změna **versionId** vlastnost ve formátu JSON, aby označily sloučené novou verzi. Importujte této verze do původní aplikace. 

Tato metoda umožňuje mít jeden aktivní verze, jednu verzi fáze a jeden publikovanou verzi. Mezi tři verze, můžete porovnat výsledky v podokně interaktivní testování.

### <a name="manage-multiple-versions-as-apps"></a>Spravovat více verzí jako aplikace
[Export](luis-how-to-manage-versions.md#export-version) základní verze. Každý autor importuje verze. Osoba, která importuje aplikace je vlastníkem verze. Pokud se provádí pomocí úpravy aplikace a export verze. 

Exportovaný aplikace jsou soubory formátu JSON, které je možné porovnávat s základní export pro změny. Kombinovat soubory a vytvořte soubor JSON jeden nové verze. Změna **versionId** vlastnost ve formátu JSON, aby označily sloučené novou verzi. Importujte této verze do původní aplikace.

## <a name="next-steps"></a>Další postup

Pochopení [Správa verzí](luis-concept-version.md) koncepty. 

V tématu [nastavení aplikace](luis-how-to-collaborate.md) se dozvíte, jak spravovat spolupracovníci ve vaší aplikaci LEOŠ.

V tématu [e-mailu přidat do seznamu přístupu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) pomocí rozhraní API pro vytváření obsahu.

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website