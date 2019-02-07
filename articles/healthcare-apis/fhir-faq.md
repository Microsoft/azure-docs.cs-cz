---
title: Informace o službách FHIR v Azure – rozhraní API služby Azure pro FHIR – nejčastější dotazy (FAQ)
description: Tento článek nejčastější dotazy k odpovědi na nejčastější dotazy týkající se rozhraní API služby Azure pro FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7403a23e236c14d77672d5b80d953b1e11088f8a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824004"
---
# <a name="frequently-asked-questions-about-azure-api-for-fhir"></a>Nejčastější dotazy ohledně Azure API pro FHIR

## <a name="storage-location"></a>Umístění úložiště

**Jsou data za FHIR&reg; rozhraní API uložená v Azure?** Ano, data se ukládají do spravovaných databází v Azure. Rozhraní API služby Azure pro FHIR neposkytuje přímý přístup k základnímu úložišti dat.

## <a name="identity-providers"></a>Zprostředkovatelé identit

Aktuálně podporujeme Microsoft Azure Active Directory jako zprostředkovatele identity.

## <a name="supported-fhir-version"></a>Podporovaná verze FHIR

Momentálně podporujeme verze 3.0.1. Zobrazit [podporované funkce](fhir-features-supported.md) podrobnosti.

## <a name="oss-and-azure-api-for-fhir"></a>Rozhraní API OSS a Azure pro FHIR

Jaký je rozdíl mezi serverem otevřete Microsoft FHIR zdroj pro Azure a rozhraní API služby Azure pro FHIR? Rozhraní API služby Azure pro FHIR je hostované a spravované verzi serveru FHIR OSS Microsoft Azure. Spravované služby společnost Microsoft poskytuje všechny údržby, aktualizace atd. Při spuštění serveru FHIR OSS pro Azure, můžete mít přímý přístup k podkladovým službám, ale nesete také zodpovědnost za údržbu, aktualizace serveru a veškerá práce požadované dodržování předpisů, pokud ukládání dat chráněných zdravotních informací.

## <a name="next-steps"></a>Další postup

V tomto článku jste si přečetli některé nejčastější dotazy o rozhraní API služby Azure pro FHIR. Přečtěte si o podporovaných funkcích rozhraní API v Microsoft FHIR serveru pro Azure.
 
>[!div class="nextstepaction"]
>[Podporované funkce FHIR](fhir-features-supported.md)