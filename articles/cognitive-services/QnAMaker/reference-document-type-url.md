---
title: Typy adres URL podporované pro import-QnA Maker
description: Pochopení způsobu, jakým jsou typy adres URL použity pro import a vytvoření párů QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91776693"
---
# <a name="urls-supported-for-importing-documents"></a>Adresy URL podporované pro import dokumentů

Pochopení způsobu, jakým jsou typy adres URL použity pro import a vytvoření párů QnA.

## <a name="faq-urls"></a>Adresy URL pro nejčastější dotazy

QnA Maker může podporovat webové stránky nejčastějších dotazů ve třech různých formulářích:

* Jednoduché stránky nejčastějších dotazů
* Stránky nejčastějších dotazů s odkazy
* Stránky s nejčastějšími dotazy s domovskou stránkou témat

### <a name="plain-faq-pages"></a>Jednoduché stránky nejčastějších dotazů

Toto je nejběžnější typ stránky s nejčastějšími dotazy, ve kterém odpovědi okamžitě následují na stejné stránce.

Níže je uveden příklad jednoduché stránky Nejčastější dotazy:

![Příklad stránky s běžnými nejčastějšími dotazy pro znalostní bázi](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Stránky nejčastějších dotazů s odkazy

V tomto typu stránky Nejčastější dotazy se otázky agreguje společně a jsou propojeny s odpověďmi, které jsou buď v různých částech stejné stránky, nebo na různých stránkách.

Níže je uveden příklad stránky Nejčastější dotazy s odkazy v sekcích, které jsou na stejné stránce:

 ![Příklad stránky s odkazy na oddíly pro znalostní bázi Knowledge Base](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Nadřazená témata odkazují na stránky s podřízenými odpověďmi.

Tento typ nejčastějších dotazů obsahuje stránku s tématy, kde jsou jednotlivá témata propojena s odpovídající sadou otázek a odpovědí na jiné stránce. QnA Maker prochází všechny propojené stránky a extrahuje příslušné otázky & odpovědi.

Níže je uveden příklad stránky témata s odkazy na části Nejčastější dotazy na různých stránkách.

 ![Příklad stránky s přímým odkazem pro znalostní bázi Knowledge Base](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Adresy URL podpory

QnA Maker může zpracovat částečně strukturované webové stránky podpory, jako jsou například webové články, které popisují, jak provést danou úlohu, jak diagnostikovat a vyřešit daný problém a jaké jsou osvědčené postupy pro daný proces. Extrakce funguje nejlépe na obsahu, který má jasně strukturu s hierarchickými nadpisy.

> [!NOTE]
> Extrakce článků podpory je nová funkce a je v rané fázi. Funguje nejlépe pro jednoduché stránky, které jsou dobře strukturované a neobsahují složitá záhlaví a zápatí.

![QnA Maker podporuje extrakci z částečně strukturovaných webových stránek tam, kde je k dispozici jasná struktura s hierarchickými nadpisy.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)