---
title: Vytvoření skupiny zařízení v aktualizaci zařízení pro Azure IoT Hub | Microsoft Docs
description: Vytvoření skupiny zařízení v aktualizaci zařízení pro Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679239"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Vytvoření skupin zařízení v aktualizaci zařízení pro IoT Hub
Aktualizace zařízení pro IoT Hub umožňuje nasadit aktualizaci do skupiny zařízení IoT.

## <a name="prerequisites"></a>Požadavky

* [Pro IoT Hub povolený přístup k IoT Hub s aktualizací zařízení](create-device-update-account.md). Doporučujeme pro IoT Hub použít úroveň S1 (Standard) nebo vyšší. 
* Zařízení IoT (nebo simulátor) zřízené pro aktualizaci zařízení v IoT Hub.
* [Pro zřízené zařízení se úspěšně naimportovala aspoň jedna aktualizace.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Přidání značky do zařízení  

Aktualizace zařízení pro IoT Hub umožňuje nasadit aktualizaci do skupiny zařízení IoT. Chcete-li vytvořit skupinu, prvním krokem je přidání značky do cílové sady zařízení v IoT Hub. Značky lze do zařízení úspěšně přidat až po připojení k aktualizaci zařízení.

Následující dokumentace popisuje, jak přidat a aktualizovat značku.

### <a name="programmatically-update-device-twin"></a>Programově aktualizovat dvojitou dvojici zařízení

Po registraci zařízení pomocí aktualizace zařízení můžete v zařízení s použitím RegistryManager aktualizovat jeho dvojitou značku s příslušnou značkou. 
[Naučte se přidávat značky pomocí ukázkové aplikace .NET.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Přečtěte si o vlastnostech značek](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Formát značky aktualizace zařízení

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Používání úloh

Je možné naplánovat úlohu na více zařízeních a přidat nebo aktualizovat značky aktualizace zařízení podle [těchto](../iot-hub/iot-hub-devguide-jobs.md) příkladů. [Další informace](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Tato akce prochází vzhledem k aktuální kvótě zpráv služby IOT hub a doporučuje se v určitém čase měnit pouze až 50 000 značek zařízení. v takovém případě bude možná potřeba koupit další IoT Hub jednotky, pokud překročíte denní kvótu IoT Hub zpráv. Podrobnosti najdete v článku [kvóty a omezování](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Přímé aktualizace s dvojitou sebou

Značky je také možné přidat nebo aktualizovat přímo v zařízení.

1. Přihlaste se [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub.

2. V levém navigačním podokně v části zařízení IoT nebo IoT Edge Najděte své zařízení IoT a přejděte k zařízení s dvojitou platností.

3. V případě, že je zařízení s dvojitou hodnotou, odstraňte všechny existující hodnoty značky aktualizace zařízení nastavením na hodnotu null.

4. Přidejte novou hodnotu značky aktualizace zařízení, jak je uvedeno níže. [Příkladem je dokument JSON s dvojitou značkou se značkami.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Omezení

* Do značky můžete přidat libovolnou hodnotu s výjimkou ' Nezařazeno do kategorie ', což je vyhrazená hodnota.
* Hodnota značky nesmí překročit 255 znaků.
* Hodnota značky může obsahovat alfanumerické znaky a následující speciální znaky: ".", "-", "_", "~".
* V názvech značek a skupin se rozlišují malá a velká písmena.
* Zařízení může mít pouze jednu značku s názvem ADUGroup, jakékoli následné přidání značky s tímto názvem přepíše existující hodnotu pro název značky ADUGroup.
* Jedno zařízení může patřit pouze do jedné skupiny.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Vytvoření skupiny zařízení výběrem existující značky IoT Hub

1. Přejděte na [Azure Portal](https://portal.azure.com).

2. Vyberte IoT Hub, kterou jste dříve připojili k instanci aktualizace zařízení.

3. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

4. V horní části stránky vyberte kartu skupiny. Budete moct zobrazit počet zařízení připojených k aktualizaci zařízení, která ještě nejsou seskupená.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Snímek obrazovky neseskupených zařízení." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Kliknutím na tlačítko Přidat vytvořte novou skupinu.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Snímek obrazovky s přidáním skupiny zařízení" lightbox="media/create-update-group/add-group.png":::

6. Vyberte ze seznamu značku IoT Hub a pak vyberte vytvořit skupinu aktualizací.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Snímek obrazovky s výběrem značky" lightbox="media/create-update-group/select-tag.png":::

7. Po vytvoření skupiny uvidíte, že je aktualizovaný seznam a graf kompatibility aktualizací.  Tabulka aktualizace kompatibility zobrazuje počet zařízení v různých stavech dodržování předpisů: v nejnovější aktualizaci jsou k dispozici nové aktualizace, probíhající aktualizace a zařízení, která ještě nejsou seskupená. [Přečtěte si o kompatibilitě aktualizací.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Snímek obrazovky s zobrazením kompatibility aktualizací" lightbox="media/create-update-group/updated-view.png":::

8. Měla by se zobrazit vaše nově vytvořená skupina a všechny dostupné aktualizace pro zařízení v nové skupině. Aktualizaci můžete nasadit do nové skupiny z tohoto zobrazení kliknutím na název aktualizace. Další podrobnosti najdete v dalším kroku: nasazení aktualizace.

## <a name="view-device-details-for-the-group-you-created"></a>Zobrazit podrobnosti o zařízení pro skupinu, kterou jste vytvořili

1. Přejděte do nově vytvořené skupiny a klikněte na název skupiny.

2. Zobrazí se seznam zařízení, která jsou součástí skupiny, spolu s vlastnostmi aktualizace zařízení. V tomto zobrazení můžete také zobrazit informace o kompatibilitě aktualizací pro všechna zařízení, která jsou členy skupiny. Tabulka aktualizace kompatibility zobrazuje počet zařízení v různých stavech dodržování předpisů: při nejnovější aktualizaci jsou k dispozici nové aktualizace a probíhá aktualizace.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Snímek obrazovky s zobrazením podrobností skupiny zařízení" lightbox="media/create-update-group/group-details.png":::

3. Můžete také kliknout na každé individuální zařízení v rámci skupiny, které se přesměruje na stránku s podrobnostmi o zařízení v IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Snímek obrazovky s zobrazením podrobností o zařízení" lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Další kroky 

[Nasazení aktualizace](deploy-update.md)

[Další informace o skupinách zařízení](device-update-groups.md)

[Přečtěte si o kompatibilitě aktualizací.](device-update-compliance.md)
