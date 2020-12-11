---
title: Zabezpečení Azure Machine Learningho pracovního prostoru pomocí virtuálních sítí
titleSuffix: Azure Machine Learning
description: K zabezpečení pracovního prostoru Azure Machine Learning a přidružených prostředků použijte izolovaný Virtual Network Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 07b8c130a2a22554e4cd5b33996d5a5ee967d47f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029528"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Zabezpečení Azure Machine Learningho pracovního prostoru pomocí virtuálních sítí

V tomto článku se dozvíte, jak zabezpečit Azure Machine Learning pracovní prostor a jeho přidružené prostředky ve virtuální síti.


Tento článek je druhou částí série s pěti částmi, která vás provede zabezpečením Azure Machine Learningho pracovního postupu. Důrazně doporučujeme, abyste si přečetli [část One: VNet – přehled](how-to-network-security-overview.md) , abyste si nejdřív porozuměli celkové architektuře. 

Podívejte se na další články v této sérii:

[1. virtuální síť – přehled](how-to-network-security-overview.md)  >  **2 Zabezpečte pracovní prostor**  >  [3. Zabezpečte školicí prostředí](how-to-secure-training-vnet.md)  >  [4. Zabezpečte prostředí Inferencing](how-to-secure-inferencing-vnet.md)  >  [5. Povolit funkci studia](how-to-enable-studio-virtual-network.md)

V tomto článku se dozvíte, jak povolit následující prostředky v pracovních prostorech ve virtuální síti:
> [!div class="checklist"]
> - Pracovní prostor služby Azure Machine Learning
> - Účty úložiště Azure
> - Azure Machine Learning úložiště dat a datové sady
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Požadavky

+ Přečtěte si článek [Přehled zabezpečení sítě](how-to-network-security-overview.md) , který vám pomůže pochopit běžné scénáře virtuální sítě a celkovou architekturu virtuální sítě.

+ Existující virtuální síť a podsíť pro použití s výpočetními prostředky.

+ Aby bylo možné nasadit prostředky do virtuální sítě nebo podsítě, musí mít váš uživatelský účet oprávnění k následujícím akcím v řízení přístupu na základě role Azure (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/JOIN/Action" na prostředku virtuální sítě.
    - "Microsoft. Network/virtualNetworks/podsíť/JOIN/Action" na prostředku podsítě.

    Další informace o RBAC v Azure s využitím sítě najdete v tématu [předdefinované role sítě](../role-based-access-control/built-in-roles.md#networking) .


## <a name="secure-the-workspace-with-private-endpoint"></a>Zabezpečení pracovního prostoru pomocí privátního koncového bodu

Privátní odkaz Azure vám umožňuje připojit se k vašemu pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. Přístup k pracovnímu prostoru pak můžete omezit tak, aby se nacházet jenom přes privátní IP adresy. Soukromý odkaz pomáhá snižovat riziko exfiltrace dat.

Další informace o nastavení pracovního prostoru privátního propojení najdete v tématu [Postup konfigurace privátního propojení](how-to-configure-private-link.md).

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Zabezpečení účtů úložiště Azure pomocí koncových bodů služby

Azure Machine Learning podporuje účty úložiště nakonfigurované pro použití koncových bodů služby nebo privátních koncových bodů. V této části se dozvíte, jak zabezpečit účet úložiště Azure pomocí koncových bodů služby. Informace o privátních koncových bodech najdete v další části.

> [!IMPORTANT]
> Do virtuální sítě můžete umístit _výchozí účet úložiště_ pro Azure Machine Learning nebo _jiné než výchozí účty úložiště_ .
>
> Výchozí účet úložiště se automaticky zřídí při vytváření pracovního prostoru.
>
> U jiných než výchozích účtů úložiště `storage_account` vám parametr ve [ `Workspace.create()` funkci](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-&preserve-view=true) umožní zadat vlastní účet úložiště podle ID prostředku Azure.

Pokud chcete použít účet úložiště Azure pro pracovní prostor ve virtuální síti, použijte následující postup:

1. V Azure Portal přejdete do služby úložiště, kterou chcete použít ve vašem pracovním prostoru.

   [![Úložiště, které je připojené k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stránce účet služby úložiště vyberte __brány firewall a virtuální sítě__.

   ![Oblast brány firewall a virtuální sítě na stránce Azure Storage v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    1. Vyberte __Vybrané sítě__.
    1. V části __virtuální sítě__ vyberte odkaz __Přidat existující virtuální síť__ . Tato akce přidá virtuální síť, ve které se nachází vaše výpočetní výkon (viz krok 1).

        > [!IMPORTANT]
        > Účet úložiště musí být ve stejné virtuální síti a podsíti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

    1. Zaškrtněte políčko __pro přístup k tomuto účtu úložiště udělit důvěryhodné služby Microsoftu__ . To nedává všem službám Azure přístup k vašemu účtu úložiště.
    
        * Prostředky některých služeb, které jsou **zaregistrované ve vašem předplatném**, můžou mít přístup k účtu úložiště **ve stejném předplatném** pro vybrané operace. Například zápis protokolů nebo vytváření záloh.
        * Prostředkům některých služeb lze udělit explicitní přístup k účtu úložiště tím, že __přiřadíte roli Azure__ ke spravované identitě přiřazené systémem.

        Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Při práci s Azure Machine Learning SDK musí být vaše vývojové prostředí schopné se připojit k účtu Azure Storage. Pokud je účet úložiště ve virtuální síti, musí brána firewall umožňovat přístup z IP adresy vývojového prostředí.
    >
    > Pokud chcete povolit přístup k účtu úložiště, přejděte na téma __brány firewall a virtuální sítě__ pro účet úložiště *z webového prohlížeče ve vývojovém klientovi*. Pak pomocí zaškrtávacího políčka __Přidat IP adresu klienta__ přidejte IP adresu klienta do __rozsahu adres__. Můžete také použít pole __Rozsah adres__ k ručnímu zadání IP adresy vývojového prostředí. Po přidání IP adresy pro klienta bude mít přístup k účtu úložiště pomocí sady SDK.

   [![Podokno brány firewall a virtuální sítě v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Zabezpečení účtů úložiště Azure pomocí privátních koncových bodů

Azure Machine Learning podporuje účty úložiště nakonfigurované pro použití koncových bodů služby nebo privátních koncových bodů. Pokud účet úložiště používá privátní koncové body, musíte nakonfigurovat dva privátní koncové body pro výchozí účet úložiště:
1. Privátní koncový bod s cílovým dílčím prostředkem **objektu BLOB** .
1. Soukromý koncový bod s cílovým dílčím prostředkem **souboru** (sdílená složka).

![Snímek obrazovky se stránkou konfigurace privátního koncového bodu s možnostmi objektů BLOB a souborů](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Pokud chcete nakonfigurovat privátní koncový bod pro účet úložiště, který **není výchozím** úložištěm, vyberte typ **cílového dílčího prostředku** , který odpovídá účtu úložiště, který chcete přidat.

Další informace najdete v tématu [použití privátních koncových bodů pro Azure Storage](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Zabezpečení úložišť a datových sad

V této části se dozvíte, jak používat úložiště dat a datové sady v prostředí SDK s virtuální sítí. Další informace o prostředí studia najdete v tématu [použití Azure Machine Learning studia ve virtuální síti](how-to-enable-studio-virtual-network.md).

Chcete-li získat přístup k datům pomocí sady SDK, je nutné použít metodu ověřování požadovanou individuální službou, v níž jsou uložena data. Pokud například zaregistrujete úložiště dat pro přístup k Azure Data Lake Store Gen2, musíte i nadále používat instanční objekt, jak je popsáno v [připojení ke službám Azure Storage](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Zakázat ověřování dat

Ve výchozím nastavení Azure Machine Learning provádí kontrolu platnosti dat a přihlašovacích údajů při pokusu o přístup k datům pomocí sady SDK. Pokud jsou data za virtuální sítí, Azure Machine Learning nemůžou tyto kontroly dokončit. Aby k tomu nedocházelo, je nutné vytvořit úložiště dat a datové sady, které vynechají ověřování.

### <a name="use-datastores"></a>Použít úložiště dat

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

### <a name="use-datasets"></a>Použití datových sad

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

## <a name="secure-azure-key-vault"></a>Azure Key Vault zabezpečení

Azure Machine Learning používá přidruženou instanci Key Vault k uložení následujících přihlašovacích údajů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Pokud chcete používat Azure Machine Learning možnosti experimentování s Azure Key Vault za virtuální sítí, použijte následující postup:

1. Přejít na Key Vault, která je přidružená k pracovnímu prostoru.

1. Na stránce __Key Vault__ v levém podokně vyberte __síť__.

1. Na kartě __brány firewall a virtuální sítě__ proveďte následující akce:
    1. V části __povolený přístup ze__ vyberte __privátní koncový bod a vybrané sítě__.
    1. V části __virtuální sítě__ vyberte __Přidat existující virtuální sítě__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní služby experimentování.
    1. V části __umožněte důvěryhodným službám Microsoftu obejít tuto bránu firewall?__ vyberte __Ano__.

   [![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Povolit Azure Container Registry (ACR)

Chcete-li použít Azure Container Registry v rámci virtuální sítě, je nutné splnit následující požadavky:

* Vaše Azure Container Registry musí být verze Premium. Další informace o upgradu najdete v tématu [Změna SKU](../container-registry/container-registry-skus.md#changing-tiers).

* Vaše Azure Container Registry musí být ve stejné virtuální síti a podsíti jako účet úložiště a cíle výpočtů používané pro školení nebo odvození.

* Váš pracovní prostor Azure Machine Learning musí obsahovat [Azure Machine Learning výpočetní cluster](how-to-create-attach-compute-cluster.md).

    Když je ACR za virtuální sítí, Azure Machine Learning ji nemůže použít k přímému vytváření imagí Docker. Místo toho se k sestavení imagí používá výpočetní cluster.

* Před použitím ACR s Azure Machine Learning ve virtuální síti musíte otevřít incident podpory, abyste tuto funkci mohli povolit. Další informace najdete v tématu [Správa a zvýšení kvót](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

Po splnění těchto požadavků můžete Azure Container Registry povolit pomocí následujícího postupu.

1. Vyhledejte název Azure Container Registry pro váš pracovní prostor pomocí jedné z následujících metod:

    __Azure Portal__

    V části Přehled pracovního prostoru se hodnota __registru__ odkazuje na Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry pracovního prostoru" border="true":::

    __Azure CLI__

    Pokud jste [nainstalovali rozšíření Machine Learning pro rozhraní příkazového řádku Azure CLI](reference-azure-machine-learning-cli.md), můžete použít `az ml workspace show` příkaz k zobrazení informací o pracovním prostoru.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Tento příkaz vrátí hodnotu podobnou `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Poslední část řetězce je název Azure Container Registry pracovního prostoru.

1. Pomocí postupu v části [Konfigurace přístupu k síti pro registr](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)omezte přístup k virtuální síti. Při přidávání virtuální sítě vyberte virtuální síť a podsíť pro prostředky Azure Machine Learning.

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
    
    Další informace najdete v referenčních informacích k metodě [Update ()](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-&preserve-view=true) .

1. Použijte následující šablonu Azure Resource Manager. Tato šablona umožňuje vašemu pracovnímu prostoru komunikovat s ACR.

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
            "tier": "basic",
            "name": "basic"
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

    Tato šablona vytvoří _privátní koncový bod_ pro přístup k síti z pracovního prostoru k vašemu ACR. Níže uvedený snímek obrazovky ukazuje příklad tohoto privátního koncového bodu.

    :::image type="content" source="media/how-to-secure-workspace-vnet/acr-private-endpoint.png" alt-text="Nastavení privátního koncového bodu ACR":::

    > [!IMPORTANT]
    > Tento koncový bod neodstraňujte. Pokud jej omylem odstraníte, můžete znovu použít šablonu v tomto kroku a vytvořit novou.

## <a name="next-steps"></a>Další kroky

Tento článek je součástí jedné z řad virtuální sítě se čtyřmi částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 1: Přehled virtuální sítě](how-to-network-security-overview.md)
* [Část 3: zabezpečení školicího prostředí](how-to-secure-training-vnet.md)
* [4. část: zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md)
* [Část 5: povolení funkcí studia](how-to-enable-studio-virtual-network.md)