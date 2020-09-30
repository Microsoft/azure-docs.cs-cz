---
title: Spuštění exportu vyvoláním příkazu $export v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak nastavit a použít neidentifikovaný export.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ee7ba96a7cc8789e1a949db80bc84c63b28f4518
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531662"
---
# <a name="how-to-export-fhir-data"></a>Jak exportovat FHIR data

Funkce hromadného exportu umožňuje exportovat data ze serveru FHIR podle specifikace] FHIR] ( https://hl7.org/fhir/uv/bulkdata/export/index.html) . 

Před použitím $export se ujistěte, že rozhraní Azure API pro FHIR je nakonfigurované tak, aby ho bylo možné používat. Pokud chcete konfigurovat nastavení exportu a vytvořit účet úložiště Azure, přečtěte si [stránku konfigurace dat pro export](configure-export-data.md).

## <a name="using-export-command"></a>Použití příkazu $export

Po nakonfigurování rozhraní API Azure pro FHIR pro export můžete pomocí příkazu $export exportovat data mimo službu. Data budou uložena do účtu úložiště, který jste zadali při konfiguraci exportu. Pokud se chcete dozvědět, jak vyvolat $export příkaz na serveru FHIR, přečtěte si dokumentaci ke [specifikaci $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Příkaz $export v rozhraní API Azure pro FHIR přebírá volitelný parametr _ \_ kontejneru_ , který určuje kontejner v nakonfigurovaném účtu úložiště, kam se mají data exportovat. Pokud je určen kontejner, data budou exportována do tohoto kontejneru do nové složky s názvem. Pokud není zadaný žádný kontejner, data se exportují do nového kontejneru s názvem.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Podporované scénáře

Rozhraní Azure API pro FHIR podporuje $export na úrovni systému, pacienta a skupiny. Pro export skupin exportujeme všechny související prostředky, ale nebudeme exportovat její charakteristiky.

> [!Note] 
> $export bude exportovat duplicitní prostředky, pokud je prostředek v prostoru více než jednoho prostředku nebo je ve více skupinách.

Kromě toho je podporována kontrola stavu exportu prostřednictvím adresy URL vrácené hlavičkou umístění v rámci služby Řízení front a zrušení aktuální úlohy exportu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak exportovat FHIR prostředky pomocí příkazu $export. Dále si můžete projít naše podporované funkce.
 
>[!div class="nextstepaction"]
>[Podporované funkce](fhir-features-supported.md)
