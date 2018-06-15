---
title: Přehled služby Azure Media Services streamování Endpoint | Microsoft Docs
description: Toto téma nabízí přehled Azure Media Services koncové body streamování.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 7b4077747d4c9ec581496786063fc8fbe0c6b247
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790430"
---
# <a name="streaming-endpoints-overview"></a>Přehled koncových bodů streamování 

## <a name="overview"></a>Přehled

V Microsoft Azure Media Services (AMS) **koncový bod streamování** představuje streamování služba, která může poskytnout obsah přímo k aplikaci player klienta nebo do obsah Delivery Network (CDN) pro další distribuci. Služba Media Services také poskytuje bezproblémovou integraci Azure CDN. Výstupní datový proud z StreamingEndpoint služba může být živý datový proud, video na vyžádání nebo progresivní stahování asset ve vašem účtu Media Services. Každý účet Azure Media Services obsahuje výchozí StreamingEndpoint. V rámci účtu se dají vytvořit další koncové body streamování. Existují dvě verze koncové body streamování, 1.0 a 2.0. Od ledna 2017 10, budou všechny nově vytvořené účty AMS obsahovat verze 2.0 **výchozí** StreamingEndpoint. Další streamování koncové body, které přidáte k tomuto účtu budou i verze 2.0. Tato změna nemá vliv na existující účty; stávající koncové body streamování bude verze 1.0 a lze upgradovat na verzi 2.0. V této změně bude změny chování, fakturace a funkce (Další informace najdete v tématu **streamování typy a verze** části popsané dole).

Kromě toho počínaje 2.15 verzí (vydané v ledna 2017), Azure Media Services přidali následující vlastnosti do koncového bodu streamování entity: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Podrobný přehled o těchto vlastností najdete v části [to](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Při vytváření účtu Azure Media Services výchozí se vám v vytvoří standardní koncový bod streamování **Zastaveno** stavu. Nelze odstranit výchozí koncový bod streamování. V závislosti na dostupnosti Azure CDN v cílové oblasti, ve výchozím nastavení nově vytvořen výchozí koncový bod streamování také zahrnuje "StandardVerizon" CDN integrace zprostředkovatele. 

>[!NOTE]
>Integrace se službou Azure CDN je možné zakázat před zahájením koncový bod streamování.

Toto téma poskytuje přehled hlavní funkce, které jsou poskytovány koncové body streamování.

## <a name="streaming-types-and-versions"></a>Streamování typy a verze

### <a name="standardpremium-types-version-20"></a>Typy Standard nebo Premium (verze 2.0)

Od ledna 2017 verze služby Media Services, máte dva typy streamování: **standardní** a **Premium**. Tyto typy jsou součástí na verzi koncový bod Streaming "2.0".

Typ|Popis
---|---
**Standard**|Toto je výchozí možnost, která bude fungovat pro většinu scénářů.<br/>Pomocí této možnosti získat nebo omezené SLA, prvních 15 dnů po spuštění koncového bodu streamování je bezplatná.<br/>Pokud vytvoříte více než jeden koncové body, streamování pouze první z nich je bezplatná prvních 15 dnů, ostatní fakturují, jakmile je spuštění. <br/>Všimněte si, že bezplatné zkušební verze se aplikuje jenom na nově vytvořený media services účty a výchozí koncový bod streamování. Stávající koncové body streamování a kromě vytvořený koncových bodů streamování není zahrnuje bezplatné zkušební období i nebudou upgradovány na verzi 2.0 nebo jsou vytvořené jako verze 2.0.
**Premium**|Tato možnost je vhodná pro odborníky v oblasti scénáře, které vyžadují vyšší škálování nebo ovládací prvek.<br/>Proměnné SLA založený na premium streamování zakoupili kapacitu jednotky (SU), vyhrazené koncových bodů streamování za provozu v izolovaném prostředí a není pokouší pro prostředky.

Podrobnější informace najdete v článku **porovnat streamování typy** následující části.

### <a name="classic-type-version-10"></a>Klasického typu (verze 1.0)

Pro uživatele, kteří vytvořili účty AMS před vydáním verze v ledna 2017 10, máte **Classic** typ koncového bodu streamování. Tento typ je součástí streamování "1.0" verzi koncový bod.

Pokud vaše **"1.0" verzi** koncový bod streamování má > = 1 premium streamování jednotky (SU), ji budou koncový bod streamování premium a bude poskytovat všechny funkce AMS (stejně jako **Standard nebo Premium** typu) bez žádné další kroky konfigurace.

>[!NOTE]
>**Classic** koncové body streamování (verze "1.0" a 0 SU), poskytuje omezené funkce a neobsahuje smlouvy o úrovni služeb. Doporučuje se migrovat do **standardní** typu získat lepší a použití funkcí jako dynamické balení nebo šifrování a další funkce, které jsou součástí **standardní** typu. Migrace do **standardní** typu, přejděte na [portál Azure](https://portal.azure.com/) a vyberte **Opt-in na Standard**. Další informace o migraci najdete v tématu [migrace](#migration-between-types) části.
>
>Mějte na paměti, že tuto operaci nelze vrátit zpět a má cenovou vliv.
>
 
## <a name="comparing-streaming-types"></a>Porovnávání datových proudů typy

### <a name="versions"></a>Verze

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Fakturace|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Classic|1.0|0|Není k dispozici|Free|Není k dispozici|
|Koncový bod streamování Standard|2.0|0|Ano|Placené|Ano|
|Jednotky streamování Premium|1.0|>0|Ano|Placené|Ano|
|Jednotky streamování Premium|2.0|>0|Ano|Placené|Ano|

### <a name="features"></a>Funkce

Funkce|Standard|Premium
---|---|---
Volné prvních 15 dnů| Ano |Ne
Propustnost |Až 600 MB/s při Azure CDN se nepoužívá. Dál škáluje s CDN.|200 MB/s na jednotku (SU) pro streaming. Dál škáluje s CDN.
SLA | 99.9|99,9 (200 MB/s na SU).
CDN|Azure CDN, třetích stran CDN nebo žádné CDN.|Azure CDN, třetích stran CDN nebo žádné CDN.
Fakturace je účtovány poměrnou částí.| Denně|Denně
Dynamické šifrování|Ano|Ano
Dynamické balení|Ano|Ano
Měřítko|Automatické škálování cílové propustnosti.|Další jednotky streamování
IP filtrování nebo G20 nebo vlastního hostitele|Ano|Ano
Progresivní stahování|Ano|Ano
Doporučené využití |Vhodné pro velká většina streamování scénáře.|Profesionální využití.<br/>Pokud se domníváte, že můžete mít potřeby nad rámec Standard. Kontaktujte nás (amsstreaming@microsoft.com) Pokud očekáváte souběžných cílovou skupinu velikost větší než 50 000 prohlížeče.


## <a name="migration-between-types"></a>Migrace mezi typy

Od | Akce | Akce
---|---|---
Classic|Standard|Potřeba opt-in
Classic|Premium| Škálování (Další jednotky streamování)
Standard nebo Premium|Classic|Není k dispozici (Pokud je streamování koncový bod verze 1.0. Je povolena změna na klasickém s nastavení scaleunits "0")
Standard (s/bez CDN)|Premium pomocí stejné konfigurace|V povolena **spuštění** stavu. (prostřednictvím portálu Azure)
Premium (s/bez CDN)|Standard s stejné konfigurace|V povolena **spuštění** stavu (prostřednictvím portálu Azure)
Standard (s/bez CDN)|Premium s různých konfiguračním|V povolena **zastavena** stavu (prostřednictvím portálu Azure). Není povoleno v běžícím stavu.
Premium (s/bez CDN)|Standard s různých konfiguračním|V povolena **zastavena** stavu (prostřednictvím portálu Azure). Není povoleno v běžícím stavu.
Verze 1.0 s SU > = 1 s CDN|Standard nebo Premium s žádné CDN|V povolena **zastavena** stavu. Není povoleno v **spuštění** stavu.
Verze 1.0 s SU > = 1 s CDN|Standard s/bez CDN|V povolena **zastavena** stavu. Není povoleno v **spuštění** stavu. Verze 1.0 CDN bude odstraněna a nové jeden vytvořit a spustit.
Verze 1.0 s SU > = 1 s CDN|Premium/bez CDN|V povolena **zastavena** stavu. Není povoleno v **spuštění** stavu. Classic CDN bude odstraněna a nové jeden vytvořit a spustit.

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

