---
title: Licencování sady Microsoft® Smooth Streaming klientských portů
description: Přečtěte si, jak licencovat sadu Microsoft® Smooth Streaminging SDK pro přenos klientů.
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
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096713"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencování sady Microsoft® Smooth Streaming klientských portů 
## <a name="overview"></a>Přehled
Sada Microsoft Smooth Streaming Client porting kit (**SSPK** pro krátké) je Smooth Streaming implementace klienta optimalizovaná tak, aby pomáhala vestavěným výrobcům zařízení, kabelovým a mobilním operátorům, poskytovatelům služeb obsahu, výrobcům na sluchátkách, nezávislým výrobcům softwaru (ISV) a poskytovatelům řešení vytvářet produkty a služby pro adaptivní obsah streamování ve formátu Smooth Streaming. SSPK je implementace klienta Smooth Streaming nezávislá na platformě, kterou může licence přenést na jakékoli zařízení a platformu. 

Níže je uveden seznam architektury vysoké úrovně a služba IIS Smooth Streaminging Kit je Smooth Streamingá implementace klienta poskytovaná Microsoftem a zahrnuje všechny základní logiky pro přehrávání Smooth Streaming obsahu. Tento obsah pak partneři přenášejí pro konkrétní zařízení nebo platformu implementací vhodných rozhraní. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Popis
SSPK je licencovaná pro výrazy, které nabízejí skvělou obchodní hodnotu. SSPK License poskytuje odvětví s:

* Zdroj Smooth Streaming pro přenosovou saduC++ 
  * implementuje klientské funkce Smooth Streaming
  * Přidá analýzu formátu, heuristiky, logiku ukládání do vyrovnávací paměti atd.
* Rozhraní API aplikací přehrávače 
  * programovací rozhraní pro interakci s aplikací přehrávače médií
* Rozhraní Abstraction Layer (PAL) platformy 
  * programovací rozhraní pro interakci s operačním systémem (vlákna, sokety)
* Rozhraní HAL (hardware Abstraction Layer) 
  * programovací rozhraní pro interakci s hardwarovými A/V dekodéry (dekódování, vykreslování)
* Rozhraní Digital Rights Management (DRM) 
  * programovací rozhraní pro zpracování DRM přes vrstvu abstrakce DRM (DAL)
  * Microsoft PlayReady porting kit se dodává samostatně, ale integruje se přes toto rozhraní. Další podrobnosti o licencování zařízení Microsoft PlayReady získáte kliknutím [sem](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Ukázky implementace 
  * Ukázka implementace PAL pro Linux
  * Ukázková implementace vrstvy HAL pro GStreamer

## <a name="licensing-options"></a>Možnosti licencování
Sada portů Microsoft Smooth Streaming clienting Kit je dostupná pro licence v rámci dvou různých licenčních smluv: jeden pro vývoj Smooth Streamingch předběžných produktů klienta a druhý pro distribuci Smooth Streaming finálních produktech klienta do koncových uživatelů.

* Pro výrobce čipové soustavy, systémové integrátory nebo nezávislé výrobce softwaru (ISV), kteří potřebují pro vývoj produktů pro vytváření dočasných produktů sadu portů pro přenos od zdrojového kódu, by se měla spustit **dočasná licence k produktu** Microsoft Smooth Streaming Client.
* Pro výrobce zařízení nebo nezávislého výrobce softwaru, kteří potřebují oprávnění k distribuci Smooth Streaming koncovým uživatelům konečné produkty klienta, by měla být spuštěna **konečná licence k produktu** Microsoft Smooth Streaming Client porting kit.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Dočasná licence produktu Microsoft Smooth Streaming Client porting kit
V rámci této licence Společnost Microsoft nabízí Smooth Streaming sadu klientských portů a nezbytná práva k duševnímu vlastnictví pro vývoj a distribuci Smooth Streamingch předběžných produktů klientů do jiných Smooth Streaming klientských licencí pro zařízení distribuovat Smooth Streaming finální produkty klienta.

#### <a name="fee-structure"></a>Struktura poplatků
V případě jednorázového licenčního poplatku v USA $50 000 poskytuje přístup k sadě Smooth Streamingch portů pro přenos klientů. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Konečná licence produktu Microsoft Smooth Streaming clienting Kit pro porting
V rámci této licence Společnost Microsoft nabízí všechna nezbytná práva k duševnímu vlastnictví pro příjem Smooth Streamingch předběžně vydaných produktů klienta z jiných Smooth Streaming klientských licencí pro portování a pro distribuci Smooth Streamingho klienta s firemními značkami. Produkty pro koncové uživatele.

#### <a name="fee-structure"></a>Struktura poplatků
Konečný produkt Smooth Streaming klienta se nabízí v rámci licencovaného modelu, jako v rámci:

* $0,10 pro implementaci zařízení byla odeslána.
* Licencovaný omezené každý rok je $50 000.
* Žádné licenční poplatky pro první implementace zařízení 10 000 v roce 

## <a name="licensing-procedure-and-sspk-access"></a>Licenční postup a přístup k SSPK
E-mailová [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pro všechny dotazy na licencování.

[Portál pro distribuci SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) má přístup k registrovaným průběžným licencím.

Průběžné a konečné licence SSPK mohou odesílat technické dotazy [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming klientských provizorních licencí k produktu

* Adroit Business Solutions, Inc
* Rozšířené digitální vysílání SA
* Kablosuz Iletism Sanayive dis Ticaret neúčinné látky
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar koupit společnost
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Služba svobody Global Services BV
* MediaTek Inc.
* MStar co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron s digitálním omezením
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Hlavní investice do Victory, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Konečné licence k produktu Microsoft Smooth Streaming Client
* Rozšířené digitální vysílání SA
* Kablosuz Iletism Sanayive dis Ticaret neúčinné látky
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar koupit společnost
* Enseo, Inc.
* FilmFlex filmy omezené
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier informace žadatele S. R. L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Oranžová SA
* Saffron s digitálním omezením
* Sagemcom Broadband SAS
* Shenzhen Chuangwei – RGB Electronics Co., Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou elektrických co., Ltd
* Digitální technologie Shenzhen Skyworth co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin průmyslová Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Technologie Technicolor Delivery, SAS
* Tongfang Global Ltd.
* Hlavní investice do Victory, Ltd.
* Toshiba životní výrobky produkty & Services Corporation
* Universal Media Corporation/Slovakia/s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

