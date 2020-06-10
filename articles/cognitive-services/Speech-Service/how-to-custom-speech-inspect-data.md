---
title: Kontrola kvality dat pro službu Custom Speech – Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech poskytuje nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání. Můžete přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: e871d2c8e0fe00fa7db3144a787447163c82e62d
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629040"
---
# <a name="inspect-custom-speech-data"></a>Kontrola dat služby Custom Speech

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [data pro přípravu testu pro Custom Speech](how-to-custom-speech-test-data.md) a nahráli jste datovou sadu pro kontrolu.

Custom Speech poskytuje nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání. Z [portálu Custom Speech](https://speech.microsoft.com/customspeech)můžete přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný. Tento nástroj vám pomůže zkontrolovat kvalitu základního rozpoznávání řeči a textu od Microsoftu, prozkoumat vyškolený vlastní model nebo porovnat přepis dvou modelů.

V tomto dokumentu se dozvíte, jak vizuálně kontrolovat kvalitu modelu pomocí školicích dat, která jste předtím nahráli.

Na této stránce se dozvíte, jak vizuálně kontrolovat kvalitu základního rozpoznávání řeči a textu od Microsoftu nebo vlastního modelu, který jste si naučili. Data, která jste nahráli na kartě **data** , budete používat k testování.

## <a name="create-a-test"></a>Vytvořit test

Při vytváření testu postupujte podle těchto pokynů:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech).
2. Přejít na **text > řeči Custom Speech > [název projektu] > testování**.
3. Klikněte na **Přidat test**.
4. Vyberte možnost **kontrolovat kvalitu (jenom zvuková data)**. Dejte testu název, popis a vyberte vaši zvukovou datovou sadu.
5. Vyberte až dva modely, které chcete testovat.
6. Klikněte na **Vytvořit**.

Po úspěšném vytvoření testu uvidíte, jak model transcribes zvukovou datovou sadu, kterou jste zadali, nebo porovnat výsledky dvou modelů vedle sebe.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Porovnání modelů souběžného sdílení

Po _úspěšném_stavu testu klikněte na název testovací položky a zobrazí se podrobnosti testu. Tato stránka podrobností obsahuje seznam všech projevy ve vaší datové sadě a zobrazuje výsledky rozpoznávání dvou modelů, které porovnáváte.

Chcete-li zkontrolovat souběžné porovnání, můžete přepínat různé typy chyb včetně vložení, odstranění a nahrazování. Díky poslechu zvuku a porovnávání výsledků rozpoznávání v jednotlivých sloupcích (ukazující přepis a výsledky dvou typů řeči na text) se můžete rozhodnout, který model vyhovuje vašim potřebám a kde jsou nutná vylepšení.

Souběžné testování modelu je užitečné k ověření, který model rozpoznávání řeči je pro aplikaci nejvhodnější. V případě míry přesnosti, která vyžaduje přepisu zvuk, postupujte podle pokynů uvedených v části [vyhodnocení přesnosti](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Další kroky

- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
- [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
- [Vylepšení modelu](how-to-custom-speech-improve-accuracy.md)
- [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

- [Příprava testovacích dat pro Custom Speech](how-to-custom-speech-test-data.md)
