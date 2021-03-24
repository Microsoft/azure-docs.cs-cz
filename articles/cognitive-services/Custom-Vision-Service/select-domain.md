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
ms.openlocfilehash: 8aba6f13957d37f843114572f001029baf41ded6
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889344"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Vyberte doménu pro Custom Vision projekt.

Na kartě nastavení v Custom Vision projektu můžete vybrat doménu pro svůj projekt. Vyberte doménu, která je nejblíže vašemu scénáři. Pokud přistupujete Custom Vision prostřednictvím klientské knihovny nebo REST API, bude při vytváření projektu nutné zadat ID domény. Můžete získat seznam ID domén s možnostmi [získat domény](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)nebo použít následující tabulku.

## <a name="image-classification"></a>Klasifikace obrázků

|Doména|Účel|
|---|---|
|__Obecné__| Optimalizováno pro širokou škálu úloh klasifikace imagí. Pokud žádná z ostatních konkrétních domén není vhodná nebo pokud si nejste jisti, kterou doménu si vybrat, vyberte jednu z obecných domén. ÚČET `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Obecné [a1]__| Optimalizováno pro lepší přesnost s srovnatelným časem odvození jako obecnou doménou. Doporučuje se u větších datových sad nebo složitějších uživatelských scénářů. Tato doména vyžaduje více času školení. ÚČET `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Obecné [a2]__| Optimalizováno pro lepší přesnost při rychlejším odvození času než obecné (a1) a obecné domény. Doporučuje se pro většinu datových sad. Tato doména vyžaduje méně školicích časů než obecné a obecné domény [a1]. ÚČET `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Simulant__|Optimalizováno pro fotografie misek, jak byste je viděli v nabídce restaurace. Pokud chcete klasifikovat fotografie jednotlivých druhů ovoce a zeleniny, použijte doménu jídla. ÚČET `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Orientační body tváře__|Optimalizováno pro rozpoznatelný orientačních bodů, jak přirozené, tak umělé. Tato doména funguje nejlépe, když je ve fotografii jasně viditelný bod. Tato doména funguje i v případě, že je bod lehce překážkou pro lidi před ním. ÚČET `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Maloobchod__|Optimalizováno pro obrázky, které se nacházejí v nákupním katalogu nebo na nákupním webu. Pokud chcete s vysokou přesností klasifikovat mezi dresses, Pants a košile, použijte tuto doménu. ÚČET `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Kompaktní domény__| Optimalizováno pro omezení klasifikace v reálném čase u hraničních zařízení.|


> [!NOTE]
> Obecné domény [a1] a obecné [a2] lze použít pro širokou škálu scénářů a jsou optimalizovány pro přesnost. Použijte obecný model [a2] pro lepší rychlost odvození a kratší dobu školení. U větších datových sad můžete použít obecné [a1] pro vykreslení lepší přesnosti než obecné [a2], i když ale vyžaduje více školení a dobu odvození. Obecný model vyžaduje více času odvození než obecné [a1] a obecné [a2].

## <a name="object-detection"></a>Detekce objektů

|Doména|Účel|
|---|---|
|__Obecné__| Optimalizováno pro širokou škálu úloh detekce objektů. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu si zvolíte, vyberte možnost Obecná doména. ÚČET `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Obecné [a1]__| Optimalizováno pro lepší přesnost s srovnatelným časem odvození jako obecnou doménou. Doporučuje se pro přesnější potřeby umístění oblastí, větší datové sady nebo složitější scénáře uživatelů. Tato doména vyžaduje více času školení a výsledky nejsou deterministické: očekává se rozdílová hodnota +-1% střední přesnost (mapa) se stejnými školicími daty. ÚČET `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logo__|Optimalizováno pro hledání loga značky v obrázcích. ÚČET `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Produkty na police__|Optimalizováno pro zjišťování a klasifikaci produktů v police. ÚČET `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Kompaktní domény__| Optimalizováno pro omezení detekce objektů v reálném čase u hraničních zařízení.|

## <a name="compact-domains"></a>Kompaktní domény

Modely generované pomocí kompaktních domén lze exportovat pro místní spuštění. V rozhraní API verze Public Preview služby Custom Vision 3,4 můžete získat seznam exportovaných platforem pro kompaktní domény voláním rozhraní API getdomainnames.

Výkon modelu se liší podle vybrané domény. V níže uvedené tabulce oznamujeme velikost modelu a dobu odvození na PROCESORech Intel Desktop a NVidia GPU \[ 1 \] . Tato čísla nezahrnují předzpracování a postprocessingí času.

|Úkol|Doména|ID|Velikost modelu|Čas odvození procesoru|Čas odvození GPU|
|---|---|---|---|---|---|
|Klasifikace|Obecné (kompaktní)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 MS|5 MS|
|Klasifikace|General (Compact) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 MS|5 MS|
|Detekce objektů|Obecné (kompaktní)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 MS|
|Detekce objektů|General (Compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>__Obecná (kompaktní)__ doména pro detekci objektu vyžaduje speciální logiku postprocessing. Podrobnosti najdete v ukázkovém skriptu v exportovaném balíčku zip. Pokud potřebujete model bez logiky postprocessing, použijte __Obecné (Compact) [S1]__.

>[!IMPORTANT]
>Neexistuje žádná záruka, že exportované modely přidávají přesně stejný výsledek jako předpověď rozhraní API v cloudu. Nepatrný rozdíl ve spuštěné platformě nebo implementace předběžného zpracování může způsobit větší rozdíl v výstupech modelu. Podrobnosti o logice předběžného zpracování najdete v [tomto dokumentu](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU a NVIDIA Tesla M60
