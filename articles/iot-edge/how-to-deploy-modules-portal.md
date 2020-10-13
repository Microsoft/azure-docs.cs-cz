---
title: Nasazení modulů z Azure Portal-Azure IoT Edge
description: Pomocí IoT Hub v Azure Portal nahrajte modul IoT Edge ze svého IoT Hub do zařízení IoT Edge, jak je nakonfigurované v manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67c7c71e1f1f3eb9e76aa4938cb4a0a15ca405c8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978794"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Nasazení Azure IoT Edgech modulů z Azure Portal

Jakmile vytvoříte IoT Edge moduly s obchodní logikou, chcete je nasadit do svých zařízení, aby fungovaly na hraničních zařízeních. Pokud máte více modulů, které spolupracují při shromažďování a zpracování dat, můžete je nasadit najednou a deklarovat pravidla směrování, která je spojují.

Tento článek ukazuje, jak Azure Portal vás provede vytvořením manifestu nasazení a vložením nasazení do IoT Edgeho zařízení. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete v tématu věnovaném [nasazení a sledování IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Předpoklady

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* Zařízení IoT Edge.

  Pokud nemáte nastavené zařízení IoT Edge, můžete ho vytvořit na virtuálním počítači Azure. Použijte postup v jednom z článků rychlý Start k [Vytvoření virtuálního zařízení](quickstart-linux.md) se systémem Linux nebo [Vytvoření virtuálního zařízení s Windows](quickstart.md).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.

Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení místo ručního vytváření dokumentu JSON. Má tři kroky: **přidat moduly**, **zadat trasy**a **zkontrolovat nasazení**.

### <a name="select-device-and-add-modules"></a>Vyberte zařízení a přidejte moduly.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
1. V levém podokně vyberte v nabídce možnost **IoT Edge** .
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Na horním panelu vyberte možnost **nastavit moduly**.
1. V části **nastavení Container Registry** na stránce zadejte přihlašovací údaje pro přístup k jakýmkoli soukromým kontejnerům, které obsahují image modulu.
1. V části **IoT Edge moduly** na stránce vyberte **Přidat**.
1. Podívejte se na typy modulů z rozevírací nabídky:

   * **IoT Edge modul** – zadejte název modulu a identifikátor URI image kontejneru. Například identifikátor URI image pro vzorový modul SimulatedTemperatureSensor je `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Pokud je image modulu uložená v soukromém registru kontejnerů, přidejte přihlašovací údaje na této stránce pro přístup k imagi.
   * **Modul Marketplace** – moduly hostované ve Azure Marketplace. Některé moduly Marketplace vyžadují další konfiguraci, proto si Projděte podrobnosti o modulu v seznamu [Azure Marketplace IoT Edge moduly](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Azure Stream Analytics modul** – moduly generované z úlohy Azure Stream Analytics.

1. Po přidání modulu vyberte v seznamu název modulu a otevřete nastavení modulu. V případě potřeby vyplňte volitelná pole. Další informace o možnostech vytvoření kontejneru, zásadách restartování a požadovaném stavu najdete v tématu [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties). Další informace o tomto modulu najdete v tématu [definice nebo aktualizace požadovaných vlastností](module-composition.md#define-or-update-desired-properties).
1. V případě potřeby opakujte kroky 5 až 8 pro přidání dalších modulů do nasazení.
1. Vyberte **Další: trasy** pro pokračování v části trasy.

### <a name="specify-routes"></a>Zadat trasy

Na kartě **trasy** definujete, jak jsou zprávy předávány mezi moduly a IoT Hub. Zprávy se vytvářejí pomocí párů název/hodnota. Ve výchozím nastavení se trasa nazývá **Route** a definuje se jako **z/Messages/ \* do $upstream**, což znamená, že do služby IoT Hub budou odesílány výstupy všech zpráv pomocí libovolných modulů.  

Přidejte nebo aktualizujte trasy s informacemi z [deklarace trasy](module-composition.md#declare-routes)a potom vyberte **Další: zkontrolovat + vytvořit** a pokračujte dalším krokem průvodce.

### <a name="review-deployment"></a>Zkontrolovat nasazení

V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozích dvou částech. Všimněte si, že jsou deklarovány dva moduly, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou požadované výchozí hodnoty v každém nasazení.

Zkontrolujte informace o svém nasazení a pak vyberte **vytvořit**.

## <a name="view-modules-on-your-device"></a>Zobrazit moduly na zařízení

Až nasadíte moduly do svého zařízení, můžete je zobrazit na stránce s podrobnostmi o zařízení v IoT Hub. Tato stránka zobrazuje název každého nasazeného modulu a také užitečné informace, jako je stav nasazení a ukončovací kód.

## <a name="deploy-modules-from-azure-marketplace"></a>Nasadit moduly z Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) je tržiště aplikací a služeb online, kde můžete procházet široké spektrum podnikových aplikací a řešení, která jsou certifikovaná a optimalizovaná pro spouštění v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

IoT Edge modul můžete nasadit z Azure Marketplace a z IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Nasazení z Azure Marketplace

Seznámení s IoT Edge moduly na webu Marketplace a když najdete tu, kterou chcete nasadit, můžete ji nasadit tak, že vyberete **vytvořit** nebo **získat nyní**. Pokračujte postupem Průvodce nasazením, který se může lišit v závislosti na vybraném modulu IoT Edge:

1. Kliknutím na **pokračovat**potvrďte podmínkami použití a zásady ochrany osobních údajů poskytovatele. Je možné, že nejprve budete muset zadat kontaktní údaje.
1. Vyberte své předplatné a IoT Hub, ke kterému je cílové zařízení připojené.
1. Vyberte **nasadit do zařízení**.
1. Zadejte název zařízení nebo vyberte **Najít zařízení** , která chcete procházet mezi zařízeními zaregistrovanými v centru.
1. Vyberte **vytvořit** , pokud chcete pokračovat v standardním procesu konfigurace manifestu nasazení, včetně přidání dalších modulů, pokud je to potřeba. Podrobnosti o novém modulu, jako je identifikátor URI obrázku, možnosti vytvoření a požadované vlastnosti, jsou předdefinovány, ale lze je změnit.

Ověřte, že je modul nasazený ve vašem IoT Hub v Azure Portal. Vyberte zařízení, vyberte **nastavit moduly** a modul by měl být uveden v části **IoT Edge moduly** .

### <a name="deploy-from-azure-iot-hub"></a>Nasazení z Azure IoT Hub

Modul můžete rychle nasadit z Azure Marketplace do svého zařízení v IoT Hub v Azure Portal.

1. V Azure Portal přejděte na IoT Hub.
1. V levém podokně v části **Automatická správa zařízení**vyberte **IoT Edge**.
1. Vyberte IoT Edge zařízení, které má přijmout nasazení.
1. Na horním panelu vyberte možnost **nastavit moduly**.
1. V části **IoT Edge moduly** klikněte na **Přidat**a v rozevírací nabídce vyberte **modul Marketplace** .

![Přidat modul v IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Vyberte modul na stránce **IoT Edge modulu pro Marketplace** . Vybraný modul se automaticky nakonfiguruje pro vaše předplatné, skupinu prostředků a zařízení. Pak se zobrazí v seznamu IoT Edge moduly. Některé moduly můžou vyžadovat další konfiguraci.

> [!TIP]
> Informace o IoT Edgech modulech z Azure IoT Hub jsou omezené. Můžete se nejdřív dozvědět víc o [IoT Edge modulech](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) v Azure Marketplace.

Vyberte **Další: trasy** a pokračujte v nasazení, jak je popsáno v tématu [určení tras](#specify-routes) a [přezkoumání nasazení](#review-deployment) výše v tomto článku.

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat a monitorovat IoT Edge moduly ve velkém měřítku](how-to-deploy-at-scale.md) .
