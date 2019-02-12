---
title: Pomocí Azure Media Clipperem na portálu | Dokumentace Microsoftu
description: Vytvářet pomocí Azure Media Clipperem z webu Azure Portal
services: media-services
keywords: Galerie, dílčí klip, kódování, médií
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 510176331a1b79a1069b42afabb63b6b326951cc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992308"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Vytvářet s Azure Media Clipperem na portálu  

Azure Media Clipperem na portálu můžete vytvářet z prostředků v rámci účtů media services. Abyste mohli začít, přejděte do účtu media services na portálu. V dalším kroku vyberte **dílčí klip** kartu.

Na **dílčí klip** kartu, budete moct začít vytváření klipy. Na portálu Clipperem načte soubory MP4 s jednou přenosovou rychlostí rychlostmi, soubory MP4 s více přenosovými rychlostmi a pořadů, které jsou publikovány s platnou Lokátor streamování. Nepublikovaných prostředky nejsou načtené.

Clipperem je aktuálně ve verzi public preview. Pro přístup k Clipperem na webu Azure Portal, přejděte na tuto [stránky ve verzi public preview](https://portal.azure.com/?feature.subclipper=true).

Následující obrázek ukazuje Clipperem cílové stránky v účtu media services: ![Azure Media Clipperem na webu Azure portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Vytváření klipů
Chcete-li vytvořit klipu, přetažením prostředek na rozhraní galerie. Pokud označit a časy jsou známé, můžete je zadat ručně do rozhraní. Jinak, přehrávání prostředku nebo ho přetáhněte ukazatel pozice vyhledat požadovanou značku v a čas. Pokud není k dispozici čas značky nebo konce, klip začne od začátku nebo dál konci vstupní asset, v uvedeném pořadí.

Přecházení pomocí rámce přesnost/GOP-přesnost, použijte tlačítko rámce – vpřed/GOP vpřed nebo rámec zpětně/GOP-zpět. Pro oříznutí proti více prostředků, přetáhněte a umístěte více prostředků do Galerie rozhraní z panel výběru prostředku. Můžete vybrat a změnit pořadí prostředky v rozhraní do požadovaného pořadí. Panel výběru asset poskytuje doba trvání assetu, typ a překlad metadat pro každý prostředek. Při zřetězení více prostředků najednou, vezměte v úvahu zdroj rozlišení každého vstupního souboru. Pokud se rozlišení zdroje liší, je nižší rozlišení vstup upscaled podle řešení nejvyšší asset řešení. Ve verzi preview výstup úlohy oříznutí, vyberte tlačítko Náhled a klip přehrává z vybrané značky časy.

## <a name="producing-dynamic-manifest-filters"></a>Vytváření filtry dynamických manifestů
[Filtry dynamických manifestů](https://azure.microsoft.com/blog/dynamic-manifest/) popisují sadu pravidel na základě manifestu atributů a časová osa asset. Tato pravidla určují, jak koncový bod streamování zpracovává výstup stop (manifest). Tento filtr je možné změnit, které segmenty jsou streamování pro přehrávání. Filtry vytvářených Clipperem jsou místní filtry a jsou specifická pro zdrojový prostředek. Na rozdíl od vykreslené klipy filtry nejsou nové materiály a nevyžadují žádné kódování úloh k vytvoření. Je možné rychle vytvořit prostřednictvím [sady .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) nebo [rozhraní REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), je však pouze GOP přesné. Prostředky kódování pro vysílání datového proudu obvykle mají velikost GOP dvou sekund.

Chcete-li vytvořit filtr dynamických manifestů, přejděte na **prostředky** kartě a vyberte požadovaný prostředek. Vyberte **dílčí klip** tlačítko v horní nabídce. Vyberte filtr dynamických manifestů výstřižek režim z nabídky upřesňující nastavení. Můžete pak opakujte stejný postup k vytvoření vykreslené Galerie pro vytvoření filtru. Filtry je možné vytvořit pouze z jednoho prostředku.

Následující obrázek ukazuje Clipperem v dynamickém režimu manifestu filtr na webu Azure Portal: ![Azure Media Clipperem v dynamickém režimu manifestu filtr na webu Azure portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Odesílání úloh oříznutí
Po dokončení vytváření klip, vyberte tlačítka pro odeslání úlohy pro zahájení odpovídající úloha výstřižek dynamického volání manifestu.

## <a name="next-steps"></a>Další postup
Abyste mohli začít používat Azure Media Clipperem, přečtěte si [Začínáme](media-services-azure-media-clipper-getting-started.md) , kde najdete podrobnosti o tom, jak nasadit widgetu.
