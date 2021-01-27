---
title: Spuštění exportu vyvoláním příkazu $export v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak exportovat FHIR data pomocí $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 8ad5ee78a525b3798bbf613168ff74a9e21fe99b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920253"
---
# <a name="how-to-export-fhir-data"></a>Jak exportovat FHIR data


Funkce hromadného exportu umožňuje exportovat data ze serveru FHIR podle [specifikace FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Před použitím $export se ujistěte, že rozhraní Azure API pro FHIR je nakonfigurované tak, aby ho bylo možné používat. Pokud chcete konfigurovat nastavení exportu a vytvořit účet úložiště Azure, přečtěte si [stránku konfigurace dat pro export](configure-export-data.md).

## <a name="using-export-command"></a>Použití příkazu $export

Po nakonfigurování rozhraní API Azure pro FHIR pro export můžete pomocí příkazu $export exportovat data mimo službu. Data budou uložena do účtu úložiště, který jste zadali při konfiguraci exportu. Pokud se chcete dozvědět, jak vyvolat $export příkaz na serveru FHIR, přečtěte si dokumentaci ke [specifikaci změněného HL7 FHIR $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Rozhraní Azure API pro FHIR podporuje $export na následujících úrovních:
* [Systém](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Pacient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Skupina pacientů *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – Azure API pro FHIR exportuje všechny související prostředky, ale neexportují charakteristiky této skupiny: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` a `Group/[ID]/$export` můžou exportovat duplicitní prostředky, pokud je prostředek v prostoru více než jednoho prostředku nebo je ve více skupinách.

Kromě toho se při zrušení aktuální úlohy exportu kontroluje stav exportu prostřednictvím adresy URL vrácené hlavičkou umístění v rámci služby Řízení front.

## <a name="settings-and-parameters"></a>Nastavení a parametry

### <a name="headers"></a>Hlavičky
Existují dva povinné parametry hlaviček, které je třeba nastavit pro $export úlohy. Hodnoty jsou definovány pomocí [specifikace aktuální $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Accept** -Application/fhir + JSON
* **Preferovat** – odpověď – asynchronní

### <a name="query-parameters"></a>Parametry dotazů
Rozhraní Azure API pro FHIR podporuje následující parametry dotazu. Všechny tyto parametry jsou volitelné:

|Parametr dotazu        | Definováno specifikací FHIR?    |  Popis|
|------------------------|---|------------|
| \_outputFormat | Yes | V současné době podporuje tři hodnoty, které se mají Zarovnat ke specifikaci FHIR: Application/FHIR + ndjson, Application/ndjson nebo pouze ndjson. Všechny úlohy exportu budou vracet `ndjson` a předaná hodnota nemá žádný vliv na chování kódu. |
| \_doby | Yes | Umožňuje exportovat pouze prostředky, které byly od zadaného času změněny. |
| \_textový | Yes | Umožňuje určit typy prostředků, které budou zahrnuty. Například \_ typ = pacient by vrátil jenom prostředky pacienta.|
| \_typefilter | Yes | Chcete-li požádat o přesnější filtrování, můžete použít \_ TypeFilter spolu s \_ parametrem typu. Hodnota parametru _typeFilter je čárkami oddělený seznam dotazů FHIR, které dále omezují výsledky. |
| \_vnitřního | No |  Určuje kontejner v nakonfigurovaném účtu úložiště, kam se mají data exportovat. Pokud je určen kontejner, data budou exportována do tohoto kontejneru do nové složky s názvem. Pokud kontejner není zadán, bude exportován do nového kontejneru pomocí časového razítka a ID úlohy. |


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak exportovat FHIR prostředky pomocí příkazu $export. V dalším kroku se dozvíte, jak exportovat de identifikovaná data:
 
>[!div class="nextstepaction"]
>[Exportovat de identifikovaná data](de-identified-export.md)
