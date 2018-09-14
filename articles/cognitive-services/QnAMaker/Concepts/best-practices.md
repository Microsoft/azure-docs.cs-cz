---
title: Osvědčené postupy – QnA Maker
titlesuffix: Azure Cognitive Services
description: Použijte tyto osvědčené postupy pro zlepšení znalostní báze a application/chatovací robot koncovým uživatelům poskytovat lepší výsledky.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: c82c117d149da39fba7b9a243aebb3e127540881
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542920"
---
# <a name="best-practices"></a>Osvědčené postupy
[Životního cyklu vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás o tom, jak spravovat KB začátku do konce. Použijte tyto osvědčené postupy pro zlepšení znalostní báze a application/chatovací robot koncovým uživatelům poskytovat lepší výsledky.

## <a name="extraction"></a>Extrakce
Nástroj QnA Maker je průběžně zlepšování algoritmů, které extrahují maximálně z obsahu a rozbalení seznamu souboru a stránky HTML formáty podporované. Postupujte podle [pokyny](../Concepts/data-sources-supported.md) extrakce podle typu dokumentu se extrahují z. 

Obecně platí nejčastější dotazy k stránky by měl být samostatný a ne kombinované spolu s dalšími informacemi. Produktových příruček by měl mít vymazat záhlaví a pokud možno indexovou stránku. 

## <a name="rankingmatching"></a>Porovnávání a řazení
Ujistěte se, že provádíte co nejlíp využít hodnocení funkce, které podporuje QnA Maker. To zvýší pravděpodobnost, která daný uživatelský dotaz je zodpovězen odpovídající odpověď.

### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní dotazy](../How-To/edit-knowledge-base.md) zvýšit pravděpodobnost, že shoda se dotaz uživatele. Alternativní dotazy jsou užitečné, pokud existuje více způsobů, ve kterém se dotaz na stejnou otázku. To může zahrnovat změny ve struktuře věta (například *"Je k dispozici parkovací?"* oproti *"Máte car park?"* ) nebo změny v aplikaci word – vizuální styl a slangem (například *"Hi"* oproti *"Yo"*, *"Existuje Hey!"* ).

### <a name="use-metadata-filters"></a>Použití filtrů metadat
[Metadata](../How-To/edit-knowledge-base.md) umožňuje zúžit výsledky dotazu uživatele na základě filtrů. Odpověď znalostní báze se může lišit podle značky metadat i v případě, že dotaz je stejný. Například *"kde je umístěn parkovací"* může mít různé odpovědi, pokud umístění restaurace větve se liší – to znamená, metadata jsou *umístění: Seattle* oproti *umístění: Redmond*.)

### <a name="use-synonyms"></a>Použití synonym
Zatímco některé podpora synonym v angličtině, použijte [word rozšiřuje](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) synonyma přidáte klíčová slova, které mají jiný formát (Příklad: *koupit* -> *nákupu*  nebo *netbanking* -> *net bankovnictví*. Synonyma by měl přidány na úrovni služby QnA Maker a sdílí všechny znalostních bází ve službě.

### <a name="use-distinct-words-to-differentiate-questions"></a>Používat různá slova k rozlišení dotazy
Nástroj QnA Maker porovnání a řazení algoritmy, které odpovídají uživatelský dotaz se dotaz znalostní báze knowledge base fungují nejlépe, pokud různé potřeby řeší všechny otázky. Opakování téhož slova nastaven v rozmezí dotazy snižuje pravděpodobnost, že je vybrána správná odpověď pro daný uživatelský dotaz se tato slova.

## <a name="collaborate"></a>Spolupráce
Nástroj QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) ve znalostní bázi. Uživatelé vyžadují přístup do skupiny prostředků Azure QnA Maker za účelem přístupu k znalostních bází. Některé organizace chtít externí pomocí úpravy znalostní báze knowledge base a údržba a stále mít možnost chránit přístup k jejich prostředky Azure. Tento model editoru schvalovatel dosáhnete nastavením dva identické [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a jednoho pro cyklus úpravy testování. Po dokončení testování se lze přenášet obsah znalostní báze [importu a exportu](../Tutorials/migrate-knowledge-base.md) zpracovat služba QnA Maker schvalovatele, který bude nakonec publikovat znalostní báze a aktualizujte koncový bod.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)

