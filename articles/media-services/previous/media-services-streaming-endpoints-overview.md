---
title: Přehled koncového bodu streamování služby Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled koncových bodů streamování Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885615"
---
# <a name="streaming-endpoints-overview"></a>Přehled koncových bodů streamování  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Ve službě Microsoft Azure Media Services (AMS) představuje **koncový bod streamování** streamovací službu, která může doručovat obsah přímo do aplikace klientského přehrávače nebo do sítě pro doručování obsahu (CDN) pro další distribuci. Mediální služby také poskytují bezproblémovou integraci Azure CDN. Odchozí datový proud ze služby StreamingEndpoint může být živý přenos, video na vyžádání nebo postupné stahování vašeho datového zdroje v účtu Mediálních služeb. Každý účet Azure Media Services obsahuje výchozí StreamingEndpoint. Další StreamingEndpoints lze vytvořit pod účtem. Existují dvě verze StreamingEndpoints, 1.0 a 2.0. ledna 2017 budou všechny nově vytvořené účty AMS obsahovat **výchozí** streamovací bod Verze 2.0. Další koncové body streamování, které přidáte do tohoto účtu, budou také verze 2.0. Tato změna nebude mít vliv na stávající účty; existující StreamingEndpoints bude verze 1.0 a lze inovovat na verzi 2.0. S touto změnou dojde k chování, fakturace a změny funkcí (další informace naleznete v části **Typy datových proudů a verze** jsou popsány níže).

Azure Media Services přidala do entity koncového bodu streamování následující vlastnosti: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Podrobný přehled těchto vlastností naleznete v [tomto](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Když vytvoříte účet Azure Media Services, vytvoří se pro vás výchozí standardní koncový bod streamování ve stavu **Zastaveno.** Výchozí koncový bod streamování nelze odstranit. V závislosti na dostupnosti Azure CDN v cílové oblasti, ve výchozím nastavení nově vytvořený výchozí koncový bod streamování také zahrnuje "StandardVerizon" CDN integrace zprostředkovatele. 
                
> [!NOTE]
> Integrace Azure CDN můžete zakázat před spuštěním koncového bodu streamování. Adresa `hostname` URL a streamovaného vysílání zůstává stejná bez ohledu na to, zda povolíte cdn.

Toto téma poskytuje přehled hlavních funkcí, které jsou poskytovány koncové body streamování.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Pro výchozí koncový bod:`{AccountName}.streaming.mediaservices.windows.net`

Pro všechny další koncové body:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Typy a verze streamování

### <a name="standardpremium-types-version-20"></a>Standardní/prémiové typy (verze 2.0)

Počínaje vydáním mediálních služeb v lednu 2017 máte dva typy streamování: **Standard** (preview) a **Premium**. Tyto typy jsou součástí koncového bodu streamování verze "2.0".


|Typ|Popis|
|--------|--------|  
|**Standard**|Výchozí koncový bod streamování je **standardní** typ, lze změnit na typ Premium úpravou jednotek streamování.|
|**Premium** |Tato možnost je vhodná pro profesionální scénáře, které vyžadují vyšší škálování nebo kontrolu. Na typ **Premium** se přesunete úpravou jednotek streamování.<br/>Vyhrazené koncové body streamování žijí v izolovaném prostředí a nesoutěží o prostředky.|

Zákazníkům, kteří chtějí doručovat obsah velkým internetovým cílovým skupinám, doporučujeme povolit cdn v koncovém bodu streamování.

Podrobnější informace naleznete v následující části [Porovnání typů datových proudů.](#comparing-streaming-types)

### <a name="classic-type-version-10"></a>Klasický typ (verze 1.0)

Pro uživatele, kteří vytvořili účty AMS před vydáním 10.ledna 2017, máte **klasický** typ koncového bodu streamování. Tento typ je součástí koncového bodu streamování verze "1.0".

Pokud vaše **verze "1.0"** streaming koncový bod má >= 1 premium streaming jednotky (SU), bude premium streaming koncový bod a bude poskytovat všechny funkce AMS (stejně jako **standard / premium** typ) bez dalších kroků konfigurace.

>[!NOTE]
>**Klasické** koncové body streamování (verze "1.0" a 0 SU) poskytují omezené funkce a neobsahují adresu SLA. Doporučujeme migrovat na **typ Standard,** abyste získali lepší prostředí a používali funkce, jako je dynamické balení nebo šifrování, a další funkce, které jsou součástí **standardního** typu. Chcete-li migrovat na typ **Standard,** přejděte na [portál Azure](https://portal.azure.com/) a **vyberte Možnost Přihlásit se ke standardu**. Další informace o migraci naleznete v části [migrace.](#migration-between-types)
>
>Mějte na konstatování, že tuto operaci nelze vrátit zpět a má vliv na ceny.
>
 
## <a name="comparing-streaming-types"></a>Porovnání typů datových proudů

### <a name="versions"></a>Verze

|Typ|Streamovací endpointversion|Jednotky měřítka|CDN|Fakturace|
|--------------|----------|-----------------|-----------------|-----------------|
|Classic|1.0|0|Není k dispozici|Free|
|Standardní koncový bod streamování (náhled)|2.0|0|Ano|Zaplaceno|
|Prémiové streamovací jednotky|1.0|> 0|Ano|Zaplaceno|
|Prémiové streamovací jednotky|2.0|> 0|Ano|Zaplaceno|

### <a name="features"></a>Funkce

Funkce|Standard|Premium
---|---|---
Propustnost |Až 600 Mb/s a může poskytnout mnohem vyšší efektivní propustnost při použití CDN.|200 Mb/s na streamovací jednotku (SU). Může poskytnout mnohem vyšší efektivní propustnost při použití CDN.
CDN|Azure CDN, CDN třetí strany nebo žádné CDN.|Azure CDN, CDN třetí strany nebo žádné CDN.
Fakturace je poměrně| denně|denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Škálování|Automatické škálování až na cílovou propustnost.|Další jednotky streamování.
Filtrování IP/G20/Vlastní hostitel <sup>1</sup>|Ano|Ano
Postupné stahování|Ano|Ano
Doporučené použití |Doporučeno pro drtivou většinu scénářů streamování.|Profesionální použití. 

<sup>1</sup> Používá se pouze přímo na koncovém bodu streamování, pokud není v koncovém bodě povolena síť CDN.<br/>

Informace o sla naleznete v [tématu Ceny a SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrace mezi typy

From | Akce | Akce
---|---|---
Classic|Standard|Potřeba opt-in
Classic|Premium| Měřítko (další jednotky streamování)
Standardní/prémiový|Classic|Není k dispozici (Pokud je verze koncového bodu streamování 1.0. Je povoleno přejít na klasiku s nastavením scaleunits na "0")
Standardní (s/bez CDN)|Premium se stejnými konfiguracemi|Povoleno v **spuštěném** stavu. (přes portál Azure)
Premium (s/bez CDN)|Standard se stejnými konfiguracemi|Povoleno ve **spuštěném** stavu (přes portál Azure)
Standardní (s/bez CDN)|Premium s různými konfiguracemi|Povoleno v **zastaveném** stavu (přes portál Azure). Není povoleno v běžícím stavu.
Premium (s/bez CDN)|Standard s různými konfiguracemi|Povoleno v **zastaveném** stavu (přes portál Azure). Není povoleno v běžícím stavu.
Verze 1.0 s SU >= 1 s CDN|Standard/Premium bez CDN|Povoleno v **zastaveném** stavu. V **spuštěném** stavu není povoleno.
Verze 1.0 s SU >= 1 s CDN|Standard s/bez CDN|Povoleno v **zastaveném** stavu. V **spuštěném** stavu není povoleno. Verze 1.0 CDN budou odstraněny a nové vytvořené a spuštěné.
Verze 1.0 s SU >= 1 s CDN|Premium s/bez CDN|Povoleno v **zastaveném** stavu. V **spuštěném** stavu není povoleno. Classic CDN budou odstraněny a nový vytvořen a spuštěn.

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

