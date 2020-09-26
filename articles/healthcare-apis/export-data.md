---
title: Spuštění exportu vyvoláním příkazu $export v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak nastavit a použít neidentifikovaný export.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ecc2134d1a528ee22710cb447f996e0c5e31a8de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308176"
---
# <a name="how-to-export-fhir-data"></a>Jak exportovat FHIR data

Před použitím $export se ujistěte, že rozhraní Azure API pro FHIR je nakonfigurované tak, aby ho bylo možné používat. Pokud chcete konfigurovat nastavení exportu a vytvořit účet úložiště Azure, přečtěte si [stránku konfigurace dat pro export](configure-export-data.md).

## <a name="using-export-command"></a>Použití příkazu $export

Po nakonfigurování rozhraní API Azure pro FHIR pro export můžete pomocí příkazu $export exportovat data mimo službu. Data budou uložena do účtu úložiště, který jste zadali při konfiguraci exportu. Pokud se chcete dozvědět, jak vyvolat $export příkaz na serveru FHIR, přečtěte si dokumentaci ke [specifikaci $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Příkaz $export v rozhraní API Azure pro FHIR přebírá volitelný parametr _ \_ kontejneru_ , který určuje kontejner v nakonfigurovaném účtu úložiště, kam se mají data exportovat.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Podporované scénáře

Rozhraní Azure API pro FHIR podporuje $export na úrovni systému, pacienta a skupiny. Pro export skupin exportujeme všechny související prostředky, ale nebudeme exportovat její charakteristiky.

> [!Note] 
> $export bude exportovat duplicitní prostředky, pokud je prostředek v prostoru více než jednoho prostředku nebo je ve více skupinách.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak exportovat FHIR prostředky pomocí příkazu $export. Dále si můžete projít naše podporované funkce.
 
>[!div class="nextstepaction"]
>[Podporované funkce](fhir-features-supported.md)
