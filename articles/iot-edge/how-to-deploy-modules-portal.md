---
title: Nasadit moduly z webu Azure portal – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí webu Azure portal můžete nasadit moduly do zařízení IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f27484fd1d47a2e29aa5083a7d440e5c7dba11c1
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839646"
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

Na webu Azure portal obsahuje průvodce, který vás provede vytvořením manifest nasazení místo ruční vytváření dokumentů JSON. Má tři kroky: **Přidejte moduly**, **Zadejte trasy**a **Zkontrolujte nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **nastavení Container Registry** na stránce zadejte přihlašovací údaje pro přístup k jakýmkoli soukromým kontejnerům, které obsahují image modulu.

1. V části **moduly nasazení** na stránce vyberte **Přidat**.

1. Podívejte se na typy moduly z rozevíracího seznamu:

   * **Modul IoT Edge** – výchozí možnost.
   * **Modul Azure Stream Analytics** – pouze moduly, které byly generovány z úlohy Azure Stream Analytics.
   * **Azure Machine Learning** pouze image modelů, které jsou vygenerovány z pracovního prostoru Azure Machine Learning.

1. Vyberte **modul IoT Edge**.

1. Zadejte název modulu a pak zadejte image kontejneru. Příklad:

   * **Název** – SimulatedTemperatureSensor
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

Azure Marketplace je tržiště aplikací a služeb online, kde můžete procházet široké spektrum podnikových aplikací a řešení, která jsou certifikovaná a optimalizovaná pro spouštění v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). K Azure Marketplace lze také přistupovat prostřednictvím Azure Portal v části **vytvoření prostředku**.

IoT Edge modul můžete nainstalovat buď z Azure Marketplace nebo Azure Portal:

1. Vyhledejte modul a zahajte proces nasazení.

   * Azure Portal: Najděte modul a vyberte **vytvořit**.

   * Azure Marketplace:

     1. Najděte modul a vyberte **získat**.
     1. Kliknutím na **pokračovat**potvrďte podmínkami použití a zásady ochrany osobních údajů poskytovatele.

1. Vyberte své předplatné a IoT Hub, ke kterému je cílové zařízení připojené.

1. Vyberte **nasadit do zařízení**.

1. Zadejte název zařízení nebo vyberte **Najít zařízení** , která chcete procházet mezi zařízeními zaregistrovanými v centru.

1. Vyberte **vytvořit** , pokud chcete pokračovat v standardním procesu konfigurace manifestu nasazení, včetně přidání dalších modulů, pokud je to potřeba. Podrobnosti o novém modulu, jako je identifikátor URI obrázku, možnosti vytvoření a požadované vlastnosti, jsou předdefinovány, ale lze je změnit.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
