---
title: Nasazení modelu Machine Learning pro Azure Functions s využitím Azure cache pro Redis
description: V tomto článku nasadíte model z Azure Machine Learning jako aplikaci funkcí v Azure Functions pomocí mezipaměti Azure pro instanci Redis. Mezipaměť Azure pro Redis je mimořádně výkonná a škálovatelná – při párování s Azure Machine Learning modelem získáte nízkou latenci a vysokou propustnost ve vaší aplikaci.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499692"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Nasazení modelu Machine Learning pro Azure Functions s využitím Azure cache pro Redis 

V tomto článku nasadíte model z Azure Machine Learning jako aplikaci funkcí v Azure Functions pomocí mezipaměti Azure pro instanci Redis.  

Mezipaměť Azure pro Redis je mimořádně výkonná a škálovatelná – při párování s Azure Machine Learning modelem získáte nízkou latenci a vysokou propustnost ve vaší aplikaci. Několik scénářů, kde je mezipaměť obzvláště výhodná, je při inferencingí dat a pro vlastní výsledky odvození modelu. V obou případech jsou metadata nebo výsledky uložené v paměti, což vede ke zvýšení výkonu. 

> [!NOTE]
> I když jsou všeobecně k dispozici Azure Machine Learning i Azure Functions, možnost zabalit model ze služby Machine Learning for Functions je ve verzi Preview.  
>

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](../machine-learning/how-to-manage-workspace.md) .
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)
* Vyškolený model strojového učení zaregistrovaný ve vašem pracovním prostoru. Pokud model nemáte, použijte [kurz k klasifikaci imagí: výukový model](../machine-learning/tutorial-train-models-with-aml.md) pro výuku a registraci k jednomu.

> [!IMPORTANT]
> Fragmenty kódu v tomto článku předpokládají, že jste nastavili následující proměnné:
>
> * `ws` – Váš pracovní prostor Azure Machine Learning.
> * `model` – Registrovaný model, který se nasadí.
> * `inference_config` – Odvození konfigurace pro model.
>
> Další informace o nastavení těchto proměnných najdete v tématu [nasazení modelů pomocí Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření mezipaměti Azure pro instanci Redis 
Budete moct nasadit model strojového učení, abyste Azure Functions s instancí mezipaměti Basic, Standard nebo Premium. Chcete-li vytvořit instanci mezipaměti, postupujte podle těchto kroků.  

1. Přejděte na domovskou stránku Azure Portal nebo otevřete nabídku postranní panel a pak vyberte **vytvořit prostředek**. 
   
1. Na stránce **Nový** vyberte **databáze** a pak vyberte **Azure cache pro Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Vyberte mezipaměť Azure pro Redis.":::
   
1. Na stránce **nový Redis Cache** nakonfigurujte nastavení pro novou mezipaměť.
   
   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude *\<DNS name> . Redis.cache.Windows.NET*. | 
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Umístění** | Rozevírací seznam a vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši mezipaměť. |
   | **Cenová úroveň** | Rozevírací seznam a vyberte [cenovou úroveň](https://azure.microsoft.com/pricing/details/cache/). |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |

1. Vyberte kartu **síť** nebo klikněte na tlačítko **sítě** v dolní části stránky.

1. Na kartě **sítě** vyberte metodu připojení.

1. Vyberte kartu **Další: Upřesnit** nebo klikněte na tlačítko **Další: Upřesnit** v dolní části stránky.

1. Na kartě **Upřesnit** pro instanci mezipaměti Basic nebo Standard vyberte přepínač Povolit, pokud chcete povolit port bez TLS.

1. Na kartě **Upřesnit** pro instanci mezipaměti úrovně Premium nakonfigurujte nastavení pro port bez TLS, clusteringu a trvalost dat.

1. Vyberte kartu **Další: značky** nebo klikněte na tlačítko **Další: značky** v dolní části stránky.

1. Volitelně můžete na kartě **značky** zadat název a hodnotu, pokud chcete prostředek zařadit do kategorií. 

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na kartu Revize + vytvořit, kde Azure ověřuje vaši konfiguraci.

1. Po zobrazení zprávy se zobrazeným zeleným ověřením vyberte **vytvořit**.

Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití. 

## <a name="prepare-for-deployment"></a>Příprava nasazení

Před nasazením musíte definovat, co je potřeba ke spuštění modelu jako webové služby. Následující seznam popisuje základní položky potřebné pro nasazení:

* __Vstupní skript__. Tento skript přijímá požadavky, vyhodnotí požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď předtím, než ji vrátí klientovi.
    >
    > Ve výchozím nastavení je při balení pro funkce vstup považován za text. Pokud vás zajímá využívání nezpracovaných bajtů vstupu (například pro triggery objektů BLOB), měli byste použít [AMLRequest k přijetí nezpracovaných dat](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

Pro funkci run se ujistěte, že se připojí ke koncovému bodu Redis.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Další informace o vstupním skriptu najdete v tématu [Definování kódu bodování.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

* **Závislosti**, například pomocné skripty nebo balíčky python/conda potřebné ke spuštění skriptu vstupu nebo modelu

Tyto entity jsou zapouzdřeny do __Konfigurace odvození__. Odvozená konfigurace odkazuje na vstupní skript a další závislosti.

> [!IMPORTANT]
> Při vytváření odvozených konfigurací pro použití s Azure Functions je nutné použít objekt [prostředí](/python/api/azureml-core/azureml.core.environment%28class%29) . Počítejte s tím, že pokud definujete vlastní prostředí, musíte přidat AzureML-Defaults s Version >= 1.0.45 jako závislost v PIP. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby. Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s odvozenou konfigurací:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](../machine-learning/how-to-use-environments.md).

Další informace o konfiguraci odvození najdete v tématu [nasazení modelů pomocí Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> Při nasazování do funkcí není nutné vytvářet __konfiguraci nasazení__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalace balíčku sady SDK Preview pro podporu funkcí

Chcete-li vytvořit balíčky pro Azure Functions, je nutné nainstalovat balíček sady SDK verze Preview.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit bitovou kopii Docker, která je nasazena do Azure Functions, použijte funkci [AzureML. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions) nebo konkrétního balíčku pro aktivační událost, které vás zajímá. Následující fragment kódu ukazuje, jak vytvořit nový balíček s triggerem HTTP z modelu a odvozené konfigurace:

> [!NOTE]
> Fragment kódu předpokládá, že `model` obsahuje registrovaný model a `inference_config` obsahuje konfiguraci pro odvození prostředí. Další informace najdete v tématu [nasazení modelů pomocí Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

`show_output=True`V případě je zobrazen výstup procesu Docker Build. Po dokončení procesu se image vytvoří v Azure Container Registry pro váš pracovní prostor. Po sestavení obrázku se zobrazí umístění v Azure Container Registry. Navrácené umístění má formát `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Balení pro funkce v současné době podporuje triggery protokolu HTTP, triggery objektů BLOB a triggery služby Service Bus. Další informace o aktivačních událostech najdete v tématu [Azure Functions Bindings](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Uložte informace o umístění, jak se používá při nasazování bitové kopie.

## <a name="deploy-image-as-a-web-app"></a>Nasazení image jako webové aplikace

1. K získání přihlašovacích údajů pro Azure Container Registry, které obsahují obrázek, použijte následující příkaz. Nahraďte `<myacr>` hodnotou, kterou jste dříve vrátili `package.location` : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Výstup tohoto příkazu je podobný následujícímu dokumentu JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Uložte hodnotu pro __uživatelské jméno__ a jedno z __hesel__.

1. Pokud ještě nemáte skupinu prostředků nebo plán služby App Service pro nasazení služby, následující příkazy ukazují, jak vytvořit obojí:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    V tomto příkladu se používá cenová úroveň _Basic pro Linux_ ( `--sku B1` ).

    > [!IMPORTANT]
    > Image vytvořené Azure Machine Learning používají Linux, takže musíte použít `--is-linux` parametr.

1. Vytvořte účet úložiště, který se použije pro úložiště webové úlohy, a získejte připojovací řetězec. Nahraďte `<webjobStorage>` názvem, který chcete použít.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. K vytvoření aplikace Function App použijte následující příkaz. Nahraďte `<app-name>` názvem, který chcete použít. `<acrinstance>`Hodnoty a nahraďte `<imagename>` hodnotami vrácenými `package.location` dříve. Nahraďte `<webjobStorage>` názvem účtu úložiště z předchozího kroku:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > V tuto chvíli se vytvořila aplikace Function App. Vzhledem k tomu, že jste nezadali připojovací řetězec pro aktivační událost HTTP nebo přihlašovací údaje k Azure Container Registry, která obsahuje obrázek, aplikace Function app není aktivní. V dalších krocích zadejte připojovací řetězec a ověřovací informace pro registr kontejneru. 

1. K poskytnutí aplikace Function App s přihlašovacími údaji potřebnými pro přístup k registru kontejneru použijte následující příkaz. Nahraďte `<app-name>` názvem aplikace Function App. `<acrinstance>`Hodnoty a nahraďte `<imagetag>` pomocí volání AZ CLI v předchozím kroku. Nahraďte `<username>` a `<password>` informacemi o přihlášení ACR získanými dříve:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Tento příkaz vrátí informace podobné následujícímu dokumentu JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

V tuto chvíli začne aplikace Function App načítat obrázek.

> [!IMPORTANT]
> Než se image načte, může to trvat několik minut. Průběh můžete sledovat pomocí Azure Portal.

## <a name="test-azure-functions-http-trigger"></a>Aktivační událost testovacího Azure Functions HTTP 

Nyní spustíme a otestujeme Azure Functions triggeru HTTP.

1. V Azure Portal přejdete do aplikace Function App.
1. V části vývojář vyberte **kód + test**. 
1. Na pravé straně vyberte kartu **vstup** . 
1. Kliknutím na tlačítko **Spustit** otestujete Azure FUNCTIONS Trigger http. 

Nyní jste úspěšně nasadili model z Azure Machine Learning jako aplikaci funkcí pomocí mezipaměti Azure pro instanci Redis. Další informace o službě Azure cache pro Redis najdete v odkazech v níže uvedené části.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste hotovi s rychlým startem, můžete odstranit prostředky Azure, které jste vytvořili v rámci tohoto rychlého startu, abyste předešli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Při odstranění skupiny prostředků se všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.

### <a name="to-delete-a-resource-group"></a>Odstranění skupiny prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Do pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. U skupiny prostředků ve výsledcích hledání vyberte **...** a pak vyberte **Odstranit skupinu prostředků**.

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a vyberte **Odstranit**.

Po chvíli se skupina prostředků včetně všech prostředků, které obsahuje, odstraní.

## <a name="next-steps"></a>Další kroky 

* Další informace o [službě Azure cache pro Redis](./cache-overview.md)
* Naučte se konfigurovat aplikaci Function App v dokumentaci k [funkcím](../azure-functions/functions-create-function-linux-custom-image.md) .
* [Referenční informace k rozhraním API](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* Vytvoření [aplikace v Pythonu, která používá Azure cache pro Redis](./cache-python-get-started.md)
