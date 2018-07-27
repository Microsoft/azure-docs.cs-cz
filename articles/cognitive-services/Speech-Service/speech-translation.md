---
title: O překladu řeči
description: Přehled možností pro překlad řeči
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 25be92162781df9bf5aefaa2411e9a091918254c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281673"
---
# <a name="about-the-speech-translation-api"></a>O rozhraní API pro překlad řeči

Rozhraní Speech API Microsoft můžete přidat začátku do konce, v reálném čase, vícejazykového překlad řeči do aplikací, nástroje a zařízení. Stejného rozhraní API můžete použít pro překlad řeči řeči a rozpoznávání řeči na text.

Pomocí rozhraní Microsoft Translator Speech API klientské aplikace zvuk řeči do služby stream a získávejte zpět výsledků. Tyto výsledky zahrnout textové rozpoznaných Zdrojový jazyk a překladu v cílovém jazyce. Dočasné překlady je možné poskytnout než utterance se dokončí, po kterém konečný překlad je k dispozici.

Volitelně můžete syntetizovaný zvukovou verzi konečný překlad mohly být připraveny, povolení true překlad řeči speech.

Rozhraní API pro překlad řeči používá protokol Websocket pro zajištění plně duplexní komunikační kanál mezi klientem a serverem. Ale nepotřebujete řešit objekty Websocket; sadou SDK pro řeč, která zpracuje za vás.

Rozhraní API pro překlad řeči využívá stejné technologie, které stojí za různých produktů a služeb Microsoftu. Tato služba je již kterou tisíce podniků po celém světě využívají ve svých aplikací a pracovních postupů.

## <a name="about-the-technology"></a>O technologii

Základní modul překlad společnosti Microsoft jsou dva různé přístupy: statistické strojový překlad (SMT) a strojovému překladu neuronových (sítí NMT). Druhá možnost, umělou inteligenci přístup využívající neuronové sítě, je více moderní přístup pro strojový překlad. Poskytuje lepší překlady NMT – ne jenom přesnější, ale také více fluent a přirozené. Klíče důvod pro tento plynulost je, že NMT používá úplný kontext větu překládat slova.

V současné době Microsoft migroval do NMT pro Nejoblíbenější jazyky, když SMT pouze pro jazyky méně používaná. Všechny [jazyky dostupné pro překlad řeči speech](supported-languages.md#speech-translation) NMT využívají. Překlad řeči na text pomocí SMT nebo NMT v závislosti na pár jazyka. Pokud cílový jazyk je podporovaná NMT, úplný překlad je s využitím NMT. Pokud cílový jazyk není podporován NMT, překlad je v hybridní kombinaci NMT a SMT, pomocí angličtina jako "kontingenční tabulku" mezi dva jazyků.

Rozdíly mezi modely jsou interní překlad modulu. Koncoví uživatelé oznámení pouze kvalitu překladu vylepšené, zejména pro arabština, čínština a japonština.

> [!NOTE]
> Uvažujete o získání informací o technologie od Microsoftu překladu modulu? Zobrazit [strojový překlad](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
