---
title: Omezení a známé problémy v Azure API Management API import | Microsoft Docs
description: Podrobnosti o známých problémech a omezení při importu do Azure API Management pomocí otevřené rozhraní API, WSDL nebo WADL formáty.
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
ms.openlocfilehash: 03d785898398cb0bcd7b43e8d7feab705bce4b34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598466"
---
# <a name="api-import-restrictions-and-known-issues"></a>Omezení import rozhraní API a známé problémy
## <a name="about-this-list"></a>O tomto seznamu
Při importu rozhraní API, můžete setkat při určitá omezení a identifikovat problémy, které musí být opravena, aby bylo možné úspěšně naimportovat. Tento článek dokumenty, tyto uspořádané podle formát import rozhraní API.

## <a name="open-api"> </a>OpenAPI/Swagger
Pokud se vám import dokumentu OpenAPI chyby, ujistěte se, jste ověřili jeho – buď pomocí návrháře na portálu Azure (návrhu - Front-endu - OpenAPI specifikace Editor), nebo s třetích stran nástroje, jako <a href="http://www.swagger.io">editoru Swagger</a>.

* Je podporován pouze formátu JSON pro OpenAPI.
* Požadované parametry napříč cesta a dotaz musí mít jedinečné názvy. (V OpenAPI název parametru pouze musí být jedinečný v rámci umístění, například cestu, dotaz, záhlaví.  Nicméně ve službě API Management povolujeme operace, které mají být rozlišované cesta a dotaz parametry (které OpenAPI nepodporuje). Proto je nutné, názvy parametrů být jedinečný v rámci celého šablony adresy URL.)
* Schémata odkazovat pomocí **$ref** vlastnosti nemůže obsahovat jiné **$ref** vlastnosti.
* **$ref** ukazatele nemůže odkazovat na externí soubory.
* **x-ms cesty** a **x servery** jsou pouze podporované přípony.
* Vlastní rozšíření se ignoruje při importu a nejsou uloženy ani zachovají pro export.

> [!IMPORTANT]
> Důležité informace a tipy týkající se importu OpenAPI najdete v tomto [dokumentu](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/).

## <a name="wsdl"> </a>WSDL
WSDL soubory se používají ke generování SOAP průchozí rozhraní API nebo sloužit jako back-end SOAP REST API.
* **Vazby protokolu SOAP** -jsou podporované jenom SOAP vazby styl "dokumentu" a "literálu" kódování. Neexistuje žádná podpora pro "rpc" styl nebo kódování SOAP.
* **WSDL: import** -tento atribut není podporován. Zákazníci měli sloučení importy do jednoho dokumentu.
* **Zprávy s více částmi** -tyto typy zpráv nejsou podporovány.
* **WCF wsHttpBinding** -SOAP služby vytvořené pomocí Windows Communication Foundation by měl používat basicHttpBinding – wsHttpBinding není podporován.
* **MTOM** – služeb pomocí MTOM <em>může</em> fungovat. V tuto chvíli není nabídnuta oficiální podporu.
* **Rekurze** – typy, které jsou definované rekurzivně (například odkazovat na pole sami) nejsou podporována APIM.

## <a name="wadl"> </a>WADL
V současné době nejsou žádné známé problémy WADL importu.
