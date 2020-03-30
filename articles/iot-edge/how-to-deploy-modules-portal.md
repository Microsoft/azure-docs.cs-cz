---
title: Nasazení modulů z portálu Azure – Azure IoT Edge
description: Pomocí služby IoT Hub na webu Azure Portal můžete z vašeho ioT hubu z vašeho IoT Hubu nasadit do zařízení IoT Edge, jak je nakonfigurovaný podle manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271444"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Nasazení modulů Azure IoT Edge z webu Azure Portal

Jakmile vytvoříte moduly IoT Edge s vaší obchodní logikou, chcete je nasadit do svých zařízení, aby fungovaly na hraničních zařízeních. Pokud máte více modulů, které spolupracují na shromažďování a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, která je spojují.

Tento článek ukazuje, jak vás portál Azure provede vytvořením manifestu nasazení a odesláním nasazení na zařízení IoT Edge. Informace o vytvoření nasazení, které se zaměřuje na více zařízení na základě jejich sdílených značek, najdete [v tématu Nasazení a monitorování modulů IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Požadavky

* Centrum [IoT v](../iot-hub/iot-hub-create-through-portal.md) předplacenéazure.
* [Zařízení IoT Edge](how-to-register-device.md#register-in-the-azure-portal) s nainstalovaným runtimem IoT Edge.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v [tématu Informace o tom, jak lze moduly IoT Edge používat, konfigurovat a znovu používat](module-composition.md).

Portál Azure má průvodce, který vás provede vytvořením manifestu nasazení, namísto ručního sestavení dokumentu JSON. Má tři kroky: **Přidat moduly**, **Zadat trasy**a **Zkontrolovat nasazení**.

### <a name="select-device-and-add-modules"></a>Výběr zařízení a přidání modulů

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do svého centra IoT hub.
1. V levém podokně vyberte z nabídky **IoT Edge.**
1. Klikněte na ID cílového zařízení ze seznamu zařízení.
1. Na horním panelu vyberte **Nastavit moduly**.
1. V části **Nastavení registru kontejnerů** na stránce zadejte pověření pro přístup ke všem privátním registrům kontejnerů, které obsahují ifotky modulu.
1. V části **Moduly okrajů IoT** na stránce vyberte **Přidat**.
1. Podívejte se na typy modulů z rozbalovací nabídky:

   * **Modul IoT Edge –** zadáte název modulu a identifikátor URI image kontejneru. Například identifikátor URI obrazu pro ukázkový modul `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`SimulatedTemperatureSensor je . Pokud je bitová kopie modulu uložena v registru soukromého kontejneru, přidejte pověření na této stránce pro přístup k bitové kopii.
   * **Modul Marketplace** – moduly hostované na Azure Marketplace. Některé moduly Marketplace vyžadují další konfiguraci, takže zkontrolujte podrobnosti o modulu v seznamu [modulů Azure Marketplace IoT Edge Modules.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Modul Azure Stream Analytics** – moduly generované z úlohy Azure Stream Analytics.

1. Po přidání modulu vyberte název modulu ze seznamu a otevřete nastavení modulu. V případě potřeby vyplňte volitelná pole. Další informace o možnostech vytváření kontejnerů naleznete v zásadách restartování a požadovaném stavu v tématu [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties). Další informace o dvojčeti modulu naleznete v [tématu Definování nebo aktualizace požadovaných vlastností](module-composition.md#define-or-update-desired-properties).
1. V případě potřeby opakujte kroky 5 až 8 a přidejte do nasazení další moduly.
1. Vyberte **Další: Trasy,** abyste pokračovali do úseku trasy.

### <a name="specify-routes"></a>Určení tras

Na kartě **Trasy** definujete, jak se zprávy předávají mezi moduly a centrem IoT Hub. Zprávy jsou konstruovány pomocí párů název/hodnota. Ve výchozím nastavení se trasa nazývá **trasa** a je definována jako **FROM\* /messages/ INTO $upstream**, což znamená, že všechny zprávy výstupní libovolné moduly jsou odesílány do centra IoT.  

Přidejte nebo aktualizujte trasy informacemi z [deklarovat trasy](module-composition.md#declare-routes)a pak vyberte **Další: Revize + vytvoření,** abyste pokračovali k dalšímu kroku průvodce.

### <a name="review-deployment"></a>Zkontrolovat nasazení

Část revize zobrazuje manifest nasazení JSON, který byl vytvořen na základě vašich výběrů v předchozích dvou částech. Všimněte si, že existují dva moduly deklarované, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou povinné výchozí hodnoty v každém nasazení.

Zkontrolujte informace o nasazení a vyberte **Vytvořit**.

## <a name="view-modules-on-your-device"></a>Zobrazení modulů v zařízení

Po nasazení modulů do zařízení je můžete všechny zobrazit na stránce s podrobnostmi o zařízení ve vašem Centru IoT Hub. Na této stránce se zobrazí název každého nasazeného modulu a také užitečné informace, jako je stav nasazení a ukončovací kód.

## <a name="deploy-modules-from-azure-marketplace"></a>Nasazení modulů z Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) je online trh s aplikacemi a službami, kde můžete procházet širokou škálu podnikových aplikací a řešení, které jsou certifikované a optimalizované pro provoz v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Můžete nasadit modul IoT Edge z Azure Marketplace a z vašeho IoT Hubu.

### <a name="deploy-from-azure-marketplace"></a>Nasazení z Azure Marketplace

Projděte si moduly IoT Edge na trhu a když najdete ten, který chcete, můžete ho nasadit výběrem **možnosti Vytvořit** nebo **získat nyní**. Postupujte podle kroků průvodce nasazením, které se mohou lišit v závislosti na vybraném modulu IoT Edge:

1. Potvrďte podmínky používání poskytovatele a zásady ochrany osobních údajů výběrem **možnosti Pokračovat**. Možná budete muset nejprve poskytnout kontaktní informace.
1. Zvolte předplatné a službu IoT Hub, ke kterému je cílové zařízení připojeno.
1. Zvolte **Nasadit do zařízení**.
1. Zadejte název zařízení nebo vyberte **Najít zařízení** a procházejte mezi zařízeními registrovanými v rozbočovači.
1. Vyberte **Vytvořit,** chcete-li pokračovat ve standardním procesu konfigurace manifestu nasazení, včetně přidání dalších modulů v případě potřeby. Podrobnosti pro nový modul, jako je například identifikátor URI obrázku, vytvoření voleb a požadované vlastnosti jsou předdefinovány, ale lze je změnit.

Ověřte, že se modul nasadí ve vašem centru IoT Hub na webu Azure Portal. Vyberte zařízení, vyberte **Nastavit moduly** a modul má být uveden v části **Moduly okrajů IoT.**

### <a name="deploy-from-azure-iot-hub"></a>Nasazení z Azure IoT Hub

Modul z Azure Marketplace můžete rychle nasadit do svého zařízení ve svém Centru IoT hub na webu Azure Portal.

1. Na webu Azure Portal přejděte do služby IoT Hub.
1. V levém podokně vyberte v části **Automatická správa zařízení** **položku IoT Edge**.
1. Vyberte zařízení IoT Edge, které má přijmout nasazení.
1. Na horním panelu vyberte **Nastavit moduly**.
1. V části **Moduly IoT Edge** klikněte na **Přidat**a v rozevírací nabídce vyberte **Modul Marketplace.**

![Přidání modulu v centru IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Zvolte modul ze stránky **Webu Marketplace modulu IoT Edge.** Vybraný modul se automaticky nakonfiguruje pro vaše předplatné, skupinu prostředků a zařízení. Pak se zobrazí v seznamu modulů IoT Edge. Některé moduly mohou vyžadovat další konfiguraci.

> [!TIP]
> Informace o modulech IoT Edge z Azure IoT Hub je omezená. Nejdřív se můžete dozvědět víc o [modulech IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) na Azure Marketplace.

Vyberte **další: Trasy** a pokračovat v nasazení, jak je popsáno [v určení trasy](#specify-routes) a [zkontrolujte nasazení](#review-deployment) dříve v tomto článku.

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
