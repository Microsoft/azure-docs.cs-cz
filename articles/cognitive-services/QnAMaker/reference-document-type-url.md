---
title: Typy adres URL podporované pro import – QnA Maker
description: Zjistěte, jak se typy adres URL používají k importu a vytváření párů QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804312"
---
# <a name="urls-supported-for-importing-documents"></a>Adresy URL podporované pro import dokumentů

Zjistěte, jak se typy adres URL používají k importu a vytváření párů QnA.

## <a name="faq-urls"></a>Adresy URL v častých dotazech

QnA Maker může podporovat faq webové stránky ve 3 různých formách:

* Prosté stránky s nejčastějšími dotazy
* Nejčastější dotazy stránky s odkazy
* Stránky s nejčastějšími dotazy s domovskou stránkou témat

### <a name="plain-faq-pages"></a>Prosté stránky s nejčastějšími dotazy

Toto je nejběžnější typ stránky faq, ve které odpovědi okamžitě následují po otázkách na stejné stránce.

Níže je uveden příklad prosté stránky FAQ:

![Příklad stránky nejčastějších dotazů pro znalostní bázi](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Nejčastější dotazy stránky s odkazy

Na tomto typu stránky s nejčastějšími dotazy jsou otázky agregovány společně a jsou propojeny s odpověďmi, které jsou buď v různých částech stejné stránky, nebo na různých stránkách.

Níže je uveden příklad stránky s nejčastějšími dotazy s odkazy v oddílech, které jsou na stejné stránce:

 ![Příklad stránky nejčastějších dotazů odkazu na oddíly pro znalostní bázi](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Stránka Nadřazená témata odkazuje na podřízené stránky odpovědí

Tento typ faq má témata stránku, kde každé téma je propojens odpovídající sadu otázek a odpovědí na jiné stránce. QnA Maker prochází všechny odkazované stránky extrahovat odpovídající otázky & odpovědi.

Níže je uveden příklad stránky Témata s odkazy na sekce nejčastějších dotazů na různých stránkách.

 ![Příklad stránky s nejčastějšími dotazy pro znalostní bázi](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Adresy URL podpory

QnA Maker může zpracovávat polostrukturované webové stránky podpory, jako jsou webové články, které by popisovalo, jak provést daný úkol, jak diagnostikovat a vyřešit daný problém a jaké jsou osvědčené postupy pro daný proces. Extrakce funguje nejlépe u obsahu, který má jasnou strukturu s hierarchickými nadpisy.

> [!NOTE]
> Extrakce pro články podpory je nová funkce a je v raných fázích. Funguje nejlépe pro jednoduché stránky, které jsou dobře strukturované a neobsahují složitá záhlaví / zápatí.

![QnA Maker podporuje extrakci z polostrukturovaných webových stránek, kde je jasná struktura prezentována s hierarchickými nadpisy](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)