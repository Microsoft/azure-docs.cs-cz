---
title: Kontrola kvality dat pro službu Custom Speech – Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech poskytuje nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání. Z portálu Custom Speech můžete přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný.  Tento nástroj umožňuje rychlou kontrolu kvality vlastního modelu rozpoznávání řeči (Speech-to-text) a vyškolený vlastní model bez nutnosti přepisovatovat data zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b58f9c17995128091b5c4badd228356dbacc6ae9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562840"
---
# <a name="inspect-custom-speech-data"></a>Kontrola Custom Speech dat

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [data pro přípravu testu pro Custom Speech](how-to-custom-speech-test-data.md) a nahráli jste datovou sadu pro kontrolu.

Custom Speech poskytuje nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání. Z portálu Custom Speech můžete přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný. Tento nástroj vám umožní rychle zkontrolovat kvalitu základního rozpoznávání řeči a textu od Microsoftu, aniž by se musel přepisovat libovolná zvuková data.

V tomto dokumentu se dozvíte, jak vizuálně kontrolovat kvalitu modelu pomocí školicích dat, která jste předtím nahráli.

Na této stránce se dozvíte, jak vizuálně kontrolovat kvalitu základního rozpoznávání řeči a textu od Microsoftu nebo vlastního modelu, který jste si naučili. Data, která jste nahráli na kartě **data** , budete používat k testování.

## <a name="create-a-test"></a>Vytvořit test

Při vytváření testu postupujte podle těchto pokynů:

1. **> Custom Speech > testování**, přejděte na text na řeč.
2. Klikněte na **Přidat test**.
3. Vyberte možnost **kontrolovat kvalitu (jenom zvuková data)** . Dejte testu název, popis a vyberte vaši zvukovou datovou sadu.
4. Vyberte až dva modely, které chcete testovat.
5. Klikněte na možnost **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat modely vedle sebe.

## <a name="side-by-side-model-comparisons"></a>Porovnání modelů souběžného sdílení

Po úspěšném stavu testu klikněte na název testovací položky a zobrazí se podrobnosti testu. Tato stránka podrobností obsahuje seznam všech projevy ve vaší datové sadě, které označují výsledky rozpoznávání dvou modelů společně s přepisem z odeslané datové sady.

Chcete-li zkontrolovat souběžné porovnání, můžete přepínat různé typy chyb včetně vložení, odstranění a nahrazování. Díky poslechu zvuku a porovnávání výsledků rozpoznávání v jednotlivých sloupcích (ukazující přepis a výsledky dvou typů řeči na text) se můžete rozhodnout, který model vyhovuje vašim potřebám a kde jsou nutná vylepšení.

Kontrola kvality testování je užitečná, pokud chcete ověřit, jestli je kvalita koncového bodu rozpoznávání řeči pro aplikaci dostačující.  V případě míry přesnosti, která vyžaduje přepisu zvuk, postupujte podle pokynů uvedených v části [vyhodnocení přesnosti](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Další postup

* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Výuka modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

* [Příprava testovacích dat pro Custom Speech](how-to-custom-speech-test-data.md)
