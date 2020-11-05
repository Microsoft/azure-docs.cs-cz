---
title: 'Koncepty: tok dat ve službě Azure IoT Connector pro FHIR (Preview) funkce Azure API pro FHIR'
description: Seznámení se službou Azure IoT Connector pro tok dat FHIR (Preview). Azure IoT Connector pro FHIR (Preview) ingestuje, normalizuje, seskupuje, transformuje a ukládá IoMT data do Azure API pro FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: punagpal
ms.openlocfilehash: 3cae648e3c2bddbafec555621d97575a007cfeb4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394862"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Tok dat konektoru Azure IoT pro FHIR (Preview)

Tento článek poskytuje přehled toku dat ve službě Azure IoT Connector pro FHIR *. V rámci služby Azure IoT Connector se dozvíte víc o různých fázích zpracování dat pro FHIR, které transformují data zařízení do [pozorovacích](https://www.hl7.org/fhir/observation.html) prostředků založených na FHIR.

![Azure IoT Connector pro tok dat FHIR](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Diagram výše znázorňuje běžné toky dat pomocí Azure IoT Connector pro FHIR. 

Níže jsou uvedeny různé fáze, kterými data procházejí po přijetí službou Azure IoT Connector pro FHIR.

## <a name="ingest"></a>Ingestování
Ingestuje se první fáze, kdy se data zařízení přijímají do služby Azure IoT Connector pro FHIR. Koncový bod přijímání dat zařízení je hostovaný v [centru událostí Azure](../event-hubs/index.yml). Platforma Azure Event hub podporuje vysoké škálování a propustnost s možností přijímat a zpracovávat miliony zpráv za sekundu. Umožňuje taky službě Azure IoT Connector pro FHIR spotřebovávat zprávy asynchronně a odebrat nutnost čekat na zpracování dat ze zařízení.

> [!NOTE]
> Formát JSON je v tuto chvíli jediným podporovaným formátem pro data zařízení.

## <a name="normalize"></a>Normalizovat
Normalizace je další fáze, kdy se data zařízení načítají z výše uvedeného centra událostí Azure a zpracovaná pomocí šablon mapování zařízení. Tento proces mapování vede k transformaci dat zařízení do normalizovaného schématu. 

Proces normalizace nejen zjednodušuje zpracování dat v pozdějších fázích, ale také nabízí možnost projektu jedné vstupní zprávy do více normalizovaných zpráv. Zařízení může například odeslat několik důležitých příznaků pro teplotu textu, pulsní kmitočet, krevní tlak a respiration sazbu v jedné zprávě. Tato vstupní zpráva by vytvořila čtyři samostatné prostředky FHIR. Každý prostředek by představoval jiný nepodstatný podpis a vstupní zpráva se prokládá se čtyřmi různými normalizovanými zprávami.

## <a name="group"></a>Group (Skupina)
Skupina je další fáze, kde se normalizované zprávy dostupné z předchozí fáze seskupují pomocí tří různých parametrů: identita zařízení, typ měření a časové období.

Seskupení typů identit a měření zařízení povoluje použití typu měření [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Tento typ poskytuje stručný způsob reprezentace časových řad měření ze zařízení v FHIR. A časové období řídí latenci, při které se prostředky sledování vygenerované službou Azure IoT Connector pro FHIR zapisují do Azure API pro FHIR.

> [!NOTE]
> Hodnota časového období je nastavena na 15 minut a nelze ji nakonfigurovat pro verzi Preview.

## <a name="transform"></a>Transformace
Ve fázi transformace jsou zpracovávány normalizované zprávy prostřednictvím šablon mapování FHIR. Zprávy, které odpovídají typu šablony, se transformují do FHIRch prostředků pro sledování, které jsou zadány prostřednictvím mapování.

V tomto okamžiku se ze serveru FHIR načte taky prostředek [zařízení](https://www.hl7.org/fhir/device.html) , společně s jeho přidruženým prostředkem [pacienta](https://www.hl7.org/fhir/patient.html) , a to pomocí identifikátoru zařízení přítomného ve zprávě. Tyto prostředky jsou přidány jako odkaz na vytvářený prostředek sledování.

> [!NOTE]
> Po vyřešení veškerého zatížení serveru FHIR jsou všechny zdroje uložené v mezipaměti. Pokud plánujete opakované použití zařízení s více pacienty, doporučujeme vytvořit prostředek virtuálního zařízení, který je specifický pro pacient a poslat identifikátor virtuálního zařízení v datové části zprávy. Virtuální zařízení je možné propojit s aktuálním prostředkem zařízení jako nadřazeným objektem.

Pokud na serveru FHIR neexistuje žádný prostředek zařízení pro daný identifikátor zařízení, bude výsledek záviset na hodnotě `Resolution Type` nastavené v době vytvoření. Při nastavení na je `Lookup` specifická zpráva ignorována a kanál bude pokračovat ve zpracování dalších příchozích zpráv. Pokud je nastavená na `Create` , Azure IoT Connector pro FHIR vytvoří holé prostředky zařízení a pacienta na serveru FHIR.  

## <a name="persist"></a>Persist
Jakmile se prostředek sledování FHIR ve fázi transformace vygeneruje, uloží se prostředek do Azure API pro FHIR. Pokud je prostředek FHIR nový, vytvoří se na serveru. Pokud prostředek FHIR už existuje, bude aktualizovaný.

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak vytvořit šablony mapování zařízení a FHIR, klikněte níže na další krok.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro šablony mapování FHIR](iot-mapping-templates.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.