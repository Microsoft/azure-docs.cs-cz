---
title: Správa služby zřizování zařízení služby IoT Hub pomocí rozšíření Azure CLI & IoT
description: Zjistěte, jak používat rozhraní příkazového nastavení Azure a rozšíření IoT ke správě služby DPS (Device Provisioning Service) služby IoT Hub.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674519"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Jak používat Azure CLI a rozšíření IoT ke správě služby zřizování zařízení služby IoT Hub

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je nástroj příkazového řádku s otevřeným zdrojovým kódem pro různé platformy pro správu prostředků Azure, jako je IoT Edge. Rozhraní příkazového příkazu Azure je dostupné ve Windows, Linuxu a MacOS. Azure CLI umožňuje spravovat prostředky Azure IoT Hub, instance služby zřizování zařízení a propojené rozbočovače po vybalení.

Rozšíření IoT obohacuje rozhraní příkazového odpovky Azure o funkce, jako je správa zařízení a úplná funkce IoT Edge.

V tomto kurzu nejprve dokončíte kroky k nastavení Azure CLI a rozšíření IoT. Pak se dozvíte, jak spustit příkazy příkazového příkazu k provedení základních operací služby Device Provisioning Service. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Instalace 

### <a name="install-python"></a>Instalace Pythonu

Vyžaduje se [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/).

### <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Podle [pokynů k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nastavte azure cli ve vašem prostředí. Minimálně vaše verze Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az –version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. Ve Windows můžete instalaci jednoduše provést stažením a instalací [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Instalace rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-iot` zobrazit podrobnosti o rozšíření IoT. K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Operace služby Základní zřizování zařízení

Příklad ukazuje, jak se přihlásit ke svému účtu Azure, vytvořit skupinu prostředků Azure (kontejner, který obsahuje související prostředky pro řešení Azure), vytvořit službu IoT Hub, vytvořit službu zřizování zařízení, uvést seznam stávajících služeb zřizování zařízení a vytvořte propojené centrum IoT s příkazy příkazu CLI. 

Než začnete, dokončete výše popsané kroky instalace. Pokud ještě nemáte účet Azure, můžete si ještě dnes [vytvořit bezplatný účet](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Přihlášení k účtu Azure
  
    az login

![přihlášení](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Vytvořte skupinu prostředků IoTHubBlogDemo v eastus

    az group create -l eastus -n IoTHubBlogDemo

![Vytvoření skupiny prostředků](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Vytvoření dvou služeb zřizování zařízení

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Vytvořit službu zřizování zařízení](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Seznam všech existujících služeb zřizování zařízení v rámci této skupiny prostředků

    az iot dps list --resource-group IoTHubBlogDemo

![Seznam služeb zřizování zařízení](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Vytvoření blogdemohubu ioT hubu v rámci nově vytvořené skupiny prostředků

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Vytvoření IoT Hubu](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Propojení jednoho existujícího centra IoT Hub se službou zřizování zařízení

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Propojení centra](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

V dalším kurzu se dozvíte, jak zřídit několik zařízení napříč centry s vyrovnáváním zatížení. 

> [!div class="nextstepaction"]
> [Zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení](./tutorial-provision-multiple-hubs.md)
