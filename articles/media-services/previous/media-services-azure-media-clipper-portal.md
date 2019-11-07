---
title: Použití Azure Media Clipperu na portálu | Microsoft Docs
description: Vytváření klipů pomocí Azure Media Clipperu z webu Azure Portal
services: media-services
keywords: Clip; dílčí klip; Encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684990"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Vytváření klipů pomocí Azure Media Clipperu na portálu  

Pomocí Azure Media Clipperu na portálu můžete vytvářet klipy z assetů v účtech Media Services. Začněte tím, že na portálu přejdete na svůj účet Media Services. Potom vyberte kartu dílčí **klip** .

Na kartě dílčí **klip** budete moci začít sestavovat klipy. V portálu Clipper načítá rychlostmi s jednou přenosovou rychlostí, rychlostmi s více přenosovými rychlostmi a živými archivy, které jsou publikovány pomocí platného lokátoru streamování. Nepublikované prostředky nejsou načteny.

Clipper je momentálně ve verzi Public Preview. Chcete-li získat přístup k Clipperu v Azure Portal, přejděte na [stránku Tato verze Public Preview](https://portal.azure.com/?feature.subclipper=true).

Následující obrázek znázorňuje cílovou stránku Clipperu v účtu Media Services: ![Azure Media Clipper v Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Vytváření klipů
Chcete-li vytvořit klip, přetáhněte Asset do rozhraní Clip. Pokud jsou známy časy označení, můžete je ručně zadat do rozhraní. V opačném případě přehrává Asset nebo přetáhněte přehrávací hlavu, aby bylo možné najít požadovanou značku a čas označení. Pokud není zadán čas nebo označení příznaku, klip začne od začátku nebo pokračuje na konec vstupního prostředku v uvedeném pořadí.

Chcete-li přejít s přesností na snímek a přesností na skupinu GOP, použijte tlačítka Frame-dopředné/skupinu GOP-dopředně nebo v rámečku – zpět a skupinu GOP. Pro oříznutí proti více prostředkům přetáhněte více prostředků do rozhraní Clip z panelu Výběr assetu. V rozhraní můžete vybrat a změnit pořadí prostředků v požadovaném pořadí. Panel výběr assetu poskytuje metadata o trvání prostředku, typ a metadata řešení pro každý Asset. Při zřetězení několika prostředků zvažte rozlišení zdroje každého vstupního souboru. Pokud se rozlišení zdrojového řešení liší, znamená to, že je nižší rozlišení, aby splňovalo řešení nejvyššího prostředku řešení. Pokud chcete zobrazit náhled výstupu úlohy oříznutí, vyberte tlačítko Náhled a klip se přehraje z vybraného času označení.

## <a name="producing-dynamic-manifest-filters"></a>Vytváření dynamických filtrů manifestu
[Dynamické filtry manifestů](https://azure.microsoft.com/blog/dynamic-manifest/) popisují sadu pravidel založených na atributech manifestu a časové ose prostředků. Tato pravidla určují, jak koncový bod streamování zpracovává výstupní seznam stop (manifest). Pomocí filtru můžete změnit, které segmenty se streamuje pro přehrávání. Filtry vytvořené v Clipperu jsou místní filtry a jsou specifické pro zdrojový prostředek. Na rozdíl od vykreslených klipů nejsou filtry nové prostředky a nevyžadují, aby se vytvořila úloha kódování. Dají se rychle vytvořit prostřednictvím [sady .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) nebo [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), ale jsou jenom přesná skupinu GOP. Prostředky kódované pro streamování mají typicky skupinu GOPou velikost 2 sekundy.

Chcete-li vytvořit dynamický filtr manifestu, přejděte na kartu **assets (prostředky** ) a vyberte požadovaný prostředek. Vyberte tlačítko **podklipu** pro horní nabídku. V nabídce Upřesnit nastavení vyberte možnost dynamický filtr manifestu jako režim vystřihování. Pak můžete postupovat podle stejného procesu a vytvořit vygenerovaný klip pro vytvoření filtru. Filtry je možné vytvářet pouze z jediného assetu.

Následující obrázek ilustruje Clipper v režimu dynamického filtru manifestu v Azure Portal: ![Azure Media Clipper v režimu dynamického filtru manifestu v Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Odesílání úloh oříznutí
Po dokončení sestavování klipu vyberte tlačítko Odeslat úlohu pro zahájení příslušné úlohy oříznutí nebo dynamického volání manifestu.

## <a name="next-steps"></a>Další kroky
Pokud chcete začít používat Azure Media Clipper, přečtěte si článek [Začínáme](media-services-azure-media-clipper-getting-started.md) , kde najdete podrobnosti o tom, jak tento widget nasadit.
