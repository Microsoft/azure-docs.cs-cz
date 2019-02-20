---
title: Nasadit moduly z webu Azure portal – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí webu Azure portal můžete nasadit moduly do zařízení IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430110"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Nasadit moduly Azure IoT Edge z portálu Azure portal

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují.

Tento článek popisuje, jak na webu Azure portal vás provede vytvořením manifest nasazení a doručením (push) nasazení do zařízení IoT Edge. Informace o vytváření nasazení, které cílí na více zařízeních na základě jejich sdílené značek, naleznete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* [Zařízení IoT Edge](how-to-register-device-portal.md) nainstalován modul runtime IoT Edge.

## <a name="select-your-device"></a>Vyberte zařízení

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
1. Vyberte **IoT Edge** z nabídky.
1. Kliknutím na ID cílové zařízení ze seznamu zařízení.
1. Vyberte **Nastavit moduly**.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

Na webu Azure portal obsahuje průvodce, který vás provede vytvořením manifest nasazení místo ruční vytváření dokumentů JSON. Má tři kroky: **Přidat moduly**, **trasy zadejte**, a **zkontrolujte nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V **nastavení registru** části stránky, zadejte přihlašovací údaje pro přístup k žádné registry privátní kontejnerů, které obsahují vaše bitové kopie modulu.

1. V **moduly nasazení** části stránky vyberte **přidat**.

1. Podívejte se na typy moduly z rozevíracího seznamu:

   * **Modul IoT Edge** – výchozí možnost.
   * **Modul Azure Stream Analytics** – pouze moduly, které byly generovány z úlohy Azure Stream Analytics.
   * **Azure Machine Learning Module** – pouze model obrázky generované z pracovního prostoru Azure Machine Learning.

1. Vyberte **modul IoT Edge**.

1. Zadejte název modulu a pak zadejte image kontejneru. Příklad:

   * **Název** -tempSensor
   * **Identifikátor URI image** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. V případě potřeby zadejte volitelná pole. Pro další informace o kontejneru vytvořte možnosti, zásady restartování a zjistěte požadovaný stav [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties). Další informace o dvojčeti modulu najdete v části [definovat nebo aktualizace požadované vlastnosti](module-composition.md#define-or-update-desired-properties).

1. Vyberte **Uložit**.

1. Opakujte kroky 2 až 6 pro přidání dalších modulů k nasazení.

1. Vyberte **Další** pokračujte k části trasy.

### <a name="specify-routes"></a>Zadejte trasy

Ve výchozím nastavení průvodce umožňuje volat trasu **trasy** a definovaná jako **FROM /* do $upstream **, což znamená, že všechny zprávy výstupu tak všechny moduly, které se odesílají do služby IoT hub.  

Přidat nebo aktualizovat trasy s informacemi z [trasy deklarovat](module-composition.md#declare-routes)a pak vyberte **Další** pokračujte k části revize.

### <a name="review-deployment"></a>Zkontrolujte nasazení

Zkontrolujte část pořady, kterou jste nasazení JSON manifestu byl vytvořen na základě vašeho výběru v předchozích dvou částech. Všimněte si, že existují dva moduly deklarovat, že jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou povinné výchozí hodnoty v každém nasazení.

Zkontrolujte informace o nasazení a pak vyberte **odeslat**.

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich **podrobnosti o zařízení** stránky portálu. Tato stránka zobrazuje název každé nasazené modulu, stejně jako užitečné informace, jako je nasazení stavu a ukončovací kód.

## <a name="deploy-modules-from-azure-marketplace"></a>Nasadit moduly z Azure Marketplace

Azure Marketplace je online tržiště aplikací a služeb, kde mohou procházet širokou škálu podnikových aplikací a řešení, které jsou certifikované a optimalizované pro spouštění v Azure, včetně [moduly IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace lze rovněž přistupovat prostřednictvím webu Azure portal v části **vytvořit prostředek**.

Modul IoT Edge můžete nainstalovat z webu Azure Marketplace nebo na webu Azure portal:

1. Vyhledejte modul a zahájíte proces nasazení.

   * Azure portal: Vyhledejte modul a vyberte **vytvořit**.

   * Azure Marketplace:

     1. Vyhledejte modul a vyberte **získat**.
     1. Potvrdit poskytovatele podmínky použití a ochrana osobních údajů zásady tak, že vyberete **pokračovat**.

1. Vyberte předplatné a IoT Hub, ke kterému je připojený cílové zařízení.

1. Zvolte **nasadit do zařízení**.

1. Zadejte název zařízení nebo vyberte **najít zařízení** procházet mezi zařízení zaregistrovaná v centru.

1. Vyberte **vytvořit** standardní proces konfigurace manifestu nasazení, včetně přidávání dalších modulů, v případě potřeby pokračovat. Možnosti vytvoření podrobnosti pro nový modul, jako je identifikátor URI, image a požadované vlastnosti jsou předdefinovány, ale je možné změnit.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
