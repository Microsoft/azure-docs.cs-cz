---
title: Počty Microsoft překladač Text API znak | Microsoft Docs
description: Jak rozhraní API služby Microsoft překladač Text vrátí počet znaků.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342765"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Jak rozhraní API služby Microsoft překladač Text počty znaků

Microsoft Translator počítá každý znak vstupu. Znaky kódování Unicode smysl, ne v bajtech. Náhrady Unicode počet jako dva znaky. Prázdné znaky a značku, se počítají jako znaky. Délka odpovědi nezáleží.

Volání metody detekovat a BreakSentence nejsou v spotřeby znak zahrnuty. Předpokládáme, že volání metody detekovat a BreakSentence jsou v přiměřené procento s použitím dalších funkcí, které se počítají. Společnost Microsoft si vyhrazuje právo spustit počítání detekovat a BreakSentence. 

Převede nabídky automatické zjišťování, pokud vynecháte parametr language From. 

Další informace o počtu znak je v [Microsoft překladač – nejčastější dotazy](https://www.microsoft.com/en-us/translator/faq.aspx).
