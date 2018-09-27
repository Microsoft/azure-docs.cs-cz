---
title: Moderování textu s použitím API pro moderování textu – Content Moderator
titlesuffix: Azure Cognitive Services
description: Vyzkoušejte si moderování textu s použitím rozhraní API pro moderování textu v konzole online.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 95fd5507287a9294f4fca6af9cc5f01f0ea9fe1c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219783"
---
# <a name="moderate-text-from-the-api-console"></a>Moderování textu z konzoly pro rozhraní API

Použití [rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) v Azure Content Moderator kontrolovat textový obsah. Operace prohledá obsah pro vulgárních výrazů a porovnává obsah proti vlastní a sdílené seznamů zakázaných položek.


## <a name="get-your-api-key"></a>Získejte klíč k rozhraní API
Předtím, než můžete vyzkoušet rozhraní API v konzole online, budete potřebovat klíč předplatného. To je umístěn na **nastavení** kartě **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [přehled](overview.md).

## <a name="navigate-to-the-api-reference"></a>Přejděte na reference k rozhraní API
Přejděte [reference k rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  **Text – obrazovky** otevře se stránka.

## <a name="open-the-api-console"></a>Otevřete konzolu pro rozhraní API
Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Text – výběr oblasti obrazovky stránky](images/test-drive-region.png)

  **Text – obrazovky** otevře se konzola rozhraní API.

## <a name="select-the-inputs"></a>Vyberte vstupy

### <a name="parameters"></a>Parametry
Vyberte parametry dotazu, které chcete použít na obrazovce text. V tomto příkladu použijte výchozí hodnotu pro **jazyka**. Můžete také necháte prázdné vzhledem k tomu, že operace bude automaticky rozpoznávat pravděpodobně jazyka jako součást jeho spuštění.

> [!NOTE]
> Pro **jazyk** parametr, přiřaďte `eng` nebo toto podokno nechat prázdné najdete v článku s počítače **klasifikace** odpovědí (funkce preview). **Tato funkce podporuje pouze angličtinu**.
>
> Pro **vulgárních výrazů podmínek** detekce, použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo nechte prázdné.

Pro **automatických oprav**, **identifikovatelné osobní údaje**, a **klasifikaci (preview)** vyberte **true**. Nechte **ListId** prázdné pole.

  ![Text – parametry dotazu obrazovky konzoly](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ obsahu
Pro **Content-Type**, vyberte typ obsahu, které chcete na obrazovku. V tomto příkladu použijte výchozí **text/plain** typ obsahu. V **Ocp-Apim-Subscription-Key** zadejte váš klíč předplatného.

### <a name="sample-text-to-scan"></a>Ukázkový text vyhledávání
V **text žádosti** zadejte nějaký text. Následující příklad ukazuje úmyslné překlep v textu.

> [!NOTE]
> Neplatné číslo sociálního pojištění v následující ukázkový text je úmyslné. Slouží k předání ukázkové vstupní a výstupní formát.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Funkce klasifikace textu

V následujícím příkladu se zobrazí odpověď klasifikace Content Moderator text s podporou počítače. To pomáhá detekovat potenciálně nežádoucí obsah. Označené příznakem obsah lze považovat za nevhodné v závislosti na kontextu. Kromě takzvané pravděpodobnost, že každá kategorie, může doporučit lidskou kontrolu obsahu. Funkce používá k identifikaci možných urážlivý nebo uráží diskriminační jazyk trénovaného modelu. To zahrnuje slang, zkrácený slova, urážlivé a záměrně chybně napsaná slova ke kontrole. 

#### <a name="explanation"></a>Vysvětlení

- `Category1` představuje potenciální přítomnost jazyk, který se dá považovat za sexuálně explicitní nebo pro dospělé v určitých situacích.
- `Category2` představuje potenciální přítomnost jazyk, který se dá považovat za sexuálně sugestivní nebo až po zralé v určitých situacích.
- `Category3` představuje potenciální přítomnost jazyk, který se dá považovat za urážlivé v určitých situacích.
- `Score` je mezi 0 a 1. Čím více bodů vyšší model je predikce kategorii lze použít. V této verzi preview spoléhá na statistických modelů a nikoli ručně kódovaný výsledků. Doporučujeme ve Visual Basicu s vlastní obsah k určení, jak každou kategorii odpovídá vašim požadavkům.
- `ReviewRecommended` je PRAVDA nebo NEPRAVDA v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, zda chcete použít tuto hodnotu nebo při rozhodování o vlastní prahové hodnoty na základě jejich obsahu zásad.

### <a name="analyze-the-response"></a>Analyzovat odpověď
Následující odpověď obsahuje různé přehledy z rozhraní API. Obsahuje vulgární, identifikovatelné osobní údaje, klasifikace (preview) a verze automaticky opravit.

> [!NOTE]
> "Klasifikaci" funkcí s asistencí počítače je ve verzi preview a podporuje pouze angličtinu.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Podrobné vysvětlení všech oddílů v odpovědi JSON, najdete [přehled rozhraní API pro moderování textu](text-moderation-api.md).

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [rychlý úvod k .NET pro moderování textu](text-moderation-quickstart-dotnet.md) integrovat s vaší aplikací.
