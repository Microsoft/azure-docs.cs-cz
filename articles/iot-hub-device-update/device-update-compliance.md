---
title: Principy aktualizace zařízení pro Azure IoT Hub dodržování předpisů | Microsoft Docs
description: Pochopte, jak aktualizace zařízení pro Azure IoT Hub měří dodržování předpisů aktualizace zařízení.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662561"
---
# <a name="device-update-compliance"></a>Update Compliance zařízení

V aktualizaci zařízení pro IoT Hub dodržování předpisů měří, kolik zařízení nainstalovalo nejvyšší verzi, která je kompatibilní. Zařízení je kompatibilní, pokud má nainstalovanou nejvyšší dostupnou aktualizaci verze, která je pro něj kompatibilní. 

Zvažte například instanci aktualizace zařízení s následujícími aktualizacemi:

|Název aktualizace|Verze aktualizace|Kompatibilní Model zařízení|
|-----------|--------------|-----------------------|
|Aktualizace1    |1.0    |Model1|
|– Datum2    |1.0    |Model2|
|Update3    |2.0    |Model1|

Řekněme, že se vytvořila následující nasazení:

|Název nasazení    |Název aktualizace    |Cílová skupina|
|-----------|--------------|-------------------|
|Deployment1    |Aktualizace1    |Group1|
|Deployment2    |– Datum2    |Skupina2|
|Deployment3    |Update3    |Group3|

Teď vezměte v úvahu následující zařízení s jejich členstvím ve skupinách a nainstalovanými verzemi:

|DeviceId   |Model zařízení   |Nainstalovaná verze aktualizace|Group (Skupina) |Dodržování předpisů|
|-----------|--------------|-----------------------|-----|---------|
|Zařízení1    |Model1 |1.0    |Group1 |Jsou k dispozici nové aktualizace</span>|
|Device2    |Model1 |2.0    |Group3 |Při nejnovější aktualizaci|
|Device3    |Model2 |1.0    |Skupina2 |Při nejnovější aktualizaci|
|Device4    |Model1 |1.0    |Group3 |Probíhá aktualizace.|

Zařízení1 a Device4 nejsou kompatibilní, protože mají nainstalovanou verzi 1,0, i když je v instanci aktualizace zařízení nainstalovaná novější verze, která je kompatibilní s modelem Update3. Device2 a Device3 jsou kompatibilní, protože mají nainstalované nejvyšší verze, které jsou kompatibilní s nainstalovanými modely.

Dodržování předpisů nebere v úvahu, jestli je aktualizace nasazená do skupiny zařízení nebo ne. Vyhledá všechny aktualizace publikované do aktualizace zařízení. Takže v předchozím příkladu, i když Zařízení1 má nainstalovanou aktualizaci nasazenou, se považuje za nevyhovující. Zařízení1 se bude nadále považovat za nedodržující předpisy, dokud ji úspěšně nainstaluje Update3. Stav dodržování předpisů vám může pomáhat zjistit, jestli jsou nová nasazení potřebná. 

Jak vidíte výše, existují tři stavy dodržování předpisů v aktualizaci zařízení pro IoT Hub:

*   **Při nejnovější aktualizaci** – na zařízení je nainstalovaná nejvyšší verze kompatibilní s aktualizací, která je publikovaná na aktualizaci zařízení.
*   **Probíhá aktualizace** – aktivní nasazení probíhá v procesu doručování nejvyšší kompatibilní verze s aktualizací do zařízení.
*   **Nové aktualizace k dispozici** – zařízení ještě nemá nainstalovanou nejvyšší verzi, která je kompatibilní, a není v aktivním nasazení pro tuto aktualizaci.
