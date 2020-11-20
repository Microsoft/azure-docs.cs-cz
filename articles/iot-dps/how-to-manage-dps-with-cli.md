---
title: Správa IoT Hub Device Provisioning Service pomocí Azure CLI & rozšíření IoT
description: Naučte se používat rozhraní příkazového řádku Azure a rozšíření IoT ke správě IoT Hub Device Provisioning Service (DPS).
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: 020b0dbc937885923b26c115f91147437b7a0f9b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964724"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Jak používat rozhraní příkazového řádku Azure a rozšíření IoT ke správě IoT Hub Device Provisioning Service

[Azure CLI](/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Edge. Rozhraní příkazového řádku Azure je dostupné v systémech Windows, Linux a macOS. Azure CLI umožňuje spravovat prostředky Azure IoT Hub, instance služby Device Provisioning a propojená centra.

Rozšíření IoT rozšiřuje rozhraní příkazového řádku Azure pomocí funkcí, jako je Správa zařízení a plná IoT Edge funkce.

V tomto kurzu nejprve dokončíte kroky pro nastavení rozhraní příkazového řádku Azure a rozšíření IoT. Pak se dozvíte, jak spouštět příkazy rozhraní příkazového řádku pro provádění základních operací služby Device Provisioning. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Instalace 

### <a name="install-python"></a>Instalace Pythonu

Vyžaduje se [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/).

### <a name="install-the-azure-cli"></a>Instalace Azure CLI

Při instalaci rozhraní příkazového řádku Azure v prostředí postupujte podle pokynů k [instalaci](/cli/azure/install-azure-cli?view=azure-cli-latest) . Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az –version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. Ve Windows můžete instalaci jednoduše provést stažením a instalací [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Nainstalovat rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-iot` zobrazit podrobnosti o rozšíření IoT. K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Základní operace služby Device Provisioning

V příkladu se dozvíte, jak se přihlásit ke svému účtu Azure, vytvořit skupinu prostředků Azure (kontejner, který obsahuje související prostředky pro řešení Azure), vytvořit IoT Hub, vytvořit službu Device Provisioning, zobrazit seznam existujících služeb Device Provisioning a vytvořit propojené centrum IoT pomocí příkazů rozhraní příkazového řádku. 

Než začnete, dokončete výše popsané kroky instalace. Pokud ještě nemáte účet Azure, můžete si ještě dnes [vytvořit bezplatný účet](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Přihlaste se k účtu Azure.
  
```azurecli
az login
```

![Snímek obrazovky zobrazující okno příkazového řádku, které spouští příkaz AZ Login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Vytvoření skupiny prostředků IoTHubBlogDemo v eastus

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Vytvoření skupiny prostředků](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. vytvoření dvou služeb Device Provisioning

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Vytvořit službu Device Provisioning Service](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. vypíše všechny existující služby Device Provisioning v rámci této skupiny prostředků.

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Vypsat službu Device Provisioning Services](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. vytvoření IoT Hub blogDemoHub v rámci nově vytvořené skupiny prostředků

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![Vytvoření IoT Hubu](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Propojte jeden existující IoT Hub se službou Device Provisioning Service

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

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