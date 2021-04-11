---
title: Aktualizace Azure Percept DK přes Air (OTA)
description: Přečtěte si, jak dostávat OTA (Air) aktualizace do Azure Percept DK.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063113"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Aktualizace Azure Percept DK přes Air (OTA)

V této příručce se dozvíte, jak aktualizovat operační systém a firmware kabiny ve službě Azure Percept DK přes Air (OTA) s aktualizací zařízení pro IoT Hub.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste sadu SDK pro vývoj k síti Wi-Fi, vytvořili IoT Hub a připojili jste sadu pro vývoj do IoT Hub
- [Aktualizace zařízení pro IoT Hub byla úspěšně nakonfigurovaná.](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Importovat soubor aktualizace a soubor manifestu

> [!NOTE]
> Pokud jste už aktualizaci naimportovali, můžete přejít přímo a **vytvořit skupinu aktualizace zařízení**.

1. [Stáhněte si příslušný soubor manifestu (. JSON) a aktualizujte soubor (. SWU) pro vaše zařízení Azure Percept](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Přejděte do IoT Hub Azure, kterou používáte pro vaše zařízení Azure Percept. Na panelu nabídky vlevo vyberte **aktualizace zařízení** v části **Automatická správa zařízení**.

1. V horní části obrazovky se zobrazí několik karet. Vyberte kartu **aktualizace** .

1. Vyberte **+ importovat novou aktualizaci** pod hlavičkou **připraveno k nasazení** .

1. Klikněte na pole v části **Vybrat importovat soubor manifestu** a **vyberte aktualizovat soubory** pro výběr souboru manifestu (. JSON) a souboru aktualizace (. SWU).

1. V části **Vyberte kontejner úložiště** vyberte ikonu složky nebo textové pole a vyberte příslušný účet úložiště. Pokud jste již vytvořili kontejner úložiště, můžete jej znovu použít. V opačném případě vyberte **+ kontejner** a vytvořte nový kontejner úložiště pro Ota aktualizace. Vyberte kontejner, který chcete použít, a klikněte na **Vybrat**.

1. Vyberte **Odeslat** a spusťte proces importu. Kvůli velikosti obrázku může proces odeslání trvat až 5 minut.

    > [!NOTE]
    > Může se zobrazit výzva k přidání pravidla žádosti o další původ (CORS) pro přístup k vybranému kontejneru úložiště. Vyberte **Přidat pravidlo a zkuste pokračovat znovu** .

1. Po zahájení procesu importu budete přesměrováni na kartu **Historie importu** stránky **aktualizace zařízení** . Kliknutím na **aktualizovat** můžete sledovat průběh během procesu importu. V závislosti na velikosti aktualizace to může trvat několik minut nebo i déle (během špičky může být služba importu trvat až 1 hodinu).

1. Když sloupec **stav** označuje, že import proběhl úspěšně, vyberte kartu **připraveno k nasazení** a klikněte na **aktualizovat**. V seznamu by se teď měla zobrazit vaše importovaná aktualizace.

## <a name="create-a-device-update-group"></a>Vytvoření skupiny aktualizace zařízení

Aktualizace zařízení pro IoT Hub umožňuje zaměřit se na aktualizaci konkrétních skupin Azure Percept DKs. Pokud chcete vytvořit skupinu, musíte do cílové sady zařízení v Azure IoT Hub přidat značku.

> [!NOTE]
> Pokud jste již vytvořili skupinu, můžete přejít k další části.

Požadavky na značky skupiny:

- Do značky můžete přidat libovolnou hodnotu s výjimkou "Nezařazeno do kategorie", což je vyhrazená hodnota.
- Hodnota značky nesmí překročit 255 znaků.
- Hodnota značky může obsahovat jenom tyto speciální znaky: ".", "-", "_", "~".
- V názvech značek a skupin se rozlišují malá a velká písmena.
- Zařízení může mít pouze jednu značku. Jakákoli následná značka přidaná do zařízení přepíše předchozí značku.
- Zařízení může patřit pouze do jedné skupiny.

1. Přidat značku do zařízení:

    1. V **IoT Edge** v levém navigačním podokně Najděte službu Azure Percept DK a přejděte do jejího **vlákna**.

    1. Přidejte novou **aktualizaci zařízení pro IoT Hub** hodnotu značky, jak je znázorněno níže ( ```<CustomTagValue>``` odkazuje na hodnotu nebo název vaší značky, např. AzurePerceptGroup1). Přečtěte si další informace o [značkách dokumentu JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)pro vyzdvojené zařízení.

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Klikněte na **Uložit** a vyřešte všechny problémy s formátováním.

1. Vytvořte skupinu výběrem existující značky IoT Hub služby Azure:

    1. Přejděte zpátky na stránku Azure IoT Hub.

    1. Na panelu nabídky na levé straně vyberte **aktualizace zařízení** v části **Automatická správa zařízení** .

    1. Vyberte kartu **skupiny** . Na této stránce se zobrazí počet neseskupených zařízení připojených k aktualizaci zařízení.

    1. Vyberte **+ Přidat** a vytvořte novou skupinu.

    1. Vyberte ze seznamu značku IoT Hub a klikněte na **Odeslat**.

    1. Po vytvoření skupiny se aktualizuje seznam a graf kompatibility aktualizací. Graf zobrazuje počet zařízení v různých stavech dodržování předpisů: **při nejnovější aktualizaci** jsou **k dispozici nové aktualizace**, **probíhající aktualizace** a **ještě nejsou seskupené**.

## <a name="deploy-an-update"></a>Nasazení aktualizace

1. V části **dostupné aktualizace** by se měla zobrazit nově vytvořená skupina s novou aktualizací (možná budete muset aktualizaci jednou aktualizovat). Vyberte aktualizaci.

1. Potvrďte, že je jako cílová skupina zařízení vybrána správná skupina zařízení. Vyberte **počáteční datum** a **čas spuštění** nasazení a pak klikněte na **vytvořit nasazení**.

    > [!CAUTION]
    > Nastavením počátečního času v minulosti dojde k okamžitému aktivaci nasazení.

1. Podívejte se na graf dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá.

1. Po dokončení aktualizace se v grafu dodržování předpisů projeví váš nový stav aktualizace.

1. V horní části stránky **aktualizace zařízení** vyberte kartu **nasazení** .

1. Vyberte nasazení, aby se zobrazily podrobnosti nasazení. Možná budete muset kliknout na **aktualizovat** , dokud se **stav** nezmění na **úspěšné**.

## <a name="next-steps"></a>Další kroky

Vaše sada dev SDK se teď úspěšně aktualizovala. Můžete pokračovat v vývoji a provozu pomocí sady dev Kit.