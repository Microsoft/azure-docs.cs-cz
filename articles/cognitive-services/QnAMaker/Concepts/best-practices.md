---
title: Osvědčené postupy služby kognitivní - QnA Maker - Azure | Microsoft Docs
description: Použijte tyto doporučené postupy pro zlepšení znalostní báze a vaše aplikace/chat robota koncovým uživatelům poskytovat lepší výsledky.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343879"
---
# <a name="best-practices"></a>Osvědčené postupy
[Znalostní báze knowledge base životního cyklu](../Concepts/development-lifecycle-knowledge-base.md) vás o tom, jak můžete spravovat KB začátku do konce. Použijte tyto doporučené postupy pro zlepšení znalostní báze a vaše aplikace/chat robota koncovým uživatelům poskytovat lepší výsledky.

## <a name="extraction"></a>Extrakce
QnA Maker je neustále zlepšení algoritmy, které extrahovat QnAs z obsahu a rozbalení seznamu souboru a podporované formáty stránku HTML. Postupujte podle [pokyny](../Concepts/data-sources-supported.md) extrakce založeného na typ dokumentu vaší se extrahují z. 

Obecně platí nejčastější dotazy týkající se stránky musí být samostatné a není kombinovaný s jinými informacemi. Příručky k produktům měli vymazat záhlaví a pokud možno indexovou stránku. 

## <a name="rankingmatching"></a>Hodnocení nebo odpovídající
Zajistěte, aby že se co nejlepší využívání funkce hodnocení, které podporuje QnA Maker. Tím se zvyšuje pravděpodobnost, je zodpovězen dotaz daného uživatele k odpovědi.

### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní otázky](../How-To/edit-knowledge-base.md) zvýšit pravděpodobnost shody s dotazem uživatele. Alternativní otázky jsou užitečné, pokud existuje více způsobů, ve kterém může zobrazit výzva na stejné otázku. To může zahrnovat změny ve struktuře větu (například *"Je k dispozici parkovací?"* porovnání *"Máte car parku?"* ) nebo změny v aplikaci word-style a slang (například *"Hi"* versus *"Yo"*, *"Existuje blogu Hey!"* ).

### <a name="use-metadata-filters"></a>Použití filtrů metadat
[Metadata](../How-To/edit-knowledge-base.md) přidává možnost zúžit výsledky dotazu uživatele podle filtry. I v případě, že dotaz je stejná, se může lišit podle u značky metadata odpovědí znalostní báze knowledge base. Například *", kde je umístěn parkovací"* může mít různé odpovědí, pokud umístění restaurace větev se liší – metadat je *umístění: Praha* versus *umístění: Redmond*.)

### <a name="use-synonyms"></a>Použít synonyma.
I když se některé podporují synonyma v anglickém jazyce, použít [aplikace word mění](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) přidat synonyma do klíčová slova, které mají jiný formát (Příklad: *koupit* -> *nákupu*  nebo *netbanking* -> *net bankovní*. Synonyma měli přidat na úrovni služby QnA Maker a sdílet všechny znalostních bází ve službě.

### <a name="use-distinct-words-to-differentiate-questions"></a>Použijte k rozlišení otázky různá slova
Pokud každou otázku řeší různé potřeby se nejlépe fungovat QnA Maker shody a pořadí algoritmy, které odpovídají dotazu uživatele s otázkou znalostní báze knowledge base. Opakování stejné slov mezi otázky snižuje pravděpodobnost pravé odpovědi vybraná pro dotaz daného uživatele s tato slova.

## <a name="collaborate"></a>Spolupráce
Umožňuje uživatelům QnA Maker [spolupracovat](../How-to/collaborate-knowledge-base.md) na bázi knowledge base. Uživatelé při přístupu k znalostních bází požadují přístup do skupiny prostředků Azure QnA Maker. Některé organizace chtít externí úpravy znalostní báze knowledge base a údržby a přesto být schopni chránit přístup k jejich prostředkům Azure. Tento model editor schvalovatel, můžete provést nastavení dva identické [QnA Maker služby](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a určení jeden cyklus testování upravit. Po dokončení testování se dá přenést obsah znalostní báze knowledge base [importu exportu](../Tutorials/migrate-knowledge-base.md) procesy ve službě QnA Maker schvalovatel, kterého se nakonec publikuje znalostní bázi knowledge base a aktualizaci koncového bodu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Upravit znalostní bázi knowledge base](../How-to/edit-knowledge-base.md)

