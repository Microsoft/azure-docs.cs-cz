---
title: Spolupráce
titleSuffix: Language Understanding - Azure Cognitive Services
description: Služba LUIS aplikace vyžadují jednoho vlastníka a volitelné spolupracovníci umožňuje více uživatelům vytvářet jednotlivé aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 9229a583dac526d09d7c5046c4bb81d30c9d4c3f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873559"
---
# <a name="collaborating-with-other-authors"></a>Spolupráce s jinými autory

Služba LUIS poskytuje spolupráci umožňují skupině lidí stát autorem aplikace.

## <a name="luis-account"></a>Účet služby LUIS
Služba LUIS účet je přidružený jeden [Microsoft Live](https://login.live.com/) účtu. Každý účet služby LUIS získá bezplatného [vytváření klíč](luis-concept-keys.md#authoring-key) pro všechny aplikace LUIS vytváření účtu má přístup k. 

Služba LUIS účet může mít mnoho aplikace LUIS.

Zobrazit [uživatel tenanta Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) získat další informace o uživatelských účtů Active Directory. 

## <a name="luis-app-owner"></a>Vlastník aplikace LUIS
Účet, který vytvoří aplikaci se vlastníka. Každá aplikace má jednoho vlastníka. Vlastník je uveden v aplikaci  **[nastavení](luis-how-to-collaborate.md)**. Toto je účet, který může odstranit aplikace. Je také účet, který obdrží e-mail, když se naplní kvóta koncový bod 75 % měsíčního limitu. 

## <a name="authorization-roles"></a>Povolení role
Služba LUIS nepodporuje různé role pro vlastníky a spolupracovníci s jednou výjimkou. Vlastník je jediný účet, který může odstranit aplikace.

Pokud vás zajímá při řízení přístupu k modelu, vezměte v úvahu dělení modelu do menších LUIS aplikací, kde každá menší aplikace má omezenější sadu spolupracovníky. Použití [odeslání](https://aka.ms/dispatch-tool) umožňující nadřazená aplikace LUIS ke správě koordinaci mezi nadřazenými a podřízenými aplikace.

## <a name="transfer-ownership"></a>Převod vlastnictví
Služba LUIS neposkytuje převod vlastnictví, ale žádné spolupracovníka můžete exportovat aplikace a pak vytvořit aplikace metodou importování, se. Mějte na paměti, že nová aplikace má jiné ID aplikace. Nová aplikace potřebuje školení, publikování a nový koncový bod používá.

## <a name="luis-app-collaborators"></a>Spolupracovníci aplikace LUIS
Vlastník aplikace můžete přidat spolupracovníky do aplikace. Vlastník je potřeba přidat spolupracovníka e-mailovou adresu v aplikaci  **[nastavení](luis-how-to-collaborate.md)**. Spolupracovníka má úplný přístup k aplikaci. Pokud spolupracovníka odstraní aplikaci, aplikace se odebere z účtu spolupracovníka ale zůstává v vlastníka účtu. 

Pokud chcete sdílet více aplikací s spolupracovníky, každé aplikaci, která musí přidat e-mailu spolupracovníka. 

## <a name="managing-multiple-authors"></a>Správa více uživatelů
[LUIS](luis-reference-regions.md#luis-website) webu nenabízí aktuálně vytváření úroveň transakce. Můžete povolit autoři pracovat na nezávislé verze ze základní verze. Dvě různé metody jsou popsány v následujících částech.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Spravovat více verzí uvnitř stejné aplikace
Začněte tím, že [klonování](luis-how-to-manage-versions.md#clone-a-version), od základní verze, pro jednotlivé autory. 

Jednotlivé autory změní svou vlastní verzi aplikace. Jakmile jednotlivé autory s modelem spokojeni, exportujte soubory JSON nové verze.  

Exportované aplikace jsou soubory ve formátu JSON, které je možné porovnat změny. Kombinovat soubory, které chcete vytvořit jeden soubor JSON s novou verzi. Změnit **versionId** vlastností ve formátu JSON místo nové sloučené verze. Tuto verzi naimportujte do původní aplikace. 

Tato metoda umožňuje mít jeden aktivní verze, jedna fáze a jednu publikovanou verzi. Můžete porovnat výsledky v podokně interaktivní testování ve všech třech verzích.

## <a name="manage-multiple-versions-as-apps"></a>Spravovat více verzí jako aplikace
[Export](luis-how-to-manage-versions.md#export-version) základní verze. Jednotlivé autory importuje verze. Osoba, která importuje aplikace je vlastníkem verze. Když se provádí úpravy aplikace a export verze. 

Exportované aplikace jsou soubory ve formátu JSON, které můžete ve srovnání s exportem základní změny. Kombinovat soubory, které chcete vytvořit jeden soubor JSON s novou verzi. Změnit **versionId** vlastností ve formátu JSON místo nové sloučené verze. Tuto verzi naimportujte do původní aplikace.

## <a name="next-steps"></a>Další postup

Vysvětlení [správy verzí](luis-concept-version.md) koncepty. 

Zobrazit [nastavení aplikace](luis-how-to-collaborate.md) na další informace o správě spolupracovníky ve vaší aplikaci LUIS.

Zobrazit [e-mailu přidejte do seznamu přístupu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) s rozhraními API pro vytváření obsahu.
