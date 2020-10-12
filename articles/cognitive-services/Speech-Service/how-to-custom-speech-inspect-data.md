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
ms.date: 09/23/2020
ms.author: erhopf
ms.openlocfilehash: c55d69e99715a0c646c4e836df06cf105f9770bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319100"
---
# <a name="inspect-custom-speech-data"></a>Kontrola dat služby Custom Speech

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [data pro přípravu testu pro Custom Speech](how-to-custom-speech-test-data.md) a nahráli jste datovou sadu pro kontrolu.

Custom Speech poskytuje nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání. Z [portálu Custom Speech](https://speech.microsoft.com/customspeech)můžete přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný. Tento nástroj vám pomůže zkontrolovat kvalitu základního rozpoznávání řeči a textu od Microsoftu, prozkoumat vyškolený vlastní model nebo porovnat přepis dvou modelů.

V tomto dokumentu se dozvíte, jak vizuálně kontrolovat kvalitu základního rozpoznávání řeči a textu od Microsoftu a vlastních modelů, které jste si naučili. Naučíte se také, jak pomocí editoru online přepisu vytvořit a zpřesnit zvukové datové sady s popisky.

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

## <a name="online-transcription-editor"></a>Online editor přepisů

Editor online přepisu vám umožní snadnou práci s přepisy zvuku v Custom Speech. Hlavní případy použití editoru jsou následující: 

* Máte jenom zvuková data, ale chcete vytvořit přesnou sadu datových sad, které se budou používat v školicích modelech, ale nemusíte vytvářet přesné audio + popisky.
* Už máte zvukové a uživatelem označené datové sady, ale v přepisu jsou chyby nebo vady. Editor vám umožní rychle upravit přepisy, abyste získali nejlepší přesnost školení.

Jediným požadavkem k použití editoru přepisu je nahrání zvukových dat (buď jenom pro zvuk, nebo zvuk + přepis).

### <a name="import-datasets-to-editor"></a>Importovat datové sady do editoru

Chcete-li importovat data do editoru, nejprve přejděte do **Custom Speech > [projekt] > Editor**.

![Karta Editor](media/custom-speech/custom-speech-editor-detail.png)

V dalším kroku importujte data pomocí následujících kroků.

1. Klikněte na **importovat data** .
1. Vytvořit novou datovou sadu a zadat její popis
1. Vyberte datové sady. Podporuje se vícenásobné výběry a vy můžete vybrat jenom zvuková data a také zvuk + údaje popisku.
1. Pro data jenom zvuků můžete volitelně použít výchozí modely pro automatické generování přepisu počítače po importu do editoru.
1. Klikněte na **Importovat**.

Po úspěšném importu dat můžete kliknout na datové sady a začít upravovat.

> [!TIP]
> Datové sady můžete také importovat do editoru přímo tak, že vyberete datové sady a kliknete na **exportovat do editoru** .

### <a name="edit-transcription-by-listening-to-audio"></a>Úpravy přepisu naslouchat zvukům

Po úspěšném odeslání dat kliknutím na název každé položky zobrazíte podrobnosti o datech. K přesunu mezi jednotlivými soubory můžete použít také **předchozí** a **Další** .

Stránka podrobností obsahuje všechny segmenty v každém zvukovém souboru a můžete kliknout na požadované utterance. U každého utterance můžete přehrát zvuk a prohlédnout si přepisy, pokud najdete jakékoli chyby vložení, odstranění nebo nahrazení, upravte přepisy. Další informace o typech chyb naleznete v tématu [vyhodnocení dat](how-to-custom-speech-evaluate-data.md) .

![Stránka editoru](media/custom-speech/custom-speech-editor.png)

Po provedení úprav klikněte na tlačítko **Uložit** .

### <a name="export-datasets-from-the-editor"></a>Export datových sad z editoru

Chcete-li exportovat datové sady zpět na kartu **data** , přejděte na stránku s podrobnostmi o datech a kliknutím na tlačítko **exportovat** Exportujte všechny soubory jako novou datovou sadu. Soubory můžete také filtrovat podle času poslední úpravy, dob trvání zvuku atd. k částečnému výběru požadovaných souborů. 

![Exportovat data](media/custom-speech/custom-speech-editor-export.png)

Soubory exportované do dat budou použity jako značka – Nová datová sada a nebudou mít vliv na žádnou existující entitu dat, školení a testování.

## <a name="next-steps"></a>Další kroky

- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
- [Trénování modelu](how-to-custom-speech-train-model.md)
- [Vylepšení modelu](how-to-custom-speech-improve-accuracy.md)
- [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

- [Příprava testovacích dat pro Custom Speech](how-to-custom-speech-test-data.md)
