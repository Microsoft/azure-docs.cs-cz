---
title: Typy adres URL podporované pro import-QnA Maker
description: Pochopení způsobu, jakým jsou typy adres URL použity pro import a vytvoření QnA sad.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651825"
---
# <a name="urls-supported-for-importing-documents"></a>Adresy URL podporované pro import dokumentů

Pochopení způsobu, jakým jsou typy adres URL použity pro import a vytvoření QnA sad.

## <a name="faq-urls"></a>Nejčastější dotazy týkající se adresy URL

QnA Maker může podporovat webové stránky nejčastějších dotazů ve třech různých formulářích:

* Jednoduché stránky – nejčastější dotazy
* Nejčastější dotazy týkající se stránky s odkazy
* Stránky s nejčastějšími dotazy s domovskou stránkou témat

### <a name="plain-faq-pages"></a>Jednoduché stránky – nejčastější dotazy

Toto je nejběžnější typ stránku s nejčastějšími dotazy, ve kterém odpovědi bezprostředně následuje dotazy na stejné stránce.

Níže je příklad prostý stránku s nejčastějšími dotazy:

![Prostý příklad stránky – nejčastější dotazy pro znalostní báze](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Nejčastější dotazy týkající se stránky s odkazy

V tomto typu stránku s nejčastějšími dotazy otázky jsou agregovány dohromady a jsou propojeny s odpovědí, které jsou v různých částech na stejné stránce nebo v různých stránkách.

Níže je příklad stránky – nejčastější dotazy s odkazy v části, které jsou na stejné stránce:

 ![Příklad stránky nejčastější dotazy týkající se propojení části pro znalostní báze](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Nadřazená témata odkazují na stránky s podřízenými odpověďmi.

Tento typ nejčastějších dotazů obsahuje stránku s tématy, kde jsou jednotlivá témata propojena s odpovídající sadou otázek a odpovědí na jiné stránce. QnA Maker prochází všechny propojené stránky a extrahuje příslušné otázky & odpovědi.

Níže je uveden příklad stránky témata s odkazy na části Nejčastější dotazy na různých stránkách.

 ![Příklad stránky přímý odkaz na nejčastější dotazy pro znalostní báze](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Adresy URL podpory

QnA Maker může zpracovat částečně strukturované webové stránky podpory, jako jsou například webové články, které popisují, jak provést danou úlohu, jak diagnostikovat a vyřešit daný problém a jaké jsou osvědčené postupy pro daný proces. Extrakce funguje nejlépe na obsahu, který má jasně strukturu s hierarchickými nadpisy.

> [!NOTE]
> Extrakce článků podpory je nová funkce a je v rané fázi. Funguje nejlépe pro jednoduché stránky, které jsou dobře strukturované a neobsahují složitá záhlaví a zápatí.

![QnA Maker podporuje extrakci z částečně strukturovaných webových stránek tam, kde je k dispozici jasná struktura s hierarchickými nadpisy.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)