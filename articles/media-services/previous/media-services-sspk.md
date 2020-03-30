---
title: Licencování sady Microsoft® Sada portů klienta pro plynulé streamování
description: Přečtěte si, jak licencovat sadu Microsoft® Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: 59ac3ab2e70bfa00f19bae7e551780f2b2bb8ccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096713"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencování sady Microsoft® Sada portů klienta pro plynulé streamování 
## <a name="overview"></a>Přehled
Microsoft Smooth Streaming Client Porting Kit **(Zkráceně SSPK)** je implementace klienta Smooth Streaming, která je optimalizována tak, aby pomohla výrobcům vestavěných zařízení, kabelovým a mobilním operátorům, poskytovatelům služeb obsahu, výrobcům mobilních telefonů, nezávislým dodavatelům softwaru (ISV) a poskytovatelům řešení pro vytváření produktů a služeb pro streamování adaptivního obsahu ve formátu Smooth Streaming. SSPK je implementace bezproblémového klienta Smooth Streaming, která je závislá na zařízení a platformě, kterou může držitel licence přenést na libovolné zařízení a platformu. 

Níže je zahrnuta architektura na vysoké úrovni a iis smooth streaming porting kit box je implementace smooth streaming klienta poskytovaná společností Microsoft a zahrnuje všechny základní logiku pro přehrávání obsahu plynulého streamování. Tento obsah je pak portován partnery pro konkrétní zařízení nebo platformu implementací příslušných rozhraní. 

![Sada SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Popis
SSPK je licencován za podmínek, které nabízejí vynikající obchodní hodnotu. Licence SSPK poskytuje odvětví:

* Zdroj sady plynulého streamování portingu v jazyce C++ 
  * implementuje funkci klienta smooth streaming
  * přidává analýzu formátu, heuristiku, logiku ukládání do vyrovnávací paměti atd.
* Api aplikace přehrávače 
  * programovací rozhraní pro interakci s aplikací přehrávače médií
* Rozhraní vrstvy abstrakce platformy (PAL) 
  * programovací rozhraní pro interakci s operačním systémem (vlákna, sokety)
* Rozhraní vrstvy abstrakce hardwaru (HAL) 
  * programovací rozhraní pro interakci s hardwarovými A/V dekodéry (dekódování, vykreslování)
* Rozhraní správy digitálních práv (DRM) 
  * programovací rozhraní pro zpracování DRM prostřednictvím vrstvy drm abstrakce (DAL)
  * Microsoft PlayReady Porting Kit dodává samostatně, ale integruje prostřednictvím tohoto rozhraní. Další podrobnosti o licencování zařízení Microsoft PlayReady naleznete [zde](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Prováděcí vzorky 
  * ukázka implementace PAL pro Linux
  * ukázková implementace HAL pro GStreamer

## <a name="licensing-options"></a>Možnosti licencování
Microsoft Smooth Streaming Client Porting Kit je k dispozici držitelům licencí na základě dvou odlišných licenčních smluv: jeden pro vývoj hladkých datových proudů klienta prozatímní produkty a další pro distribuci smooth streaming klienta konečné produkty pro koncové uživatele.

* Pro výrobce čipových sad, systémové integrátory nebo nezávislé dodavatele softwaru ,,isv), kteří k vývoji prozatímních produktů vyžadují portovací sadu zdrojového kódu, by měla být spuštěna **prozatímní licence pro** portování klienta Microsoft Smooth Streaming.
* Pro výrobce zařízení nebo nedržitelé nešíření, kteří potřebují distribuční práva pro konečné produkty klienta pro hladké streamování koncovým uživatelům, by měla být spuštěna licence ke konečnému produktu Microsoft Smooth Streaming Client Porting **Kit.**

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit Prozatímní licence produktu
V rámci této licence nabízí společnost Microsoft sadu Smooth Streaming Client Porting Kit a nezbytná práva duševního vlastnictví k vývoji a distribuci dočasných produktů pro klienty hladkého streamování jiným držitelům licencí sady Pro portování klienta pro hladký streamování, kteří distribuovat produkty Smooth Streaming Client Final Products.

#### <a name="fee-structure"></a>Struktura poplatků
Americký jednorázový licenční poplatek ve výši 50 000 USD poskytuje přístup k portovací kitu Klienta pro plynulé streamování. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming Client Porting Kit Final Product License
V rámci této licence nabízí společnost Microsoft všechna potřebná práva duševního vlastnictví k získání dočasných produktů klienta Smooth Streaming od jiných držitelů licencí na portování klienta pro hladký streamování a k distribuci společnosti s označením Smooth Streaming Client Final Produkty pro koncové uživatele.

#### <a name="fee-structure"></a>Struktura poplatků
Konečný produkt klienta Smooth Streaming je nabízen v rámci licenčního modelu, jak je tomu v:

* $0.10 za dodané implementaci zařízení
* Licenční poplatky jsou omezeny na 50.000 dolarů ročně
* Žádné licenční poplatky za prvních 10 000 implementací zařízení každý rok 

## <a name="licensing-procedure-and-sspk-access"></a>Licenční řízení a přístup s spk
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pro všechny licenční dotazy.

[Distribuční portál SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) je přístupný registrovaným dočasným držitelům licencí.

Prozatímní a konečné SSPK nabyvatelové mohou předložit technické otázky . [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licence na předběžnou smlouvu o produktu klienta Microsoft Smooth Streaming

* Společnost Adroit Business Solutions, Inc.
* Rozšířené digitální vysílání SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Společnost Alticast
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Nákupní corporation
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Liberty Globální služby BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Šafrán Digitální Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Top Vítězství Investice, Sro
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licence na konečnou smlouvu o produktech klienta Microsoft Smooth Streaming
* Rozšířené digitální vysílání SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Společnost Arcadyan Technology
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Šen-čen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Nákupní corporation
* Enseo, Inc.
* FilmFlex Filmy Limited
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier informace žadatelé S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Oranžová SA
* Šafrán Digitální Limited
* Sagemcom Širokopásmové SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Technologie dodávky Technicolor, SAS
* Tongfang Global S.r.o.
* Top Vítězství Investice, Sro
* Toshiba Lifestyle Produkty & Services Corporation
* Universal Media Corporation /Slovensko/ s.r.o.
* VIZIO, Inc.
* Společnost Wistron
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

