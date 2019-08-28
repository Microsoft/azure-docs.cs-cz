---
title: Omezení a známé problémy v Azure API Management API importování | Microsoft Docs
description: Podrobnosti o známých problémech a omezeních pro import do Azure API Management s využitím formátů Open API, WSDL nebo WADL.
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
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 1a1196da62b9d28280150dd1ddf1582db64a93d4
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073794"
---
# <a name="api-import-restrictions-and-known-issues"></a>Omezení importu rozhraní API a známé problémy

## <a name="about-this-list"></a>O tomto seznamu

Při importu rozhraní API se můžete setkat s určitými omezeními nebo určit problémy, které je potřeba opravit, než budete moct úspěšně naimportovat. Tento článek popisuje tyto dokumenty, seřazené podle formátu importu rozhraní API.

## <a name="open-api"> </a>Openapi/Swagger

Pokud obdržíte chyby při importu dokumentu OpenAPI, ujistěte se, že jste ho předem ověřili. Můžete to udělat buď pomocí návrháře v Azure Portal (Editor specifikace front-end-OpenAPI Specification), nebo pomocí nástroje třetí strany, jako je <a href="https://editor.swagger.io">Editor Swagger</a>.

### <a name="open-api-general"> </a>Obecné

-   Požadované parametry v obou cestách i dotazech musí mít jedinečné názvy. (V OpenAPI musí být název parametru jedinečný jenom v rámci umístění, například cesta, dotaz, záhlaví. V API Management ale povolujeme, aby byly operace rozlišené pomocí cest i parametrů dotazů (které OpenAPI nepodporuje). To je důvod, proč potřebujeme, aby názvy parametrů byly jedinečné v rámci celé šablony URL.)
-   ukazatele ref nemůžou odkazovat na externí soubory.  **\$**
-   jedinou podporovanou příponou jsou **x-MS-Paths** a **x-servery** .
-   Vlastní rozšíření se při importu ignorují a neukládají se ani neuchovávají pro export.
-   Rekurze – API Management nepodporuje definice, které jsou definovány rekurzivně (například schémata odkazující samy na sebe).
-   Adresa URL zdrojového souboru (je-li k dispozici) se použije na relativní adresy URL serveru.
-   Definice zabezpečení jsou ignorovány.

### <a name="open-api-v2"> </a>Openapi verze 2

-   Podporován je pouze formát JSON.

### <a name="open-api-v3"> </a>Openapi verze 3

-   Pokud je zadáno mnoho **serverů** , API Management se pokusí vybrat první adresu URL https. Pokud nejsou žádné adresy URL protokolu HTTPs, první adresa URL protokolu HTTP. Pokud neexistují žádné adresy URL protokolu HTTP, adresa URL serveru bude prázdná.
-   **Příklady** nejsou podporované, ale **příklad** je.
-   **Multipart/form-data** nejsou podporována.

> [!IMPORTANT]
> Důležité informace a tipy týkající se importu OpenAPI najdete v tomto [dokumentu](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/).

## <a name="wsdl"> </a>WSDL

Soubory WSDL slouží k vytváření předávacích rozhraní SOAP a rozhraní SOAP-REST API.

-   **Vazby SOAP** : podporovány jsou pouze vazby SOAP stylu "Document" a "Literal" Encoding. Neexistuje žádná podpora pro styl RPC ani pro kódování SOAP.
-   **WSDL: import** – tento atribut není podporován. Zákazníci by měli sloučit importy do jednoho dokumentu.
-   **Zprávy s více částmi** – tyto typy zpráv nejsou podporovány.
-   **WCF WSHttpBinding** – služby SOAP vytvořené pomocí Windows Communication Foundation by měly používat BasicHttpBinding-WSHttpBinding, se nepodporuje.
-   **MTOM** -služby, které používají MTOM, <em>můžou</em> fungovat. Oficiální podpora není v tuto chvíli nabídnutá.
-   Typy rekurze, které jsou definovány rekurzivně (například odkaz na pole sebe sama), nejsou podporovány v APIM.
-   **Více oborů názvů** – ve schématu lze použít více oborů názvů, ale k definování částí zprávy lze použít pouze cílový obor názvů. Jiné obory názvů, než je cíl, který slouží k definování jiných vstupních nebo výstupních prvků, nejsou zachovány. I když takový dokument WSDL lze importovat, v části Export všech částí zprávy bude mít cílový obor názvů WSDL.
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

## <a name="wadl"> </a>WADL

V současné době nejsou k dispozici žádné známé problémy s WADL importu.
