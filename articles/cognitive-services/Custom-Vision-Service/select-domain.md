---
title: Vyberte doménu pro Custom Vision projekt – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak vybrat doménu pro projekt v Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82127778"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Vyberte doménu pro Custom Vision projekt.

V okně nastavení pro váš Custom Vision projekt můžete vybrat doménu pro svůj projekt. Vyberte doménu, která je nejblíže vašemu scénáři.

## <a name="image-classification"></a>Klasifikace obrázků

|Doména|Účel|
|---|---|
|__Obecné__| Optimalizováno pro širokou škálu úloh klasifikace imagí. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, jakou doménu chcete vybrat, vyberte obecnou doménu.|
|__Simulant__|Optimalizováno pro fotografie misek, jak byste je viděli v nabídce restaurace. Pokud chcete klasifikovat fotografie jednotlivých druhů ovoce a zeleniny, použijte doménu jídla.|
|__Orientační body tváře__|Optimalizováno pro rozpoznatelný orientačních bodů, jak přirozené, tak umělé. Tato doména funguje nejlépe, když je ve fotografii jasně viditelný bod. Tato doména funguje i v případě, že je bod lehce překážkou pro lidi před ním.|
|__Maloobchod__|Optimalizováno pro obrázky, které se nacházejí v nákupním katalogu nebo na nákupním webu. Pokud požadujete vysokou přesnost klasifikace mezi dresses, Pants a košile, použijte tuto doménu.|
|__Kompaktní domény__| Optimalizováno pro omezení klasifikace v reálném čase u hraničních zařízení.|

## <a name="object-detection"></a>Detekce objektů

|Doména|Účel|
|---|---|
|__Obecné__| Optimalizováno pro širokou škálu úloh detekce objektů. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu si zvolíte, vyberte obecnou doménu.|
|__Logo__|Optimalizováno pro hledání loga značky v obrázcích.|
|__Produkty na police__|Optimalizováno pro zjišťování a klasifikaci produktů v police.|
|__Kompaktní domény__| Optimalizováno pro omezení detekce objektů v reálném čase u hraničních zařízení.|

## <a name="compact-domains"></a>Kompaktní domény

Modely generované pomocí kompaktních domén lze exportovat pro místní spuštění. Výkon modelu se liší podle vybrané domény. V níže uvedené tabulce oznamujeme velikost modelu a dobu odvození na PROCESORech Intel Desktop a NVidia GPU \[ 1 \] . 

> [!NOTE]
> Tato čísla nezahrnují předzpracování a postprocessingí času.

|Úloha|Doména|Velikost modelu|Čas odvození procesoru|Čas odvození GPU|
|---|---|---|---|---|
|Classification|Obecné (kompaktní)|5 MB|13 MS|5 MS|
|Detekce objektů|Obecné (kompaktní)|45 MB|35 MS|5 MS|
|Detekce objektů|General (Compact) [S1]|14 MB|27 MS|7 MS|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI dev Kit)

Když je vybraná kompaktní doména, je k dispozici možnost exportovat možnosti, která umožňuje rozlišovat základní platformy a Vision AI dev Kit.

V části _Možnosti exportu_ jsou dvě možnosti:

- Základní platformy (Tensorflow, CoreML, ONNX atd.)
- Sada Vision AI dev Kit.

Když je _sada Vision AI dev Kit_ vybraná jako _Obecné_, _orientační_a _maloobchodní_ , ale ne i _v případě_ , že jsou k dispozici obě _Obecné (kompaktní)_ a _Obecné (kompaktní) [S1]_ pro detekci objektů.

>[!NOTE]
>__Obecná (kompaktní)__ doména pro detekci objektu vyžaduje speciální logiku postprocessing. Podrobnosti najdete v ukázkovém skriptu v exportovaném balíčku zip. Pokud potřebujete model bez logiky postprocessing, použijte __Obecné (Compact) [S1]__.

>[!IMPORTANT]
>Neexistuje žádná záruka, že exportované modely přidávají přesně stejný výsledek jako předpověď rozhraní API v cloudu. Nepatrný rozdíl ve spuštěné platformě nebo implementace předběžného zpracování může způsobit větší rozdíl v výstupech modelu. Podrobnosti o logice předběžného zpracování najdete v [tomto dokumentu](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU a NVIDIA Tesla M60
