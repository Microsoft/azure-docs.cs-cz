---
title: 'Koncepty: tok dat v rozhraní API Azure pro FHIR v konektoru IoT (Preview)'
description: Pochopení toku dat konektoru IoT IoT Connector ingestuje, normalizuje, seskupuje, transformuje a ukládá IoMT data do Azure API pro FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097332"
---
# <a name="iot-connector-preview-data-flow"></a>Tok dat konektoru IoT Connector (Preview)

Tento článek poskytuje přehled toku dat v konektoru IoT. Dozvíte se o různých fázích zpracování dat v rámci služby IoT Connector, které transformují data zařízení na FHIRé prostředky pro [pozorování](https://www.hl7.org/fhir/observation.html) .

![Tok dat konektoru IoT](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Diagram výše znázorňuje různé fáze toku dat v rámci konektoru IoT. 

## <a name="ingest"></a>Ingestování
Ingestuje se první fáze, kdy se data zařízení přijímají do konektoru IoT. Koncový bod přijímání dat zařízení je hostovaný v [centru událostí Azure](https://docs.microsoft.com/azure/event-hubs/). Platforma Azure Event hub podporuje vysoké škálování a propustnost s možností přijímat a zpracovávat miliony zpráv za sekundu. Také umožňuje konektoru IoT Connector zpracovávat zprávy asynchronně a odebrat nutnost čekat na zpracování dat ze zařízení.

> [!NOTE]
> Formát JSON je v tuto chvíli jediným podporovaným formátem pro data zařízení.

## <a name="normalize"></a>Normalizovat
Normalizace je další fáze, kdy se data zařízení načítají z výše uvedeného centra událostí Azure a zpracovaná pomocí šablon mapování zařízení. Tento proces mapování vede k transformaci dat zařízení do normalizovaného schématu. 

Proces normalizace nejen zjednodušuje zpracování dat v pozdějších fázích, ale také nabízí možnost projektu jedné vstupní zprávy do více normalizovaných zpráv. Zařízení může například odeslat několik důležitých příznaků pro teplotu textu, pulsní kmitočet, krevní tlak a respiration sazbu v jedné zprávě. Tato vstupní zpráva by vytvořila čtyři samostatné prostředky FHIR. Každý prostředek by představoval jiný nepodstatný podpis a vstupní zpráva se prokládá se čtyřmi různými normalizovanými zprávami.

## <a name="group"></a>Skupina
Skupina je další fáze, kde se normalizované zprávy dostupné z předchozí fáze seskupují pomocí tří různých parametrů: identita zařízení, typ měření a časové období.

Seskupení typů identit a měření zařízení povoluje použití typu měření [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Tento typ poskytuje stručný způsob reprezentace časových řad měření ze zařízení v FHIR. A časové období řídí latenci, při které se prostředky sledování vygenerované službou IoT Connector zapisují do Azure API pro FHIR.

> [!NOTE]
> Hodnota časového období je nastavena na 15 minut a nelze ji nakonfigurovat pro verzi Preview.

## <a name="transform"></a>Transformace
Ve fázi transformace jsou zpracovávány normalizované zprávy prostřednictvím šablon mapování FHIR. Zprávy, které odpovídají typu šablony, se transformují do FHIRch prostředků pro sledování, které jsou zadány prostřednictvím mapování.

V tomto okamžiku se ze serveru FHIR načte taky prostředek [zařízení](https://www.hl7.org/fhir/device.html) , společně s jeho přidruženým prostředkem [pacienta](https://www.hl7.org/fhir/patient.html) , a to pomocí identifikátoru zařízení přítomného ve zprávě. Tyto prostředky jsou přidány jako odkaz na vytvářený prostředek sledování.

> [!NOTE]
> Po vyřešení veškerého zatížení serveru FHIR jsou všechny zdroje uložené v mezipaměti. Pokud plánujete opakované použití zařízení s více pacienty, doporučujeme vytvořit prostředek virtuálního zařízení, který je specifický pro pacient a poslat identifikátor virtuálního zařízení v datové části zprávy. Virtuální zařízení je možné propojit s aktuálním prostředkem zařízení jako nadřazeným objektem.

Pokud na serveru FHIR neexistuje žádný prostředek zařízení pro daný identifikátor zařízení, bude výsledek záviset na hodnotě `Resolution Type` nastavené v době vytvoření. Při nastavení na je `Lookup` specifická zpráva ignorována a kanál bude pokračovat ve zpracování dalších příchozích zpráv. Pokud je nastaveno na `Create` , IoT Connector vytvoří holé prostředky zařízení a pacienta na serveru FHIR.  

## <a name="persist"></a>Persist
Jakmile se prostředek sledování FHIR ve fázi transformace vygeneruje, uloží se prostředek do Azure API pro FHIR. Pokud je prostředek FHIR nový, vytvoří se na serveru. Pokud prostředek FHIR už existuje, bude aktualizovaný.

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak vytvořit šablony mapování zařízení a FHIR, klikněte níže na další krok.

>[!div class="nextstepaction"]
>[Šablony mapování konektoru IoT](iot-mapping-templates.md)


FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.
