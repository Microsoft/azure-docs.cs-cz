---
title: Hloubka kamery Azure Kinect DK
description: Seznamte se s provozními zásadami a klíčovými funkcemi hloubkové kamery ve službě Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, snímač, SDK, hloubka kamery, TOF, zásady, výkon, neplatnost
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277157"
---
# <a name="azure-kinect-dk-depth-camera"></a>Hloubka kamery Azure Kinect DK

Tato stránka popisuje, jak používat hloubkovou kameru v Azure Kinect DK. Hloubka kamery je druhá z obou kamer. Jak je popsáno v předchozích částech, druhá kamera je fotoaparát RGB.  

## <a name="operating-principles"></a>Provozní principy

Hloubka kamery Azure Kinect DK implementuje princip AMCW (ToF) s amplitudou (Time-of-letu). Fotoaparát přetypování na scénu přetypování do prostředí v blízkosti IR (Jmenuji Nir A). Pak zaznamená nepřímou měření času, který je z kamery potřebný k přenosu do scény a zpět.

Tato měření jsou zpracována za účelem generování mapy hloubky. Mapa hloubky je sada hodnot souřadnic Z pro každý pixel obrázku, měřená v jednotkách milimetrů.

Společně s mapou hloubky získáme také tzv. čistý infračervený čtení. Hodnota pixelů v čistém INFRAČERVENém čtení je úměrná množství světla vrácenému z scény. Obrázek vypadá podobně jako běžná bitová kopie IR. Následující obrázek ukazuje příklad mapy hloubky (vlevo) a odpovídající čistý obrázek IR (vpravo).

![Hloubka a IR vedle sebe](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Klíčové funkce

Mezi technické charakteristiky kamery hloubky patří:

- 1. ToFý čip pro vytváření obrázků s pokročilou technologií v pixelech, který umožňuje vyšší četnost modulace a přesnost hloubky.
- Dvě Jmenuji Nir A laserové diody povolují blízko a širší režimy hloubky (FoV).
- Nejmenší ToF pixel na světě, v 3,5 μm od 3,5 μm.
- Automatický výběr na základě počtu pixelů umožňuje čistě zachytit objekty blízko a daleko.
- Globální závěr, který umožňuje zvýšit výkon při slunečním záření.
- Metoda výpočtu hloubky s více fázemi, která umožňuje robustní přesnost i v případě, že se jedná o variaci čipu, laserové a napájecího napájení.
- Méně systematické a náhodné chyby.

![Depth – modul](./media/concepts/depth-camera-depth-module.jpg)

Hloubka kamery přenáší nezpracované počítačové bitové kopie s nezpracovanými modulem IR do hostitelského počítače. Na počítači převede software hloubky akcelerovaného grafického procesoru na mapě hloubky nezpracovaných signálů.Hloubka kamery podporuje několik režimů. Režimy **úzká pole zobrazení (FOV)** jsou ideální pro scény s menšími rozsahy v rozměrech X a Y, ale většími rozsahy v dimenzi Z--. Pokud má scéna velký rozsah X-a Y, ale menší rozsahy Z, jsou lépe vhodné **režimy FOV** .

Hloubka kamery podporuje **režimy binningu 2x2** pro rozšiřování rozsahu z v porovnání s odpovídajícími **režimy unbinned**. Binningu se provádí za cenu snížení rozlišení obrazu. Všechny režimy lze spustit až 30 snímků za sekundu (FPS) s výjimkou režimu 1 megapixel (MP), který běží s maximální frekvencí 15 fps. Hloubka kamery také poskytuje **pasivní režim IR**. V tomto režimu se neaktivují světelné signály kamery a pozorují se jenom okolní osvětlení.

## <a name="camera-performance"></a>Výkon kamery

Výkon kamery se měří jako systematické a náhodné chyby.

### <a name="systematic-error"></a>Systematická chyba

Systematická chyba je definována jako rozdíl mezi měřenou hloubkou po odebrání šumu a správnou hloubkou (podle potřeby). Vypočítáme dočasný průměr v mnoha snímcích statické scény, aby se co nejvíce odstranil šum s hloubkou. Systematická chyba je přesnější a je definována takto:

![Systematická chyba hloubky](./media/concepts/depth-camera-systematic-error.png)

Kde *d <sub>t</sub>* označuje hloubku míry v čase *t*, *N* je počet snímků použitých v postupu průměrování a *d <sub>gt</sub>* je hloubka pravdivosti základní desky.

Specifikace systematické chyby kamery hloubky se vylučuje rušení více cest (MPI). MPI je v případě, že jeden senzor obrazu integruje světlo, který se projeví více než jedním objektem. MPI je částečně zmírňovaná v naší hloubce fotoaparátu pomocí vyšších četností modulace a neplatnosti hloubky, kterou zavádíme později.

### <a name="random-error"></a>Náhodná chyba

Předpokládejme, že vezmeme 100 imagí stejného objektu, aniž byste museli přesunovat fotoaparát. Hloubka objektu bude v každém z imagí 100 mírně odlišná. Tento rozdíl je způsoben hlukem snímku. Hluk snímku je počet photonsů, které se na senzory v průběhu času mění náhodným faktorem. Tuto náhodnou chybu definujeme u statické scény jako směrodatnou odchylku hloubky v čase vypočítaném jako:

![Náhodná chyba hloubky](./media/concepts/depth-camera-random-error.png)

Kde *N* označuje počet měření hloubky, představuje *d <sub>t</sub>* hloubkové měření v čase *t* a *d* značí střední hodnotu vypočítanou ve všech měřeních hloubky *d <sub>t</sub>*.

## <a name="invalidation"></a>Zneplatnění

V některých situacích nemusí hloubka kamery poskytovat správné hodnoty pro některé pixely. V těchto situacích jsou pixely s hloubkou neověřené. Hodnota hloubky je označena jako neplatné pixely, která se rovná nule. Důvody pro nástroj pro hloubku nemůžou způsobit správné hodnoty:

- Mimo aktivní masku INFRAČERVENého osvětlení
- Nasycený infračervený signál
- Nízká signalizace IR
- Filtrovat izolované
- Rušení více cest

### <a name="illumination-mask"></a>Maska osvětlení

Pokud se nachází mimo aktivní masku INFRAČERVENého osvětlení, jsou pixely neověřené. Nedoporučujeme používat signál takových pixelů k výpočtu hloubky. Následující obrázek ukazuje příklad neplatnosti pomocí masky osvětlení. Neověřené pixely jsou černé barvy v pixelech v různých režimech FoV (vlevo) a šestiúhelník v úzkých FoV režimech (vpravo).

![Neplatnost mimo masku osvětlení](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Síla signálu

V pixelech se zruší platnost, pokud obsahují nasycený infračervený signál. Když jsou pixely nasycené, dojde ke ztrátě informací o fázi. Následující obrázek ukazuje příklad neplatnosti pomocí nasycených signálů IR. Podívejte se na šipky, které ukazují na ukázkové pixely v hloubkovém i INFRAČERVENém obrázku.

![Neplatnost sytosti](./media/concepts/depth-camera-invalidation-saturation.png)

K chybě může dojít také v případě, že signál IR není dostatečně silný pro generování hloubky. Následující obrázek ukazuje příklad neplatnosti pomocí nízkého signálu IR. Podívejte se na šipky, které ukazují na ukázkové pixely v hloubkovém i INFRAČERVENém obrazu.

![Nízký signál o neplatnosti](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Dvojznačná Hloubka

Je také možné zrušit platnost pixelů, pokud byly přijaty signály z více než jednoho objektu ve scéně. Častým případem, kdy je možné tento druh neplatnosti zobrazit, je v rozích.  Z důvodu geometrie scény se INFRAČERVENé světlo z kamery odrážejí na jednu zeď a na druhou. Tato odraza světla způsobuje nejednoznačnost v měřené hloubkě pixelu. Filtry v algoritmu hloubky zjišťují tyto dvojznačné signály a invalidateují pixely.

Následující obrázky znázorňují příklady neplatnosti pomocí zjišťování více cest. Také se můžete podívat, jak se v jednom zobrazení kamery (horní řádek) nebo v jiném zobrazení kamery (dolní řádek) zobrazí znovu platnost stejné oblasti Surface, která byla zrušena. Tento obrázek ukazuje, že povrchy, jejichž platnost byla zrušena z jedné perspektivy, mohou být viditelné z jiné.

![Navýšení neplatných – rohový roh](./media/concepts/depth-camera-invalidation-multipath.png)

Dalším běžným případem funkce Multipath je pixely, které obsahují smíšený signál z popředí a pozadí (například kolem okrajů objektů). Během rychlého pohybu se můžou zobrazit další neověřené pixely kolem okrajů. Další neověřené pixely jsou z důvodu intervalu expozice nezpracovaného zachycení hloubky,

![Navýšení neplatných funkcí MultiPath – okraje](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Další kroky

[Systémy souřadnic](coordinate-systems.md)
