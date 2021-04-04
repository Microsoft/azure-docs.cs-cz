---
author: ericmitt
ms.author: ericmitt
ms.service: iot-pnp
ms.topic: include
ms.date: 07/13/2020
ms.openlocfilehash: 3ab9161c006a88a254b247a921512698ed46fa95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87352746"
---
1. Otevřete Azure IoT Explorer.

1. Pokud jste ještě nepřidali připojení ke službě IoT Hub, na stránce **centra IoT** vyberte **+ Přidat připojení**. Zadejte připojovací řetězec pro IoT Hub, který jste vytvořili dříve, a vyberte **Uložit**.

1. Na stránce **nastavení technologie Plug and Play IoT** vyberte **+ Přidat > místní složka** a vyberte složku místních *modelů* , kam jste uložili soubory modelu.

1. Na stránce **centra IoT** klikněte na název centra, se kterým chcete pracovat. Zobrazí se seznam zařízení zaregistrovaných ve službě IoT Hub.

1. Klikněte na **ID zařízení** zařízení, které jste předtím vytvořili.

1. V nabídce na levé straně vidíte různé typy informací, které jsou pro zařízení k dispozici.

1. Vyberte **komponenty IoT technologie Plug and Play** , abyste si zobrazili informace o modelu pro vaše zařízení.

1. Můžete zobrazit různé součásti zařízení. Výchozí součást a všechny další. Vyberte součást, se kterou chcete pracovat.

1. Vyberte stránku **telemetrie** a pak výběrem **Spustit** zobrazte data telemetrie, které zařízení odesílá pro tuto součást.

1. Vyberte stránku **vlastnosti (jen pro čtení)** , chcete-li zobrazit vlastnosti jen pro čtení hlášené pro tuto součást.

1. Výběrem stránky **vlastnosti (zapisovatelné)** zobrazíte vlastnosti, které lze pro tuto součást aktualizovat.

1. Vyberte vlastnost podle jejího **názvu**, zadejte pro ni novou hodnotu a vyberte **aktualizovat požadovanou hodnotu**.

1. Pokud chcete zobrazit novou hodnotu, klikněte na tlačítko **aktualizovat** .

1. Výběrem stránky **příkazy** zobrazíte všechny příkazy pro tuto součást.

1. Vyberte příkaz, který chcete otestovat, pokud existuje parametr. Vyberte **Odeslat příkaz** pro volání příkazu na zařízení. V okně příkazového řádku, kde je spuštěný vzorový kód, se vaše zařízení může na příkaz reagovat.
