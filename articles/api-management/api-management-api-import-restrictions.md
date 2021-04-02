---
title: Omezení a podrobnosti o podpoře formátů API
titleSuffix: Azure API Management
description: Podrobnosti o známých problémech a omezeních pro formáty Open API, WSDL a WADL podporované ve službě Azure API Management.
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
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932630"
---
# <a name="api-import-restrictions-and-known-issues"></a>Omezení pro import rozhraní API a známé problémy

## <a name="about-this-list"></a>O tomto seznamu

Při importu rozhraní API se můžete setkat s určitými omezeními nebo určit problémy, které je potřeba opravit, než budete moct úspěšně provést import. Tento článek popisuje tato omezení uspořádaná podle formátu importu rozhraní API. Popisuje také způsob fungování OpenAPI exportu.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Omezení importu openapi/Swagger

Pokud obdržíte chyby při importu dokumentu OpenAPI, ujistěte se, že jste ho předem ověřili. Můžete to udělat buď pomocí návrháře v Azure Portal (Editor specifikace front-end-OpenAPI Specification), nebo pomocí nástroje třetí strany, jako je <a href="https://editor.swagger.io">Editor Swagger</a>.

### <a name="general"></a><a name="open-api-general"> </a>Obecné

-   Požadované parametry v obou cestách i dotazech musí mít jedinečné názvy. (V OpenAPI musí být název parametru jedinečný jenom v rámci umístění, například cesta, dotaz, záhlaví. V API Management ale povolujeme, aby byly operace rozlišené pomocí cest i parametrů dotazů (které OpenAPI nepodporuje). To je důvod, proč potřebujeme, aby názvy parametrů byly jedinečné v rámci celé šablony URL.)
-   `\$ref` Ukazatelé nemůžou odkazovat na externí soubory.
-   `x-ms-paths` a `x-servers` jsou jediná podporovaná rozšíření.
-   Vlastní rozšíření se při importu ignorují a neukládají se ani neuchovávají pro export.
-   `Recursion` -API Management nepodporuje definice, které jsou definovány rekurzivně (například schémata odkazující samy na sebe).
-   Adresa URL zdrojového souboru (je-li k dispozici) se použije na relativní adresy URL serveru.
-   Definice zabezpečení jsou ignorovány.
-   Vložené definice schématu pro operace rozhraní API nejsou podporovány. Definice schématu jsou definované v oboru rozhraní API a můžou se na ně odkazovat v oborech požadavků a odpovědích na operace rozhraní API.
-   Definovaný parametr adresy URL musí být součástí šablony adresy URL.
-   `server` objekt není podporován na úrovni operace rozhraní API.
-   `Produces` klíčové slovo, které popisuje typy MIME vracené rozhraním API, se nepodporuje. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>Openapi verze 2

-   Podporován je pouze formát JSON.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>Openapi verze 3

-   Pokud `servers` je zadáno mnoho, API Management se pokusí vybrat první adresu URL https. Pokud nejsou žádné adresy URL protokolu HTTPs, první adresa URL protokolu HTTP. Pokud neexistují žádné adresy URL protokolu HTTP, adresa URL serveru bude prázdná.
-   `Examples` není podporován, ale `example` je.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI mechanismy importu, aktualizací a exportu

### <a name="general"></a><a name="open-import-export-general"> </a>Obecné

-   Definice rozhraní API exportované z API Management služby jsou primárně určené pro aplikace, které jsou externí pro API Management službu, které potřebují volat rozhraní API hostované ve službě API Management Service. Exportované definice rozhraní API nejsou určené k importování do stejné nebo jiné API Management služby. Pro správu konfigurace rozhraní API defiitions napříč různými či/envionments najdete informace v dokumentaci týkající se používání služby API Management v Gitu. 

### <a name="add-new-api-via-openapi-import"></a>Přidání nového rozhraní API prostřednictvím importu OpenAPI

Pro každou operaci nalezenou v dokumentu OpenAPI se vytvoří nová operace s názvem prostředku Azure a zobrazovaným názvem nastaveným na `operationId` a `summary` v uvedeném pořadí. `operationId` hodnota je normalizována podle pravidel popsaných níže. `summary` hodnota je importována tak, jak je, a její délka je omezena na 300 znaků.

Pokud `operationId` není zadaný (tj. není přítomen, `null` nebo je prázdný), bude hodnota názvu prostředku Azure vygenerována kombinací metody HTTP a šablony cesty, například `get-foo` .

Pokud `summary` není zadán (tj. není přítomen, `null` nebo je prázdný), `display name` hodnota se nastaví na `operationId` . Pokud `operationId` parametr není zadán, hodnota zobrazovaného názvu bude vygenerována kombinací metody HTTP a šablony cesty, například `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Aktualizace existujícího rozhraní API prostřednictvím importu OpenAPI

Během importu se existující rozhraní API změní tak, aby odpovídalo rozhraní API popsanému v dokumentu OpenAPI. Každá operace v dokumentu OpenAPI se shoduje se stávající operací porovnáním `operationId` hodnoty s názvem prostředku Azure existující operace.

Pokud se najde shoda, vlastnosti stávající operace se aktualizují na místě.

Pokud se shoda nenajde, vytvoří se nová operace pomocí pravidel popsaných v předchozí části. U každé nové operace se import pokusí zkopírovat zásady z existující operace se stejnou metodou HTTP a šablonou cesty.

Odstraní se všechny existující neodpovídající operace.

Aby bylo možné importovat více, postupujte prosím podle těchto pokynů:

- Nezapomeňte zadat `operationId` vlastnost pro každou operaci.
- `operationId`Po počátečním importu upustit od změny.
- Nikdy neměňte `operationId` metodu ani šablonu cesty http.

### <a name="export-api-as-openapi"></a>Exportovat rozhraní API jako OpenAPI

Pro každou operaci bude název prostředku Azure exportován jako `operationId` a název zobrazení bude exportován jako `summary` .
Normalizační pravidla pro operationId

- Převést na malá písmena.
- Nahraďte každou sekvenci nealfanumerických znaků jednou pomlčkou, například, `GET-/foo/{bar}?buzz={quix}` do `get-foo-bar-buzz-quix-` .
- Na obou stranách můžete oříznout pomlčky, například se `get-foo-bar-buzz-quix-` stane `get-foo-bar-buzz-quix`
- Ořízne tak, aby odpovídala 76 znakům, čtyři znaky menší než maximální omezení pro název prostředku.
- V případě potřeby použijte zbývající čtyři znaky pro příponu odstranění duplicitních dat ve formě `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Soubory WSDL slouží k vytváření předávacích rozhraní SOAP a rozhraní SOAP-REST API.

-   **Vazby SOAP** : podporovány jsou pouze vazby SOAP stylu "Document" a "Literal" Encoding. Neexistuje žádná podpora pro styl RPC ani pro kódování SOAP.
-   **WSDL: import** – tento atribut není podporován. Zákazníci by měli sloučit importy do jednoho dokumentu.
-   **Zprávy s více částmi** – tyto typy zpráv nejsou podporovány.
-   **WCF WSHttpBinding** – služby SOAP vytvořené pomocí Windows Communication Foundation by měly používat BasicHttpBinding-WSHttpBinding, se nepodporuje.
-   **MTOM** -služby, které používají MTOM, <em>můžou</em> fungovat. Oficiální podpora není v tuto chvíli nabídnutá.
-   Typy **rekurze** , které jsou definovány rekurzivně (například odkaz na pole sebe sama), nejsou podporovány v APIM.
-   **Více oborů názvů** – ve schématu lze použít více oborů názvů, ale k definování částí zprávy lze použít pouze cílový obor názvů. Obory názvů jiné než cíl, které slouží k definování jiných vstupních nebo výstupních prvků, nejsou zachovány. I když takový dokument WSDL lze importovat, v části Export všech částí zprávy bude mít cílový obor názvů WSDL.
-   **Pole** – transformace SOAP na REST podporuje pouze zabalená pole zobrazená v následujícím příkladu:

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

V současné době nejsou k dispozici žádné známé problémy s WADL importu.
