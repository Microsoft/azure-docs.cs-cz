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
ms.openlocfilehash: 6d915145e64a5f1a097f38cf79b19426c3acbaf2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457423"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Nasadit moduly Azure IoT Edge z portálu Azure portal

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují.

Tento článek popisuje, jak na webu Azure portal vás provede vytvořením manifest nasazení a doručením (push) nasazení do zařízení IoT Edge. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete v tématu [nasazení a sledování IoT Edgech modulů ve velkém měřítku](how-to-deploy-monitor.md) .

## <a name="prerequisites"></a>Požadavky

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* [IoT Edge zařízení](how-to-register-device.md#register-in-the-azure-portal) s nainstalovaným modulem runtime IoT Edge.

## <a name="select-your-device"></a>Vyberte zařízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
1. V nabídce vyberte **IoT Edge** .
1. Kliknutím na ID cílové zařízení ze seznamu zařízení.
1. Vyberte **Nastavit moduly**.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.

Na webu Azure portal obsahuje průvodce, který vás provede vytvořením manifest nasazení místo ruční vytváření dokumentů JSON. Má tři kroky: **přidat moduly**, **zadat trasy**a **zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **nastavení Container Registry** na stránce zadejte přihlašovací údaje pro přístup k jakýmkoli soukromým kontejnerům, které obsahují image modulu.

1. V části **moduly nasazení** na stránce vyberte **Přidat**.

1. Podívejte se na typy moduly z rozevíracího seznamu:

   * **IoT Edge modul** – výchozí možnost.
   * **Azure Stream Analytics** moduly pouze modulu generované z úlohy Azure Stream Analytics.
   * **Azure Machine Learning** pouze image modelů, které jsou vygenerovány z pracovního prostoru Azure Machine Learning.

1. Vyberte **modul IoT Edge**.

1. Zadejte název modulu a pak zadejte image kontejneru. Příklad:

   * **Název** – SimulatedTemperatureSensor
   * **Identifikátor URI image** – MCR.Microsoft.com/azureiotedge-Simulated-Temperature-Sensor:1.0

1. V případě potřeby zadejte volitelná pole. Další informace o možnostech vytvoření kontejneru, zásadách restartování a požadovaném stavu najdete v tématu [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties). Další informace o tomto modulu najdete v tématu [definice nebo aktualizace požadovaných vlastností](module-composition.md#define-or-update-desired-properties).

1. Vyberte **Uložit**.

1. Opakujte kroky 2 až 6 pro přidání dalších modulů k nasazení.

1. Kliknutím na tlačítko **Další** pokračujte v části trasy.

### <a name="specify-routes"></a>Zadejte trasy

Ve výchozím nastavení průvodce poskytuje trasu nazvanou **Route** a definovanou jako **FROM/* into $upstream * *, což znamená, že všechny zprávy ve službě IoT Hub budou odesílány do výstupů všech modulů.  

Přidejte nebo aktualizujte trasy s informacemi z [deklarace směrování](module-composition.md#declare-routes)a pak výběrem **Další** pokračujte do části přezkoumání.

### <a name="review-deployment"></a>Zkontrolujte nasazení

Zkontrolujte část pořady, kterou jste nasazení JSON manifestu byl vytvořen na základě vašeho výběru v předchozích dvou částech. Všimněte si, že jsou deklarovány dva moduly, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou požadované výchozí hodnoty v každém nasazení.

Zkontrolujte informace o svém nasazení a pak vyberte **Odeslat**.

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Až nasadíte moduly do svého zařízení, můžete je zobrazit na stránce **Podrobnosti o zařízení** na portálu. Tato stránka zobrazuje název každé nasazené modulu, stejně jako užitečné informace, jako je nasazení stavu a ukončovací kód.

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

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat a monitorovat IoT Edge moduly ve velkém měřítku](how-to-deploy-monitor.md) .
