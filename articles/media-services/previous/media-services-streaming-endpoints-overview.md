---
title: Přehled služby Azure Media Services koncový bod | Dokumentace Microsoftu
description: Toto téma obsahuje přehled služby Azure Media Services koncové body streamování.
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
ms.openlocfilehash: a45e2af6f2cb9c105c084585a03a6de615fa1397
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573042"
---
# <a name="streaming-endpoints-overview"></a>Přehled koncových bodů streamování  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Projděte si nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

V Microsoft Azure Media Services (AMS) **koncový bod streamování** reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) k další distribuci. Služba Media Services také poskytuje bezproblémovou integraci Azure CDN. Výstupní datový proud ze služby StreamingEndpoint může být v živém datovém proudu, videa na vyžádání nebo progresivní stahování asset ve vašem účtu Media Services. Každý účet služby Azure Media Services obsahuje výchozí StreamingEndpoint. Další koncové body streamování se dají vytvořit v rámci účtu. Existují dvě verze koncové body streamování, 1.0 a 2.0. Od 10. ledna 2017, budou všechny nově vytvořené účty AMS zahrnují verze 2.0 **výchozí** StreamingEndpoint. Další koncové body streamování, které přidáte k tomuto účtu budou také verze 2.0. Tato změna neovlivní existující účty; stávající koncové body streamování se verze 1.0 a je možné upgradovat na verzi 2.0. Tato změna bude změny chování, fakturace a funkce (Další informace najdete v tématu **streamování typy a verze** části je uvedeno níže).

Azure Media Services přidat následující vlastnosti k entitě koncový bod streamování: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Podrobný přehled o těchto vlastnostech najdete v části [to](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Při vytváření účtu Azure Media Services koncový bod streamování standard je pro vás vytvořen výchozí **Zastaveno** stavu. Nelze odstranit výchozí koncový bod streamování. V závislosti na dostupnosti Azure CDN v cílové oblasti, ve výchozím nastavení nově vytvořen výchozí koncový bod streamování také zahrnuje "StandardVerizon" CDN poskytovatele integrace. 
                
> [!NOTE]
> Integrace s Azure CDN je možné zakázat před zahájením koncový bod streamování. `hostname` a adresu URL streamování zůstala stejná, zda povolit síť CDN.

Toto téma obsahuje přehled hlavních funkcí, které jsou poskytovány koncové body streamování.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Pro výchozí koncový bod: `{AccountName}.streaming.mediaservices.windows.net`

Pro všechny další koncové body: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Streamování typy a verze

### <a name="standardpremium-types-version-20"></a>Typy Standard nebo Premium (verze 2.0)

Od verze. ledna 2017 služby Media Services, budete mít dva typy datových proudů: **Standardní** (preview) a **Premium**. Tyto typy jsou součástí verze koncový bod streamování "2.0".


|Type|Popis|
|--------|--------|  
|**Standard**|Výchozí hodnota je koncový bod streamování **standardní** typu, typu Premium lze změnit úpravou jednotek streamování.|
|**Premium** |Tato možnost je vhodná pro profesionální scénáře, které vyžadují větší měřítko nebo ovládací prvek. Přejdete **Premium** typ úpravou jednotek streamování.<br/>Vyhrazené koncové body streamování živě v izolovaném prostředí a nesoupeřily o prostředky.|

Zákazníci, kteří se chtějí k doručování obsahu velkému publiku internet doporučujeme, abyste povolili CDN na koncový bod streamování.

Podrobnější informace najdete v článku [porovnání datových proudů typy](#comparing-streaming-types) následující části.

### <a name="classic-type-version-10"></a>Typ Classic (verze 1.0)

Pro uživatele, kteří si vytvořili účty AMS před vydáním verze 10. ledna 2017, budete mít **Classic** typu koncový bod streamování. Tento typ je součástí koncového bodu streamování verze "1.0".

Pokud vaše **verze "1.0"** koncový bod streamování má > = 1 (SU), jednotky streamování premium bude koncový bod streamování premium a bude poskytovat všechny funkce AMS (stejně jako **úrovně Standard nebo Premium** typ) bez všech dalších kroků konfigurace.

>[!NOTE]
>**Klasické** koncové body streamování (verze "1.0" a 0 SU), poskytuje omezené funkce a neobsahuje žádnou smlouvu SLA. Doporučuje se migrovat do **standardní** typ lepší prostředí a pomocí funkcí, jako je dynamické balení a šifrování a další funkce, které jsou součástí **standardní** typu. Migrace na **standardní** typu, přejděte na [webu Azure portal](https://portal.azure.com/) a vyberte **Opt-in na úroveň Standard**. Další informace o migraci, najdete v článku [migrace](#migration-between-types) oddílu.
>
>Mějte na paměti, že tuto operaci nelze vrátit zpět a má vliv na cenu.
>
 
## <a name="comparing-streaming-types"></a>Porovnání typů datových proudů

### <a name="versions"></a>Verze

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Fakturace|
|--------------|----------|-----------------|-----------------|-----------------|
|Classic|1.0|0|Není k dispozici|Free|
|Koncový bod streamování Standard (preview)|2.0|0|Ano|Placené|
|Jednotky streamování Premium|1.0|> 0|Ano|Placené|
|Jednotky streamování Premium|2.0|> 0|Ano|Placené|

### <a name="features"></a>Funkce

Funkce|Standard|Premium
---|---|---
Zdarma prvních 15 dnů <sup>1</sup>| Ano |Ne
Propustnost |Až 600 MB/s a může poskytnout mnohem vyšší efektivní výkon při použití sítě CDN.|200 MB/s za jednotku (SU) pro streaming. Může poskytnout mnohem vyšší efektivní výkon při použití sítě CDN.
CDN|Azure CDN, třetích stran CDN nebo žádné CDN.|Azure CDN, třetích stran CDN nebo žádné CDN.
Ceny jsou poměrně přepočítané| Denně|Denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Měřítko|Automatické škálování až na cílové propustnosti.|Dodatečné jednotky streamování.
Filtrování/G20/vlastního hostitele IP <sup>2</sup>|Ano|Ano
Progresivní stahování|Ano|Ano
Doporučené použití |Doporučuje se pro většinu scénářů streamování.|Profesionální použití. 

<sup>1</sup> bezplatné zkušební verze platí jenom pro účty služby nově vytvořené médium a ve výchozím nastavení koncového bodu streamování.<br/>
<sup>2</sup> pouze použít přímo na koncový bod streamování, pokud síť CDN není povolené pro koncový bod.<br/>

Informace o smlouvách SLA najdete v článku [ceny a smlouva SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrace mezi typy

Od | Akce | Akce
---|---|---
Classic|Standard|Muset vyjádřit výslovný souhlas
Classic|Premium| Škálování (dodatečné jednotky streamování)
Úrovně Standard nebo Premium|Classic|Není k dispozici (Pokud streamování koncový bod verze je 1.0. Je povoleno ji změňte na classic se nastavení scaleunits "0")
Standard (s nebo bez CDN)|Premium se stejnou konfigurací|V povolena **spuštění** stavu. (prostřednictvím webu Azure portal)
Premium (s nebo bez CDN)|Standard s stejnou konfiguraci|V povolena **spuštění** stavu (prostřednictvím webu Azure portal)
Standard (s nebo bez CDN)|Premium s jinou config|V povolena **zastavena** stavu (prostřednictvím webu Azure portal). Není povoleno v běžícím stavu.
Premium (s nebo bez CDN)|Standard s jinou config|V povolena **zastavena** stavu (prostřednictvím webu Azure portal). Není povoleno v běžícím stavu.
Verze 1.0 s SU > = 1 s CDN|Úrovně Standard nebo Premium se sítěmi CDN, žádné|V povolena **zastavena** stavu. Není povoleno v **spuštění** stavu.
Verze 1.0 s SU > = 1 s CDN|Standard s/bez CDN|V povolena **zastavena** stavu. Není povoleno v **spuštění** stavu. Verze 1.0 CDN bude jeden odstraněný a nového vytvoření a spuštění.
Verze 1.0 s SU > = 1 s CDN|Úrovně Premium/bez CDN|V povolena **zastavena** stavu. Není povoleno v **spuštění** stavu. Klasické CDN bude jeden odstraněný a nového vytvoření a spuštění.

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

