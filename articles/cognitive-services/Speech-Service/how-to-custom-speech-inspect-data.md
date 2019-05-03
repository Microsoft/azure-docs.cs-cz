---
title: Kontrola kvality dat pro Custom Speech – hlasové služby
titlesuffix: Azure Cognitive Services
description: Vlastní řeč poskytuje nástroje, které umožňují vizuálně zkoumat kvalitu rozpoznávání modelu porovnáním zvukových dat se odpovídající výsledek rozpoznání. Z portálu Custom Speech můžete přehrávat nahrané zvuk a určení, zda je výsledek rozpoznání zadaná správná.  Tento nástroj umožňuje rychle kontrolu kvality modelu řeči na text standardních hodnot od Microsoftu nebo vlastní trénovaného modelu bez nutnosti přepisy všechna zvuková data.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025815"
---
# <a name="inspect-custom-speech-data"></a>Kontrola dat Custom Speech

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [připravit testovací data pro Custom Speech](how-to-custom-speech-test-data.md) a nahráli datové sady pro kontrolu.

Vlastní řeč poskytuje nástroje, které umožňují vizuálně zkoumat kvalitu rozpoznávání modelu porovnáním zvukových dat se odpovídající výsledek rozpoznání. Z portálu Custom Speech můžete přehrávat nahrané zvuk a určení, zda je výsledek rozpoznání zadaná správná. Tento nástroj umožňuje rychle kontrolu kvality modelu řeči na text standardních hodnot od Microsoftu nebo vlastní trénovaného modelu bez nutnosti přepisy všechna zvuková data.

V tomto dokumentu se dozvíte postupy vizuálně zkoumat kvality modelu pomocí trénovacích dat, který jste nahráli dříve.

Na této stránce se dozvíte, jak vizuálně zkoumat kvality modelu řeči na text směrného plánu společnosti Microsoft a/nebo vlastního modelu, který jste školení. Použijete dat nahrávaných do **Data** kartu pro účely testování.

## <a name="create-a-test"></a>Vytvoření testu

Postupujte podle těchto pokynů pro vytvoření testu:

1. Přejděte do **Speech to text > Custom Speech > testování**.
2. Klikněte na tlačítko **přidat Test**.
3. Vyberte **kontrola kvality (pouze se zvukem data)**. Zadejte název, popis, testu a vyberte zvukové datové sady.
4. Vyberte až dva modely, které chcete testovat.
5. Klikněte na možnost **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat modelů vedle sebe.

## <a name="side-by-side-model-comparisons"></a>Porovnání modelů vedle sebe

Pokud je stav testu *Succeeded*, klikněte na název položky testu můžete zobrazit podrobnosti o testu. Tato stránka podrobností uvádí všechny projevy ve vaší datové sadě, určující rozpoznávání výsledky ze dvou modelů vedle přepis z odeslané datové sady.

Chcete-li zkontrolovat porovnání vedle sebe, můžete přepínat různých typů chyb včetně vkládání, odstraňování a nahrazení. Poslech Audio nahrávky patřící a porovnáním výsledky rozpoznávání v jednotlivých sloupcích (včetně lidských s názvem bez přípony určené k transkripci a výsledky dva modely řeči na text), můžete rozhodnout, které model bude vyhovovat vašim potřebám a kde jsou potřeba vylepšení.

Kontrola kvality testování je vhodné ověřit, jestli kvality koncový bod rozpoznání řeči je dostatečná pro aplikaci.  Objektivní změření přesnosti, které vyžadují přepisu zvuku postupujte podle pokynů uvedených v testování: Vyzkoušejte přesnost.

## <a name="next-steps"></a>Další postup

* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)
* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další materiály

* [Připravte testovací data pro Custom Speech](how-to-custom-speech-test-data.md)
