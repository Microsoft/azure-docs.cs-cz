---
title: Omezení a podrobnosti o podpoře formátů rozhraní API
titleSuffix: Azure API Management
description: Podrobnosti o známých problémech a omezeních ve formátech Open API, WSDL a WADL podporují ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513367"
---
# <a name="api-import-restrictions-and-known-issues"></a>Omezení pro import rozhraní API a známé problémy

## <a name="about-this-list"></a>O tomto seznamu

Při importu rozhraní API můžete narazit na některá omezení nebo identifikovat problémy, které je třeba opravit, než budete moci úspěšně provést import. Tento článek dokumentuje tato omezení uspořádaná podle formátu importu rozhraní API. Také popisuje, jak openapi export funguje.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Omezení importu OpenAPI/Swagger

Pokud se při importu dokumentu OpenAPI doručují chyby, ujistěte se, že jste ho předem ověřili. Můžete to udělat buď pomocí návrháře na portálu Azure (Design - Front End - OpenAPI Specifikace Editor), nebo s nástrojem jiného výrobce, jako je <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>Obecné informace

-   Požadované parametry napříč cestou i dotazem musí mít jedinečné názvy. (V OpenAPI musí být název parametru jedinečný pouze v rámci umístění, například cesta, dotaz, záhlaví. Ve správě API však umožňujeme, aby operace byly diskriminovány parametry cesty i dotazu (které OpenAPI nepodporuje). Proto požadujeme, aby názvy parametrů byly jedinečné v rámci celé šablony adresy URL.)
-   `\$ref`ukazatele nemohou odkazovat na externí soubory.
-   `x-ms-paths`a `x-servers` jsou jedinými podporovanými rozšířeními.
-   Vlastní rozšíření jsou při importu ignorována a nejsou uložena ani zachována pro export.
-   `Recursion`- Správa rozhraní API nepodporuje definice definované rekurzivně (například schémata odkazující na sebe).
-   Adresa URL zdrojového souboru (je-li k dispozici) se použije na relativní adresy URL serveru.
-   Definice zabezpečení jsou ignorovány.
-   Definice inline schématu pro operace rozhraní API nejsou podporovány. Definice schématu jsou definovány v oboru rozhraní API a lze na ně odkazovat v oboru požadavků na operace rozhraní API nebo v oborech odpovědí.
-   Definovaný parametr adresy URL musí být součástí šablony adresy URL.
-   `Produces`klíčové slovo, které popisuje typy MIME vrácené rozhraním API, není podporováno. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI verze 2

-   Podporován je pouze formát JSON.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI verze 3

-   Pokud `servers` jsou zadány mnohé, správa rozhraní API se pokusí vybrat první adresu URL http. Pokud neexistují žádné adresy URL https - první adresa URL HTTP. Pokud nejsou k dispozici žádné adresy URL HTTP , adresa URL serveru bude prázdná.
-   `Examples`není podporován, ale `example` je.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mechanismy importu, aktualizace a exportu OpenAPI

### <a name="add-new-api-via-openapi-import"></a>Přidání nového rozhraní API prostřednictvím importu OpenAPI

Pro každou operaci nalezenou v dokumentu OpenAPI se vytvoří nová operace s `operationId` `summary` názvem prostředku Azure a zobrazovaným názvem nastaveným na a příslušně. `operationId`hodnota normalizuje podle níže popsaných pravidel. `summary`hodnota je importována tak, jak je, a její délka je omezena na 300 znaků.

Pokud `operationId` není zadán (to znamená, `null`není k dispozici, nebo prázdné), azure hodnota názvu prostředku se vygeneruje kombinací metody HTTP a šablony cesty, `get-foo`například .

Pokud `summary` není zadán (to znamená, `null`není k `display name` dispozici, nebo `operationId`prázdné), hodnota bude nastavena na . Pokud `operationId` není zadán, hodnota zobrazovaného názvu bude generována kombinací metody `Get - /foo`HTTP a šablony cesty, například .

### <a name="update-an-existing-api-via-openapi-import"></a>Aktualizace existujícího rozhraní API pomocí importu OpenAPI

Během importu se stávající rozhraní API změní tak, aby odpovídalo rozhraní API popsanému v dokumentu OpenAPI. Každá operace v dokumentu OpenAPI je spárována s `operationId` existující operací porovnáním její hodnoty s názvem prostředku Azure existující operace.

Pokud je nalezena shoda, vlastnosti existující operace budou aktualizovány "na místě".

Pokud shoda není nalezena, bude vytvořena nová operace pomocí pravidel popsaných ve výše uvedené části. Pro každou novou operaci se import pokusí zkopírovat zásady z existující operace se stejnou metodou HTTP a šablonou cesty.

Všechny existující neodpovídající operace budou odstraněny.

Chcete-li, aby byl import předvídatelnější, postupujte podle následujících pokynů:

- Ujistěte se, že určit `operationId` vlastnost pro každou operaci.
- Po počátečním importu se neměnte. `operationId`
- Nikdy `operationId` měnit a HTTP metoda nebo cesta šablony současně.

### <a name="export-api-as-openapi"></a>Exportovat rozhraní API jako OpenAPI

Pro každou operaci bude její název prostředku `operationId`Azure exportován jako a `summary`zobrazovaný název bude exportován jako .
Normalizační pravidla pro operationId

- Převést na malá písmena.
- Nahraďte každou sekvenci nealfanumerických znaků `GET-/foo/{bar}?buzz={quix}` například jednou `get-foo-bar-buzz-quix-`pomlčkou, která bude transformována na .
- Trim pomlčky na obou stranách, například, `get-foo-bar-buzz-quix-` se stane`get-foo-bar-buzz-quix`
- Zkrátit tak, aby se vešly 76 znaků, čtyři znaky menší než maximální limit pro název prostředku.
- Zbývající čtyři znaky použijte pro příponu odstranění duplicit, pokud je `-1, -2, ..., -999`to nutné, ve formě .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL soubory se používají k vytvoření SOAP předávací a SOAP-to-REST API.

-   **SOAP vazby** -Pouze SOAP vazby stylu "dokument" a "literál" kódování jsou podporovány. Neexistuje žádná podpora pro "rpc" styl nebo SOAP-Encoding.
-   **WSDL:Import** - Tento atribut není podporován. Zákazníci by měli sloučit importy do jednoho dokumentu.
-   **Zprávy s více částmi** – tyto typy zpráv nejsou podporovány.
-   **WCF wsHttpBinding** - SLUŽBY SOAP vytvořené pomocí windows communication foundation by měly používat základní httpbinding - wsHttpBinding není podporována.
-   **MTOM** - Služby používající MTOM <em>mohou</em> fungovat. Oficiální podpora není v tuto chvíli nabízena.
-   **Rekurze** – Typy, které jsou definovány rekurzivně (například odkazují na pole sami) nejsou podporovány APIM.
-   **Více oborů názvů** – ve schématu lze použít více oborů názvů, ale k definování částí zpráv lze použít pouze cílový obor názvů. Obory názvů jiné než cíl, které se používají k definování jiných vstupních nebo výstupních prvků, nejsou zachovány. Přestože takový dokument WSDL lze importovat, při exportu všechny části zpráv bude mít cílový obor názvů WSDL.
-   **Pole** - Transformace SOAP-to-REST podporuje pouze zalomená pole uvedená v příkladu níže:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

V současné době nejsou známy žádné problémy s importem wadl.
