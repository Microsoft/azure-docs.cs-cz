---
title: Izolace sítě & ochraně osobních údajů
titleSuffix: Azure Machine Learning
description: Použijte izolovaný Virtual Network Azure s Azure Machine Learning k zabezpečení experimentů a školení a také k odvození a bodování úloh.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121200"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Izolace sítě během školení a odvození s privátními koncovými body a virtuálními sítěmi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zabezpečit životní cyklus strojového učení tím, že izolujete Azure Machine Learning školení a odvozování úloh v rámci Azure Virtual Network (VNET). __Virtuální síť__ funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. Virtuální síť Azure se taky můžete připojit k místní síti. Připojením sítí můžete bezpečně prosazovat modely a přistupovat k nasazeným modelům pro odvození.

K pracovnímu prostoru Azure Machine Learning lze přistupovat z virtuální sítě pomocí __privátního koncového bodu__. Soukromý koncový bod je sada privátních IP adres ve vaší virtuální síti a přístup k vašemu pracovnímu prostoru je omezený na virtuální síť. Soukromý koncový bod pomáhá snižovat riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](/azure/private-link/private-link-overview) .

> [!NOTE]
> Při použití Mozilla Firefox můžete narazit na problémy s přístupem k pracovnímu prostoru prostřednictvím privátního koncového bodu. Problém se může vztahovat k nastavení DNS přes HTTPS v prohlížeči. K vyřešení tohoto problému doporučujeme použít Microsoft Edge nebo Google Chrome.

Azure Machine Learning spoléhá na další služby Azure pro úložiště dat a výpočetní prostředky (používá se ke školení a nasazení modelů). Tyto prostředky je také možné vytvořit v rámci virtuální sítě. Můžete například využít Azure Machine Learning COMPUTE pro výuku modelu a pak model nasadit do služby Azure Kubernetes Service (AKS). 

## <a name="prerequisites"></a>Požadavky

+ [Pracovní prostor](how-to-manage-workspace.md)Azure Machine Learning.

+ Obecné praktické znalosti [služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a [sítě IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Již existující virtuální síť a podsíť, která se má použít s výpočetními prostředky.

+ Pokud chcete nasadit prostředky do virtuální sítě nebo podsítě, musí mít váš uživatelský účet oprávnění k následujícím akcím v řízení přístupu na základě role (RBAC) v Azure.

    - "Microsoft. Network/virtualNetworks/JOIN/Action" na prostředku virtuální sítě.
    - "Microsoft. Network/virtualNetworks/podsíť/JOIN/Action" na prostředku podsítě.

    Další informace o RBAC se sítí najdete v tématu [předdefinované role sítě](/azure/role-based-access-control/built-in-roles#networking) .

## <a name="secure-your-workspace"></a>Zabezpečení pracovního prostoru

Pracovní prostor Azure Machine Learning může mít buď __veřejný koncový bod__ , nebo __privátní koncový bod__. Veřejný koncový bod je sada IP adres, které jsou přístupné na veřejném Internetu, zatímco privátní koncový bod je sada privátních IP adres v rámci virtuální sítě. 

Pokud chcete omezit přístup k vašemu pracovnímu prostoru jenom v případě privátních IP adres, použijte privátní koncový bod.

Vzhledem k tomu, že komunikace s pracovním prostorem je povolená jenom z virtuální sítě, musí být všechna vývojová prostředí, která používají pracovní prostory, členy virtuální sítě. Například virtuální počítač ve virtuální síti.

> [!IMPORTANT]
> Privátní koncový bod neovlivňuje plochu ovládacího prvku Azure (operace správy), jako je například odstranění pracovního prostoru nebo Správa výpočetních prostředků. Například vytvoření, aktualizace nebo odstranění cíle služby Compute. Tyto operace se provádějí na veřejném Internetu jako normální.
>
> Privátní koncový bod brání přístupu k pracovnímu prostoru mimo virtuální síť.

Některé kombinace prostředků s privátním koncovým bodem vyžadují pracovní prostor Enterprise Edition. Následující tabulka vám pomůže pochopit, jaké scénáře vyžaduje Enterprise Edition:

| Scénář | Enterprise</br>Edition | Základní</br>Edition |
| ----- |:-----:|:-----:| 
| Žádná virtuální síť ani privátní koncový bod | ✔ | ✔ |
| Pracovní prostor bez privátního koncového bodu. Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
| Pracovní prostor bez privátního koncového bodu. Další prostředky s privátním koncovým bodem | ✔ | |
| Pracovní prostor s privátním koncovým bodem Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
| Pracovní prostor a jakýkoliv jiný prostředek s privátním koncovým bodem | ✔ | |
| Pracovní prostor s privátním koncovým bodem Další prostředky bez privátního koncového bodu nebo virtuální sítě | ✔ | ✔ |
| Azure Container Registry ve virtuální síti | ✔ | |
| Klíče spravované zákazníkem pro pracovní prostor | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning výpočetní instance nejsou podporované v pracovním prostoru, kde je povolený privátní koncový bod.
>
> Azure Machine Learning nepodporuje použití služby Azure Kubernetes s povoleným privátním koncovým bodem. Místo toho můžete použít službu Azure Kubernetes ve virtuální síti. Další informace najdete v tématu [zabezpečení experimentů s Azure ml a odvozování úloh v rámci Azure Virtual Network](how-to-enable-virtual-network.md).

Další informace o privátních koncových bodech v Azure najdete v článku věnovaném [privátním odkazům Azure](/azure/private-link/private-link-overview) .

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Vytvoření pracovního prostoru, který používá privátní koncový bod

Můžete vytvořit nový pracovní prostor s privátním koncovým bodem pomocí Azure Machine Learning SDK, CLI, Azure Resource Manager šablony nebo Azure Portal.

__Požadavky__

* Virtuální síť, kterou používáte s privátním koncovým bodem, musí mít zakázané zásady sítě. Další informace najdete v tématu [zakázání zásad sítě pro soukromý koncový bod](/azure/private-link/disable-private-endpoint-network-policy).

__Omezení__

* Nemůžete se připojit k pracovnímu prostoru přes veřejný Internet, a to jenom v rámci virtuální sítě.

* Pokud se pomocí privátních koncových bodů vytvoří víc pracovních prostorů a používají stejnou privátní zónu DNS, přidá se do __odkazů virtuální sítě__ privátní zóny DNS jenom první pracovní prostor.

    Pokud chcete toto omezení obejít, přidejte ručně propojení virtuální sítě pro další pracovní prostory. Další informace najdete v tématu [co je připojení virtuální sítě](/azure/dns/private-dns-virtual-network-links).

__Konfigurace__

Informace o tom, jak vytvořit pracovní prostor pomocí virtuální sítě a privátního koncového bodu, spolu s dalšími možnostmi konfigurace, najdete v následujících článcích:

* [K vytvoření pracovního prostoru pro Azure Machine Learning použijte šablonu Azure Resource Manager](how-to-create-workspace-template.md).
* [Vytvářejte pracovní prostory na portálu](how-to-manage-workspace.md).
* [Vytvářejte pracovní prostory pomocí Azure CLI](how-to-manage-workspace-cli.md).
* Chcete-li použít sadu Python SDK, přečtěte si referenční dokumentaci k [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) a [pracovnímu prostoru. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) .

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Požadavky__

* Pro přístup k datům v účtu úložiště musí být účet úložiště ve stejné virtuální síti jako pracovní prostor.

* Pokud jsou vaše data uložená ve virtuální síti, musíte pomocí [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) v pracovním prostoru udělit přístup k vašim datům v studiu.

    > [!IMPORTANT]
    > I když většina studia funguje s daty uloženými ve virtuální síti, integrované poznámkové bloky __ne__. Integrované poznámkové bloky nepodporují používání úložiště, které je ve virtuální síti. Místo toho můžete použít poznámkové bloky Jupyter z výpočetní instance. Další informace najdete v části [výpočetní clustery & instance](#compute-instance) .

    Pokud přístup k nástroji Studio neudělíte, zobrazí se tato chyba `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` : tyto operace nebudou dostupné:

    * Náhled dat v studiu
    * Vizualizujte data v návrháři.
    * Odešlete experiment AutoML.
    * Spusťte Popis projektu.

__Omezení__

* Azure Machine Learning Studio podporuje čtení dat z následujících typů úložiště dat ve virtuální síti:

    * Azure Blob
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure SQL Database

__Konfigurace__

* __Nakonfigurujte úložiště dat, aby pro přístup k datům používala spravovanou identitu__ . Tyto kroky přidají spravovanou identitu pracovního prostoru jako __čtenáře__ do služby úložiště pomocí řízení přístupu na základě prostředků Azure (RBAC). Přístup __Čtenář__ umožňuje pracovnímu prostoru načíst nastavení brány firewall a zajistit, aby data nezůstala virtuální síť.

    1. V nástroji Studio vyberte __úložiště dat__.

    1. Pokud chcete vytvořit nové úložiště dat, vyberte __+ nové úložiště dat__. Pokud chcete aktualizovat existující, vyberte úložiště dat a vyberte __Aktualizovat přihlašovací údaje__.

    1. V nastavení úložiště dat vyberte __Ano__ , pokud __chcete, aby služba Azure Machine Learning měla přístup k úložišti pomocí identity spravované pracovním prostorem__.

    > [!NOTE]
    > Tyto změny mohou trvat až 10 minut, než se projeví.

* V případě __úložiště objektů BLOB v Azure__musí být identita spravovaná pracovním prostorem přidána také jako [čtecí modul objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , aby mohla číst data z úložiště objektů BLOB.

* Návrhář používá účet úložiště připojený k vašemu pracovnímu prostoru k uložení výstupu ve výchozím nastavení. Můžete ji však zadat pro uložení výstupu do libovolného úložiště dat, ke kterému máte přístup. Pokud vaše prostředí používá virtuální sítě, můžete pomocí těchto ovládacích prvků zajistit, aby data zůstala zabezpečená a přístupná. Nastavení nového výchozího úložiště pro kanál:

    1. V konceptu kanálu vyberte **ikonu ozubeného kolečka pro nastavení** vedle názvu vašeho kanálu.
    1. Vyberte položku **Vybrat výchozí úložiště dat** .
    1. Zadejte nové úložiště dat.

    Výchozí úložiště dat můžete také přepsat na základě jednotlivých modulů. Díky tomu budete mít kontrolu nad umístěním úložiště pro každý jednotlivý modul.

    1. Vyberte modul, jehož výstup chcete zadat.
    1. Rozbalte část **Nastavení výstupu** .
    1. Vyberte **přepsat výchozí nastavení výstupu**.
    1. Vyberte **nastavit výstupní nastavení**.
    1. Zadejte nové úložiště dat.

* Pokud používáte __Azure Data Lake Storage Gen2__, můžete k řízení přístupu k datům v rámci virtuální sítě použít jak RBAC, tak i POSIX – seznamy řízení přístupu (ACL) ve stylu POSIX.

    Pokud chcete použít RBAC, přidejte do role [čtečky dat objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) spravovanou identitu pracovního prostoru. Další informace najdete v tématu [řízení přístupu na základě role](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

    Aby bylo možné používat seznamy řízení přístupu (ACL), je možné přiřadit přístup stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [seznam řízení přístupu k souborům a adresářům](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

* __Azure Data Lake Storage Gen1__ podporuje jenom seznamy řízení přístupu ve stylu POSIX. Přístup ke spravovaným identitám pracovního prostoru můžete přiřadit k prostředkům stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

* Pokud chcete získat přístup k datům uloženým v __Azure SQL Database__ pomocí spravované identity, musíte vytvořit uživatele obsahující SQL, který se mapuje na spravovanou identitu. Další informace o tom, jak vytvořit uživatele z externího poskytovatele, najdete v tématu [Vytvoření obsažených uživatelů mapovaných na identity Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    Po vytvoření uživatele s omezením SQL udělte mu oprávnění pomocí [příkazu Grant T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Pokud __k studiu přistupujete z prostředku uvnitř virtuální sítě__ (například výpočetní instance nebo virtuální počítač), musíte z této virtuální sítě pro Studio dovolit odchozí přenosy. 

    Pokud například používáte skupiny zabezpečení sítě (NSG) k omezení odchozího provozu, přidejte pravidlo do cíle __značky služby__ __AzureFrontDoor. front-end__.

## <a name="use-datastores-and-datasets"></a>Použití datových úložišť a datových sad

> [!NOTE]
> Tato část se zabývá úložištěm dat a využitím datových sad pro prostředí SDK. Další informace o prostředí studia najdete v části [Machine Learning Studiu](#machine-learning-studio).

__Omezení__

Ve výchozím nastavení Azure Machine Learning provádí kontrolu platnosti dat a přihlašovacích údajů při pokusu o přístup k datům pomocí sady SDK. Pokud jsou vaše data za virtuální sítí, Azure Machine Learning nemůžou získat přístup k datům a jejich kontroly selžou. Pokud chcete tento problém obejít, vynechejte ověřování při vytváření úložišť dat a datových sad.

* Při použití __úložiště dat__:

    Azure Data Lake Store Gen1 a Azure Data Lake Store ve výchozím nastavení přeskočí ověřování, takže není potřeba žádná další akce. Pro následující služby však můžete použít podobnou syntaxi k přeskočení ověřování úložiště dat:

    - Azure Blob Storage
    - Sdílená složka Azure
    - PostgreSQL
    - Azure SQL Database

    Následující ukázka kódu vytvoří nové úložiště a sady dat objektů BLOB v Azure `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* Při použití __datové sady__:

    Syntaxe pro přeskočení ověřování DataSet je podobná pro následující typy datových sad:
    - Soubor s oddělovači
    - JSON 
    - Parquet
    - SQL
    - Soubor

    Následující kód vytvoří novou datovou sadu a sady JSON `validate=False` .

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Účet služby Azure Storage

> [!IMPORTANT]
> Do virtuální sítě můžete umístit _výchozí účet úložiště_ pro Azure Machine Learning a _jiné než výchozí účty úložiště_ .

__Požadavky__

* Účet úložiště musí být ve stejné virtuální síti a podsíti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

__Konfigurace__

Pokud chcete zabezpečit účet Azure Storage používaný vaším pracovním prostorem, buď povolte pro účet úložiště ve vaší virtuální síti __privátní koncový bod__ nebo __koncový bod služby__ .

* Informace o konfiguraci účtu úložiště pro použití __privátního koncového bodu__najdete v článku [použití privátních koncových bodů](/azure/storage/common/storage-private-endpoints.md) .

* K nakonfigurování účtu úložiště pro použití __koncového bodu služby__použijte následující postup:

    1. Pokud chcete přidat účet úložiště do virtuální sítě, kterou používá váš pracovní prostor, použijte informace v části __udělení přístupu z virtuální sítě__ v článku [konfigurace Azure Storagech bran firewall a virtuálních sítí](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) .
    1. K povolení přístupu ze služeb společnosti Microsoft ve virtuální síti (například Azure Machine Learning) použijte informace v části __výjimky__ v článku [konfigurace Azure Storage brány firewall a virtuální sítě](/azure/storage/common/storage-network-security#exceptions) .
    1. Při práci s Azure Machine Learning SDK musí být vaše vývojové prostředí schopné se připojit k účtu Azure Storage. Pokud je účet úložiště ve virtuální síti, musí brána firewall umožňovat přístup z IP adresy vývojového prostředí. Pokud chcete přidat IP adresu vývojového prostředí, použijte informace v části __udělení přístupu z rozsahu IP adres Internetu__ v článku [konfigurace Azure Storagech bran firewall a virtuálních sítí](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) .

## <a name="azure-container-registry"></a>Azure Container Registry

__Požadavky__

* Váš pracovní prostor Azure Machine Learning musí být Enterprise Edition. Informace o upgradu nástroje najdete v tématu [upgrade na verzi Enterprise Edition](how-to-manage-workspace.md#upgrade).
* Oblast Azure Machine Learning pracovního prostoru by měla být [povolená v oblasti s povoleným privátním propojením](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) 
* Vaše Azure Container Registry musí být verze Premium. Další informace o upgradu najdete v tématu [Změna SKU](/azure/container-registry/container-registry-skus#changing-skus).
* Vaše Azure Container Registry musí být ve stejné virtuální síti a podsíti jako účet úložiště a cíle výpočtů používané pro školení nebo odvození.
* Váš pracovní prostor Azure Machine Learning musí obsahovat [Azure Machine Learning výpočetní cluster](how-to-set-up-training-targets.md#amlcompute).

__Omezení__

* Když je ACR za virtuální sítí, Azure Machine Learning ji nemůže použít k přímému vytváření imagí Docker. Místo toho se k sestavení imagí používá výpočetní cluster.

__Konfigurace__

1. Chcete-li najít název Azure Container Registry pro váš pracovní prostor, použijte jednu z následujících metod:

    __Azure Portal__

    V části Přehled pracovního prostoru se hodnota __registru__ odkazuje na Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry pracovního prostoru" border="true":::

    __Azure CLI__

    Pokud jste [nainstalovali rozšíření Machine Learning pro rozhraní příkazového řádku Azure CLI](reference-azure-machine-learning-cli.md), můžete použít `az ml workspace show` příkaz k zobrazení informací o pracovním prostoru.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Tento příkaz vrátí hodnotu podobnou `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Poslední část řetězce je název Azure Container Registry pracovního prostoru.

1. Pokud chcete omezit přístup k virtuální síti, použijte postup v části [Konfigurace přístupu k síti pro registr](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Při přidávání virtuální sítě vyberte virtuální síť a podsíť pro prostředky Azure Machine Learning.

1. Pomocí sady Azure Machine Learning Python SDK můžete nakonfigurovat výpočetní cluster pro vytváření imagí Docker. Následující fragment kódu ukazuje, jak to provést:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Váš účet úložiště, výpočetní cluster a Azure Container Registry musí být všechny ve stejné podsíti virtuální sítě.
    
    Další informace najdete v referenčních informacích k metodě [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Pokud chcete pracovnímu prostoru povolit komunikaci s instancí ACR, použijte následující šablonu Azure Resource Manager:

    > [!WARNING]
    > Tato šablona umožňuje pro váš pracovní prostor privátní koncový bod a mění ho v pracovním prostoru Enterprise. Tyto změny nelze zrušit.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Instance trezoru klíčů 

__Požadavky__

__Omezení__

__Konfigurace__ 

Pokud chcete používat Azure Machine Learning možnosti experimentování s Azure Key Vault za virtuální sítí, použijte článek [konfigurace Azure Key Vault firewall a virtuální sítě](/azure/key-vault/general/network-security) .

> [!IMPORTANT]
> Pokud budete postupovat podle kroků v článku, použijte stejnou virtuální síť jako využívaná výpočetními prostředky experimentování. __Aby bylo možné tuto bránu firewall obejít, musíte taky dovolit důvěryhodným službám Microsoftu__.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Výpočetní clustery & instance 

__Požadavky__

* Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
* Podsíť zadaná pro výpočetní instanci nebo cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu cílových virtuálních počítačů. Pokud podsíť nemá dostatek nepřiřazených IP adres, výpočetní cluster se částečně přidělí.
* Pokud plánujete zabezpečit virtuální síť omezením provozu, musí být některé porty pro výpočetní službu otevřené.
* Pokud hodláte do jedné virtuální sítě umístit víc výpočetních instancí nebo clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo víc vašich prostředků.
* Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako Azure Machine Learning výpočetní instance nebo cluster. 
* Aby funkce COMPUTE instance Jupyter fungovala, ujistěte se, že komunikace webového soketu není zakázána.

__Omezení__

* Instance Machine Learning COMPUTE nebo cluster automaticky přiděluje další síťové prostředky __ve skupině prostředků, která obsahuje virtuální síť__. Pro každou výpočetní instanci nebo cluster přiděluje služba následující prostředky:

    * Jedna skupina zabezpečení sítě
    * Jedna veřejná IP adresa
    * Jeden nástroj pro vyrovnávání zatížení
    
    U __výpočetních clusterů__se tyto prostředky odstraní (a znovu vytvoří) pokaždé, když se cluster škáluje na 0 uzlů.
    
    U __výpočetních instancí__ se prostředky uchovávají do doby, než dojde k odstranění instance (zastavení neodstraní prostředky).

    Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) předplatného.

__Konfigurace__

* Chcete-li vytvořit cluster Výpočetní prostředky služby Machine Learning, použijte následující postup:

    1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/)a pak vyberte své předplatné a pracovní prostor.
    1. Na levé straně vyberte __COMPUTE__ .
    1. V centru vyberte __školicí clustery__ a pak vyberte __+__ .
    1. V dialogovém okně __Nový školicí cluster__ rozbalte oddíl __Upřesnit nastavení__ .
    1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, proveďte v části __Konfigurace virtuální sítě__ následující akce:

        1. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.
        1. V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.
        1. V rozevíracím seznamu __podsíť__ vyberte podsíť, která se má použít.

    ![Nastavení virtuální sítě pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    Cluster Výpočetní prostředky služby Machine Learning můžete vytvořit také pomocí sady Azure Machine Learning SDK. Následující kód vytvoří nový cluster Výpočetní prostředky služby Machine Learning v `default` podsíti virtuální sítě s názvem `mynetwork` :

    ```python
    from azureml.core.compute import ComputeTarget, AmlCompute
    from azureml.core.compute_target import ComputeTargetException

    # The Azure virtual network name, subnet, and resource group
    vnet_name = 'mynetwork'
    subnet_name = 'default'
    vnet_resourcegroup_name = 'mygroup'

    # Choose a name for your CPU cluster
    cpu_cluster_name = "cpucluster"

    # Verify that cluster does not exist already
    try:
        cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
        print("Found existing cpucluster")
    except ComputeTargetException:
        print("Creating new cpucluster")

        # Specify the configuration for the new cluster
        compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            min_nodes=0,
                                                            max_nodes=4,
                                                            vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                            vnet_name=vnet_name,
                                                            subnet_name=subnet_name)

        # Create the cluster with the specified name and configuration
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

        # Wait for the cluster to be completed, show the output log
        cpu_cluster.wait_for_completion(show_output=True)
    ```

    Po dokončení procesu vytváření můžete model pomocí clusteru v experimentu proškolit. Další informace najdete v tématu [Výběr a použití výpočetní cíle pro školení](how-to-set-up-training-targets.md).

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* Pokud používáte poznámkové bloky ve službě Azure COMPUTE instance, musíte zajistit, aby váš Poznámkový blok běžel na výpočetním prostředku za stejnou virtuální sítí a podsítí jako vaše data. 

    Nakonfigurujte instanci služby COMPUTE tak, aby byla ve stejné virtuální síti během vytváření v části **Upřesnit nastavení**  >  **Konfigurace služby Virtual Network**. Existující výpočetní instanci nemůžete přidat do virtuální sítě.

* Pokud plánujete zabezpečit virtuální síť tím, že omezíte síťový provoz na veřejný Internet, musíte povolit příchozí komunikaci ze služby Azure Batch.

    Služba Batch přidá skupiny zabezpečení sítě (skupin zabezpečení sítě) na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

    - Příchozí provoz TCP na portech 29876 a 29877 ze __značky služby__ __BatchNodeManagement__.

    - Volitelné Příchozí provoz TCP na portu 22 pro povolení vzdáleného přístupu. Tento port použijte pouze v případě, že se chcete připojit pomocí SSH na veřejné IP adrese.

    - Odchozí provoz do virtuální sítě na jakémkoli portu.

    - Odchozí provoz do internetu na jakémkoli portu.

    - Pro příchozí provoz TCP pro výpočetní instance na portu 44224 ze __značky služby__ __AzureMachineLearning__.

    > [!IMPORTANT]
    > Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud NSG blokuje komunikaci s výpočetními uzly, služba COMPUTE nastaví stav výpočetních uzlů na nepoužitelné.
    >
    > Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud však podsíť, která obsahuje Azure Machine Learning COMPUTE, má přidruženou skupin zabezpečení sítě nebo bránu firewall, musíte také povolený provoz.

* Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, použijte následující postup:

    1. Odmítne odchozí připojení k Internetu pomocí pravidel NSG.
    1. V případě __výpočetní instance__ nebo __výpočetního clusteru__omezte odchozí provoz na následující položky:
        - Azure Storage pomocí __označení služby__ __Storage. RegionName__. Kde `{RegionName}` je název oblasti Azure.
        - Azure Container Registry pomocí __označení služby__ __AzureContainerRegistry. RegionName__. Kde `{RegionName}` je název oblasti Azure.
        - Azure Machine Learning pomocí __označení služby__ __AzureMachineLearning__
        - Azure Resource Manager pomocí __označení služby__ __AzureResourceManager__
        - Azure Active Directory pomocí __označení služby__ __azureactivedirectory selhala__

    > [!NOTE]
    > Pokud plánujete používání výchozích imagí Docker poskytovaných Microsoftem a povolením spravovaných závislostí uživatelů, musíte použít taky následující __značky služby__:
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
    >
    > Tato konfigurace je nutná, pokud máte kód podobný následujícímu fragmentu kódu jako součást vašich školicích skriptů:
    >
    > __Školení RunConfig__
    > ```python
    > # create a new runconfig object
    > run_config = RunConfiguration()
    > 
    > # configure Docker 
    > run_config.environment.docker.enabled = True
    > # For GPU, use DEFAULT_GPU_IMAGE
    > run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
    > run_config.environment.python.user_managed_dependencies = True
    > ```
    >
    > __Školení Estimator__
    > ```python
    > est = Estimator(source_directory='.',
    >                 script_params=script_params,
    >                 compute_target='local',
    >                 entry_script='dummy_train.py',
    >                 user_managed=True)
    > run = exp.submit(est)
    > ```

* Pokud používáte [vynucené tunelování](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) s využitím Azure Machine Learning COMPUTE, musíte z podsítě, která obsahuje výpočetní prostředky, umožňovat komunikaci s veřejným internetem. Tato komunikace se používá pro plánování úloh a přístup k Azure Storage.

    Můžete to provést dvěma způsoby:

    * Použijte [Virtual Network překlad adres (NAT)](../virtual-network/nat-overview.md). Brána NAT poskytuje odchozí připojení k Internetu pro jednu nebo více podsítí ve vaší virtuální síti. Informace najdete v tématu [navrhování virtuálních sítí s využitím prostředků brány NAT](../virtual-network/nat-gateway-resource.md).

    * Přidejte [uživatelsky definované trasy (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředek. Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch, v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Přidejte také IP adresu pro službu Azure Machine Learning, kde existují prostředky, jak je to nutné pro přístup k výpočetním instancím. Chcete-li získat seznam IP adres služby Batch a služby Azure Machine Learning, použijte jednu z následujících metod:

        * Stáhněte si [rozsahy IP adres Azure a značky služby](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v něm soubor `BatchNodeManagement.<region>` a `AzureMachineLearning.<region>` , kde `<region>` je vaše oblast Azure.

        * Informace si můžete stáhnout pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) . Následující příklad stáhne informace o IP adrese a odfiltruje informace o Východní USA 2 oblasti:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. 

        Kromě všech udr, které definujete, musí být odchozí provoz do Azure Storage povolen prostřednictvím místního síťového zařízení. Konkrétně adresy URL tohoto provozu jsou v následujících formulářích: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net` . 

        Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__Požadavky__

* Instance služby Azure Kubernetes Service (AKS) a virtuální síť Azure musí být ve stejné oblasti. Pokud zabezpečujete Azure Storage účty používané pracovním prostorem ve virtuální síti, musí být ve stejné virtuální síti jako instance AKS.

* Pokud chcete __naplánovat přidělování IP adres__ pro váš cluster, postupujte podle pokynů v článku [Konfigurace pokročilé sítě v Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) .

* Pokud chcete omezit příchozí a odchozí komunikaci do clusteru AKS, postupujte podle pokynů v tématu [řízení provozu výstupů uzlů clusteru v Azure Kubernetes služby](/azure/aks/limit-egress-traffic) a ujistěte se, že je správně nakonfigurovaná _odchozí_ komunikace z AKS. V níže uvedeném konfiguračním oddílu jsou vyvolány požadavky na _příchozí_ komunikaci, pokud jsou nějaké.

__Omezení__

* Pokud chcete používat službu Azure Kubernetes s povoleným privátním odkazem, musíte ji připojit k vašemu pracovnímu prostoru. Nemůžete vytvořit cluster služby Azure Kubernetes s privátním odkazem z Azure Machine Learning (SDK, portál, CLI atd.).

__Konfigurace__

> [!IMPORTANT]
> Tato část obsahuje několik konfigurací. Vyberte ten, který nejlépe vyhovuje vašim potřebám.

* __Použití AKS za virtuální sítí s veřejným nástrojem pro vyrovnávání zatížení__:

    1. Vytvořte nebo připojte cluster AKS. Pokud __vytváříte__ nový cluster, musíte zadat virtuální síť, která se má použít pro cluster.
    
        Následující příklad ukazuje, jak vytvořit nový cluster AKS pomocí sady Python SDK:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Pokud máte existující cluster AKS, který už je za virtuální sítí, použijte informace v článku [nasazení do Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Ujistěte se, že skupina zabezpečení sítě, která řídí virtuální síť, má pro koncový bod bodování povolené __příchozí__ pravidlo zabezpečení, aby se mohlo volat mimo virtuální síť.

* __Použití AKS za virtuální sítí s privátním nástrojem pro vyrovnávání zatížení__:

    1. Vytvořte nebo připojte cluster AKS. Pokud __vytváříte__ nový cluster, musíte zadat virtuální síť, která se má použít pro cluster.
    
        Následující příklad ukazuje, jak vytvořit nový cluster AKS pomocí sady Python SDK:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Pokud máte existující cluster AKS, který už je za virtuální sítí, použijte informace v článku [nasazení do Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Pokud chcete najít instanční objekt nebo ID spravované identity pro AKS, použijte následující příkazy rozhraní příkazového řádku Azure CLI. Nahraďte `<aks-cluster-name>` názvem clusteru. Nahraďte `<resource-group-name>` názvem skupiny prostředků, která _obsahuje cluster AKS_:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Pokud tento příkaz vrátí hodnotu `msi` , použijte následující příkaz k identifikaci ID objektu zabezpečení pro spravovanou identitu:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. Pokud chcete najít ID skupiny prostředků, která obsahuje vaši virtuální síť, použijte následující příkaz. Nahraďte `<resource-group-name>` názvem skupiny prostředků, která _obsahuje virtuální síť_:

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. K přidání instančního objektu nebo spravované identity jako přispěvatele sítě použijte následující příkaz. Nahraďte `<SP-or-managed-identity>` identifikátorem vráceným pro instanční objekt nebo spravovanou identitu. Nahraďte `<resource-group-id>` identifikátorem vráceným pro skupinu prostředků, která obsahuje virtuální síť:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Pokud chcete aktualizovat cluster AKS tak, aby používal __privátní Nástroj pro vyrovnávání zatížení__, použijte sadu Python SDK. Následující fragment kódu ukazuje, jak aktualizovat existující cluster AKS, který byl přidán nebo připojen k pracovnímu prostoru:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* Postup __připojení AKS pomocí privátního koncového bodu__:

    1. Pomocí následujícího příkazu rozhraní příkazového řádku Azure Získejte __ID podsítě__ podsítě, kterou bude cluster AKS používat. Například výchozí podsíť pro virtuální síť:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Tento příkaz vrátí pole ID pro podsítě ve virtuální síti. Následující kód JSON je příklad z virtuální sítě, která má pouze jednu podsíť:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Pokud je vráceno více identifikátorů, vyberte tu, kterou chcete použít.

    1. Pokud chcete vytvořit cluster AKS privátním koncovým bodem, použijte informace v části __Pokročilé sítě__ v článku [Vytvoření privátního clusteru služby Azure Kubernetes](/azure/aks/private-clusters#advanced-networking) . Při vytváření clusteru použijte ID podsítě z předchozího příkazu s `--vnet-subnet-id` parametrem.

    1. K připojení clusteru použijte informace v článku [nasazení do Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    > [!TIP]
    > Při použití této konfigurace a omezení odchozí komunikace z clusteru AKS není nutné povolit komunikaci na portech __1194__ nebo __9000__. Další porty, které by se měly povolit, najdete v článku pokyny v tématu [řízení provozu výstupů pro uzly clusteru v Azure Kubernetes Service](/azure/aks/limit-egress-traffic) .

## <a name="use-azure-container-instances-aci"></a>Použít Azure Container Instances (ACI)

__Požadavky__

* Azure Container Instances se dynamicky vytvářejí při nasazování modelu. Pokud chcete povolit Azure Machine Learning vytváření ACI uvnitř virtuální sítě, musíte povolit __delegování podsítě__ pro podsíť, kterou používá nasazení.

__Omezení__

* Virtuální síť musí být ve stejné skupině prostředků jako pracovní prostor Azure Machine Learning.

* Azure Container Registry (ACR) pro váš pracovní prostor nemůže být také ve virtuální síti.

__Konfigurace__

Pokud chcete použít ACI ve virtuální síti k vašemu pracovnímu prostoru, použijte následující postup:

1. K povolení delegování podsítě ve virtuální síti použijte informace v článku [Přidání nebo odebrání delegování podsítě](../virtual-network/manage-subnet-delegation.md) . Delegování můžete povolit při vytváření virtuální sítě nebo jejich přidání do existující sítě.

    > [!IMPORTANT]
    > Při povolování delegování použijte `Microsoft.ContainerInstance/containerGroups` jako hodnotu __pro podsíť delegáta na službu__ .

2. Nasaďte model pomocí [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), použijte `vnet_name` parametry a `subnet_name` . Nastavte tyto parametry na název virtuální sítě a podsíť, ve které jste povolili delegování.

## <a name="azure-databricks"></a>Azure Databricks

__Požadavky__

* Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
* Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako cluster Azure Databricks.
* Kromě __datacihly –__ veřejné podsítě a __datacihly__ , které používají Azure Databricks, je také nutná __výchozí__ podsíť vytvořená pro virtuální síť.

__Omezení__

__Konfigurace__

Konkrétní informace o použití Azure Databricks s virtuální sítí najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuální počítač nebo cluster HDInsight

__Požadavky__

* Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu.
* Port SSH musí být povolený na virtuálním počítači nebo v clusteru HDInsight.

__Omezení__

__Konfigurace__

1. Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte cluster do virtuální sítě Azure. Další informace najdete v následujících článcích:

    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšiřování HDInsight pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. NSG se automaticky vytvoří pro Azure Virtual Machines v systému Linux. Tento NSG umožňuje přístup k portu 22 z libovolného zdroje. Chcete-li omezit přístup k portu SSH, je nutné přístup z Azure Machine Learning udělit. Pokud chcete zachovat přístup pro Azure ML, musíte přístup ze __zdrojové služby__ zpřístupnit se __značkou zdrojové služby__ __AzureMachineLearning__. Například následující příkazy rozhraní příkazového řádku Azure mění pravidlo SSH tak, aby povolovaly přístup jenom z Azure Machine Learning.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Další informace najdete v části [Vytvoření skupin zabezpečení sítě](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) v článku virtuální sítě Azure pro virtuální počítače se systémem Linux.
    
    Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

1. Připojte virtuální počítač nebo cluster HDInsight k pracovnímu prostoru Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Další kroky

* [Použijte Azure Machine Learning pracovního prostoru za Azure firewall](how-to-access-azureml-behind-firewall.md).
* [Nastavení prostředí trénování](how-to-set-up-training-targets.md)
* [Nastavení privátních koncových bodů](how-to-configure-private-link.md)
* [Kam se mají modely nasadit](how-to-deploy-and-where.md)
* [Použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
