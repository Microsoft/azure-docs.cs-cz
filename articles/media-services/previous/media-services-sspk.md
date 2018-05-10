---
title: Licencování Smooth streamování klienta Microsoft® portování Kit
description: Další informace o tom k licencování Microsoft® technologie Smooth Streaming klienta portování Kit.
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
ms.openlocfilehash: b4472f522571e0056ce6b28d67a69b0dcabba8a6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencování Smooth streamování klienta Microsoft® portování Kit
## <a name="overview"></a>Přehled
Microsoft technologie Smooth Streaming klienta portování Kit (**SSPK** pro zkrácení) je technologie Smooth Streaming implementace klienta, která je optimalizovaná tak, aby pomůže výrobců vložená zařízení, kabel a mobilní operátory, poskytovatelé přístupu k obsahu, telefonního sluchátka výrobců nezávislí dodavatelé softwaru (ISV) a poskytovatelé řešení k vytvoření produktů a služeb pro streamování s adaptivní obsah ve formátu technologie Smooth Streaming. SSPK je nezávislé na platformě implementace technologie Smooth Streaming klienta, který může přenést držitel licence na žádný zařízení a platformy a zařízení. 

Uvedeném níž je architektura vysoké úrovně a pole IIS technologie Smooth Streaming portování Kit je implementace klienta funkce Smooth Streaming od společnosti Microsoft a obsahuje všechny základní logiku pro přehrávání obsahu, technologie Smooth Streaming. Tento obsah je poté přenést partnery pro určité zařízení nebo platformu implementací příslušná rozhraní. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Popis
SSPK je licencovaná podle podmínek, které nabízí vynikající obchodní hodnotu. Licence SSPK poskytuje oborový s:

* Technologie Smooth Streaming portování Kit zdroje v jazyce C++ 
  * implementuje funkce klienta funkce Smooth Streaming
  * Přidá formátu analýzy heuristiky, ukládání do vyrovnávací paměti logiku atd.
* Aplikace přehrávače rozhraní API 
  * programovací rozhraní pro interakci s aplikací media player
* Platforma Abstraction Layer (PAL) rozhraní 
  * programovací rozhraní pro interakci s operačním systémem (vláken, sockets)
* Hardware abstraktní vrstvu HAL rozhraní 
  * programovací rozhraní pro interakci s hardwarem A / dekodérů V (dekódování, vykreslování)
* Rozhraní digitální Rights Management (DRM) 
  * programovací rozhraní pro zpracování DRM prostřednictvím DRM Abstraction Layer (DAL)
  * Microsoft PlayReady portování Kit dodává samostatně, ale integruje prostřednictvím tohoto rozhraní. Další informace o licencování Microsoft PlayReady Device, klikněte na tlačítko [zde](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Implementace ukázky 
  * Ukázka PAL implementace pro Linux
  * Ukázka HAL implementace pro GStreamer

## <a name="licensing-options"></a>Možnosti licencování
Microsoft technologie Smooth Streaming klienta portování Kit je k dispozici pro držitele licence v rámci dvou různých licenční smlouvy: jeden pro vývoj technologie Smooth Streaming produkty klienta provizorní a druhý pro distribuci technologie Smooth Streaming klienta konečné produkty pro koncové uživatele.

* Pro čipovou sadou výrobců, integrátory nebo nezávislé softwaru dodavatelé (ISV), kteří vyžadují zdroj kódu přenosem kit vyvíjet provizorní produkty, Microsoft technologie Smooth Streaming klienta portování Kit **licenci produktu provizorní** by měl být spuštěn.
* Pro výrobce zařízení nebo nezávislí výrobci softwaru, kteří potřebují oprávnění pro distribuční produktů technologie Smooth Streaming klienta konečné koncovým uživatelům, technologie Smooth Streaming klienta portování sadu Kit Microsoft **konečné licenci produktu** by měl být spuštěn.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft klienta funkce Smooth Streaming portování licenci produktu provizorní Kit
V části tuto licenci společnost Microsoft nabízí technologie Smooth Streaming klienta portování sady a potřebné duševnímu vlastnictví vyvinout a distribuovat technologie Smooth Streaming produkty klienta provizorní pro držitele licence jiné technologie Smooth Streaming klienta portování Kit zařízení, Distribuujte technologie Smooth Streaming klienta konečné produkty.

#### <a name="fee-structure"></a>Struktura poplatků
Jednorázové licenční poplatek 50 000 USA Kč poskytuje přístup k sadě technologie Smooth Streaming klienta portování Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft klienta funkce Smooth Streaming portování licenci produktu konečné Kit
V části tuto licenci společnost Microsoft nabízí všechny potřebné duševnímu vlastnictví přijímat technologie Smooth Streaming produkty klienta provizorní z jiné technologie Smooth Streaming klienta portování Kit držitele licence a distribuovat značky společnosti technologie Smooth Streaming klienta konečné Produkty pro koncové uživatele.

#### <a name="fee-structure"></a>Struktura poplatků
Technologie Smooth Streaming konečné produktu klienta se nabízí v rámci modelu Licencovaní jako v části:

* dodaný 0.10 za implementaci zařízení
* Licencovaní je omezená na 50 000 $ každý rok
* Žádné licencované pro prvních 10 000 zařízení implementace každý rok 

## <a name="licensing-procedure-and-sspk-access"></a>Licencování postupu a SSPK přístup
E-mailu [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) pro všechny licencování dotazy.

[SSPK distribuční portál](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) je pro držitele licence registrované provizorní dostupné.

Provizorní a finální SSPK držitele licence můžete odeslat technické dotazy k [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft funkce Smooth Streaming držitele licence smlouvy dočasné produktu klienta
* Adroit obchodní řešení, Inc
* Pokročilé SA digitální všesměrové vysílání
* AirTies Kablosuz Iletism Sanayive zprávy Ticaret a. s.
* Albis technologie Ltd.
* Alticast Corporation
* Digitální Amazon služby, Inc.
* Technologie Arion, Inc.
* Software multimédia AVC Co., Ltd.
* Cavium, Inc.
* Zakoupení Corporation EchoStar
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Globální služby BV Liberty
* MediaTek Inc.
* MStar Co, Ltd
* NINTENDO Co., Ltd.
* OpenTV, Inc.
* Omezené digitální šafrán
* Elektrický Changhong Sichuan Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology, Inc.
* TCL technologie Electronics (Huizhou) Co., Ltd.
* Horní vítězství investice, Ltd.
* Vestel Elektronik Sanayi sunout Ticaret a. s.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft funkce Smooth Streaming držitele licence smlouvu konečného produktu klienta
* Pokročilé SA digitální všesměrové vysílání
* AirTies Kablosuz Iletism Sanayive zprávy Ticaret a. s.
* Albis technologie Ltd.
* Digitální Amazon služby, Inc.
* Technologie AmTRAN Co., Ltd.
* Arcadyan technologie Corporation
* Technologie Arion, Inc.
* SÍŤ SAN ELEKTRONİK ATMACA. JSTE TİC. A.Ş
* Britské Sky všesměrové vysílání omezené
* CastPal technologie Inc., Shenzhen
* Compal Electronics, Inc.
* Digitální Dongguan AV technologie Corp., Ltd.
* Zakoupení Corporation EchoStar
* Enseo, Inc.
* Omezené filmy Filmflex
* Fluendo S.A.
* Omezené inovace Gibson
* Informace o Applicantion Haier S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Mezinárodní Hisense Co., Ltd. 
* Homecast Co., Ltd
* Svazek Hai přesnost odvětví Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* NINTENDO Co., Ltd.
* SA oranžová
* Omezené digitální šafrán
* Širokopásmové připojení Sagemcom SAS
* Shenzhen Coship Electronics CO., LTD
* Elektrický Jiuzhou Shenzhen Co., Ltd
* Shenzhen Skyworth digitální technologie Co., Ltd
* Elektrický Changhong Sichuan Co., Ltd.
* Skardin průmyslové Corp.
* SKY Německo Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Omezené TCL zámořské Marketing (moři komerční zboží)
* Technicolor doručení technologie SAS
* Tongfang globální Ltd.
* Horní vítězství investice, Ltd.
* Produkty Toshiba Lifestyle & Corporation služby
* Univerzální Corporation média /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

