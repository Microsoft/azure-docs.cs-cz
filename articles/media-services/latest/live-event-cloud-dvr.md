---
title: Použití času na posunutí k vytvoření přehrávání videa na vyžádání
description: Tento článek popisuje, jak používat časová posunutí a živé výstupy k nahrávání živých streamů a k vytváření přehrávání na vyžádání.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a2bb876c164f0df56a8b7f3c4a3666ff306e9416
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955932"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Použití časových posunutí a živých výstupů k vytvoření přehrávání videa na vyžádání

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V Azure Media Services je objekt [živého výstupu](/rest/api/media/liveoutputs) podobný digitálnímu záznamu videa, který zachytí a zaznamená svůj živý stream do assetu v účtu Media Services. Zaznamenaný obsah je trvale uložen do kontejneru definovaného prostředkem [assetu](/rest/api/media/assets) (kontejner je v účtu Azure Storage připojeném k vašemu účtu). Živý výstup také umožňuje řídit některé vlastnosti odchozího živého streamu, například kolik dat v archivu je uchováváno (například kapacita záznamu v cloudu), nebo když můžou čtenáři začít sledovat živý datový proud. Archiv na disku je kruhový archiv "Window", který uchovává pouze množství obsahu, které je zadáno ve vlastnosti **ArchiveWindowLength** živého výstupu. Obsah, který spadá mimo toto okno, se automaticky zahodí z kontejneru úložiště a nedá se obnovit. Hodnota archiveWindowLength představuje dobu trvání časového rozmezí ISO-8601 (například PTHH: MM: SS), která určuje kapacitu souboru DVR. Hodnotu lze nastavit od nejméně jedné minuty po dobu maximálně 25 hodin.

Vztah mezi živou událostí a jeho živými výstupy je podobný tradičnímu TELEVIZNÍmu vysílání, v tom, že kanál (živá událost) představuje konstantní datový proud videa a záznam (živý výstup) je vymezen na určitý časový segment (například večer zprávy z 6:17:30 až 7:13:00). Jakmile datový proud přetéká do živé události, můžete zahájit streamování událostí vytvořením assetu, živého výstupu a lokátoru streamování. Živý výstup bude archivovat datový proud a zpřístupní ho návštěvníkům prostřednictvím [koncového bodu streamování](/rest/api/media/streamingendpoints). Můžete vytvořit více živých výstupů (až tří maximum) na živé události s různými délkami archivů a nastavením. Informace o pracovním postupu živého streamování najdete v části [Obecné kroky](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Použití DVR během události

Tato část popisuje, jak používat záznam DVR během události pro kontrolu nad tím, jaké části streamu jsou k dispozici pro Rewind.

`archiveWindowLength`Hodnota určuje, jak daleko v čase může prohlížeč přejít z aktuální živé pozice. `archiveWindowLength`Hodnota také určuje, jak dlouho mohou klientské manifesty růst.

Předpokládejme, že jste proudem fotbalového hry a máte `ArchiveWindowLength` jenom 30 minut. Prohlížeč, který začne sledovat událost 45 minut po zahájení hry, může hledat zpět na nejvíce 15 minut. Vaše živé výstupy pro hru budou pokračovat, dokud se zastaví živá událost. Obsah, který se nachází mimo archiveWindowLength, se nepřetržitě zruší z úložiště a je neobnovitelný. V tomto příkladu bylo video mezi začátkem události a značkou od 15 minut vymazáno z vašeho DVR a z kontejneru v úložišti objektů BLOB pro daný prostředek. Archiv se nedá obnovit a odebere se z kontejneru v úložišti objektů BLOB v Azure.

Živá událost podporuje až tři souběžně běžící živé výstupy (můžete vytvořit maximálně 3 záznamy/archivy z jednoho živého datového proudu ve stejnou dobu). Tato podpora umožňuje publikování a archivaci různých částí události podle potřeby. Předpokládejme, že budete potřebovat vysílat nepřetržitě živý lineární kanál a v průběhu dne vytvářet nahrávky různých programů, které zákazníkům nabízíme jako obsah na vyžádání pro účely zachytávání. V tomto scénáři nejprve vytvoříte primární živý výstup s krátkým oknem archivu o 1 hodinu nebo méně – jedná se o primární živý datový proud, ke kterému by se návštěvníci mohli naladit. Vytvořili jste Lokátor streamování pro tento živý výstup a publikujete ho na aplikaci nebo web jako datový kanál "Live". I když je živá událost spuštěná, můžete programově vytvořit druhý souběžný živý výstup na začátku programu (nebo 5 minut, kdy budete chtít, aby se některé úchyty mohly oříznout později). Tento druhý živý výstup se dá odstranit 5 minut po skončení programu. S tímto druhým prostředkem můžete vytvořit nový Lokátor streamování pro publikování tohoto programu jako prostředku na vyžádání v katalogu vaší aplikace. Tento proces můžete opakovat několikrát pro jiné hranice programu nebo světla, které chcete sdílet jako videa na vyžádání, a to vše, když se "živý" kanál z prvního živého výstupu pokračuje ve vysílání lineárního kanálu.

## <a name="creating-an-archive-for-on-demand-playback"></a>Vytvoření archivu pro přehrávání na vyžádání

Asset, na který živý výstup archivuje, se automaticky stal Assetem na vyžádání, když se odstraní živý výstup. Aby bylo možné zastavit živou událost, je nutné odstranit všechny živé výstupy. Pomocí volitelného příznaku [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) můžete automaticky odebrat živé výstupy při zastavení.

I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, pokud prostředek neodstraníte. Asset by neměl být odstraněn, pokud jej používá událost; nejdříve je nutné odstranit událost.

Pokud jste publikovali Asset svého živého výstupu pomocí lokátoru streamování, bude se dál zobrazovat živá událost (až do délky okna DVR), dokud nevyprší platnost nebo odstranění lokátoru streamování, podle toho, co nastane dřív.

Další informace naleznete v tématu:

- [Přehled živého streamování](live-streaming-overview.md)
- [Kurz živého streamování](stream-live-tutorial-with-api.md)

> [!NOTE]
> Když odstraníte živý výstup, neodstraníte základní Asset a obsah v assetu.

## <a name="next-steps"></a>Další kroky

* [Vystřihnout vaše videa](subclip-video-rest-howto.md).
* [Definujte filtry pro vaše prostředky](filters-dynamic-manifest-rest-howto.md).
