---
title: Informace o službách FHIR v Azure – rozhraní API služby Azure pro FHIR – nejčastější dotazy (FAQ)
description: Tento článek nejčastější dotazy k odpovědi na nejčastější dotazy týkající se rozhraní API služby Azure pro FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e3889ed9f758ce2c374eae106674930ba67f7620
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878778"
---
# <a name="frequently-asked-questions-about-azure-api-for-fhir"></a>Nejčastější dotazy ohledně Azure API pro FHIR

## <a name="storage-location"></a>Umístění úložiště

**Jsou data za FHIR&reg; rozhraní API uložená v Azure?** Ano, jsou data uložená ve spravované databáze v Azure. Rozhraní API služby Azure pro FHIR neposkytuje přímý přístup k základnímu úložišti dat.

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