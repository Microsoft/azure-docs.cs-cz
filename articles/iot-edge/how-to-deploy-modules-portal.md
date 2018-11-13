---
title: Nasadit moduly Azure IoT Edge (portál) | Dokumentace Microsoftu
description: Pomocí webu Azure portal můžete nasadit moduly do zařízení IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b135832c1f0cb8af23a513d4914d7e32b398be7e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564931"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Nasadit moduly Azure IoT Edge z portálu Azure portal

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují. 

Tento článek popisuje, jak na webu Azure portal vás provede vytvořením manifest nasazení a doručením (push) nasazení do zařízení IoT Edge. Informace o vytváření nasazení, které cílí na více zařízeních na základě jejich sdílené značek, naleznete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 
* [Zařízení IoT Edge](how-to-register-device-portal.md) nainstalován modul runtime IoT Edge. 

## <a name="select-your-device"></a>Vyberte zařízení

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
2. Vyberte **IoT Edge** z nabídky.
3. Kliknutím na ID cílové zařízení ze seznamu zařízení. 
4. Vyberte **Nastavit moduly**.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

Na webu Azure portal obsahuje průvodce, který vás provede vytvořením manifest nasazení místo ruční vytváření dokumentů JSON. Má tři kroky: **přidat moduly**, **trasy zadejte**, a **zkontrolujte nasazení**. 

### <a name="add-modules"></a>Přidat moduly

1. V **nastavení registru** části stránky, zadejte přihlašovací údaje pro přístup k žádné registry privátní kontejnerů, které obsahují vaše bitové kopie modulu. 
2. V **moduly nasazení** části stránky vyberte **přidat**. 
3. Podívejte se na typy moduly z rozevíracího seznamu: 
   * **Modul IoT Edge** – výchozí možnost.
   * **Modul Azure Stream Analytics** – pouze moduly, které byly generovány z úlohy Azure Stream Analytics. 
4. Vyberte **modul IoT Edge**.
5. Zadejte název modulu a pak zadejte image kontejneru. Příklad: 
   * **Název** -tempSensor
   * **Identifikátor URI image** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. V případě potřeby zadejte volitelná pole. Pro další informace o kontejneru vytvořte možnosti, zásady restartování a zjistěte požadovaný stav [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties). Další informace o dvojčeti modulu najdete v části [definovat nebo aktualizace požadované vlastnosti](module-composition.md#define-or-update-desired-properties).
7. Vyberte **Uložit**.
8. Opakujte kroky 2 až 6 pro přidání dalších modulů k nasazení. 
9. Vyberte **Další** pokračujte k části trasy.

### <a name="specify-routes"></a>Zadejte trasy

Ve výchozím nastavení průvodce umožňuje volat trasu **trasy** a definovaná jako **FROM /* do $upstream **, což znamená, že všechny zprávy výstupu tak všechny moduly, které se odesílají do služby IoT hub.  

Přidat nebo aktualizovat trasy s informacemi z [trasy deklarovat](module-composition.md#declare-routes)a pak vyberte **Další** pokračujte k části revize.

### <a name="review-deployment"></a>Zkontrolujte nasazení

Zkontrolujte část pořady, kterou jste nasazení JSON manifestu byl vytvořen na základě vašeho výběru v předchozích dvou částech. Všimněte si, že existují dva moduly deklarovat, že jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou povinné výchozí hodnoty v každém nasazení. 

Zkontrolujte informace o nasazení a pak vyberte **odeslat**. 

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich **podrobnosti o zařízení** stránky portálu. Tato stránka zobrazuje název každé nasazené modulu, stejně jako užitečné informace, jako je nasazení stavu a ukončovací kód. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
