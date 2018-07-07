---
title: Principy spolupráce aplikace služby LUIS – Azure | Dokumentace Microsoftu
description: Služba LUIS aplikace vyžadují jednoho vlastníka a spolupracovníky volitelné.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 54a7ecec653218af8f92b405bd0cf8c049d18616
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888445"
---
# <a name="collaborating"></a>Spolupráce

Služba LUIS poskytuje spolupráci umožňují skupině lidí stát autorem aplikace.

## <a name="luis-account"></a>Účet služby LUIS
Služba LUIS účet je přidružený jeden [Microsoft Live](https://login.live.com/) účtu. Každý účet služby LUIS získá bezplatného [vytváření klíč](luis-concept-keys.md#authoring-key) pro všechny aplikace LUIS vytváření účtu má přístup k. 

Služba LUIS účet může mít mnoho aplikace LUIS.

Zobrazit [uživatel tenanta Azure Active Directory](luis-how-to-account-settings.md#azure-active-directory-tenant-user) získat další informace o uživatelských účtů Active Directory. 

## <a name="luis-app-owner"></a>Vlastník aplikace LUIS
Účet, který vytvoří aplikaci se vlastníka. Každá aplikace má jednoho vlastníka. Vlastník je uveden v aplikaci  **[nastavení](luis-how-to-collaborate.md)**. Toto je účet, který může odstranit aplikace. Je také účet, který obdrží e-mail, když se naplní kvóta koncový bod 75 % měsíčního limitu. 

## <a name="transfer-ownership"></a>Převod vlastnictví
Služba LUIS neposkytuje převod vlastnictví, ale žádné spolupracovníka můžete exportovat aplikace a pak vytvořit aplikace metodou importování, se. Mějte na paměti, že nová aplikace má jiné ID aplikace. Nová aplikace potřebuje školení, publikování a nový koncový bod používá.

## <a name="luis-app-collaborators"></a>Spolupracovníci aplikace LUIS
Vlastník aplikace můžete přidat spolupracovníky do aplikace. Vlastník je potřeba přidat spolupracovníka e-mailovou adresu v aplikaci  **[nastavení](luis-how-to-collaborate.md)**. Spolupracovníka má úplný přístup k aplikaci. Pokud spolupracovníka odstraní aplikaci, aplikace se odebere z účtu spolupracovníka ale zůstává v vlastníka účtu. 

Pokud chcete sdílet více aplikací s spolupracovníky, každé aplikaci, která musí přidat e-mailu spolupracovníka. 

## <a name="managing-multiple-authors"></a>Správa více uživatelů
[LUIS](luis-reference-regions.md#luis-website) webu nenabízí aktuálně vytváření úroveň transakce. Můžete povolit autoři pracovat na nezávislé verze ze základní verze. Dvě různé metody jsou popsány v následujících částech.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Spravovat více verzí uvnitř stejné aplikace
Začněte tím, že [klonování](luis-how-to-manage-versions.md#clone-a-version), od základní verze, pro jednotlivé autory. 

Jednotlivé autory změní svou vlastní verzi aplikace. Jakmile jednotlivé autory s modelem spokojeni, exportujte soubory JSON nové verze.  

Exportované aplikace jsou soubory ve formátu JSON, které je možné porovnat změny. Kombinovat soubory, které chcete vytvořit jeden soubor JSON s novou verzi. Změnit **versionId** vlastností ve formátu JSON místo nové sloučené verze. Tuto verzi naimportujte do původní aplikace. 

Tato metoda umožňuje mít jeden aktivní verze, jedna fáze a jednu publikovanou verzi. Můžete porovnat výsledky v podokně interaktivní testování ve všech třech verzích.

### <a name="manage-multiple-versions-as-apps"></a>Spravovat více verzí jako aplikace
[Export](luis-how-to-manage-versions.md#export-version) základní verze. Jednotlivé autory importuje verze. Osoba, která importuje aplikace je vlastníkem verze. Když se provádí úpravy aplikace a export verze. 

Exportované aplikace jsou soubory ve formátu JSON, které můžete ve srovnání s exportem základní změny. Kombinovat soubory, které chcete vytvořit jeden soubor JSON s novou verzi. Změnit **versionId** vlastností ve formátu JSON místo nové sloučené verze. Tuto verzi naimportujte do původní aplikace.

## <a name="next-steps"></a>Další postup

Vysvětlení [správy verzí](luis-concept-version.md) koncepty. 

Zobrazit [nastavení aplikace](luis-how-to-collaborate.md) na další informace o správě spolupracovníky ve vaší aplikaci LUIS.

Zobrazit [e-mailu přidejte do seznamu přístupu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) s rozhraními API pro vytváření obsahu.