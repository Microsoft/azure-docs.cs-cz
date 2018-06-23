---
title: Informace o řeči kognitivní služby SDK | Microsoft Docs
description: Přehled sady SDK služby řeči je k dispozici.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343786"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Informace o řeči kognitivní služby SDK

Kognitivní služby řeči Software Development Kit (SDK) poskytuje aplikace nativní přístup k funkcím služby rozpoznávání řeči, což usnadňuje vývoj softwaru. V současné době sada SDK poskytuje přístup k **řeči na Text**, **řeči překlad**, a **záměr rozpoznávání**.

V tabulce jsou uvedeny aktuálně podporované programovací jazyky a operační systémy.

|Podporovaný operační systém|Programovací jazyk|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Zařízení|Java\*|

\* *Je součástí sady Java SDK [řeči zařízení SDK](speech-devices-sdk.md).*

## <a name="get-the-windows-sdk"></a>Získání sady Windows SDK

Verze Windows SDK řeči zahrnuje 32bitové a 64bitové verze klientské knihovny jazyka C/C++, jakož i spravované knihovny (.NET) pro použití s C#. Sada SDK může být nainstalován v sadě Visual Studio pomocí nástroje NuGet; jednoduše vyhledejte balíček `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Získání sady Linux SDK

Ujistěte se, že máte požadované kompilátoru a knihovny spuštěním následujících příkazů prostředí:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Tyto pokyny předpokládají, že používáte Ubuntu 16.04 na počítači (x86 nebo x64). Různé verze Ubuntu nebo jiný distribuční Linux přizpůsobíte tyto kroky pro vaše prostředí.

Potom [stažení sady SDK](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v do adresáře podle svého výběru. Tato tabulka ukazuje struktura složek SDK.

|Cesta|Popis|
|-|-|
|`license.md`|Licence|
|`third-party-notices.md`|Oznámení třetích stran|
|`include`|Soubory hlaviček pro C a C++|
|`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
|`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

Vytvořit aplikaci, zkopírovat nebo přesunout do vývojového prostředí vyžaduje binární soubory (a knihovny) a zahrnout je podle potřeby do procesu sestavení.

## <a name="get-the-java-sdk"></a>Získat Java SDK

Je součástí sady Java SDK [řeči zařízení SDK](speech-devices-sdk.md).

## <a name="next-steps"></a>Další postup

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Informace o tom, rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
