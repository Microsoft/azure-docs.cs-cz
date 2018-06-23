---
title: O překlad řeči | Microsoft Docs
description: Přehled možností řeči posunutí
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 43fcde887c21794989aa43540a214ef34893a630
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343776"
---
# <a name="about-the-speech-translation-api"></a>O překlad řeči rozhraní API

Rozhraní API pro rozpoznávání řeči Microsoft můžete přidat začátku do konce, v reálném čase, vícejazyčné překlad řeči do vaší aplikace, nástroje a zařízení. Lze použít stejné rozhraní API pro rozpoznávání řeči řeči a řeči na text překlad.

Klientské aplikace s rozhraním API Microsoft překladač řeči stream řeči zvuk ke službě a zobrazí zpět proud výsledky. Tyto výsledky obsahovat rozpoznaný text v source jazyk a jeho překlad do cílového jazyka. Dočasné překlady je možné zadaný až do dokončení utterance, které během poslední posunutí je k dispozici.

Volitelně můžete syntetizovaná zvuk verzi konečné překlad mohly být připraveny, povolení true řeči řeči překlad.

Rozhraní API pro překlad řeči používá k poskytnutí plně duplexní komunikační kanál mezi klientem a serverem protokolu Websocket. Ale nemusíte zabývat Websocket; který zpracovává řeči SDK.

Rozhraní API pro překlad řeči využívá stejné technologie, které stojí za různé produkty a služby Microsoftu. Tato služba je již používán tisíce firmy po celém světě v jejich aplikace a pracovních postupů.

## <a name="about-the-technology"></a>O technologii

Základní modul překlad společnosti Microsoft se používají dva různé přístupy: statistické strojový překlad (SMT) a neuronové strojový překlad (NMT). K tomu, umělé intelligence přístup zaměstnávající neuronové sítě, je více moderní přístup k strojového překladu. NMT poskytuje lepší překlady – ne jenom přesnější, ale také více fluent a přirozené. Z důvodu klíče pro tuto přenositelnost je, že NMT používá kontext úplné věty překládat slova.

Dnes Microsoft migroval na NMT pro Nejoblíbenější jazyky zaměstnávající SMT pouze pro jazyky, méně použít. Všechny [jazyky, které jsou k dispozici pro překlad řeči řeči](supported-languages.md#speech-translation) se používá technologii NMT. Překlad řeči na text může používat SMT nebo NMT v závislosti na pár jazyk. Pokud cílový jazyk je podporovaná NMT, úplná překlad je NMT zapnuté. Pokud cílový jazyk nepodporuje NMT, překlad je hybridní NMT a SMT, pomocí angličtina jako "pivot" mezi nimi.

Rozdíly mezi modely jsou interní modul překlad. Koncoví uživatelé Všimněte si pouze vylepšené překlad kvality, hlavně pro čínštiny, japonštiny a arabské.

> [!NOTE]
> Zajímá vás to dozvědět další informace o technologii za modul překlad společnosti Microsoft? V tématu [strojový překlad](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>Další postup

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Informace o tom, rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
