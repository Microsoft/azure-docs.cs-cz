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
ms.openlocfilehash: 87b9e4a3ca7151b3666928b00add175eddeea050
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409378"
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

Modely generované pomocí kompaktních domén lze exportovat pro místní spuštění. V rozhraní API verze Public Preview služby Custom Vision 3,4 můžete získat seznam exportovaných platforem pro kompaktní domény voláním rozhraní API getdomainnames.

Výkon modelu se liší podle vybrané domény. V níže uvedené tabulce oznamujeme velikost modelu a dobu odvození na PROCESORech Intel Desktop a NVidia GPU \[ 1 \] . Tato čísla nezahrnují předzpracování a postprocessingí času.

|Úkol|Doména|Velikost modelu|Čas odvození procesoru|Čas odvození GPU|
|---|---|---|---|---|
|Classification|Obecné (kompaktní)|5 MB|13 MS|5 MS|
|Detekce objektů|Obecné (kompaktní)|45 MB|35 MS|5 MS|
|Detekce objektů|General (Compact) [S1]|14 MB|27 MS|7 MS|

>[!NOTE]
>__Obecná (kompaktní)__ doména pro detekci objektu vyžaduje speciální logiku postprocessing. Podrobnosti najdete v ukázkovém skriptu v exportovaném balíčku zip. Pokud potřebujete model bez logiky postprocessing, použijte __Obecné (Compact) [S1]__.

>[!IMPORTANT]
>Neexistuje žádná záruka, že exportované modely přidávají přesně stejný výsledek jako předpověď rozhraní API v cloudu. Nepatrný rozdíl ve spuštěné platformě nebo implementace předběžného zpracování může způsobit větší rozdíl v výstupech modelu. Podrobnosti o logice předběžného zpracování najdete v [tomto dokumentu](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU a NVIDIA Tesla M60
