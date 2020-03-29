---
title: Úložiště dat – LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS ukládá data zašifrovaná v úložišti dat Azure, které odpovídá oblasti určené klíčem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220014"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Úložiště a odebrání dat v jazykových znalostech (LUIS) Cognitive Services
Služba LUIS ukládá data zašifrovaná v úložišti dat Azure, které odpovídá oblasti určené klíčem. Tato data jsou uložena po dobu 30 dnů. 

## <a name="export-and-delete-app"></a>Export a odstranění aplikace
Uživatelé mají plnou kontrolu nad [exportem](luis-how-to-start-new-app.md#export-app) a [odstraněním](luis-how-to-start-new-app.md#delete-app) aplikace. 

## <a name="utterances"></a>Projevy

Projevy mohou být uloženy na dvou různých místech. 

* Během **procesu vytváření**projevy jsou vytvořeny a uloženy v záměru. Projevy v záměrech jsou vyžadovány pro úspěšnou aplikaci LUIS. Jakmile je aplikace publikována a přijímá dotazy v koncovém bodě, řetězec `log=false`dotazu požadavku koncového bodu , určuje, zda je uložen projev koncového bodu. Pokud je uložen koncový bod, stane se součástí projevy aktivní učení nalezené v části **Sestavení** portálu, v části **Zkontrolovat koncový bod projevy.** 
* Při **kontrole promluv y koncového bodu**a přidání utterance k záměru utterance již není uložen jako součást projevy koncového bodu, které mají být přezkoumány. Přidá se k záměrům aplikace. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Odstranění ukázkových projevů ze záměru

Odstranit příklad projevy používané pro školení [LUIS](luis-reference-regions.md). Pokud odstraníte příklad utterance z aplikace LUIS, je odebrán z webové služby LUIS a není k dispozici pro export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Odstranění projevů v recenzi z aktivního učení

Můžete odstranit projevy ze seznamu projevy uživatelů, které luis navrhuje na **[stránce Zkontrolovat projevy koncového bodu](luis-how-to-review-endpoint-utterances.md)**. Odstranění projevy z tohoto seznamu zabraňuje jejich navržení, ale neodstraní je z protokolů.

Pokud nechcete aktivní učení projevy, můžete [zakázat aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Zakázání aktivní učení také zakáže protokolování.

### <a name="disable-logging-utterances"></a>Zakázat protokolování promluv
[Zakázání aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning) je zakáže protokolování.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Odstranění účtu
Pokud odstraníte účet, budou odstraněny všechny aplikace spolu s jejich příklady projevy a protokoly. Data jsou uchovávána po dobu 60 dnů před trvalým odstraněním účtu a dat.

Odstranění účtu je k dispozici na stránce **Nastavení.** Vyberte název účtu v pravém horním navigačním panelu a přejděte na stránku **Nastavení.**

## <a name="data-inactivity-as-an-expired-subscription"></a>Nečinnost dat jako předplatné s ukončenou platností
Pro účely uchovávání a odstraňování dat může být neaktivní aplikace LUIS _dle uvážení společnosti Microsoft_ považována za předplatné, jehož platnost vypršela. Aplikace je považována za neaktivní, pokud splňuje následující kritéria za posledních 90 dní: 

* Nikdo k tomu **nevolal.**
* Nebylzměněn.
* Nemá přiřazen aktuální klíč.
* Nemá uživatel přihlásit k němu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o exportu a odstranění aplikace](luis-how-to-start-new-app.md)
