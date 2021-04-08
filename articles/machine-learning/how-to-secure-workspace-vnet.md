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
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: a923f65e5c6183d045f4b7455e0a01edda75d499
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584329"
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

> [!Warning]
> Zabezpečení pracovního prostoru pomocí privátních koncových bodů nezajišťuje ucelené zabezpečení sám o sobě. Abyste mohli zabezpečit jednotlivé komponenty vašeho řešení, musíte postupovat podle kroků ve zbývající části tohoto článku a z řady virtuálních sítí.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Zabezpečení účtů úložiště Azure pomocí koncových bodů služby

Azure Machine Learning podporuje účty úložiště nakonfigurované pro použití koncových bodů služby nebo privátních koncových bodů. V této části se dozvíte, jak zabezpečit účet úložiště Azure pomocí koncových bodů služby. Informace o privátních koncových bodech najdete v další části.

Pokud chcete použít účet úložiště Azure pro pracovní prostor ve virtuální síti, použijte následující postup:

1. V Azure Portal přejdete do služby úložiště, kterou chcete použít ve vašem pracovním prostoru.

   [![Úložiště, které je připojené k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stránce účet služby úložiště vyberte __sítě__.

   ![Oblast síť na stránce Azure Storage v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na kartě __brány firewall a virtuální sítě__ proveďte následující akce:
    1. Vyberte __Vybrané sítě__.
    1. V části __virtuální sítě__ vyberte odkaz __Přidat existující virtuální síť__ . Tato akce přidá virtuální síť, ve které se nachází vaše výpočetní výkon (viz krok 1).

        > [!IMPORTANT]
        > Účet úložiště musí být ve stejné virtuální síti a podsíti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

    1. Zaškrtněte políčko __pro přístup k tomuto účtu úložiště udělit důvěryhodné služby Microsoftu__ . Tato změna nedává všem službám Azure přístup k vašemu účtu úložiště.
    
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
1. Privátní koncový bod s cílovým podprostředkem **objektu BLOB** .
1. Privátní koncový bod s cílovým podprostředkem **souboru** (sdílená složka).

![Snímek obrazovky se stránkou konfigurace privátního koncového bodu s možnostmi objektů BLOB a souborů](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Pokud chcete nakonfigurovat privátní koncový bod pro účet úložiště, který **není výchozím** úložištěm, vyberte typ **cílového subprostředku** , který odpovídá účtu úložiště, který chcete přidat.

Další informace najdete v tématu [použití privátních koncových bodů pro Azure Storage](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Zabezpečení úložišť a datových sad

V této části se dozvíte, jak používat úložiště dat a datové sady v prostředí SDK s virtuální sítí. Další informace o prostředí studia najdete v tématu [použití Azure Machine Learning studia ve virtuální síti](how-to-enable-studio-virtual-network.md).

Chcete-li získat přístup k datům pomocí sady SDK, je nutné použít metodu ověřování požadovanou individuální službou, v níž jsou uložena data. Pokud například zaregistrujete úložiště dat pro přístup k Azure Data Lake Store Gen2, musíte i nadále používat instanční objekt, jak je popsáno v [připojení ke službám Azure Storage](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Zakázat ověřování dat

Ve výchozím nastavení Azure Machine Learning provádí kontrolu platnosti dat a přihlašovacích údajů při pokusu o přístup k datům pomocí sady SDK. Pokud jsou data za virtuální sítí, Azure Machine Learning nemůžou tyto kontroly dokončit. Chcete-li tuto kontrolu obejít, je nutné vytvořit úložiště dat a datové sady, které vynechají ověřování.

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
    1. V části __umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall__, vyberte __Ano__.

   [![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Povolit Azure Container Registry (ACR)

Chcete-li použít Azure Container Registry v rámci virtuální sítě, je nutné splnit následující požadavky:

* Vaše Azure Container Registry musí být verze Premium. Další informace o upgradu najdete v tématu [Změna SKU](../container-registry/container-registry-skus.md#changing-tiers).

* Vaše Azure Container Registry musí být ve stejné virtuální síti a podsíti jako účet úložiště a cíle výpočtů používané pro školení nebo odvození.

* Váš pracovní prostor Azure Machine Learning musí obsahovat [Azure Machine Learning výpočetní cluster](how-to-create-attach-compute-cluster.md).

    Když je ACR za virtuální sítí, Azure Machine Learning ji nemůže použít k přímému vytváření imagí Docker. Místo toho se k sestavení imagí používá výpočetní cluster.

    > [!IMPORTANT]
    > Výpočetní cluster, který se používá k sestavení imagí Docker, musí mít přístup k úložištím balíčku, která se používají ke školení a nasazení vašich modelů. Možná budete muset přidat pravidla zabezpečení sítě, která umožňují přístup k veřejným úložištím, [používat privátní balíčky Pythonu](how-to-use-private-python-packages.md), nebo používat [vlastní image Docker](how-to-train-with-custom-image.md) , které už balíčky obsahují.

Po splnění těchto požadavků můžete Azure Container Registry povolit pomocí následujícího postupu.

> [!TIP]
> Pokud jste při vytváření pracovního prostoru nepoužili existující Azure Container Registry, jedna možná neexistuje. Ve výchozím nastavení se v pracovním prostoru nevytvoří instance ACR, dokud ji nepotřebuje. Chcete-li vynutit vytvoření jednoho, výukového nebo nasazení modelu pomocí pracovního prostoru před použitím kroků v této části.

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

1. Nakonfigurujte ACR pro pracovní prostor tak, aby [povoloval přístup pro důvěryhodné služby](../container-registry/allow-access-trusted-services.md).

1. Pomocí sady Azure Machine Learning Python SDK můžete nakonfigurovat výpočetní cluster pro vytváření imagí Docker. Následující fragment kódu ukazuje, jak to provést:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = None)
    ```

    > [!IMPORTANT]
    > Váš účet úložiště, výpočetní cluster a Azure Container Registry musí být všechny ve stejné podsíti virtuální sítě.
    
    Další informace najdete v referenčních informacích k metodě [Update ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

## <a name="next-steps"></a>Další kroky

Tento článek je druhou částí z řady virtuálních sítí s pěti částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 1: Přehled virtuální sítě](how-to-network-security-overview.md)
* [Část 3: zabezpečení školicího prostředí](how-to-secure-training-vnet.md)
* [4. část: zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md)
* [Část 5: povolení funkcí studia](how-to-enable-studio-virtual-network.md)

Přečtěte si také článek o používání [vlastního DNS](how-to-custom-dns.md) pro překlad IP adres.