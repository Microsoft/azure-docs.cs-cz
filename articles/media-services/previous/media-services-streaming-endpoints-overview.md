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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: f0f3ec25f10faa25b6b90ba4d8114c15d25131c6
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979135"
---
# <a name="streaming-endpoints-overview-legacy"></a>Přehled koncových bodů streamování (starší verze)

## <a name="overview"></a>Přehled

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

Od verze. ledna 2017 služby Media Services, budete mít dva typy datových proudů: **Standardní** a **Premium**. Tyto typy jsou součástí verze koncový bod streamování "2.0".

Type|Popis
---|---
**Standard**|Toto je výchozí možnost, která bude fungovat pro většinu scénářů.<br/>Pomocí této možnosti získáte/limited SLA, prvních 15 dnů po spuštění koncový bod streamování je zdarma.<br/>Pokud vytvoříte více než jeden datový proud koncové body, pouze první z nich je zdarma pro prvních 15 dnů, ostatní se účtují, jakmile je spuštění. <br/>Všimněte si, že bezplatné zkušební verze platí jenom pro účty služby nově vytvořené médium a výchozí koncový bod streamování. Stávající koncové body streamování a kromě vytvořených koncových bodů streamování není zahrnuje bezplatné zkušební období ještě jsou upgradovány na verzi 2.0 nebo jsou vytvořené jako verze 2.0.
**Premium**|Tato možnost je vhodná pro profesionální scénáře, které vyžadují větší měřítko nebo ovládací prvek.<br/>Proměnné SLA, která je založena na premium streamování (SU) jednotky zakoupená kapacita služby vyhrazené koncové body streamování živě v izolovaném prostředí a nesoupeřily o prostředky.

Podrobnější informace najdete v článku **porovnání datových proudů typy** následující části.

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

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Fakturace|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Classic|1.0|0|Není k dispozici|Free|Není k dispozici|
|Koncový bod streamování Standard|2.0|0|Ano|Placené|Ano|
|Jednotky streamování Premium|1.0|>0|Ano|Placené|Ano|
|Jednotky streamování Premium|2.0|>0|Ano|Placené|Ano|

### <a name="features"></a>Funkce

Funkce|Standard|Premium
---|---|---
Zdarma prvních 15 dnů| Ano |Ne
Propustnost |Až 600 MB/s při Azure CDN se nepoužívá. Škálování s CDN.|200 MB/s za jednotku (SU) pro streaming. Škálování s CDN.
SLA | 99.9|99,9 (200 MB/s za SU).
CDN|Azure CDN, třetích stran CDN nebo žádné CDN.|Azure CDN, třetích stran CDN nebo žádné CDN.
Ceny jsou poměrně přepočítané| Denně|Denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Měřítko|Automatické škálování až na cílové propustnosti.|Dodatečné jednotky streamování
IP filtrování/G20/vlastního hostitele|Ano|Ano
Progresivní stahování|Ano|Ano
Doporučené použití |Doporučuje se pro většinu scénářů streamování.|Profesionální použití.<br/>Pokud se domníváte, že máte potřebám Standard. Kontaktujte nás (amsstreaming@microsoft.com) Pokud budete chtít souběžných cílovou skupinu velikost větší než 50 000 prohlížeče.


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

