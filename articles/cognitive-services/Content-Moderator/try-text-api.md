---
title: Střední text pomocí rozhraní API přerušování textu v obsahu moderátora Azure | Microsoft Docs
description: Test-Drive přerušování text pomocí rozhraní API přerušování Text v konzole online.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: ed696c31a886626819414c45eb7995edaf161fff
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342811"
---
# <a name="moderate-text-from-the-api-console"></a>Střední text z konzoly pro rozhraní API

Použití [Text přerušování API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) v Azure obsah moderátora ke kontrole textového obsahu. Operace kontroly obsahu testujeme a porovná obsah proti sdílené a vlastní rozhraní API.


## <a name="get-your-api-key"></a>Získat klíč rozhraní API
Než můžete test-drive rozhraní API v konzole online, musíte svůj klíč předplatného. To se nachází na **nastavení** ve **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [přehled](overview.md).

## <a name="navigate-to-the-api-reference"></a>Přejděte na referenční dokumentace rozhraní API
Přejděte na [referenční dokumentace rozhraní API přerušování Text](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  **Text – obrazovky** otevře se stránka.

## <a name="open-the-api-console"></a>Otevřete konzolu pro rozhraní API
Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Text – výběr oblast stránky obrazovky](images/test-drive-region.png)

  **Text – obrazovky** otevře se konzola rozhraní API.

## <a name="select-the-inputs"></a>Vyberte vstupní hodnoty

### <a name="parameters"></a>Parametry
Vyberte parametry dotazu, které chcete použít na vaší obrazovce text. V tomto příkladu použijte výchozí hodnotu pro **jazyk**. Můžete také necháte prázdné protože operaci automaticky zjistí pravděpodobně jazyk v rámci jeho spuštění.

> [!NOTE]
> Pro **jazyk** parametr přiřadit `eng` nebo hodnotu nevyplňujte najdete v části asistované počítač **klasifikace** odpovědi (funkce ve verzi preview). **Tato funkce podporuje pouze Angličtina**.
>
> Pro **vulgárnost podmínky** detekce, použijte [kód ISO 639 3](http://www-01.sil.org/iso639-3/codes.asp) z podporovaných jazyků uvedených v tomto článku, nebo hodnotu nevyplňujte.

Pro **automatické opravy**, **PII**, a **klasifikovat (preview)**, vyberte **true**. Ponechte **ListId** pole prázdná.

  ![Text – parametry dotazu konzoly obrazovky](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ obsahu
Pro **Content-Type**, vyberte typ obsahu, které chcete obrazovky. V tomto příkladu použijte výchozí **text/plain** typ obsahu. V **Ocp-Apim-Subscription-Key** zadejte svůj klíč předplatného.

### <a name="sample-text-to-scan"></a>Ukázkový text ke kontrole
V **text žádosti** zadejte nějaký text. Následující příklad ukazuje úmyslné máte překlep v textu.

> [!NOTE]
> Neplatné číslo sociálního pojištění v následující ukázkový text je úmyslné. Účelem je nesou ukázka vstupní a výstupní formát.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Funkce textových klasifikace

V následujícím příkladu se zobrazí odpověď klasifikace obsahu moderátora s asistencí počítač text. Pomáhá rozpoznání potenciálně nežádoucí obsahu. Označení obsahu mohou být považovány za jako nevhodný v závislosti na kontextu. Kromě zdůraznění pravděpodobnost každou kategorii, může doporučit kontrolu lidského obsahu. Funkce modulu trained model používá k identifikaci možných urážlivé, derogační nebo diskriminační jazyk. To zahrnuje slang, zkrácené slova, urážlivé a záměrně překlepu slova ke kontrole. 

#### <a name="explanation"></a>Vysvětlení

- `Category1` představuje potenciální přítomnost jazyk, který může být považováno za zřejmý explicitní nebo pro dospělé v určitých situacích.
- `Category2` představuje potenciální přítomnost jazyk, který může být považováno za zřejmý sugestivní nebo vyspělá v určitých situacích.
- `Category3` představuje potenciální přítomnost jazyk, který může být považováno za urážlivé v určitých situacích.
- `Score` je mezi 0 a 1. Čím více bodů, tím vyšší modelu je predikci, můžou být příslušné kategorii. Tato verze preview spoléhá na statistické model, místo ručně programové výstupy. Doporučujeme, abyste testování s vlastním obsahem určit, jakým způsobem bude každou kategorii zarovnán vašim požadavkům.
- `ReviewRecommended` je PRAVDA nebo NEPRAVDA v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, jestli se má použít tuto hodnotu nebo rozhodněte o vlastní prahové hodnoty na základě jejich obsahu zásad.

### <a name="analyze-the-response"></a>Analyzovat odpověď
Následující odpověď ukazuje různé přehledy z rozhraní API. Obsahuje potenciální vulgárnost, PII, klasifikace (preview) a verzi vyřešen automaticky.

> [!NOTE]
> "Třídění" funkce s asistencí počítač je ve verzi preview a podporuje pouze angličtina.

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

Podrobné vysvětlení všech oddílů v odpovědi JSON, najdete v části [text přerušování API přehled](text-moderation-api.md).

## <a name="next-steps"></a>Další postup

Použít rozhraní API REST v kódu ani začínat [text přerušování .NET rychlý Start](text-moderation-quickstart-dotnet.md) integrovat s vaší aplikací.
