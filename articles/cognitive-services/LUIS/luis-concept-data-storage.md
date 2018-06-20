---
title: Pochopení ukládání dat v LEOŠ - Azure | Microsoft Docs
description: Zjistěte, jak jsou uložena data znalosti jazyka (LEOŠ)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: f235c787e7d2064696e5421219a297d914b5882d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266001"
---
# <a name="data-storage-and-removal"></a>Úložiště dat a odebrání
LEOŠ ukládá data šifrovaná v úložišti dat Azure odpovídající oblasti určený klíčem. Tato data jsou ukládána po dobu 30 dnů. 

## <a name="export-and-delete-app"></a>Export a odstranit aplikaci
Uživatelé mají plnou kontrolu nad [export](create-new-app.md#export-app) a [odstraňování](create-new-app.md#delete-app) aplikace. 

## <a name="utterances-in-an-intent"></a>Utterances v záměrem
Odstranit utterances příklad používá pro školení [LEOŠ][LUIS]. Pokud odstraníte utterance příklad z vaší aplikace LEOŠ, je odebrán z LEOŠ webové služby a není k dispozici pro export.

## <a name="utterances-in-review"></a>Utterances probíhající kontrola
Utterances můžete odstranit ze seznamu utterances uživatele, které LEOŠ navrhuje v  **[kontrolní koncový bod utterances stránku](label-suggested-utterances.md)**. Z tohoto seznamu odstraníte utterances sdělovat navržena, ale nedojde k jejich odstranění z protokolů.

## <a name="accounts"></a>Účty
Pokud účet odstraníte, se odstraní všechny aplikace, spolu s jejich příklad utterances a protokoly. Data se uchovávají 60 dnů před účet a data jsou trvale odstraněny.

Odstranění účtu je k dispozici **nastavení** stránky. Vyberte název účtu v pravém horním navigačním panelu zobrazíte **nastavení** stránky.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data nečinnosti jako vypršela platnost předplatného
Pro účely uchovávání dat a odstranění, může aplikace neaktivní LEOŠ v _společnosti Microsoft uvážení_ zacházet jako vypršela platnost předplatného. Aplikace je považováno za neaktivní, pokud splňuje následující kritéria za posledních 90 dnů: 

* Zaznamenala **žádné** volání provedená do ní.
* Nebyl změněn.
* Není aktuální klíč přiřadili k němu.
* Nebyly uživatele, přihlaste se k němu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o exportu a odstranění aplikace](create-new-app.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions