---
title: Nasazení Azure IoT Edge moduly (portál) | Microsoft Docs
description: Použití portálu Azure k nasazení modulů na IoT hraniční zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4082189d451f670c1ae3f76b8ec785d8bd0518b3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035814"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Nasazení moduly Azure IoT Edge z portálu Azure

Po vytvoření IoT Edge moduly s obchodní logiky, budete chtít nasadit do zařízení tak, aby provoz na hranici. Pokud máte více modulů, které vzájemně spolupracují ke sběru a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují. 

Tento článek ukazuje, jak na portálu Azure provede vás vytváření manifest nasazení a nabízet nasazení IoT hraniční zařízení. Informace o vytváření nasazení, které cílí více zařízení podle jejich sdílené značky najdete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 
* [IoT hraniční zařízení](how-to-register-device-portal.md) s modulem runtime IoT Edge nainstalována. 

## <a name="select-your-device"></a>Vyberte příslušné zařízení.

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do služby IoT hub.
2. Vyberte **IoT Edge** z nabídky.
3. Kliknutím na ID cílové zařízení ze seznamu zařízení. 
4. Vyberte **Nastavit moduly**.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifest nasazení

Manifest nasazení je dokument JSON, který popisuje, jaké moduly pro nasazení, tok dat mezi moduly a požadované vlastnosti dvojčata modulu. Další informace o tom, jak manifesty nasazení pracovních a jak při jejich vytváření najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

Portál Azure obsahuje průvodce, který vás provede procesem vytvoření manifestu nasazení, místo vytvoření dokumentu JSON ručně. Má tři kroky: **přidat moduly**, **určit trasy**, a **zkontrolujte nasazení**. 

### <a name="add-modules"></a>Přidání modulů

1. V **nastavení registru** části stránky, zadejte přihlašovací údaje pro přístup k žádné privátní kontejneru registrech obsahující modulu obrázků. 
2. V **moduly nasazení** části stránky, vyberte **přidat**. 
3. Z rozevíracího seznamu vyberte typ modulu: 
   * **Modul Edge IoT** -výchozí možnost.
   * **Azure Stream Analytics modulu** -pouze moduly vygenerovat z pracovního vytížení Azure Stream Analytics. 

4. Zadejte název pro modul a potom zadejte bitovou kopii kontejneru. Příklad: 
   * **Název** -tempSensor
   * **Obrázek URI** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
5. Vyplňte volitelná pole, v případě potřeby. Pro další informace o kontejneru vytvořit možnosti, zásady restartování, a zobrazit požadovaný stav [EdgeAgent potřeby vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties). Další informace o dvojici modulu najdete v části [definovat nebo aktualizace potřeby vlastnosti](module-composition.md#define-or-update-desired-properties).
6. Vyberte **Uložit**.
7. Opakujte kroky 2 až 6 pro přidání dalších modulů do vašeho nasazení. 
8. Vyberte **Další** nadále části trasy.

### <a name="specify-routes"></a>Zadejte trasy

Ve výchozím nastavení Průvodce poskytuje trasu názvem **trasy** a definované jako **FROM /* do $ proti proudu **, což znamená, že všechny zprávy výstupních podle všech modulů se odesílají do služby IoT hub.  

Přidat nebo aktualizovat trasy informace z [deklarovat trasy](module-composition.md#declare-routes), pak vyberte **Další** nadále části revize.

### <a name="review-deployment"></a>Zkontrolujte nasazení

V kontrolní části se dozvíte, které jste nasazení JSON manifestu které byla vytvořena na základě svého výběru v předchozích dvou částech. Všimněte si, že existují dvě moduly deklarovaný jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dvě moduly tvoří [IoT Edge runtime](iot-edge-runtime.md) a jsou požadované výchozí nastavení v každém nasazení. 

Zkontrolujte informace o nasazení a pak vyberte **odeslání**. 

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Po nasazení modulů do svého zařízení můžete zobrazit všechny z nich **podrobnosti o zařízení** na portálu. Tato stránka zobrazuje název každé nasazených modul, jakož i užitečné informace, jako je nasazení stavu a ukončovací kód. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasazení a monitorování modulů IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
