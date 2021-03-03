---
title: Vysvětlení aktualizace zařízení pro IoT Hub importování | Microsoft Docs
description: Klíčové pojmy pro import nové aktualizace do aktualizace zařízení pro IoT Hub.
author: andbrown
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: c6c6707a74007898c3cd73250709f4df3fabb37d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662537"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Naimportují se aktualizace do aktualizace zařízení pro IoT Hub.
Aby bylo možné nasadit aktualizaci do zařízení z aktualizace zařízení pro IoT Hub, musíte nejdřív _naimportovat_ tuto aktualizaci do služby aktualizace zařízení. Tady je přehled některých důležitých konceptů, které je potřeba pochopit při importu aktualizací.

## <a name="import-manifest"></a>Importovat manifest

Manifest importu je soubor JSON, který definuje důležité informace o aktualizaci, kterou importujete. Jako součást procesu importu odešlete jak manifest importu, tak i přidružený soubor aktualizace nebo soubory (například balíček aktualizace firmwaru). Metadata, která jsou definována v manifestu import, slouží k ingestování aktualizace. Některá metadata jsou také použita v době nasazení – například k ověření, zda byla aktualizace nainstalována správně.

Manifest importu obsahuje několik položek, které reprezentují důležitou aktualizaci zařízení pro IoT Hub konceptů. Níže jsou uvedené koncepty.

### <a name="update-identity-update-id"></a>Aktualizovat identitu (ID aktualizace)

Identita aktualizace představuje jedinečný identifikátorem aktualizace. Definuje důležité vlastnosti týkající se importované aktualizace. Identita aktualizace se skládá ze tří částí:
* Poskytovatel: Toto je entita, která vytváří nebo přímo zodpovídá za aktualizaci. Často se jedná o název společnosti.
* Name (název): identifikátor třídy aktualizací. Třída může být cokoliv, co zvolíte. Často se jedná o název zařízení nebo modelu.
* Verze: Toto je číslo verze, které rozlišuje tuto aktualizaci od ostatních, které mají stejného poskytovatele a název. Tuto verzi používá aktualizace zařízení pro službu IoT Hub a může nebo nemusí odpovídat verzi jednotlivé softwarové komponenty v zařízení. 

### <a name="compatibility"></a>Kompatibilita

Pro zjednodušení nasazení aktualizací aktualizace zařízení pro IoT Hub porovnává vlastnosti kompatibility pro aktualizaci, která je definována v manifestu import, s odpovídajícími vlastnostmi zařízení. Budou vráceny a k dispozici pro nasazení pouze aktualizace, které mají stejné vlastnosti.

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria používá agent aktualizace na zařízení k určení, zda byla aktualizace úspěšně nainstalována.


## <a name="next-steps"></a>Další kroky

Až budete připraveni, vyzkoušejte si [Průvodce importem How-To](./import-update.md), který vás provede procesem importu krok za krokem.


