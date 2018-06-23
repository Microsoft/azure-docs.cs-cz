---
title: Zdroje dat podporované - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Podporované zdroje dat
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343271"
---
# <a name="data-sources"></a>Zdroje dat 
QnA Maker může automaticky extrahovat páry otázku odpovědí z běžných částečně strukturovaných obsahu formáty například nejčastější dotazy a příručky k produktům. Obsah lze také přidat do znalostní báze z strukturovaných souborů.

## <a name="plain-faq-pages"></a>Nešifrovaná stránky – nejčastější dotazy
Toto je nejběžnější typ stránka s nejčastějšími dotazy, ve kterém odpovědi bezprostředně následující dotazy na stejné stránce. 

![Nešifrovaná stránka nejčastější dotazy](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Nejčastější dotazy týkající se stránky s odkazy na části 
V tomto typu stránka s nejčastějšími dotazy otázky jsou agregovat společně a jsou propojeny s odpovědí, které jsou v různých oddílů na stejné stránce.

 ![Stránka části Nejčastější dotazy týkající se propojení](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Nejčastější dotazy týkající se stránky s odkazy na jiné stránky. 
Tento typ stránka s nejčastějšími dotazy je podobná nejčastější dotazy týkající se na část propojené stránce s tím rozdílem, že odkazy přesměrovat na jinou stránku. QnA Maker prochází všechny propojené stránky k extrakci odpovídající odpovědi.

 ![Stránka přímý odkaz na nejčastější dotazy](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Příručky k produktům

Ruční je obvykle materiálu pokyny, které doprovází produktu. Pomáhá uživatelům nastavit, použijte, udržovat a řešení potíží s produktem. Jakmile QnA Maker zpracovává ruční, extrahuje záhlaví a podpoložek jako dotazy a další obsah, jako odpovědi. Prohlédněte si příklad [zde](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Extrakce použít v příruček, které mají tabulku obsah nebo indexovou stránku a Vymazat strukturu s hierarchické záhlaví.


## <a name="structured-data-format-through-file-upload"></a>Formát strukturovaných dat prostřednictvím nahrávání souborů

Strukturované soubory, jako jsou TSV, XLSX s formátovaný sloupce můžete také nahrán do QnA Maker během vytváření znalostní báze knowledge base. Můžete také nahrát soubory z **nastavení** ve znalostní bázi

| Otázka  | Odpověď  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | `Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 |      `Key:Value`           |
Žádné další sloupce ve zdrojovém souboru budou ignorovány.

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat prostřednictvím importu
Import znalostní bázi knowledge base nahradí obsah existující znalostní bázi knowledge base. Strukturované TSV soubor, který obsahuje informace o zdroji dat vyžaduje import. Tyto informace pomáhají QnA Maker skupiny páry otázku odpovědí a atribut je určitý zdroj dat.

| Otázka  | Odpověď  | Zdroj| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|`Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="editorial"></a>Redakční
Pokud nemáte existující obsah k naplnění znalostní bázi knowledge base, můžete také přidat jejich redakčně v QnA Maker Knowledge base. Zjistěte, jak aktualizovat znalostní báze [zde](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled QnA Maker](../Overview/overview.md)