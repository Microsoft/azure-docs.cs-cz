---
title: Licencování Microsoft® technologie Smooth Streaming portování klienta
description: Další informace o tom, k licencování Microsoft® technologie Smooth Streaming klienta portování Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: cfowler
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: xpouyat
ms.openlocfilehash: 71cb4ff7c5948067dfb039caf69241cfa9e22408
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243892"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencování Microsoft® technologie Smooth Streaming portování klienta
## <a name="overview"></a>Přehled
Microsoft Smooth Streaming klienta portování Kit (**SSPK** zkráceně) je technologie Smooth Streaming klienta implementace, která je optimalizována pro pomáhají výrobci zařízení se systémem embedded, kabelu a mobilní operátoři, poskytovatelé služeb obsahu, telefonu výrobci, nezávislí výrobci softwaru (ISV) a poskytovatelé řešení k vytvoření produktů a služeb pro streamování obsahu s adaptivním ve formátu technologie Smooth Streaming. SSPK je nezávislá na platformě implementace klienta Smooth Streaming, který může přenést pomocí licence na jakékoli zařízení a platformy a zařízení. 

Uvedeném níž je architektura vysoké úrovně a služby IIS technologie Smooth Streaming portování Kit pole je implementace klienta funkce Smooth Streaming poskytované společností Microsoft a obsahuje všechny základní logiku pro přehrávání obsahu Smooth Streaming. Tento obsah je poté přenáší od partnerů pro určité zařízení nebo platformu implementací odpovídající rozhraní. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Popis
SSPK je licencovaný podle podmínek, které nabízí vynikající přidanou hodnotu. Obsahuje licenci SSPK obor pomocí:

* Technologie Smooth Streaming portování Kit zdroj v jazyce C++ 
  * implementuje funkce klienta funkce Smooth Streaming
  * Přidá formátu parsování heuristickými metodami, ukládání do vyrovnávací paměti logiky atd.
* Aplikace přehrávače rozhraní API 
  * programovací rozhraní pro interakci s aplikací media player
* Platforma Abstraction Layer (PAL) rozhraní 
  * programovací rozhraní pro interakci s operačním systémem (vláken, sockets)
* Hardware Abstrakční vrstvu HAL rozhraní 
  * programovací rozhraní pro interakci s hardwarem A / dekodérů V (dekódování, vykreslování)
* Rozhraní Digital Rights Management (DRM) 
  * programovací rozhraní pro zpracování DRM prostřednictvím DRM DAL (Abstraction Layer)
  * Microsoft PlayReady portování Kit dodává samostatně, ale integruje prostřednictvím tohoto rozhraní. Podrobné informace o licencování Microsoft PlayReady Device, klikněte na tlačítko [tady](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Implementace ukázky 
  * Ukázková implementace PAL pro Linux
  * Ukázková implementace vrstvy HAL pro GStreamer

## <a name="licensing-options"></a>Možnosti licencování
Microsoft Smooth Streaming klienta portování Kit je k dispozici pro nabyvatelů licence za dvě odlišné licenční smlouvy: jeden pro vývoj, technologie Smooth Streaming produkty klienta prozatímní a druhý pro distribuci technologie Smooth Streaming klientské finální produkty pro koncové uživatele.

* Pro výrobce čipů, systémoví integrátoři nebo nezávislé výrobce softwaru (ISV), kteří vyžadují zdroj kódu přenosem kit na vývoj produktů prozatímní, Microsoft Smooth Streaming klienta přenesení sady **licenci na produkt prozatímní** by měl být spuštěn.
* Výrobci zařízení nebo nezávislým výrobcům softwaru, kteří požadují distribuční práva pro technologie Smooth Streaming klientské finální produkty koncovým uživatelům, Microsoft Smooth Streaming klienta portování Kit **konečný stav licencí produktu** by měl být spuštěn.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Portování licenci na produkt prozatímní Kit klienta Microsoft Smooth Streaming
Společnost Microsoft nabízí v rámci této licence, technologie Smooth Streaming klienta přenesení sady a práva k duševnímu potřebné k vývoji a distribuci technologie Smooth Streaming produkty klienta prozatímní nabyvatelů licence jiné technologie Smooth Streaming klienta přenesení sady zařízení, která distribuce technologie Smooth Streaming klientské finální produkty.

#### <a name="fee-structure"></a>Struktura poplatků
Jednorázové licenčního poplatku 50 000 dolarů USA poskytuje přístup k technologie Smooth Streaming klienta portování sadě. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming licenci na produkt závěrečné sady pro portování klienta
V rámci této licence společnost Microsoft nabízí všechny potřebné práva na duševní vlastnictví pro příjem technologie Smooth Streaming produkty klienta prozatímní z jiné technologie Smooth Streaming klienta portování Kit nabyvatelů licence a distribuovat značky společnosti technologie Smooth Streaming klienta konečný Produkty, aby koncoví uživatelé.

#### <a name="fee-structure"></a>Struktura poplatků
Technologie Smooth Streaming konečného produktu klienta se nabízí v rámci modelu nevýhradní jako v části:

* dodané 0.10 za implementaci zařízení
* Licencovaní je omezené na 50 000 dolarů, každý rok
* Žádné licencované pro prvních 10 000 zařízení implementace každý rok 

## <a name="licensing-procedure-and-sspk-access"></a>Licencování postup a SSPK přístup
E-mailu [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) pro všechny licence dotazy.

[SSPK distribuce portál](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) je přístupný pro registrované nabyvatelů prozatímní licence.

Prozatím a finální SSPK nabyvatelů licence můžete odeslat technické dotazy do [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming nabyvatelů klienta dočasné produktu smlouvy licence
* Adroit obchodních řešení, Inc.
* Pokročilé digitální vysílání SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret a. s.
* Albis technologie Ltd.
* Alticast Corporation
* Amazon digitálních služeb, Inc.
* Arion technologie, Inc.
* Software multimédia AVC Co., Ltd.
* Cavium, Inc.
* EchoStar zakoupení Corporation
* Enseo, Inc.
* S.A. Fluendo
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* S.A. iWEDIA 
* Globální služby BV svobody
* MediaTek Inc.
* Co MStar, Ltd
* NINTENDO Co., Ltd.
* OpenTV, Inc.
* Šafrán digitální Limited
* Sichuan Changhong Electric Co., Ltd.
* SoftAtHome
* Sony Corporation
* Tatung technologie Inc.
* TCL technologie (Huizhou) Electronics Co., Ltd.
* Horní vítězství investic, Ltd.
* Odebrat Vestel Elektronik Sanayi Ticaret a. s.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming nabyvatelů licence smlouvu konečného produktu klienta
* Pokročilé digitální vysílání SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret a. s.
* Albis technologie Ltd.
* Amazon digitálních služeb, Inc.
* Technologie AmTRAN Co., Ltd.
* Arcadyan technologie Corporation
* Arion technologie, Inc.
* ATMACA ELEKTRONİK PO SÍTI SAN. ODEBRAT TİC. A.Ş
* Britské Sky všesměrové vysílání Limited
* CastPal technologie Inc. Shenzhen
* Compal Electronics, Inc.
* Digitální Dongguan antivirová ochrana v programu Technology Corp., Ltd.
* EchoStar zakoupení Corporation
* Enseo, Inc.
* Omezené Filmflex filmy
* S.A. Fluendo
* Omezené Gibson inovace
* Informace o Applicantion Haier S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Mezinárodní Hisense Co., Ltd. 
* Homecast Co., Ltd.
* Svazek Hai přesnost Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* NINTENDO Co., Ltd.
* Oranžová SA
* Šafrán digitální Limited
* Širokopásmové připojení Sagemcom SAS
* Shenzhen Coship Electronics CO., Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd.
* Shenzhen Skyworth digitální technologie Co., Ltd.
* Sichuan Changhong Electric Co., Ltd.
* Skardin průmyslové Corp.
* Nebeská Deutschland Fernsehen GmbH & Co. KG
* S.A. SmarDTV
* SoftAtHome
* Sony Corporation
* Omezené TCL zámořské Marketing (Macao komerční moři)
* Technicolor doručování technologie SAS
* Globální Tongfang Ltd.
* Horní vítězství investic, Ltd.
* Toshiba Lifestyle produkty a služby Corporation
* S.r.o. /Slovakia/ univerzální Corporation média
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

