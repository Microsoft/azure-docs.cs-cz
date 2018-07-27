---
title: Omezení a známé problémy v rozhraní API služby Azure API Management importovat | Dokumentace Microsoftu
description: Podrobnosti o známých problémech a omezení pro import do Azure API Management pomocí formáty Open API, WSDL nebo WADL.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: a9f4a4ed4a8771f32a4d66aed2457a43abb92a63
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285710"
---
# <a name="api-import-restrictions-and-known-issues"></a>Omezení pro import rozhraní API a známé problémy
## <a name="about-this-list"></a>Informace o tomto seznamu
Při importu rozhraní API, může setkat určitá omezení nebo identifikovat problémy, které je potřeba opravit, bylo možné úspěšně naimportovat. Tento článek dokumenty, uspořádány podle formát importu rozhraní API.

## <a name="open-api"> </a>OpenAPI/Swagger
Pokud se vám zobrazuje chyby, Import dokumentu OpenAPI, ujistěte se, jste ověřili jeho – buď pomocí návrháře na webu Azure Portal (návrh - front-endu – Editor specifikace OpenAPI), nebo s třetími stranami nástroj, jako <a href="http://editor.swagger.io">editoru Swagger</a>.

* Je podporován jen formát JSON OpenAPI.
* Požadované parametry napříč cestu a dotaz musí mít jedinečné názvy. (V OpenAPI název parametru pouze musí být jedinečný v rámci umístění, třeba cestu, dotaz, záhlaví.  Ale ve službě API Management umožňujeme operace rozlišované cestu a dotaz parametry (které OpenAPI nepodporuje). Proto požadujeme, aby názvy parametrů být jedinečný v rámci celou adresu URL šablony.)
* Schémata odkazovat pomocí **$ref** vlastnosti nemůže obsahovat jiné **$ref** vlastnosti.
* **$ref** ukazatelé nemohou odkazovat na externí soubory.
* **x-ms cesty** a **servery x** jsou jediné podporované přípony.
* Vlastní rozšíření jsou ignorovány při importu a nejsou uloženy ani zachovají pro export.

> [!IMPORTANT]
> Důležité informace a tipy týkající se importu OpenAPI najdete v tomto [dokumentu](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/).

## <a name="wsdl"> </a>WSDL
Soubory WSDL se používají k rozhraní API protokolu SOAP předávací Generovat nebo sloužit jako back-end rozhraní SOAP do REST API.
* **Vazby SOAP** – jsou podporovány pouze SOAP vazby styl "dokumentu" a "literál" kódování. Není dostupná podpora pro styl "rpc" nebo kódování SOAP.
* **WSDL: import** – tento atribut se nepodporuje. Zákazníci by měl sloučení importů do jednoho dokumentu.
* **Zprávy s více částmi** – tyto typy zpráv, které nejsou podporovány.
* **WCF wsHttpBinding** -SOAP služby vytvořené pomocí Windows Communication Foundation by měl používat basicHttpBinding – wsHttpBinding nepodporuje.
* **MTOM** – služby pomocí MTOM <em>může</em> fungovat. V tuto chvíli není dostupná podpora oficiální.
* **Rekurze** – typy, které jsou definované rekurzivně (například odkazovat na pole samy o sobě) nejsou podporovány APIM.

## <a name="wadl"> </a>WADL
V současné době nejsou žádné známé problémy při importu WADL.
