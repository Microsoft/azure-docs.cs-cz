---
title: Licencování &reg; sady pro portování klienta Microsoft Smooth Streaming
description: Přečtěte si, jak licencovat &reg; sadu Microsoft Smooth streaminging SDK pro přenos klientů.
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
ms.openlocfilehash: c82fb9764b29119092bf313b2c10f00c983e8c03
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710766"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licencování &reg; sady pro portování klienta Microsoft Smooth Streaming

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Přehled
Sada Microsoft Smooth Streaming Client porting kit (**SSPK** pro krátké) je Smooth Streaming implementace klienta optimalizovaná tak, aby pomáhala vestavěným výrobcům zařízení, kabelovým a mobilním operátorům, poskytovatelům služeb obsahu, výrobcům na sluchátkách, nezávislým výrobcům softwaru (ISV) a poskytovatelům řešení vytvářet produkty a služby pro adaptivní obsah streamování ve formátu Smooth Streaming. SSPK je implementace klienta Smooth Streaming nezávislá na platformě, kterou může licence přenést na jakékoli zařízení a platformu. 

Níže je uveden seznam architektury vysoké úrovně a služba IIS Smooth Streaminging Kit je Smooth Streamingá implementace klienta poskytovaná Microsoftem a zahrnuje všechny základní logiky pro přehrávání Smooth Streaming obsahu. Tento obsah pak partneři přenášejí pro konkrétní zařízení nebo platformu implementací vhodných rozhraní. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
SSPK je licencovaná pro výrazy, které nabízejí skvělou obchodní hodnotu. SSPK License poskytuje odvětví s:

* Zdroj Smooth Streaminging Kit pro portování v C++ 
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
V rámci této licence Společnost Microsoft nabízí Smooth Streaming sadu klientských portů a nezbytná práva k duševnímu vlastnictví pro vývoj a distribuci Smooth Streamingch předběžných produktů klientů do jiných Smooth Streaming klientských licencí pro zařízení, které distribuují Smooth Streaming finální produkty klienta.

#### <a name="fee-structure"></a>Struktura poplatků
V případě jednorázového licenčního poplatku v USA $50 000 poskytuje přístup k sadě Smooth Streamingch portů pro přenos klientů. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Konečná licence produktu Microsoft Smooth Streaming clienting Kit pro porting
V rámci této licence Společnost Microsoft nabízí všechna nezbytná práva k duševnímu vlastnictví pro příjem Smooth Streamingch prodaných klientských produktů z jiných Smooth Streaming klientských licencí pro porting a pro koncové uživatele, které Smooth Streaming konečné produkty od klientů.

#### <a name="fee-structure"></a>Struktura poplatků
Konečný produkt Smooth Streaming klienta se nabízí v rámci licencovaného modelu, jako v rámci:

* $0,10 pro implementaci zařízení byla odeslána.
* Licencovaný omezené každý rok je $50 000.
* Žádné licenční poplatky pro první implementace zařízení 10 000 v roce 

## <a name="licensing-procedure-and-sspk-access"></a>Licenční postup a přístup k SSPK
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pro všechny dotazy na licencování.

Portál pro distribuci SSPK má přístup k registrovaným průběžným licencím.

Dočasné a konečné licence SSPK mohou odesílat technické dotazy [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming klientských provizorních licencí k produktu

* Adroit Business Solutions, Inc
* Rozšířené digitální vysílání SA
* Kablosuz Iletism Sanayive dis Ticaret neúčinné látky
* Albis Technologies Ltd.
* Alticast Corporation
* Služba Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia software co., Ltd.
* Cavium, Inc.
* EchoStar koupit společnost
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Služba svobody Global Services BV
* MediaTek Inc.
* MStar co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron s digitálním omezením
* Iština Changhong elektrických co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Hlavní investice do Victory, Ltd.
* Vestel Elektronik Sanayi ve Ticaret neúčinné
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Konečné licence k produktu Microsoft Smooth Streaming Client
* Rozšířené digitální vysílání SA
* Kablosuz Iletism Sanayive dis Ticaret neúčinné látky
* AmTRAN Technology Co., Ltd 
* Arcadyan Technology Corporation
* Arcelik A. S
* Kompatibilní s elektronikou, Inc.
* OMEZENÁ TECHNOLOGIE EXPRESS
* Fluendo S.A.
* FUNAI ELEKTROINSTALACE CO., LTD
* Hisense International co., Ltd. 
* HKC Corporation – omezená
* Hongkong Konka Ltd
* InnoLux Corporation
* Innopia Technologies, Inc
* K-Tronics (Suzhou) technologie co., Ltd. 
* Kaonmedia co., Ltd.
* KDDI Corporation
* Megabajtů Fame elektronika co. omezeno
* MIRC elektronika omezená
* Nintendo Co., Ltd.
* ONEPLUS ELECTRONICS (SHENZHEN) CO., LTD.
* Panasonic Corporation
* Qingdao Haier Optronics co., Ltd.
* Shenzhen ATEKO PHOTO elektřiny co., Ltd.
* Shenzhen Chuangwei – RGB Electronics Co., Ltd.
* Shenzhen Jiuzhou elektrických co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Maxmade Technology Co., Ltd
* Shenzhen MTC co., Ltd
* Digitální technologie Shenzhen Skyworth co., Ltd
* Iština Changhong elektrických co., Ltd.
* Skardin průmyslová Corp.
* Nebeská Deutschlanda Fernsehen GmbH & co. KG
* GLOBÁLNÍ SAS SMARDTV
* SoftAtHome
* Sony Corporation
* Technologie Technicolor Delivery, SAS
* Hlavní investice do Victory, Ltd.
* Aktualizace UMC v/v z. o.o.
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

