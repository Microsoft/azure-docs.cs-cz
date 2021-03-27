---
title: Aktualizace zařízení pro Azure v reálném čase – operační systém | Microsoft Docs
description: Začínáme s aktualizací zařízení pro Azure v reálném čase – operační systém
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629049"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Kurz aktualizace zařízení pro Azure IoT Hub s využitím operačního systému Azure v reálném čase (RTO)

Tento kurz vás seznámí s postupem vytvoření aktualizace zařízení pro IoT Hub agenta v Azure RTO NetX Duo. Poskytuje taky jednoduchá rozhraní API pro vývojáře, aby mohli integrovat funkce aktualizace zařízení ve svých aplikacích. Seznamte se s [ukázkami](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) karet pro vyhodnocení klíčů, které obsahují příručky Začínáme, a Naučte se konfigurovat, sestavovat a nasazovat aktualizace OTA (Over-The-Air) do zařízení.

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Začínáme
> * Označení zařízení
> * Vytvoření skupiny zařízení
> * Nasazení aktualizace image
> * Monitorování nasazení aktualizace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* Přístup k IoT Hub. Doporučuje se použít úroveň S1 (Standard) nebo vyšší.
* Instance aktualizace zařízení a účet propojený s vaším IoT Hub. Podle pokynů v průvodci [vytvořte a propojte](http://create-device-update-account.md/) účet aktualizace zařízení, pokud jste to ještě neudělali dříve.

## <a name="get-started"></a>Začínáme

V každém z ukázkových projektů Azure RTO, které jsou specifické pro jednotlivé desky, najdete kód a dokumentaci, jak používat aktualizaci zařízení pro IoT Hub. 
1. Stažení ukázkových souborů specifických pro jednotlivé karty z [ukázek Azure RTO a aktualizace zařízení](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Vyhledejte složku docs ze stažené ukázky.
3. V dokumentaci použijte postup přípravy prostředků Azure, účtu a registrace zařízení IoT.
5. Dále postupujte podle dokumentace a sestavte novou image firmwaru a importujte manifest pro svou kartu.
6. Další obrázek pro publikování firmwaru a manifest do aktualizace zařízení pro IoT Hub.
7. Nakonec Stáhněte a spusťte projekt na svém zařízení.

Přečtěte si další informace o [Azure RTO](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Označení zařízení

1. Nechte aplikaci zařízení spuštěnou v předchozím kroku.
2. Přihlaste se [Azure Portal](https://portal.azure.com) a přejděte do IoT Hub.
3. V levém navigačním podokně v části zařízení IoT Najděte své zařízení IoT a přejděte k zařízení s dvojitou platností.
4. V případě, že je zařízení s dvojitou hodnotou, odstraňte všechny existující hodnoty značky aktualizace zařízení nastavením na hodnotu null.
5. Přidejte novou hodnotu značky aktualizace zařízení, jak je uvedeno níže.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Vytvořit skupinu aktualizací

1. Přejít na IoT Hub, kterou jste dříve připojili k instanci aktualizace zařízení.
2. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.
3. V horní části stránky vyberte kartu skupiny. 
4. Kliknutím na tlačítko Přidat vytvořte novou skupinu.
5. V seznamu vyberte značku IoT Hub, kterou jste vytvořili v předchozím kroku. Vyberte vytvořit skupinu aktualizací.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Snímek obrazovky znázorňující výběr značky" lightbox="media/create-update-group/select-tag.PNG":::

[Další informace](create-update-group.md) o přidávání značek a vytváření skupin aktualizací

## <a name="deploy-new-firmware"></a>Nasadit nový firmware

1. Po vytvoření skupiny byste měli vidět novou aktualizaci dostupnou pro skupinu zařízení s odkazem na aktualizaci v části čekají na aktualizace. Možná budete muset aktualizaci jednou aktualizovat. 
2. Klikněte na dostupnou aktualizaci.
3. Potvrďte, že je jako cílová skupina vybraná správná skupina. Naplánujte nasazení a pak vyberte nasadit aktualizaci.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Vybrat aktualizaci" lightbox="media/deploy-update/select-update.png":::

4. Zobrazení grafu dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Probíhá aktualizace." lightbox="media/deploy-update/update-in-progress.png":::

5. Po úspěšné aktualizaci zařízení by se měl zobrazit graf dodržování předpisů a aktualizace podrobností nasazení, aby odrážely stejnou hodnotu. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Úspěšná aktualizace" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorování nasazení aktualizací

1. V horní části stránky vyberte kartu nasazení.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta nasazení" lightbox="media/deploy-update/deployments-tab.png":::

2. Vyberte nasazení, které jste vytvořili pro zobrazení podrobností o nasazení.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Podrobnosti o nasazení" lightbox="media/deploy-update/deployment-details.png":::

3. Pokud chcete zobrazit nejnovější podrobnosti o stavu, vyberte aktualizovat. Pokračujte v tomto procesu, dokud se stav nezmění na úspěšné.

Nyní jste dokončili úspěšnou aktualizaci kompletních imagí na konci pomocí aktualizace zařízení pro IoT Hub na zařízení malin 3 B + +. 

## <a name="cleanup-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete, vyčistěte vyčištění účtu aktualizace zařízení, instance IoT Hub a zařízení IoT. 

## <a name="next-steps"></a>Další kroky

Další informace o Azure RTO a o tom, jak funguje se službou Azure IoT, najdete v tématu https://azure.com/rtos .
