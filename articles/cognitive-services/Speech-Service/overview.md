---
title: Co je Speech service (preview)?
description: 'Speech service součástí společnosti Microsoft Cognitive Services, sjednotí několik služeb Azure řeči, které byly dříve k dispozici samostatně: (zahrnující rozpoznávání řeči a převod textu na řeč) pro zpracování řeči Bingu, vlastní řeč a překladu řeči.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445630"
---
# <a name="what-is-the-speech-service-preview"></a>Co je Speech service (preview)?

Speech service využívá k tomu technologií používaných pro ostatní produkty Microsoftu, včetně Cortana a Microsoft Office.  Tato služba stejné je k dispozici všem zákazníkům jako služby Cognitive Services. 

> [!NOTE]
> Speech service je aktuálně ve verzi public preview. Vraťte se sem pravidelně aktualizací do dokumentace, ukázky kódu Další a další.

Naše Speech service s jedním oděrem poskytuje vývojářům snadný způsob, jak poskytnout funkce výkonnou podporou hlasových jejich aplikací. Své aplikace můžete nyní funkcí hlasových příkazů, přepis, diktování, syntézu řeči a překladu.

## <a name="speech-service-features"></a>Funkce služby rozpoznávání řeči

|Funkce|Popis|
|-|-|
|[Převod řeči na text](speech-to-text.md)| Transcribes audiostreamy do textu, který vaše aplikace může přijmout jako vstup. Se také integruje s [služby Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) k odvození z projevy záměru uživatele.|
|[Převod textu na řeč](text-to-speech.md)| Převede prostého textu do mluvené řeči přirozeného to nezní, doručí do vaší aplikace v zvukový soubor. Více hlasů ve pohlaví nebo zvýraznění, jsou k dispozici pro mnoho podporované jazyky. |
|[Překlad řeči](speech-translation.md)| Je možné přeložit zvukové streamy v téměř reálném čase nebo zpracovat zaznamenané řeči. |
|[Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)| Se zavedením jednotné Speech service Microsoft a příslušní partneři nabízí platformu integrované hardware a software pro optimalizovaný pro vývoj zařízení s podporou řeči |

## <a name="using-the-speech-service"></a>Pomocí služby řeči

Speech service je k dispozici dvě možnosti. [Sada SDK](speech-sdk.md) vyčleňuje podrobnostmi síťových protokolů. [Rozhraní REST API](rest-apis.md) funguje s libovolným programovacím jazykem, ale neposkytuje všechny funkce nabízené prostřednictvím sady SDK.

|<br>Metoda|Řeč<br>na Text|Text, který se<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Sady SDK](speech-sdk.md)|Ano|Ne|Ano|Knihovny pro konkrétní programovací jazyky, které zjednodušují vývoj.|
|[REST](rest-apis.md)|Ano|Ano|Ne|Jednoduché založené na protokolu HTTP rozhraní API, která umožňuje snadno přidat rozpoznávání řeči do vaší aplikace.|

## <a name="next-steps"></a>Další postup

Získejte bezplatné předplatné zkušební verze klíče pro službu rozpoznávání řeči.

> [!div class="nextstepaction"]
> [Bezplatné vyzkoušení Speech service](get-started.md)
