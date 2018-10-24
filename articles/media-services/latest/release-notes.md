---
title: Azure Media Services v3 poznámky k verzi | Dokumentace Microsoftu
description: Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje nejnovější informace o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: db68f979239a5783338d99360209ae231a75c936
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945031"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 poznámky k verzi 

Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

* Nejnovější vydané verzi
* Známé problémy
* Opravy chyb
* Zastaralé funkce
* Plány pro změny

## <a name="october-2018---ga"></a>. Října 2018 – GA

Tato část popisuje aktualizace Azure Media Services (AMS) dne.

### <a name="rest-v3-ga-release"></a>Verze GA v3 REST

[Verze GA v3 REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zahrnuje další rozhraní API pro Live, manifestu filtry na úrovni účtu/Asset a podpora DRM.

#### <a name="azure-resource-management"></a>Správa prostředků Azure 

Podpora správy prostředků Azure umožňuje jednotnou správu a operace rozhraní API (teď všechno na jednom místě).

Od této verze, můžete pomocí šablon Resource Manageru k vytvoření živé události.

#### <a name="improvement-of-asset-operations"></a>Zlepšení Asset operací 

Byly zavedeny následující vylepšení:

- Ingestování z adresy URL http (s) nebo adresy URL SAS úložiště objektů Blob v Azure.
- Zadejte vlastní kontejner názvy pro prostředky. 
- Jednodušší podpora výstupu k vytvoření vlastních pracovních postupů s využitím Azure Functions.

#### <a name="new-transform-object"></a>Nové transformace objektů

Nové **transformace** objekt zjednodušuje modelu kódování. Nový objekt umožňuje snadno vytvářet a sdílet kódování šablon Resource Manageru a předvolby. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Ověřování služby Active Directory a Azure RBAC

Ověřování Azure AD a řízení přístupu na základě Role (RBAC) umožňují zabezpečené transformací, LiveEvents, zásady klíč obsahu nebo prostředků podle Role nebo uživatelé ve službě Azure AD.

#### <a name="client-sdks"></a>Klientské sady SDK  

Jazyky podporované v Media Services v3: .NET Core, Javy, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Živé kódování aktualizace

Jsou zavedené následující živé kódování aktualizace:

- Nový režim s nízkou latencí pro živé (10 sekund end-to-end).
- (Zvýšení stability a další podporu zdrojového kodér) Vylepšená podpora RTMP ve službě.
- Ingestování RTMPS zabezpečené.

    Když vytvoříte Livestream, můžete teď získání 4 ingestovaných adres URL. Ingestování 4 adresy URL jsou téměř identické, mít stejný token streamování (AppId), jenom část čísla portu se liší. Dva z adres URL jsou primární a záložní pro RTMPS. 
- Podpora překódování 24 hodin. 
- Vylepšená podpora ad signalizace v RTMP prostřednictvím SCTE35.

#### <a name="improved-event-grid-support"></a>Vylepšená podpora služby Event Grid

Zobrazí se následující služby Event Grid podporují vylepšení:

- Integrace s Azure EventGrid pro jednodušší vývoj pomocí Logic Apps a Azure Functions. 
- Přihlásit k odběru událostí v kódování, živých kanálů a další.

### <a name="cmaf-support"></a>Podpora CMAF

Podpora šifrování CMAF a "cbcs" (iOS 11 +) Apple HLS a MPEG-DASH přehrávačů, které podporují CMAF.

### <a name="video-indexer"></a>Video Indexer

Video verzi Indexer GA byl jsme oznámili v srpnu. Nové informace o aktuálně podporovaných funkcích najdete v tématu [co je Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plány pro změny

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Modul Azure CLI 2.0, který obsahuje operace s funkcí (včetně Live, obsahu zásady klíčů, účtu nebo Asset filtry, streamování zásady) je již brzy. 

### <a name="known-issues"></a>Známé problémy

Tento problém týká pouze zákazníků, které používají rozhraní API ve verzi preview pro prostředek nebo AccountFilters.

Pokud jste vytvořili prostředky nebo filtry účtů 09/28 až 10/12 pomocí Media Services v3 rozhraní příkazového řádku nebo rozhraní API, musíte odebrat všechny prostředky a AccountFilters a znovu je vytvořit z důvodu konfliktu verze. 

## <a name="may-2018---preview"></a>Květen 2018 – Preview

### <a name="net-sdk"></a>.NET SDK

Následující funkce jsou k dispozici v rozhraní .net SDK:

* **Transformuje** a **úlohy** k zakódování nebo analyzovat mediálního obsahu. Příklady najdete v tématu [Stream soubory](stream-files-tutorial-with-api.md) a [analyzovat](analyze-videos-tutorial-with-api.md).
* **StreamingLocators** pro publikování a streamování obsahu do zařízení koncových uživatelů
* **StreamingPolicies** a **ContentKeyPolicies** konfigurace doručení klíče a ochrana obsahu (DRM) při doručování obsahu.
* **LiveEvents** a **LiveOutputs** konfigurace ingestování a archivaci obsah živého streamování.
* **Prostředky** ukládat a publikovat mediálního obsahu ve službě Azure Storage. 
* **Koncové body streamování** ke konfiguraci a škálování dynamické balení, šifrování a streamování živě i na vyžádání multimediálního obsahu.

### <a name="known-issues"></a>Známé problémy

* Po odeslání úlohy, můžete ingestovat zdrojového videa zdroje pomocí adresy URL HTTPS, adresy URL SAS nebo cesty k souborům umístěným v úložišti objektů Blob v Azure. AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled](media-services-overview.md)
