---
title: Vyberte doménu pro vlastní projekt Vision - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak vybrat doménu pro váš projekt ve službě Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899448"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Výběr domény pro vlastní projekt vize

V okně nastavení pro váš vlastní projekt Vision můžete vybrat doménu pro váš projekt. Zvolte doménu, která je nejblíže k vašemu scénáři.

## <a name="image-classification"></a>Klasifikace obrázků

|Domain (Doména)|Účel|
|---|---|
|__Obecné__| Optimalizováno pro širokou škálu úloh klasifikace obrázků. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu zvolit, vyberte obecnou doménu.|
|__Potravin__|Optimalizováno pro fotografie jídel, jak byste je viděli v menu restaurace. Chcete-li klasifikovat fotografie jednotlivých druhů ovoce nebo zeleniny, použijte doménu Potraviny.|
|__Památek__|Optimalizováno pro rozpoznatelné orientační body, přírodní i umělé. Tato doména funguje nejlépe, když je orientační bod jasně viditelný na fotografii. Tato doména funguje i v případě, že orientační bod je mírně blokován lidmi před ním.|
|__Maloobchod__|Optimalizováno pro obrázky, které se nacházejí v nákupním katalogu nebo na webu nákupu. Pokud chcete vysoce přesné třídění mezi šaty, kalhoty a košile, použijte tuto doménu.|
|__Kompaktní domény__| Optimalizováno pro omezení klasifikace v reálném čase na hraničních zařízeních.|

## <a name="object-detection"></a>Detekce objektů

|Domain (Doména)|Účel|
|---|---|
|__Obecné__| Optimalizováno pro širokou škálu úloh detekce objektů. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu zvolit, vyberte obecnou doménu.|
|__Logo__|Optimalizováno pro vyhledávání log značky v obrázcích.|
|__Výrobky na regálech__|Optimalizováno pro detekci a klasifikaci výrobků na regálech.|
|__Kompaktní domény__| Optimalizováno pro omezení detekce objektů v reálném čase na hraničních zařízeních.|

## <a name="compact-domains"></a>Kompaktní domény

Modely generované kompaktními doménami lze exportovat pro místní spuštění. Výkon modelu se liší podle vybrané domény. V následující tabulce uvádíme velikost modelu a čas odvození na procesorech Intel \[\]Desktop CPU a GPU NVidia 1 . 

> [!NOTE]
> Tato čísla nezahrnují čas předběžného zpracování a postprocessingu.

|Úkol|Domain (Doména)|Velikost modelu|Čas odvození procesoru|Doba odvození GPU|
|---|---|---|---|---|
|Classification|Obecné (kompaktní)|5 MB|13 ms|5 ms|
|Detekce objektů|Obecné (kompaktní)|45 MB|35 ms|5 ms|
|Detekce objektů|Obecné (kompaktní) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Při výběru kompaktní domény je k dispozici další možnost "Export schopnosti" umožňuje rozlišovat mezi "Základní platformy" a "Vision AI Dev Kit".

V části _Možnosti exportu_ jsou dvě možnosti:

- Základní platformy (Tensorflow, CoreML, ONNX atd.)
- Vision AI Dev Kit.

_Je-li vybrána sada Vision AI Dev Kit,_ jsou pro klasifikaci obrázků k dispozici _obecné_(kompaktní) a obecné (kompaktní) [S1] obecné , _orientační body_a _maloobchod,_ ale nikoli kompaktní domény _potravin,_ zatímco pro detekci objektů jsou k dispozici _obecné (kompaktní)_ i _obecné (kompaktní) [S1]._

>[!NOTE]
>__Obecná (kompaktní)__ doména pro detekci objektů vyžaduje speciální logiku postprocessingu. Podrobnosti naleznete v ukázkovém skriptu v exportovaném zip balíčku. Pokud potřebujete model bez logiky postprocessingu, použijte __obecné (kompaktní) [S1]__.

>[!IMPORTANT]
>Neexistuje žádná záruka, že exportované modely poskytují přesně stejný výsledek jako rozhraní API předpověď v cloudu. Mírný rozdíl v běžící platformě nebo implementaci předběžného zpracování může způsobit větší rozdíl ve výstupech modelu. Podrobnosti logiky předběžného zpracování naleznete v [tomto dokumentu](python-tutorial.md).

\[1\] Procesor Intel Xeon E5-2690 a NVIDIA Tesla M60
