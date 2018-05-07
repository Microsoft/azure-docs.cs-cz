---
title: "Pomocí Azure Media výřez na portálu | Microsoft Docs"
description: "Vytvořit klipy pomocí Azure Media výřez z portálu Azure"
services: media-services
keywords: "klip; subclip; kódování; média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1deca68cd6a61ede7536c4d5544036a10c54209b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Vytvoření klipů s Azure Media výřez v portálu.
Azure Media výřez portálu slouží k vytvoření klipů z prostředků ve svých účtů media services. Chcete-li začít, přejděte na svůj účet mediálních služeb v portálu. Potom vyberte **Subclip** kartě.

Na **Subclip** karty, budete moci začít skládání klipů. Na portálu výřez načte soubory MP4 s jednou přenosovou rychlostí rychlostmi, soubory MP4 s více přenosovými rychlostmi a archivy za provozu, které jsou publikovány s platný Lokátor streamování. Publikování prostředků nebyly načteny.

Výřez je aktuálně ve verzi public preview. Chcete-li získat přístup k výřez na portálu Azure, přejděte na to [verzi public preview stránky](https://portal.azure.com/?feature.subclipper=true).

Následující obrázek ukazuje výřez cílová stránka v svůj účet mediálních služeb: ![výřez média Azure na portálu Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Vytváření klipů
Vytvořit klip, přetažením prostředek na rozhraní klip. Pokud jsou známé označit časy, můžete je zadat ručně do rozhraní. Jinak hodnota přehrávání asset nebo přetáhněte počátek přehrávání najít požadované značky v a čas. Pokud není k dispozici čas začátku nebo konce, klip začne od začátku nebo pokračuje na konec vstupu prostředku, v uvedeném pořadí.

Přejděte s rámečkem přesnost nebo GOP-přesnost, použijte tlačítko Předat dál/GOP-převíjení nebo rámce zpětné/GOP-zpátky. Pro výstřižek proti více prostředků, přetažení více prostředků do rozhraní klip z panelu Výběr asset. Můžete vybrat a změnit pořadí prostředky v rozhraní požadované pořadí. Panel výběru asset poskytuje asset trvání, typ a překlad metadat pro každý prostředek. Pokud společně zřetězení více prostředků, zvažte rozlišení zdroje každého vstupního souboru. Pokud zdroj řešení liší, je nižší rozlišení vstup upscaled ke splnění řešení nejvyšší prostředku řešení. Chcete-li zobrazit náhled výstup úlohy výstřižek, vyberte tlačítko Náhled a klip přehrává z vybrané označit časy.

## <a name="producing-dynamic-manifest-filters"></a>Vytváření dynamické filtry manifestu
[Dynamické filtry manifestu](https://azure.microsoft.com/blog/dynamic-manifest/) popisují sadu pravidel na základě manifestu atributy a časová osa asset. Tato pravidla určují, jak koncový bod streamování zpracovává seznam stop výstup (manifestu). Filtr lze změnit, které segmenty streamovaných pro přehrávání. Filtry vyprodukované výřez jsou místní filtry a jsou specifické pro daný prostředek zdroje. Na rozdíl od vykreslené klipů filtry nejsou nové prostředky a nevyžadují úlohu kódování k vytvoření. Může být rychle vytvořené prostřednictvím [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) nebo [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), je však pouze GOP přesné. Prostředky kódováním pro streamování obvykle mají velikost GOP dvou sekund.

Chcete-li vytvořit dynamické manifestu filtr, přejděte na **prostředky** a vyberte požadovaného assetu. Vyberte **Subclip** tlačítko v hlavní nabídce. Vyberte jako výstřižek režim z nabídky Rozšířené nastavení dynamické manifestu filtr. Potom můžete provést stejný postup k vytvoření vykreslené klip k vytvoření filtru rozhraní. Filtry je možné vytvořit jenom z jednoho datového zdroje.

Následující obrázek ukazuje výřez v dynamické manifestu režim filtru na portálu Azure: ![výřez média Azure v dynamické manifestu režim filtru na portálu Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Odesílání úloh výstřižek
Po skončení skládání klip, vyberte tlačítko pro spuštění úlohy odeslání odpovídající úloha výstřižek nebo dynamické manifestu volání.

## <a name="next-steps"></a>Další kroky
Chcete-li začít používat Azure Media výřez, přečtěte si [Začínáme](media-services-azure-media-clipper-getting-started.md) článku podrobnosti o tom, jak nasadit widgetu.