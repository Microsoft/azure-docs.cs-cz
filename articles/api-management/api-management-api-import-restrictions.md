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
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265443"
---
# <a name="api-import-restrictions-and-known-issues"></a>Omezení pro import rozhraní API a známé problémy
## <a name="about-this-list"></a>Informace o tomto seznamu
Při importu rozhraní API, může setkat určitá omezení nebo identifikovat problémy, které je potřeba opravit, bylo možné úspěšně naimportovat. Tento článek dokumenty, uspořádány podle formát importu rozhraní API.

## <a name="open-api"> </a>OpenAPI/Swagger

Pokud se zobrazuje chyby importu OpenAPI dokumentu, ujistěte se, že jste se ověřili předem. Můžete to udělat buď pomocí návrháře na webu Azure Portal (Editor specifikace OpenAPI návrhu - front-endu -) nebo pomocí nástroje třetích stran, jako <a href="https://editor.swagger.io">editoru Swagger</a>.

### <a name="open-api-general"> </a>Obecné

* Požadované parametry napříč cestu a dotaz musí mít jedinečné názvy. (V OpenAPI název parametru pouze musí být jedinečný v rámci umístění, třeba cestu, dotaz, záhlaví. Ale ve službě API Management jsme umožní operace, které mají být rozlišované cestu a dotaz parametry (které OpenAPI nepodporuje). That's důvod, proč požadujeme, aby názvy parametrů být jedinečný v rámci celou adresu URL šablony.)
* **$ref** ukazatelé nemohou odkazovat na externí soubory.
* **x-ms cesty** a **servery x** jsou jediné podporované přípony.
* Vlastní rozšíření jsou ignorovány při importu a nejsou uloženy ani zachovají pro export.
* **Rekurze** – API Management nepodporuje rekurzivní definice (například schémata odkazující na sebe).
* Adresa URL souboru zdroje (Pokud je k dispozici) se použije pro relativní serverové adresy URL.

### <a name="open-api-v2"> </a>OpenAPI verze 2

* Je podporován jen formát JSON.

### <a name="open-api-v3"> </a>OpenAPI verze 3

* Pokud mnoho **servery** nejsou zadány, API Management se pokusí vyberte první adresa URL protokolu HTTPs. Pokud nejsou k dispozici žádné adresy URL HTTPs - první adresa URL protokolu HTTP. Pokud nejsou k dispozici žádné adresy URL protokolu HTTP – adresa URL serveru bude prázdný.
* **Příklady** není podporován, ale **příklad** je.
* **Multipart/formulář data** se nepodporuje.

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
* **Více oborů názvů** – více oborů názvů, je možné ve schématu, ale jenom cílový obor názvů lze definovat části zprávy. Obory názvů než je cíl, které se používají k definování dalších vstupních nebo výstupních prvky nejsou zachovány. I když je možné importovat dokument WSDL, při exportu všechny části zprávy bude mít cílový obor názvů schématu WSDL.

## <a name="wadl"> </a>WADL
V současné době nejsou žádné známé problémy při importu WADL.
