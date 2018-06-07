---
title: Správa služeb Device Provisioning pomocí Azure CLI 2.0 a rozšíření IoT | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI 2.0 a rozšíření IoT spravovat služby Device Provisioning.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 174f8447b17d1fa580472cbb45d0a72f41c793c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628313"
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Správa služeb Device Provisioning pomocí Azure CLI 2.0 a rozšíření IoT

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy určený ke správě prostředků Azure, jako je služba IoT Edge. Azure CLI 2.0 je k dispozici v systémech Windows, Linux a MacOS. Azure CLI 2.0 bez dalších úprav umožňuje spravovat prostředky služby Azure IoT Hub, instance služby Device Provisioning a propojená centra.

Rozšíření IoT vylepšuje Azure CLI 2.0 o funkce, jako jsou správa zařízení a úplné možnosti služby IoT Edge.

V tomto kurzu nejprve dokončíte kroky k instalaci Azure CLI 2.0 a rozšíření IoT. Pak se naučíte spouštět příkazy rozhraní příkazového řádku pro provádění základních operací služby Device Provisioning. 

## <a name="installation"></a>Instalace 

### <a name="step-1---install-python"></a>Krok 1 – Instalace Pythonu

Vyžaduje se [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Krok 2 – Instalace Azure CLI 2.0

Nainstalujte ve svém prostředí Azure CLI 2.0 podle [pokynů k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. Ve Windows můžete instalaci jednoduše provést stažením a instalací [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Krok 3 – Instalace rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-cli-iot-ext`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-cli-iot-ext` zobrazit podrobnosti o rozšíření IoT. K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Základní operace služby Device Provisioning
Tento příklad ukazuje, jak pomocí příkazů rozhraní příkazového řádku provést přihlášení k účtu Azure, vytvoření skupiny prostředků Azure (kontejner obsahující související prostředky pro řešení Azure), vytvoření centra IoT, vytvoření služby Device Provisioning, výpis existujících služeb Device Provisioning a vytvoření propojeného centra IoT. 

Než začnete, dokončete výše popsané kroky instalace. Pokud ještě nemáte účet Azure, můžete si ještě dnes [vytvořit bezplatný účet](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Přihlášení k účtu Azure
  
    az login

![přihlášení][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Vytvoření skupiny prostředků IoTHubBlogDemo v umístění eastus

    az group create -l eastus -n IoTHubBlogDemo

![Vytvoření skupiny prostředků][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Vytvoření dvou služeb Device Provisioning

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Vytvoření služeb DPS][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Výpis všech existujících služeb Device Provisioning v rámci této skupiny prostředků

    az iot dps list --resource-group IoTHubBlogDemo

![Výpis služeb DPS][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Vytvoření IoT Hubu blogDemoHub v rámci nově vytvořené skupiny prostředků

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Vytvoření IoT Hubu][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Propojení existujícího IoT Hubu se službou Device Provisioning

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Propojení centra][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Další postup
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

V dalším kurzu se dozvíte, jak zřídit několik zařízení napříč centry s vyrovnáváním zatížení. 

> [!div class="nextstepaction"]
> [Zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení](./tutorial-provision-multiple-hubs.md)
