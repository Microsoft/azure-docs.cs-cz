---
title: Ze služby Azure Machine Learning nasazovat modely do zařízení IoT Edge | Dokumentace Microsoftu
description: Informace o nasazení modelu ze služby Azure Machine Learning pro zařízení Azure IoT Edge. Nasazení modelu do hraničního zařízení umožňuje skóre data na zařízení místo odesláním do cloudu a čekání na odpověď.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 66370aec76044454ab4f11eb432fe2e9b0cdb9cf
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248581"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Příprava na nasazení modelů v prostředí IoT Edge

V tomto dokumentu zjistěte, jak pomocí služby Azure Machine Learning můžete připravit pro trénovaný model nasazení tak, aby zařízení Azure IoT Edge.

Zařízení Azure IoT Edge je na Linuxu nebo zařízení se systémem Windows, který spustí modul runtime Azure IoT Edge. Modely strojového učení je možné nasadit do těchto zařízení jako moduly IoT Edge. Nasazení modelu do zařízení IoT Edge umožňuje zařízení využívat model přímo, namísto toho, aby k zasílání dat do cloudu pro zpracování. Získáte rychlejší reakční dobu a menší datové přenosy.

Před nasazením modelu do hraničního zařízení, postupujte podle kroků v tomto dokumentu Příprava modelu a zařízení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Pracovní prostor služby Azure Machine Learning. Pokud chcete jeden vytvořit, postupujte podle kroků v [Začínáme se službou Azure Machine Learning](quickstart-get-started.md) dokumentu.

* Vývojové prostředí. Další informace najdete v tématu [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

* [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 

* Trénovaného modelu. Příklad toho, jak pro trénování modelu, najdete v článku [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu. Předem natrénovaných modelů je k dispozici na [AI Toolkit pro Azure IoT Edge na Githubu úložiště](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

## <a name="prepare-the-iot-device"></a>Připravte zařízení IoT

Informace o registraci vašeho zařízení a nainstalovat modul runtime IoT, postupujte podle kroků v [rychlý start: nasazení prvního modulu IoT Edge do zařízení Linux x64](../../iot-edge/quickstart-linux.md) dokumentu.

## <a name="register-the-model"></a>Zaregistrujte model

Moduly Azure IoT Edge jsou založeny na Image kontejneru. Jak model nasadit do zařízení IoT Edge, použijte následující kroky k registraci modelu na pracovní prostor služby Azure Machine Learning service a vytvoření image Dockeru. 

> [!IMPORTANT]
> Pokud použijete k natrénování modelu, který může zaregistrovat již ve vašem pracovním prostoru Azure Machine Learning, v tomto případě přeskočte krok 3.

1. Inicializovat pracovní prostor a načtení souboru config.json:

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. Zaregistrujte model do pracovního prostoru. Nahraďte text výchozí cestu model, název, značky a popis:

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Načtěte registrovaného modelu: 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Vytvoření image Dockeru

1. Vytvoření **vyhodnocování skript** s názvem `score.py`. Tento soubor se používá ke spuštění modelu v bitové kopii. Musí zahrnovat následující funkce:

    * `init()` Funkce, která obvykle načte do globálního objektu modelu. Tato funkce se spustí pouze jednou při spuštění kontejneru Dockeru. 

    * `run(input_data)` Funkce používá model k predikci hodnoty založené na vstupní data. Vstupy a výstupy spustit obvykle používají JSON pro serializaci a rušení serializace, ale ostatní formáty jsou podporovány.

    Příklad najdete v tématu [kurz klasifikace obrázků](tutorial-deploy-models-with-aml.md#make-script).

1. Vytvoření **souborem prostředí** s názvem `myenv.yml`. Tento soubor je specifikace prostředí Conda a jsou uvedeny všechny závislosti vyžadované skript a modelu. Příklad najdete v tématu [kurz klasifikace obrázků](tutorial-deploy-models-with-aml.md#make-myenv).

1. Nakonfigurujte pomocí image Dockeru `score.py` a `myenv.yml` soubory:
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Vytvoření image pomocí konfigurace modelu a bitové kopie:

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    Vytvoření image trvá přibližně 5 minut.

## <a name="get-the-container-registry-credentials"></a>Získání přihlašovacích údajů registru kontejneru

Azure IoT potřebuje přihlašovací údaje pro registr kontejneru, který ukládá Image dockeru ve službě Azure Machine Learning. Použijte následující postup k získání přihlašovacích údajů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/signin/index).

1. Přejděte do pracovního prostoru služby Azure Machine Learning a vyberte __přehled__. Chcete-li přejít do nastavení registru kontejneru, vyberte __registru__ odkaz.

    ![Obrázek položky registru kontejneru](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. Jednou v registru kontejneru, vyberte **přístupové klíče** a pak povolte uživatele s rolí správce.

    ![Snímek obrazovky klíčů přístup](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Uložte příslušné hodnoty pro přihlášení na server, uživatelské jméno a heslo. 

   Tyto přihlašovací údaje jsou nezbytné k zajištění přístupu zařízení IoT edge k obrázkům v váš privátní registr kontejnerů.

## <a name="next-steps"></a>Další postup

Dokončení přípravy pro nasazení. Nyní, postupujte podle kroků v [moduly nasazení Azure IoT Edge z portálu Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) dokumentu k nasazení do hraničních zařízení. Při konfiguraci __nastavení registru__ pro zařízení, použijte přihlašovací údaje, které jste nakonfigurovali dříve.
