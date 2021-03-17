---
title: Import informací o zařízení
description: Defender pro senzory IoT monitoruje a analyzuje zrcadlený provoz. V těchto případech můžete chtít importovat data pro rozšíření informací o zařízeních, která jsou už zjištěná.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 53c342005d2c33e2ee18057fe0e667ebdec5166c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522236"
---
# <a name="import-device-information-to-a-sensor"></a>Import informací o zařízení do snímače

Azure Defender pro IoT snímač monitoruje a analyzuje zrcadlený provoz. V některých případech se může stát, že některé informace nebudou přenášeny z důvodu zásad konfigurace sítě specifických pro konkrétní organizaci.

V těchto případech můžete chtít importovat data pro rozšíření informací o zařízeních, která jsou už zjištěná. K dispozici jsou dvě možnosti importu informací do senzorů:

- **Importovat z mapy**: aktualizujte název zařízení, typ, skupinu nebo Purdue vrstvu na mapu.

- **Import z nastavení importu**: importovat operační systém zařízení, IP adresu, úroveň opravy nebo stav autorizace.

## <a name="import-from-the-map"></a>Importovat z mapy

Tato část popisuje import názvů zařízení, typů, skupin nebo Purdue vrstev do mapy zařízení. To provedete z mapy.

Tady jsou požadavky na import:

- **Names**: může mít maximálně 30 znaků.

- **Typ** nebo **Purdue vrstva**: použijte možnosti, které se zobrazí v dialogovém okně **vlastnosti zařízení** . (Klikněte pravým tlačítkem na zařízení a vyberte **Zobrazit vlastnosti**.)

- **Skupina zařízení**: Vytvořte novou skupinu až o 30 znaků. 

> [!NOTE]
> Aby nedocházelo ke konfliktům, importujte data, která jste exportovali z jednoho senzoru do jiného snímače.

Import:

1. V postranní nabídce vyberte **zařízení**.

2. V pravém horním rohu okna **zařízení** vyberte :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Snímek obrazovky okna zařízení":::

3. Vyberte **exportovat zařízení**. V exportovaném souboru se zobrazí rozsáhlá škála informací. Tyto informace zahrnují protokoly, které zařízení používá, a stav autorizace zařízení.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informace v exportovaném souboru.":::

4. V souboru CSV změňte pouze název zařízení, typ, skupinu a Purdue vrstvu. Pak soubor uložte. 

   Použijte standardy kapitalizace zobrazené v exportovaném souboru. Například pro vrstvu Purdue použijte všechna písmena za první písmeno.

5. V rozbalovací nabídce **Import/export** v okně **zařízení** vyberte **importovat zařízení**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importujte zařízení přes okno zařízení.":::

6. Vyberte **importovat zařízení** a vyberte soubor CSV, který chcete naimportovat. Stavové zprávy importu se zobrazí na obrazovce, dokud se nezavře dialogové okno **Import zařízení** .

## <a name="import-from-import-settings"></a>Import z nastavení importu

Tato část popisuje, jak naimportovat IP adresu zařízení, operační systém, úroveň opravy nebo stav autorizace na mapu zařízení. To provedete v dialogovém okně **Import nastavení** .

Import IP adresy, operačního systému a úrovně opravy:

1. Stáhněte soubor [devices_info_2.2.8 a up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z centra pro [technickou podporu](https://cyberx-labs.zendesk.com/hc/en-us) a zadejte následující informace:

   - **IP adresa**: zadejte IP adresu zařízení.

   - **Operační systém**: v rozevíracím seznamu vyberte.

   - **Poslední aktualizace**: použijte formát rrrr-mm-dd.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Obrazovka možnosti.":::

2. V postranní nabídce vyberte **importovat nastavení**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importujte nastavení.":::

3. Požadovanou konfiguraci nahrajete tak, že v části **informace o zařízení** vyberete **Přidat** a nahrajete soubor. csv, který jste připravili.

Import stavu autorizace:

1. Stáhněte a uložte soubor [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z programu Defender pro centrum helpdesk pro IoT. Ověřte, že jste soubor uložili jako sdílený svazek clusteru.

2. Zadejte tyto informace:

   - **IP adresa**: IP adresa zařízení.

   - **Name (název**): název autorizovaného zařízení. Ujistěte se, že jsou názvy přesné. Názvy zadané pro zařízení v importovaném seznamu přepíšou názvy zobrazené v mapě zařízení.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Excelové soubory se seznamem importovaných zařízení":::

3. V postranní nabídce vyberte **importovat nastavení**.

4. V části **autorizované zařízení** vyberte **Přidat** a nahrajte soubor CSV, který jste uložili.

Po importu informací zobrazíte výstrahy týkající se neautorizovaných zařízení pro všechna zařízení, která se v tomto seznamu nevyskytují.

## <a name="import-device-information-to-the-sensor"></a>Import informací o zařízení do snímače

Senzor monitoruje a analyzuje zrcadlený provoz. V některých případech se může stát, že některé informace nebudou přenášeny z důvodu zásad konfigurace sítě specifických pro konkrétní organizaci.

V těchto případech možná budete chtít importovat data a rozšířit informace o zařízení na zařízeních, která jsou už zjištěná. K dispozici jsou dvě možnosti importu informací do senzorů:

- **Importovat z mapy**: aktualizujte název zařízení, typ, skupinu nebo Purdue vrstvu na mapu.

- **Import z nastavení importu**: importovat operační systém zařízení, IP adresu, úroveň opravy nebo stav autorizace.

### <a name="import-from-the-map"></a>Importovat z mapy

Tato část popisuje import názvů zařízení, typů, skupin nebo Purdue vrstev do mapy zařízení. To provedete z mapy.

Tady jsou požadavky na import:

- **Names**: může mít maximálně 30 znaků.

- **Typ** nebo **Purdue vrstva**: použijte možnosti, které se zobrazí v dialogovém okně **vlastnosti zařízení** . (Klikněte pravým tlačítkem na zařízení a vyberte **Zobrazit vlastnosti**.)

- **Skupina zařízení**: Vytvořte novou skupinu až o 30 znaků. 

> [!NOTE]
> Aby nedocházelo ke konfliktům, importujte data, která jste exportovali z jednoho senzoru do jiného snímače.

Import:

1. V postranní nabídce vyberte **zařízení**.

2. V pravém horním rohu okna **zařízení** vyberte :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Okno, ze kterého se má zařízení vybrat":::

3. Vyberte **exportovat zařízení**. V exportovaném souboru se zobrazí rozsáhlá škála informací. Mezi příklady patří protokoly, které zařízení používá, a stav autorizace zařízení.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informace v exportovaném souboru.":::

4. V souboru CSV změňte pouze název zařízení, typ, skupinu a Purdue vrstvu. Pak soubor uložte. 

   Použijte standardy kapitalizace zobrazené v exportovaném souboru. Například pro vrstvu Purdue použijte všechna písmena za první písmeno.

5. V rozbalovací nabídce **Import/export** v okně **zařízení** vyberte **importovat zařízení**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importujte svoje zařízení.":::

6. Vyberte **importovat zařízení** a vyberte soubor CSV, který chcete naimportovat. Stavové zprávy importu se zobrazí na obrazovce, dokud se nezavře dialogové okno **Import zařízení** .

### <a name="import-from-import-settings"></a>Import z nastavení importu 

Tato část popisuje, jak naimportovat IP adresu zařízení, operační systém, úroveň opravy nebo stav autorizace na mapu zařízení. To provedete v dialogovém okně **Import nastavení** .

Import IP adresy, operačního systému a úrovně opravy:

1. Stáhněte soubor [devices_info_2.2.8 a up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z centra pro [technickou podporu](https://cyberx-labs.zendesk.com/hc/en-us) a zadejte následující informace:

   - **IP adresa**: IP adresa zařízení.

   - **Operační systém**: v rozevíracím seznamu vyberte.

   - **Datum poslední aktualizace**: použijte formát rrrr-mm-dd.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Obsah na obrazovce":::

2. V postranní nabídce vyberte **importovat nastavení**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Vyplňte obrazovku import nastavení.":::

3. Požadovanou konfiguraci nahrajete tak, že v části **informace o zařízení** vyberete **Přidat** a nahrajete soubor. csv, který jste připravili.

Import stavu autorizace:

1. Stáhněte a uložte soubor [authorized_devices-examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z programu Defender pro centrum helpdesk pro IoT. Ověřte, že jste soubor uložili jako sdílený svazek clusteru.

2. Zadejte tyto informace:

   - **IP adresa**: IP adresa zařízení.

   - **Name (název**): název autorizovaného zařízení. Ověřte, zda jsou názvy přesné. Názvy zadané pro zařízení v importovaném seznamu přepíšou názvy zobrazené v mapě zařízení.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Seznam pro import na mapu zařízení.":::

3. V postranní nabídce vyberte **importovat nastavení**.

4. V části **autorizované zařízení** vyberte **Přidat** a nahrajte soubor CSV, který jste uložili.

Po importu informací zobrazíte výstrahy týkající se neautorizovaných zařízení pro všechna zařízení, která se v tomto seznamu nevyskytují.

## <a name="next-steps"></a>Další kroky

[Určení, který provoz se monitoruje](how-to-control-what-traffic-is-monitored.md)

[Prošetření detekovaných senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md)
