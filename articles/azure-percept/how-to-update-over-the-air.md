---
title: Aktualizace Azure Percept DK přes Air
description: Přečtěte si, jak dostávat aktualizace Air do Azure Percept DK.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e627e582b47c5174e70f5d21d758148cde8dbdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022845"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Aktualizace Azure Percept DK přes Air

V této příručce se dozvíte, jak aktualizovat desku v rámci služby Azure Percept DK v ovzduší pomocí aktualizace zařízení pro IoT Hub.

## <a name="import-your-update-file-and-manifest-file"></a>Importujte soubor aktualizace a soubor manifestu.

> [!NOTE]
> Pokud jste už aktualizaci naimportovali, můžete přejít přímo a **vytvořit aktualizovanou skupinu zařízení**.

1. Přejděte do IoT Hub Azure, kterou používáte pro vaše zařízení Azure Percept. Na panelu nabídky vlevo vyberte **aktualizace zařízení** v části **Automatická správa zařízení**.
 
1. V horní části obrazovky se zobrazí několik karet. Vyberte kartu **aktualizace** .
 
1. Vyberte **+ importovat novou aktualizaci** pod hlavičkou **připraveno k nasazení** .
 
1. Klikněte na pole v části **Vybrat importovat soubor manifestu** a **vyberte aktualizovat soubory** a vyberte příslušný soubor manifestu (. JSON) a jeden soubor aktualizace (. SWU). Tyto aktualizační soubory pro vaše zařízení Azure Percept najdete [tady](https://go.microsoft.com/fwlink/?linkid=2155625).
 
1. V části **Vyberte kontejner úložiště** vyberte ikonu složky nebo textové pole a pak vyberte příslušný účet úložiště.
 
1. Pokud jste už kontejner úložiště vytvořili, můžete ho znovu použít. V opačném případě vyberte **+ kontejner** a vytvořte nový kontejner úložiště pro Ota aktualizace. Vyberte kontejner, který chcete použít, a klikněte na **Vybrat**.
 
    >[!Note]
    >Pokud nemáte kontejner, budete požádáni, abyste ho vytvořili.
 
1. Vyberte **Odeslat** a spusťte proces importu. Proces odeslání bude trvat přibližně 4 minuty.
 
    >[!Note]
    >Může se zobrazit výzva k přidání pravidla žádosti o další původ (CORS) pro přístup k vybranému kontejneru úložiště. Vyberte **Přidat pravidlo a zkuste pokračovat znovu** .
 
    >[!Note]
    >V důsledku velikosti obrázku se může zobrazit stránka **odeslání...** Až 5 minut, než se dohlížíte na další krok.
    
1. Proces importu se spustí a budete přesměrováni na kartu **Historie importu** stránky **aktualizace zařízení** . Kliknutím na **aktualizovat** můžete sledovat průběh během procesu importu. V závislosti na velikosti aktualizace to může trvat několik minut nebo i déle (během špičky prosím, očekává se, že služba pro import zabere až 1hr).

1. Když sloupec status (stav) indikuje, že import proběhl úspěšně, vyberte kartu **připraveno k nasazení** a klikněte na **aktualizovat**. V seznamu by se teď měla zobrazit vaše importovaná aktualizace.
 
## <a name="create-a-device-update-group"></a>Vytvoření skupiny aktualizace zařízení
Aktualizace zařízení pro IoT Hub umožňuje zaměřit se na aktualizaci konkrétních skupin Azure Percept DKs. Pokud chcete vytvořit skupinu, musíte do cílové sady zařízení v Azure IoT Hub přidat značku.

> [!NOTE]
> Pokud jste již vytvořili skupinu, můžete přejít přímo k dalšímu kroku.

Požadavky na značky skupiny:
- Do značky můžete přidat libovolnou hodnotu s výjimkou **Nezařazeno do kategorie**, což je vyhrazená hodnota.
- Hodnota značky nesmí překročit 255 znaků.
- Hodnota značky může obsahovat jenom tyto speciální znaky: ".", "-", "_", "~".
- V názvech značek a skupin se rozlišují malá a velká písmena.
- Zařízení může mít pouze jednu značku. Jakákoli následná značka přidaná do zařízení přepíše předchozí značku.
- Zařízení může patřit pouze do jedné skupiny.

1. Přidejte do svých zařízení značku.
    1. V **IoT Edge** v levém navigačním podokně Najděte službu Azure Percept DK a přejděte do jejího **vlákna**.
    1. Přidejte novou **aktualizaci zařízení pro IoT Hub** hodnotu značky, jak je znázorněno níže (změňte ```<CustomTagValue>``` hodnotu, tj. AzurePerceptGroup1). Přečtěte si další informace o [značkách dokumentu JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)pro vyzdvojené zařízení.

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Klikněte na **Uložit** a vyřešte všechny problémy s formátováním.
 
1. Vytvořte skupinu výběrem existující značky Azure IoT Hub.
    1. Přejděte zpátky na stránku Azure IoT Hub.
    1. Na panelu nabídky na levé straně vyberte **aktualizace zařízení** v části **Automatická správa zařízení** .
    1. Vyberte kartu **skupiny** . Na této stránce se zobrazí počet neseskupených zařízení připojených k aktualizaci zařízení.
    1. Vyberte **+ Přidat** a vytvořte novou skupinu.
    1. Vyberte ze seznamu značku IoT Hub a klikněte na **Odeslat**.
    1. Po vytvoření skupiny se aktualizuje seznam a graf kompatibility aktualizací. Graf zobrazuje počet zařízení v různých stavech dodržování předpisů: **při nejnovější aktualizaci** jsou **k dispozici nové aktualizace**, **probíhající aktualizace** a **ještě nejsou seskupené**.
 

## <a name="deploy-an-update"></a>Nasazení aktualizace
1. V části **dostupné aktualizace** by se měla zobrazit nově vytvořená skupina s novou aktualizací (možná budete muset aktualizaci jednou aktualizovat). Vyberte aktualizaci.
 
1. Potvrďte, že je jako cílová skupina zařízení vybrána správná skupina zařízení. Vyberte **počáteční datum** a **čas spuštění** nasazení a pak klikněte na **vytvořit nasazení**. 

    >[!CAUTION]
    >Nastavením počátečního času v minulosti dojde k okamžitému aktivaci nasazení.
 
1. Podívejte se na graf dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá.
 
1. Po dokončení aktualizace se v grafu dodržování předpisů projeví váš nový stav aktualizace.
 
1. V horní části stránky **aktualizace zařízení** vyberte kartu **nasazení** .
 
1. Vyberte nasazení, aby se zobrazily podrobnosti nasazení. Možná budete muset kliknout na **aktualizovat** , dokud se **stav** nezmění na **úspěšné**.

## <a name="next-steps"></a>Další kroky

Vaše sada dev SDK se teď úspěšně aktualizovala. V DevKit můžete pokračovat v vývoji a provozu.