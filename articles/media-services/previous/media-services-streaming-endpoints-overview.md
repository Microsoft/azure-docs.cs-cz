---
title: Přehled koncového bodu streamování Azure Media Services | Microsoft Docs
description: Tento článek obsahuje přehled Azure Media Services koncových bodů streamování.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009083"
---
# <a name="streaming-endpoints-overview"></a>Přehled koncových bodů streamování  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

V Microsoft Azure Media Services (AMS) **koncový bod streamování** představuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content DELIVERY Network (CDN) pro další distribuci. Media Services také poskytuje bezproblémovou integraci Azure CDN. Odchozí datový proud ze služby StreamingEndpoint může být živý datový proud, video na vyžádání nebo progresivní stažení assetu ve vašem účtu Media Services. Každý účet Azure Media Services obsahuje výchozí StreamingEndpoint. V rámci účtu se dají vytvořit další starají. Existují dvě verze starají, 1,0 a 2,0. Od desátého ledna 2017 budou všechny nově vytvořené účty AMS zahrnovat **výchozí** StreamingEndpoint verze 2,0. Další koncové body streamování, které přidáte do tohoto účtu, budou mít také verzi 2,0. Tato změna nebude mít vliv na stávající účty; stávající starají bude verze 1,0 a bude možné ji upgradovat na verzi 2,0. Tato změna bude mít za způsob změny chování, fakturace a funkcí (Další informace najdete v části **typy a verze streamování** ).

Azure Media Services do entity koncového bodu streamování se přidaly následující vlastnosti: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Podrobný přehled těchto vlastností najdete v [tomto](/rest/api/media/operations/streamingendpoint)tématu. 

Když vytvoříte účet Azure Media Services výchozí koncový bod streamování Standard se vytvoří v **zastaveném** stavu. Nemůžete odstranit výchozí koncový bod streamování. V závislosti na dostupnosti Azure CDN v cílové oblasti ve výchozím nastavení nově vytvořený výchozí koncový bod streamování zahrnuje taky integraci poskytovatele CDN "StandardVerizon". 
                
> [!NOTE]
> Před spuštěním koncového bodu streamování je možné zakázat integraci Azure CDN. `hostname`Adresa URL streamování a adresa URL pro streamování zůstane stejné, ať už povolíte CDN.

Toto téma obsahuje přehled hlavních funkcí poskytovaných koncovými body streamování.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Pro výchozí koncový bod: `{AccountName}.streaming.mediaservices.windows.net`

Pro všechny další koncové body: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Typy a verze streamování

### <a name="standardpremium-types-version-20"></a>Typy Standard a Premium (verze 2,0)

Od verze Media Services od ledna 2017 jsou k dispozici dva typy streamování: **Standard** (Preview) a **Premium**. Tyto typy jsou součástí koncového bodu streamování verze "2,0".


|Typ|Description|
|--------|--------|  
|**Standard**|Výchozí koncový bod streamování je **standardní** typ, dá se změnit na typ Premium úpravou jednotek streamování.|
|**Premium** |Tato možnost je vhodná pro profesionální scénáře, které vyžadují vyšší škálu nebo řízení. Přesunete se na typ **Premium** úpravou jednotek streamování.<br/>Vyhrazené koncové body streamování živě v izolovaném prostředí a nesoutěží o prostředky.|

Pro zákazníky, kteří chtějí doručovat obsah do rozsáhlých internetových cílových skupin, doporučujeme povolit CDN pro koncový bod streamování.

Podrobnější informace najdete v části [porovnání typů streamování](#comparing-streaming-types) v následujícím oddílu.

### <a name="classic-type-version-10"></a>Klasický typ (verze 1,0)

Pro uživatele, kteří vytvořili účty AMS před vydáním ledna 10 2017, máte **klasický** typ koncového bodu streamování. Tento typ je součástí koncového bodu streamování verze "1,0".

Pokud má koncový bod streamování **verze "1,0"** >= 1 prémiové jednotky streamování (SU), bude koncový bod streamování Premium a bude poskytovat všechny funkce AMS (stejně jako u typu **Standard/Premium** ) bez jakýchkoli dalších kroků konfigurace.

>[!NOTE]
>Koncové body streamování **Classic** (verze "1,0" a 0 SU) poskytují omezené funkce a nezahrnují smlouvu SLA. Doporučuje se migrovat na **standardní** typ a získat tak lepší možnosti a používat funkce, jako je dynamické balení nebo šifrování, a další funkce, které jsou součástí **standardního** typu. Pokud chcete provést migraci na **standardní** typ, klikněte na [Azure Portal](https://portal.azure.com/) a vyberte **výslovný souhlas se standardem**. Další informace o migraci najdete v části věnované [migraci](#migration-between-types) .
>
>Upozorňujeme, že tato operace se nedá vrátit zpátky a má dopad na ceny.
>
 
## <a name="comparing-streaming-types"></a>Porovnávání typů streamování

### <a name="versions"></a>Verze

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Fakturace|
|--------------|----------|-----------------|-----------------|-----------------|
|Klasický|1.0|0|NA|Free|
|Koncový bod streamování Standard (Preview)|2.0|0|Yes|Placené|
|Jednotky streamování na úrovni Premium|1.0|> 0|Yes|Placené|
|Jednotky streamování na úrovni Premium|2.0|> 0|Yes|Placené|

### <a name="features"></a>Funkce

Funkce|Standard|Premium
---|---|---
Propustnost |Až 600 MB/s a při použití CDN může poskytovat mnohem vyšší efektivní propustnost.|200 MB/s na jednotku streamování (SU). Může poskytovat mnohem vyšší efektivní propustnost při použití CDN.
CDN|Azure CDN, CDN třetí strany nebo síť CDN.|Azure CDN, CDN třetí strany nebo síť CDN.
Fakturuje se poměrná hodnota| Každý den|Každý den
Dynamické šifrování|Yes|Yes
Dynamické balení|Yes|Yes
Měřítko|Automatické škálování až na cílovou propustnost.|Další jednotky streamování.
Filtrování IP/G20/vlastní hostitel <sup>1</sup>|Yes|Yes
Progresivní stahování|Yes|Yes
Doporučené použití |Doporučuje se pro velká většina scénářů streamování.|Profesionální použití. 

<sup>1</sup> používá se jenom přímo na koncovém bodu streamování, když CDN není na koncovém bodu povolený.<br/>

Informace o smlouvě SLA najdete v článku [ceny a smlouvy SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrace mezi typy

Z | Záměr | Akce
---|---|---
Classic|Standard|Musíte se přihlásit
Klasický|Premium| Škálování (další jednotky streamování)
Standard/Premium|Klasický|Není k dispozici (Pokud je verze koncového bodu streamování 1,0. Může se změnit na klasický s nastavením scaleunits na hodnotu 0).
Standard (s/bez CDN)|Premium se stejnými konfiguracemi|Povoleno v **počátečním** stavu. (prostřednictvím Azure Portal)
Premium (s/bez CDN)|Standard se stejnými konfiguracemi|Povoleno v **počátečním** stavu (prostřednictvím Azure Portal)
Standard (s/bez CDN)|Premium s jinou konfigurací|Povoleno ve stavu **Zastaveno** (prostřednictvím Azure Portal). Není povoleno ve stavu spuštěno.
Premium (s/bez CDN)|Standard s jinou konfigurací|Povoleno ve stavu **Zastaveno** (prostřednictvím Azure Portal). Není povoleno ve stavu spuštěno.
Verze 1,0 s SU >= 1 s CDN|Standard/Premium bez CDN|Povoleno v **zastaveném** stavu. Není povoleno v **počátečním** stavu.
Verze 1,0 s SU >= 1 s CDN|Standard s/bez CDN|Povoleno v **zastaveném** stavu. Není povoleno v **počátečním** stavu. Verze 1,0 CDN se odstraní a vytvoří nový a spustí se.
Verze 1,0 s SU >= 1 s CDN|Premium s/bez CDN|Povoleno v **zastaveném** stavu. Není povoleno v **počátečním** stavu. Klasická síť CDN se odstraní a vytvoří nový a spustí se.

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
