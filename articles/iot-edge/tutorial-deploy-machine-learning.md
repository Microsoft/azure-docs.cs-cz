---
title: 'Tutorial: Deploy Azure Machine Learning to a device - Azure IoT Edge'
description: In this tutorial, you create an Azure Machine Learning model, then deploy it as a module to an edge device
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f1bd4ce5b701652318679f3277bc7c9109fa529
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457637"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Kurz: Nasazení Azure Machine Learning jako modulu IoT Edge (Preview)

Use Azure Notebooks to develop a machine learning module and deploy it to a Linux device running Azure IoT Edge. 

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provede nasazením modulu Azure Machine Learning, který předpovídá, kdy zařízení selže, na základě simulovaných dat teploty počítače. For more information about Azure Machine Learning on IoT Edge, see [Azure Machine Learning documentation](../machine-learning/service/how-to-deploy-to-iot.md).

Modul Azure Machine Learning, který vytvoříte v tomto kurzu, přečte data o prostředí vygenerovaná zařízením a označí zprávy jako standardní nebo neobvyklé.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit modul Azure Machine Learning
> * Doručit kontejner modulu do služby Azure Container Registry
> * Nasadit modul Azure Machine Learning na zařízení IoT Edge
> * Zobrazení vygenerovaných dat

>[!NOTE]
>Moduly Azure Machine Learning na Azure IoT Edge jsou ve verzi Public Preview.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Předpoklady

Zařízení Azure IoT Edge:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md).
* The Azure Machine Learning module doesn't support Windows containers.
* The Azure Machine Learning module doesn't support ARM processors.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Pracovní prostor služby Azure Machine Learning. Follow the instructions in [Use the Azure portal to get started with Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) to create one and learn how to use it.
   * Make a note of the workspace name, resource group, and subscription ID. These values are all available on the workspace overview in the Azure portal. You'll use these values later in the tutorial to connect an Azure notebook to your workspace resources. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Create and deploy Azure Machine Learning module

In this section, you convert trained machine learning model files and into an Azure Machine Learning container. Veškeré komponenty potřebné pro image Dockeru najdete v [úložišti Git AI Toolkit pro Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Follow these steps to upload that repository into Microsoft Azure Notebooks to create the container and push it to Azure Container Registry.


1. Navigate to your Azure Notebooks projects. You can get there from your Azure Machine Learning workspace in the [Azure portal](https://portal.azure.com) or by signing in to [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) with your Azure account.

2. Select **Upload GitHub Repo**.

3. Provide the following GitHub repository name: `Azure/ai-toolkit-iot-edge`. Uncheck the **Public** box if you want to keep your project private. Select **Import**. 

4. Once the import is finished, navigate into the new **ai-toolkit-iot-edge** project and open the **IoT Edge anomaly detection tutorial** folder. 

5. Verify that your project is running. If not, select **Run on Free Compute**.

   ![Run on free compute](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Open the **aml_config/config.json** file.

7. Edit the config file to include the values for your Azure subscription ID, a resource group in your subscription, and your Azure Machine Learning workspace name. You can get all these values from the **Overview** section of your workspace in Azure. 

8. Save the config file.

9. Open the **00-anomaly-detection-tutorial.ipynb** file.

10. When prompted, select the **Python 3.6** kernel then select **Set Kernel**.

11. Edit the first cell in the notebook according to the instructions in the comments. Use the same resource group, subscription ID, and workspace name that you added to the config file.

12. Run the cells in the notebook by selecting them and selecting **Run** or pressing `Shift + Enter`.

    >[!TIP]
    >Some of the cells in the anomaly detection tutorial notebook are optional, because they create resources that some users may or may not have yet, like an IoT Hub. If you put your existing resource information in the first cell, you'll receive errors if you run the cells that create new resources because Azure won't create duplicate resources. This is fine, and you can ignore the errors or skip those optional sections entirely. 

By completing all the steps in the notebook, you trained an anomaly detection model, built it as a Docker container image, and pushed that image to Azure Container Registry. Then, you tested the model and finally deployed it to your IoT Edge device. 

## <a name="view-container-repository"></a>View container repository

Check that your container image was successfully created and stored in the Azure container registry associated with your machine learning environment. The notebook that you used in the previous section automatically provided the container image and the registry credentials to your IoT Edge device, but you should know where they're stored so that you can find the information yourself later. 

1. In the [Azure portal](https://portal.azure.com), navigate to your Machine Learning service workspace. 

2. The **Overview** section lists the workspace details as well its associated resources. Select the **Registry** value, which should be your workspace name followed by random numbers. 

3. In the container registry, select **Repositories**. You should see a repository called **tempanomalydetection** that was created by the notebook you ran in the earlier section. 

4. Select **tempanomalydetection**. You should see that the repository has one tag: **1**. 

   Now that you know the registry name, repository name, and tag, you know the full image path of the container. Image paths look like **\<registry_name\>.azurecr.io/tempanomalydetection:1**. You can use the image path to deploy this container to IoT Edge devices. 

5. In the container registry, select **Access keys**. You should see a number of access credentials, including **Login server** and the **Username**, and **Password** for an admin user.

   These credentials can be included in the deployment manifest to give your IoT Edge device access to pull container images from the registry. 

Now you know where the Machine Learning container image is stored. The next section walks through steps to view the container running as a module on your IoT Edge device. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Můžete zobrazit zprávy generované každým modulem IoT Edge a také zprávy, které se doručují do vašeho IoT Hubu.

### <a name="view-data-on-your-iot-edge-device"></a>Zobrazení dat na zařízení IoT Edge

Na zařízení IoT Edge můžete zobrazit zprávy odesílané z každého jednotlivého modulu.

You may need to use `sudo` for elevated permissions to run `iotedge` commands. Signing out and signing back in to your device automatically updates your permissions.

1. Takto zobrazíte všechny moduly na zařízení IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Takto zobrazíte zprávy odesílané z konkrétního zařízení. Použijte název modulu z výstupu předchozího příkazu.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Zobrazení dat odesílaných do IoT Hubu

You can view the device-to-cloud messages that your IoT hub receives by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

Následující kroky ukazují, jak nastavit Visual Studio Code k monitorování zpráv zařízení-cloud, které přicházejí do vašeho IoT Hubu.

1. V editoru Visual Studio Code vyberte **IoT Hub Devices** (zařízení IoT Hubu).

2. Vyberte **...** a potom v nabídce vyberte **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu).

   ![Set IoT Hub Connection String](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Do textového pole, které se otevře nahoře na stránce, zadejte připojovací řetězec iothubowner svého IoT Hubu. Vaše zařízení IoT Edge by se mělo zobrazit v seznamu zařízení IoT Hubu.

4. Select **...** again then select **Start Monitoring Built-in Event Endpoint**.

5. Sledujte zprávy, které přicházejí každých pět sekund ze senzoru tempSenzor. The message body contains a property called **anomaly**, which the machinelearningmodule provides with a true or false value. Pokud bylo spuštění modelu úspěšné, obsahuje vlastnost **AzureMLResponse** hodnotu „OK“.

   ![Azure Machine Learning response in message body](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili modul IoT Edge, který používá technologii Azure Machine Learning. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Klasifikace obrázků s využitím služby Custom Vision](tutorial-deploy-custom-vision.md)

