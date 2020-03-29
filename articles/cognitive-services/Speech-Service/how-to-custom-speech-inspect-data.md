---
title: Kontrola kvality dat pro vlastní řeč - služba řeč
titleSuffix: Azure Cognitive Services
description: Vlastní řeč poskytuje nástroje, které umožňují vizuálně zkontrolovat kvalitu rozpoznávání modelu porovnáním zvukových dat s odpovídajícím výsledkem rozpoznávání. Můžete přehrát nahraný zvuk a zjistit, zda je zadaný výsledek rozpoznávání správný.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806075"
---
# <a name="inspect-custom-speech-data"></a>Kontrola vlastních dat řeči

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [připravit testovací data pro vlastní řeč](how-to-custom-speech-test-data.md) a nahráli datovou sadu pro kontrolu.

Vlastní řeč poskytuje nástroje, které umožňují vizuálně zkontrolovat kvalitu rozpoznávání modelu porovnáním zvukových dat s odpovídajícím výsledkem rozpoznávání. Na [portálu Vlastní řeč](https://speech.microsoft.com/customspeech)můžete přehrát nahraný zvuk a zjistit, zda je zadaný výsledek rozpoznávání správný. Tento nástroj umožňuje rychle kontrolovat kvalitu základního modelu řeči na text společnosti Microsoft nebo trénovaného vlastního modelu bez nutnosti přepisovat žádná zvuková data.

V tomto dokumentu se dozvíte, jak vizuálně zkontrolovat kvalitu modelu pomocí trénovacích dat, která jste dříve nahráli.

Na této stránce se dozvíte, jak vizuálně zkontrolovat kvalitu základního modelu převodu řeči na text společnosti Microsoft nebo vlastního modelu, který jste trénovali. Data, která jste nahráli na kartu **Data,** použijete k testování.

## <a name="create-a-test"></a>Vytvoření testu

Podle těchto pokynů vytvořte test:

1. Přihlaste se k [portálu Vlastní řeč](https://speech.microsoft.com/customspeech).
2. Přejděte **na možnost Převod řeči na text > vlastní testování > řeči**.
3. Klepněte na **tlačítko Přidat test**.
4. Vyberte **Zkontrolovat kvalitu (data pouze zvuku).** Pojmenujte test, popis a vyberte zvukovou datovou sadu.
5. Vyberte až dva modely, které chcete otestovat.
6. Klikněte na **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat modely vedle sebe.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Porovnání modelů vedle sebe

Pokud je stav testu _úspěšný_, klepnutím na název testovací položky zobrazíte podrobnosti o testu. Tato stránka podrobností obsahuje seznam všech projevy v datové sadě, označující výsledky rozpoznávání dvou modelů vedle přepisu z odeslané datové sady.

Chcete-li zkontrolovat porovnání vedle sebe, můžete přepnout různé typy chyb, včetně vkládání, odstraňování a nahrazení. Poslechem zvuku a porovnáním výsledků rozpoznávání v každém sloupci (zobrazujícím přepis s lidským označením a výsledky dvou modelů převodu řeči na text) se můžete rozhodnout, který model vyhovuje vašim potřebám a kde jsou zapotřebí vylepšení.

Kontrola testování kvality je užitečné k ověření, pokud je pro aplikaci dostatečná kvalita koncového bodu rozpoznávání řeči. Pro objektivní míru přesnosti, vyžadující přepisování zvuku, postupujte podle pokynů v [části Vyhodnotit přesnost](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Další kroky

- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
- [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
- [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

- [Příprava testovacích dat pro vlastní řeč](how-to-custom-speech-test-data.md)
