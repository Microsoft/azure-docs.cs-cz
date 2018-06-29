---
title: Azure obsahu moderátora - Text přerušování | Microsoft Docs
description: Použijte přerušování text pro možné nežádoucí text, osobní údaje, a vlastní seznamy podmínek.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 6924807a64cec074d9688eaad158bb9bb638f6bb
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085755"
---
# <a name="text-moderation"></a>Moderování textu

Použít moderátora obsah s asistencí počítač text přerušování a [lidské zkontrolujte](Review-Tool-User-Guide/human-in-the-loop.md) funkce pro střední textového obsahu.

Můžete blokovat, schválit nebo zkontrolujte obsah na základě zásad a prahové hodnoty. Použijte k posílení lidské přerušování prostředí, kde partnery, zaměstnanci a spotřebitelé generovat textového obsahu. Mezi ně patří, chatovací místnosti, diskusní vývěsky, chatbots, elektronické obchodování katalogů a dokumenty. 

Odpověď služby obsahuje následující informace:

- Vulgárnost: na základě podmínek odpovídající pomocí seznam předdefinovaných znevažujícího podmínek v různých jazycích
- Klasifikace: klasifikace s asistencí počítače do tří kategorií
- Identifikovatelné osobní údaje (PII)
- Automaticky opravit text
- Původní text
- Jazyk

## <a name="profanity"></a>Vulgárnost

Pokud rozhraní API zjistí znevažujícího podmínek v některém z [podporované jazyky](Text-Moderation-API-Languages.md), tyto podmínky jsou zahrnuty v odpovědi. Odpověď obsahuje také jejich umístění (`Index`) v původní text. `ListId` Následující ukázka JSON odkazuje na podmínky pro najít v [vlastní termín seznamy](try-terms-list-api.md) Pokud je k dispozici.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Pro **jazyk** parametr přiřadit `eng` nebo hodnotu nevyplňujte najdete v části asistované počítač **klasifikace** odpovědi (funkce ve verzi preview). **Tato funkce podporuje pouze Angličtina**.
>
> Pro **vulgárnost podmínky** detekce, použijte [kód ISO 639 3](http://www-01.sil.org/iso639-3/codes.asp) z podporovaných jazyků uvedených v tomto článku, nebo hodnotu nevyplňujte.

## <a name="classification"></a>Klasifikace

Obsahu moderátora je s asistencí počítač **funkce klasifikace textových** podporuje **pouze v angličtině,**, a pomáhá zjišťovat potenciálně nežádoucí obsah. Označení obsahu lze vyhodnotit jako nevhodný v závislosti na kontextu. To přenese tak pravděpodobnost každou kategorii a může doporučujeme lidského kontrolu. Funkce modulu trained model používá k identifikaci možných urážlivé, derogační nebo diskriminační jazyk. To zahrnuje slang, zkrácené slova, urážlivé a záměrně překlepu slova ke kontrole. 

Následující extract v JSON extract ukazuje příklad výstupu:

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
    }

### <a name="explanation"></a>Vysvětlení

- `Category1` označuje potenciální přítomnost jazyk, který může být považováno za zřejmý explicitní nebo pro dospělé v určitých situacích.
- `Category2` označuje potenciální přítomnost jazyk, který může být považováno za zřejmý sugestivní nebo vyspělá v určitých situacích.
- `Category3` označuje potenciální přítomnost jazyk, který může být považováno za urážlivé v určitých situacích.
- `Score` je mezi 0 a 1. Čím více bodů, tím vyšší modelu je predikci, můžou být příslušné kategorii. Tato verze preview spoléhá na statistické model, místo ručně programové výstupy. Doporučujeme, abyste testování s vlastním obsahem určit, jakým způsobem bude každou kategorii zarovnán vašim požadavkům.
- `ReviewRecommended` je PRAVDA nebo NEPRAVDA v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, jestli se má použít tuto hodnotu nebo rozhodněte o vlastní prahové hodnoty na základě jejich obsahu zásad.

## <a name="personally-identifiable-information-pii"></a>Identifikovatelné osobní údaje (PII)

Funkci PII zjistí potenciální přítomnost tyto informace:

- E-mailová adresa
- Poštovní adresa USA
- IP adresa
- Telefonní číslo USA
- Spojené království telefonní číslo
- Číslo sociálního pojištění (SSN)

Následující příklad ukazuje ukázková odpověď:

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
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
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
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Automatické opravy

Předpokládejme, že je vstupního textu ('lzay' a 'f0x' jsou úmyslně):

    The qu!ck brown f0x jumps over the lzay dog.

Pokud je požádat o automatické opravy, odpověď obsahuje opravené verzi text:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Vytváření a správě vlastních seznamů, které podmínek

Při výchozím seznamu pro globální podmínek dobře funguje pro většinou, můžete na obrazovce s výrazy, které jsou specifické pro vaše obchodní potřeby. Můžete například filtrovat žádné konkurenční brand názvy z příspěvcích uživatelé.

> [!NOTE]
> Maximální limit je **5 termín uvádí** s každou seznamu **není delší než 10 000 podmínky**.
>

Následující příklad ukazuje odpovídající ID seznamu:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Poskytuje obsahu moderátora [rozhraní API seznamu termín](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) s operacemi sady pro správu vlastní termín uvádí. Začínat [termín uvádí rozhraní API konzoly](try-terms-list-api.md) a používat ukázky kódu rozhraní REST API. Také si přečtěte [rychlý start termín uvádí .NET](term-lists-quickstart-dotnet.md) Pokud jste obeznámeni s Visual Studio a C#.

## <a name="next-steps"></a>Další postup

Vyzkoušejte [Text přerušování rozhraní API konzoly](try-text-api.md) a používat ukázky kódu rozhraní REST API. Také si přečtěte [Text přerušování .NET rychlý Start](text-moderation-quickstart-dotnet.md) Pokud jste obeznámeni s Visual Studio a C#.
