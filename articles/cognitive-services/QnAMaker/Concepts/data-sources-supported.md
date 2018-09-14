---
title: Zdroje dat podporované – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker můžete automaticky extrahovat páry otázka – odpověď z běžných semistrukturovaná obsahu formátů, jako jsou nejčastější dotazy a produktových příruček. Obsah lze také přidat do znalostní báze z strukturované soubory.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 0436d5015ee4b369aeead7691f87ef313259d391
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543992"
---
# <a name="data-sources"></a>Zdroje dat 
Nástroj QnA Maker můžete automaticky extrahovat páry otázka – odpověď z běžných semistrukturovaná obsahu formátů, jako jsou nejčastější dotazy a produktových příruček. Obsah lze také přidat do znalostní báze z strukturované soubory.

## <a name="plain-faq-pages"></a>Jednoduché stránky – nejčastější dotazy
Toto je nejběžnější typ stránku s nejčastějšími dotazy, ve kterém odpovědi bezprostředně následuje dotazy na stejné stránce. 

![Prostý stránku s nejčastějšími dotazy](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Nejčastější dotazy týkající se stránky s odkazy oddílu 
V tomto typu stránku s nejčastějšími dotazy otázky jsou agregovány dohromady a jsou propojeny s odpovědí, které jsou v různých částech na stejné stránce.

 ![Stránku s nejčastějšími dotazy odkazu oddílu](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Nejčastější dotazy týkající se stránky s odkazy na jiné stránky. 
Tento typ stránku s nejčastějšími dotazy je podobný část propojené stránku s nejčastějšími dotazy, s tím rozdílem, že přesměrovat odkazy na jinou stránku. Nástroj QnA Maker prochází všechny připojené stránky k extrakci příslušné odpovědi.

 ![Stránka přímý odkaz na nejčastější dotazy](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Produktových příruček

Ruční je obvykle pokyny materiál, který doprovází produktu. Umožňuje uživateli nastavit, použít, udržovat a řešit produktu. Nástroj QnA Maker zpracovává ruční, extrahuje položek a podpoložek jako dotazy a další obsah jako odpovědi. Podívejte se příklad [tady](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Extrakce funguje nejlépe na příručky, které mají tabulku obsah a/nebo indexovou stránku a Vymazat strukturu s hierarchické záhlaví.


## <a name="structured-data-format-through-file-upload"></a>Nahrání souboru formátu strukturovaných dat

Strukturované soubory například TSV, XLSX s formátovaných sloupců také se dají nahrát pro nástroj QnA Maker během vytváření znalostní báze knowledge base. Můžete také nahrát soubory **nastavení** kartu znalostní báze

| Otázka  | Odpověď  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | "Key1:Value1|Key2:Value2. |
| Question2 | Answer2 |      `Key:Value`           |
Žádné další sloupce ve zdrojovém souboru jsou ignorovány.

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat pomocí importu
Import znalostní báze nahradí obsah znalostní báze existující. Strukturované TSV soubor, který obsahuje informace o zdroji dat vyžaduje import. Tyto informace pomáhají QnA Maker skupině páry otázka – odpověď a atribut pro konkrétní datové zdroje.

| Otázka  | Odpověď  | Zdroj| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|"Key1:Value1|Key2:Value2. |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="editorial"></a>Redakční
Pokud nemáte existující obsah k vyplnění znalostní báze, můžete také přidat je pomocí redakčních úprav v nástroje QnA Maker znalostní bázi. Zjistěte, jak aktualizovat znalostní báze [tady](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled nástroje QnA Maker](../Overview/overview.md)
