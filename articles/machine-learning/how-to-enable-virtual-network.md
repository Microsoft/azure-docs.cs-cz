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
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: 0a7a5f21ee868da2b9c3a6c7dc8bb5968531d0d0
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824198"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>Izolace sítě během školení & odvození s privátními virtuálními sítěmi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zabezpečit životní cyklus strojového učení tím, že izolujete Azure Machine Learning školení a odvozování úloh v rámci Azure Virtual Network (VNET). Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky, označované také jako [cíle výpočtů](concept-compute-target.md), školení a nasazení modelů. Cíle lze vytvořit v rámci virtuální sítě. Můžete například využít Azure Machine Learning COMPUTE pro výuku modelu a pak model nasadit do služby Azure Kubernetes Service (AKS). 

__Virtuální síť__ funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. Virtuální síť Azure se taky můžete připojit k místní síti. Připojením sítí můžete bezpečně prosazovat modely a přistupovat k nasazeným modelům pro odvození.

## <a name="prerequisites"></a>Předpoklady

+ [Pracovní prostor](how-to-manage-workspace.md)Azure Machine Learning.

+ Obecné praktické znalosti [služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a [sítě IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Již existující virtuální síť a podsíť, která se má použít s výpočetními prostředky.

+ Pokud chcete nasadit prostředky do virtuální sítě nebo podsítě, musí mít váš uživatelský účet oprávnění k následujícím akcím v řízení přístupu na základě role (RBAC) v Azure.

    - "Microsoft. Network/virtualNetworks/JOIN/Action" na prostředku virtuální sítě.
    - "Microsoft. Network/virtualNetworks/podsíť/JOIN/Action" na prostředku podsítě.

    Další informace o RBAC se sítí najdete v tématu [předdefinované role sítě](/azure/role-based-access-control/built-in-roles#networking) .

## <a name="private-endpoints"></a>Soukromé koncové body

Můžete také [Povolit privátní propojení Azure](how-to-configure-private-link.md) a připojit se k vašemu pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. [Přečtěte si, jak nastavit Tento soukromý koncový bod.](how-to-configure-private-link.md)



> [!TIP]
> Můžete kombinovat virtuální síť a privátní propojení a chránit tak komunikaci mezi vaším pracovním prostorem a dalšími prostředky Azure. Některé kombinace ale vyžadují pracovní prostor Enterprise Edition. Následující tabulka vám pomůže pochopit, jaké scénáře vyžaduje Enterprise Edition:
>
> | Scénář | Enterprise</br>Edition | Základní</br>Edition |
> | ----- |:-----:|:-----:| 
> | Žádná virtuální síť ani privátní odkaz | ✔ | ✔ |
> | Pracovní prostor bez privátního odkazu Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
> | Pracovní prostor bez privátního odkazu Další prostředky s privátním odkazem | ✔ | |
> | Pracovní prostor s privátním odkazem. Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
> | Pracovní prostor a jakýkoliv jiný prostředek s privátním odkazem | ✔ | |
> | Pracovní prostor s privátním odkazem. Další prostředky bez privátního propojení nebo virtuální sítě | ✔ | ✔ |
> | Azure Container Registry ve virtuální síti | ✔ | |
> | Klíče spravované zákazníkem pro pracovní prostor | ✔ | |
> 

> [!WARNING]
> 
> V pracovním prostoru, kde je povolený privátní odkaz, se nepodporuje Azure Machine Learning výpočetních instancí Preview.

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

Pokud jsou vaše data uložená ve virtuální síti, musíte pomocí [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) v pracovním prostoru udělit přístup k vašim datům v studiu.

> [!IMPORTANT]
> I když většina studia funguje s daty uloženými ve virtuální síti, integrované poznámkové bloky __ne__. Integrované poznámkové bloky nepodporují používání úložiště, které je ve virtuální síti. Místo toho můžete použít poznámkové bloky Jupyter z výpočetní instance. Další informace najdete v části [přístup k datům v poznámkovém bloku COMPUTE instance](#access-data-in-a-compute-instance-notebook) .

Pokud se vám nepodaří udělit přístup do studia, zobrazí se tato chyba `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` a zakažte následující operace:

* Náhled dat v studiu
* Vizualizujte data v návrháři.
* Odešlete experiment AutoML.
* Spusťte Popis projektu.

Studio podporuje čtení dat z následujících typů úložiště dat ve virtuální síti:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="add-resources-to-the-virtual-network"></a>Přidat prostředky do virtuální sítě 

Přidejte svůj pracovní prostor a účet úložiště do stejné virtuální sítě, aby k nim měli přístup.

1. Pokud chcete připojit svůj pracovní prostor k virtuální síti, [Povolte privátní odkaz na Azure](how-to-configure-private-link.md). Tato funkce je aktuálně ve verzi Preview a je dostupná v oblastech USA – východ a USA – západ 2.

1. Pokud chcete připojit svůj účet úložiště k virtuální síti, [nakonfigurujte nastavení brány firewall a virtuální sítě](#use-a-storage-account-for-your-workspace).

### <a name="configure-a-datastore-to-use-managed-identity"></a>Konfigurace úložiště dat pro použití spravované identity

Až přidáte pracovní prostor a účet služby úložiště do virtuální sítě, je potřeba nakonfigurovat úložiště dat tak, aby se k datům používala spravovaná identita. Tyto kroky přidají spravovanou identitu pracovního prostoru jako __čtenáře__ do služby úložiště pomocí řízení přístupu na základě prostředků Azure (RBAC). Přístup __Čtenář__ umožňuje pracovnímu prostoru načíst nastavení brány firewall a zajistit, aby data nezůstala virtuální síť.

1. V nástroji Studio vyberte __úložiště dat__.

1. Pokud chcete vytvořit nové úložiště dat, vyberte __+ nové úložiště dat__. Pokud chcete aktualizovat existující, vyberte úložiště dat a vyberte __Aktualizovat přihlašovací údaje__.

1. V nastavení úložiště dat vyberte __Ano__ , pokud  __chcete, aby služba Azure Machine Learning měla přístup k úložišti pomocí identity spravované pracovním prostorem__.

> [!NOTE]
> Tyto změny mohou trvat až 10 minut, než se projeví.

### <a name="azure-blob-storage-blob-data-reader"></a>Čtečka dat objektů BLOB v Azure Blob Storage

V případě __úložiště objektů BLOB v Azure__je identita spravovaná pracovním prostorem také přidána jako [čtečka dat objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , aby mohla číst data z úložiště objektů BLOB.


### <a name="azure-machine-learning-designer-default-datastore"></a>Výchozí úložiště dat návrháře Azure Machine Learning

Návrhář používá účet úložiště připojený k vašemu pracovnímu prostoru k uložení výstupu ve výchozím nastavení. Můžete ji však zadat pro uložení výstupu do libovolného úložiště dat, ke kterému máte přístup. Pokud vaše prostředí používá virtuální sítě, můžete pomocí těchto ovládacích prvků zajistit, aby data zůstala zabezpečená a přístupná.

Nastavení nového výchozího úložiště pro kanál:

1. V konceptu kanálu vyberte **ikonu ozubeného kolečka pro nastavení** vedle názvu vašeho kanálu.
1. Vyberte **Vybrat výchozí úložiště dat**.
1. Zadejte nové úložiště dat.

Výchozí úložiště dat můžete také přepsat na základě jednotlivých modulů. Díky tomu budete mít kontrolu nad umístěním úložiště pro každý jednotlivý modul.

1. Vyberte modul, jehož výstup chcete zadat.
1. Rozbalte část **Nastavení výstupu** .
1. Vyberte **přepsat výchozí nastavení výstupu**.
1. Vyberte **nastavit výstupní nastavení**.
1. Zadejte nové úložiště dat.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Řízení přístupu Azure Data Lake Storage Gen2

K řízení přístupu k datům v rámci virtuální sítě můžete použít seznamy RBAC a seznam řízení přístupu (ACL) ve stylu POSIX.

Pokud chcete použít RBAC, přidejte do role [čtečky dat objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) spravovanou identitu pracovního prostoru. Další informace najdete v tématu [řízení přístupu na základě role](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

Aby bylo možné používat seznamy řízení přístupu (ACL), je možné přiřadit přístup stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [seznam řízení přístupu k souborům a adresářům](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="azure-data-lake-storage-gen1-access-control"></a>Řízení přístupu Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 podporuje jenom seznamy řízení přístupu ve stylu POSIX. Přístup ke spravovaným identitám pracovního prostoru můžete přiřadit k prostředkům stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).


### <a name="azure-sql-database-contained-user"></a>Azure SQL Database zahrnutý uživatel

Pokud chcete získat přístup k datům uloženým v Azure SQL Database pomocí spravované identity, musíte vytvořit uživatele obsahující SQL, který se mapuje na spravovanou identitu. Další informace o tom, jak vytvořit uživatele z externího poskytovatele, najdete v tématu [Vytvoření obsažených uživatelů mapovaných na identity Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Po vytvoření uživatele s omezením SQL udělte mu oprávnění pomocí [příkazu Grant T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="connect-to-the-studio"></a>Připojit k studiu

Pokud k studiu přistupujete z prostředku uvnitř virtuální sítě (například výpočetní instance nebo virtuální počítač), musíte z této virtuální sítě pro Studio dovolit odchozí přenosy. 

Pokud například používáte skupiny zabezpečení sítě (NSG) k omezení odchozího provozu, přidejte pravidlo do cíle __značky služby__ __AzureFrontDoor. front-end__.

## <a name="use-a-storage-account-for-your-workspace"></a>Použití účtu úložiště pro váš pracovní prostor

> [!IMPORTANT]
> Do virtuální sítě můžete umístit _výchozí účet úložiště_ pro Azure Machine Learning nebo _jiné než výchozí účty úložiště_ .
>
> Výchozí účet úložiště se automaticky zřídí při vytváření pracovního prostoru.
>
> U jiných než výchozích účtů úložiště `storage_account` vám parametr ve [ `Workspace.create()` funkci](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umožní zadat vlastní účet úložiště podle ID prostředku Azure.

Pokud chcete pro pracovní prostor ve virtuální síti použít službu Azure Storage, použijte následující postup:

1. Vytvořte výpočetní prostředek (například Machine Learning výpočetní instance nebo cluster) za virtuální sítí nebo připojte výpočetní prostředek k pracovnímu prostoru (například cluster HDInsight, virtuální počítač nebo cluster služby Azure Kubernetes). Výpočetní prostředek může být pro experimentování nebo nasazení modelu.

   Další informace najdete v částech [použití výpočetní služby Machine Learning](#amlcompute), [použití virtuálního počítače nebo clusteru HDInsight](#vmorhdi)a [používání služeb Azure Kubernetes](#aksvnet) v tomto článku.

1. V Azure Portal přejdete do služby úložiště, kterou chcete použít ve vašem pracovním prostoru.

   [![Úložiště, které je připojené k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stránce účet služby úložiště vyberte __brány firewall a virtuální sítě__.

   ![Oblast brány firewall a virtuální sítě na stránce Azure Storage v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    - Vyberte __Vybrané sítě__.
    - V části __virtuální sítě__vyberte odkaz __Přidat existující virtuální síť__ . Tato akce přidá virtuální síť, ve které se nachází vaše výpočetní výkon (viz krok 1).

        > [!IMPORTANT]
        > Účet úložiště musí být ve stejné virtuální síti a podsíti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

    - Zaškrtněte políčko __pro přístup k tomuto účtu úložiště udělit důvěryhodné služby Microsoftu__ .

    > [!IMPORTANT]
    > Při práci s Azure Machine Learning SDK musí být vaše vývojové prostředí schopné se připojit k účtu Azure Storage. Pokud je účet úložiště ve virtuální síti, musí brána firewall umožňovat přístup z IP adresy vývojového prostředí.
    >
    > Pokud chcete povolit přístup k účtu úložiště, přejděte na téma __brány firewall a virtuální sítě__ pro účet úložiště *z webového prohlížeče ve vývojovém klientovi*. Pak pomocí zaškrtávacího políčka __Přidat IP adresu klienta__ přidejte IP adresu klienta do __rozsahu adres__. Můžete také použít pole __Rozsah adres__ k ručnímu zadání IP adresy vývojového prostředí. Po přidání IP adresy pro klienta bude mít přístup k účtu úložiště pomocí sady SDK.

   [![Podokno brány firewall a virtuální sítě v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>Použití datových úložišť a datových sad

Tato část se zabývá úložištěm dat a využitím datových sad pro prostředí SDK. Další informace o prostředí studia najdete v části [Machine Learning Studiu](#machine-learning-studio).

Ve výchozím nastavení Azure Machine Learning provádí kontrolu platnosti dat a přihlašovacích údajů při pokusu o přístup k datům pomocí sady SDK. Pokud jsou vaše data za virtuální sítí, Azure Machine Learning nemůžou získat přístup k datům a jejich kontroly selžou. Aby k tomu nedocházelo, je nutné vytvořit úložiště dat a datové sady, které vynechají ověřování.

### <a name="use-a-datastore"></a>Použití úložiště dat

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

### <a name="use-a-dataset"></a>Použití datové sady

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


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Výpočetní clustery & instance 

Pokud chcete ve virtuální síti použít [spravovaný Azure Machine Learning __výpočetní cíl__ ](concept-compute-target.md#azure-machine-learning-compute-managed) nebo [Azure Machine Learning výpočetní __instanci__ ](concept-compute-instance.md) , musí být splněné následující požadavky na síť:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Podsíť zadaná pro výpočetní instanci nebo cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu cílových virtuálních počítačů. Pokud podsíť nemá dostatek nepřiřazených IP adres, výpočetní cluster se částečně přidělí.
> * Zkontrolujte, jestli zásady zabezpečení nebo zámky v předplatném virtuální sítě nebo skupině prostředků omezují oprávnění ke správě virtuální sítě. Pokud máte v úmyslu zabezpečit virtuální síť omezením provozu, nechte některé porty pro výpočetní službu otevřené. Další informace najdete v části [požadované porty](#mlcports) .
> * Pokud hodláte do jedné virtuální sítě umístit víc výpočetních instancí nebo clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo víc vašich prostředků.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako Azure Machine Learning výpočetní instance nebo cluster. 
> * Aby funkce COMPUTE instance Jupyter fungovala, ujistěte se, že komunikace webového soketu není zakázána.

> [!TIP]
> Instance Machine Learning COMPUTE nebo cluster automaticky přiděluje další síťové prostředky __ve skupině prostředků, která obsahuje virtuální síť__. Pro každou výpočetní instanci nebo cluster přiděluje služba následující prostředky:
> 
> * Jedna skupina zabezpečení sítě
> * Jedna veřejná IP adresa
> * Jeden nástroj pro vyrovnávání zatížení
> 
> V případě clusterů těchto prostředků se odstraní (a znovu vytvoří), když se cluster škáluje na 0 uzlů, ale u instance, na kterou se prostředky ukládají, ale do úplného odstranění instance (zastavování neodebere prostředky). 
> Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) předplatného.


### <a name="required-ports"></a><a id="mlcports"></a> Požadované porty

Pokud plánujete zabezpečit virtuální síť tím, že omezíte síťový provoz na veřejný Internet, musíte povolit příchozí komunikaci ze služby Azure Batch.

Služba Batch přidá skupiny zabezpečení sítě (skupin zabezpečení sítě) na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz TCP na portech 29876 a 29877 ze __značky služby__ __BatchNodeManagement__.

    ![Příchozí pravidlo, které používá značku služby BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Volitelné Příchozí provoz TCP na portu 22 pro povolení vzdáleného přístupu. Tento port použijte pouze v případě, že se chcete připojit pomocí SSH na veřejné IP adrese.

- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

- Pro příchozí provoz TCP pro výpočetní instance na portu 44224 ze __značky služby__ __AzureMachineLearning__.

> [!IMPORTANT]
> Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud NSG blokuje komunikaci s výpočetními uzly, služba COMPUTE nastaví stav výpočetních uzlů na nepoužitelné.
>
> Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud však podsíť, která obsahuje Azure Machine Learning COMPUTE, má přidruženou skupin zabezpečení sítě nebo bránu firewall, musíte také povolený provoz.

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujících obrázcích:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Příchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning" border="true":::



![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, použijte následující postup:

- Odmítne odchozí připojení k Internetu pomocí pravidel NSG.

- V případě __výpočetní instance__ nebo __výpočetního clusteru__omezte odchozí provoz na následující položky:
   - Azure Storage pomocí __označení služby__ __Storage. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Container Registry pomocí __označení služby__ __AzureContainerRegistry. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Machine Learning pomocí __označení služby__ __AzureMachineLearning__
   - Azure Resource Manager pomocí __označení služby__ __AzureResourceManager__
   - Azure Active Directory pomocí __označení služby__ __azureactivedirectory selhala__

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujícím obrázku:

[![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

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

### <a name="forced-tunneling"></a>Vynucené tunelování

Pokud používáte [vynucené tunelování](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) s využitím Azure Machine Learning COMPUTE, musíte z podsítě, která obsahuje výpočetní prostředky, umožňovat komunikaci s veřejným internetem. Tato komunikace se používá pro plánování úloh a přístup k Azure Storage.

Můžete to provést dvěma způsoby:

* Použijte [Virtual Network překlad adres (NAT)](../virtual-network/nat-overview.md). Brána NAT poskytuje odchozí připojení k Internetu pro jednu nebo více podsítí ve vaší virtuální síti. Informace najdete v tématu [navrhování virtuálních sítí s využitím prostředků brány NAT](../virtual-network/nat-gateway-resource.md).

* Přidejte [uživatelsky definované trasy (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředek. Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch, v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Přidejte také IP adresu pro službu Azure Machine Learning, kde existují prostředky, jak je to nutné pro přístup k výpočetním instancím. Chcete-li získat seznam IP adres služby Batch a služby Azure Machine Learning, použijte jednu z následujících metod:

    * Stáhněte si [rozsahy IP adres Azure a značky služby](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v něm soubor `BatchNodeManagement.<region>` a `AzureMachineLearning.<region>` , kde `<region>` je vaše oblast Azure.

    * Informace si můžete stáhnout pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) . Následující příklad stáhne informace o IP adrese a odfiltruje informace o Východní USA 2 oblasti:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > Pokud používáte oblasti US-) – Virginia, US-Arizona nebo Čínu – východ-2, tyto příkazy nevrátí žádné IP adresy. Místo toho použijte jeden z následujících odkazů ke stažení seznamu IP adres:
        >
        > * [Rozsahy IP adres a značek služeb Azure pro Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Rozsahy IP adres a značky služeb Azure pro Azure Čína](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

    ![Příklad UDR pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

    Kromě všech udr, které definujete, musí být odchozí provoz do Azure Storage povolen prostřednictvím místního síťového zařízení. Konkrétně adresy URL tohoto provozu jsou v následujících formulářích: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net` . 

    Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Vytvoření výpočetního clusteru ve virtuální síti

Chcete-li vytvořit cluster Výpočetní prostředky služby Machine Learning, použijte následující postup:

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

### <a name="access-data-in-a-compute-instance-notebook"></a>Přístup k datům v poznámkovém bloku instance COMPUTE

Pokud používáte poznámkové bloky ve službě Azure COMPUTE instance, musíte zajistit, aby váš Poznámkový blok běžel na výpočetním prostředku za stejnou virtuální sítí a podsítí jako vaše data. 

Instanci služby COMPUTE musíte nakonfigurovat tak, aby byla ve stejné virtuální síti během vytváření v části **Upřesnit nastavení**  >  **Konfigurace služby Virtual Network**. Existující výpočetní instanci nemůžete přidat do virtuální sítě.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pokud chcete do svého pracovního prostoru přidat službu Azure Kubernetes Service (AKS) ve virtuální síti, použijte následující postup:

> [!IMPORTANT]
> Než začnete s následujícím postupem, postupujte podle pokynů v tématu [Konfigurace pokročilé sítě ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) postupy a naplánování IP adres pro váš cluster.
>
> Instance AKS a virtuální síť Azure musí být ve stejné oblasti. Pokud zabezpečujete Azure Storage účty používané pracovním prostorem ve virtuální síti, musí být ve stejné virtuální síti jako instance AKS.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/)a pak vyberte své předplatné a pracovní prostor.

1. Na levé straně vyberte __COMPUTE__ .

1. Vyberte __odvozené clustery__ z centra a pak vyberte __+__ .

1. V dialogu __nový cluster pro odvození__ vyberte v části __Konfigurace sítě__možnost __Upřesnit__ .

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, proveďte následující akce:

    1. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.
    1. V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.
    1. V rozevíracím seznamu __podsíť__ vyberte podsíť.
    1. Do pole __Rozsah adres služby Kubernetes__ zadejte rozsah adres služby Kubernetes. Tento rozsah adres používá pro definování IP adres, které jsou k dispozici pro cluster, rozsah IP adres zápisu CIDR (Classless Inter-Domain Routing). Nesmí se překrývat s žádnými rozsahy IP adres podsítě (například 10.0.0.0/16).
    1. Do pole __IP adresa služby KUBERNETES DNS__ zadejte IP adresu služby DNS Kubernetes. Tato IP adresa se přiřadí službě DNS Kubernetes. Musí se nacházet v rozsahu adres služby Kubernetes (například 10.0.0.10).
    1. Do pole __adresa mostu Docker__ zadejte adresu mostu Docker. Tato IP adresa je přiřazená k mostu Docker. Nesmí se jednat o žádné rozsahy IP adres podsítě ani rozsah adres služby Kubernetes (například 172.17.0.1/16).

   ![Azure Machine Learning: Výpočetní prostředky služby Machine Learning nastavení virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ujistěte se, že skupina NSG, která řídí virtuální síť, má pro koncový bod bodování povolené příchozí pravidlo zabezpečení, aby se mohlo volat mimo virtuální síť.
   > [!IMPORTANT]
   > Ponechte výchozí odchozí pravidla pro NSG. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Příchozí pravidlo zabezpečení](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Pomocí sady Azure Machine Learning SDK můžete také přidat službu Azure Kubernetes ve virtuální síti. Pokud už máte cluster AKS ve virtuální síti, připojte ho k pracovnímu prostoru, jak je popsáno v tématu [Jak nasadit do AKS](how-to-deploy-and-where.md). Následující kód vytvoří novou instanci AKS v `default` podsíti virtuální sítě s názvem `mynetwork` :

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

Po dokončení procesu vytváření můžete spustit odvození nebo model bodování v clusteru AKS za virtuální sítí. Další informace najdete v tématu [Jak nasadit do AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Použití privátních IP adres ve službě Azure Kubernetes

Ve výchozím nastavení je veřejná IP adresa přiřazená AKS nasazením. Při použití AKS v rámci virtuální sítě můžete místo toho použít privátní IP adresu. Privátní IP adresy jsou dostupné jenom v rámci virtuální sítě nebo v připojených sítích.

Privátní IP adresa je povolena konfigurací AKS k použití _interního nástroje pro vyrovnávání zatížení_. 

#### <a name="network-contributor-role"></a>Role Přispěvatel sítě

> [!IMPORTANT]
> Pokud vytvoříte nebo připojíte cluster AKS tím, že zadáte dříve vytvořenou virtuální síť, musíte instančnímu objektu (SP) nebo spravované identitě pro svůj cluster AKS udělit roli _Přispěvatel sítě_ do skupiny prostředků, která obsahuje virtuální síť. Tato operace se musí provést předtím, než se pokusíte změnit interní nástroj pro vyrovnávání zatížení na soukromou IP adresu.
>
> Chcete-li přidat identitu jako Přispěvatel sítě, použijte následující postup:

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
Další informace o používání interního nástroje pro vyrovnávání zatížení s AKS najdete v tématu [použití interního nástroje pro vyrovnávání zatížení se službou Azure Kubernetes Service](/azure/aks/internal-lb).

#### <a name="enable-private-ip"></a>Povolit privátní IP adresu

> [!IMPORTANT]
> Při vytváření clusteru služby Azure Kubernetes není možné povolit privátní IP adresu. Musí být povolená jako Aktualizace existujícího clusteru.

Následující fragment kódu ukazuje, jak __vytvořit nový cluster AKS__a pak ho aktualizovat tak, aby používal privátní IP/interní nástroj pro vyrovnávání zatížení:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

Obsah souboru, na `body.json` který se odkazuje pomocí příkazu, je podobný následujícímu dokumentu JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

Když k pracovnímu prostoru __připojíte existující cluster__ , musíte počkat, až po operaci připojit ke konfiguraci nástroje pro vyrovnávání zatížení.

Informace o připojení clusteru najdete v tématu [připojení existujícího clusteru AKS](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster).

Po připojení existujícího clusteru můžete cluster aktualizovat tak, aby používal privátní IP adresu.

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

## <a name="use-azure-container-instances-aci"></a>Použít Azure Container Instances (ACI)

Azure Container Instances se dynamicky vytvářejí při nasazování modelu. Pokud chcete povolit Azure Machine Learning vytváření ACI uvnitř virtuální sítě, musíte povolit __delegování podsítě__ pro podsíť, kterou používá nasazení.

> [!WARNING]
> Při použití Azure Container Instances ve virtuální síti musí být virtuální síť ve stejné skupině prostředků jako pracovní prostor Azure Machine Learning.
>
> Při použití Azure Container Instances uvnitř virtuální sítě se Azure Container Registry (ACR) pro váš pracovní prostor nemůže nacházet i ve virtuální síti.

Pokud chcete použít ACI ve virtuální síti k vašemu pracovnímu prostoru, použijte následující postup:

1. K povolení delegování podsítě ve virtuální síti použijte informace v článku [Přidání nebo odebrání delegování podsítě](../virtual-network/manage-subnet-delegation.md) . Delegování můžete povolit při vytváření virtuální sítě nebo jejich přidání do existující sítě.

    > [!IMPORTANT]
    > Při povolování delegování použijte `Microsoft.ContainerInstance/containerGroups` jako hodnotu __pro podsíť delegáta na službu__ .

2. Nasaďte model pomocí [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), použijte `vnet_name` parametry a `subnet_name` . Nastavte tyto parametry na název virtuální sítě a podsíť, ve které jste povolili delegování.

## <a name="azure-firewall"></a>Azure Firewall

Informace o použití Azure Machine Learning s Azure Firewall najdete v tématu [použití Azure Machine Learningho pracovního prostoru za Azure firewall](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> Azure Container Registry (ACR) lze umístit do virtuální sítě, ale musíte splňovat následující požadavky:
>
> * Váš pracovní prostor Azure Machine Learning musí být Enterprise Edition. Informace o upgradu nástroje najdete v tématu [upgrade na verzi Enterprise Edition](how-to-manage-workspace.md#upgrade).
> * Oblast vašeho Azure Machine Learning pracovního prostoru by měla být [oblast povolená soukromým odkazem](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
> * Vaše Azure Container Registry musí být verze Premium. Další informace o upgradu najdete v tématu [Změna SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Vaše Azure Container Registry musí být ve stejné virtuální síti a podsíti jako účet úložiště a cíle výpočtů používané pro školení nebo odvození.
> * Váš pracovní prostor Azure Machine Learning musí obsahovat [Azure Machine Learning výpočetní cluster](how-to-set-up-training-targets.md#amlcompute).
>
>     Když je ACR za virtuální sítí, Azure Machine Learning ji nemůže použít k přímému vytváření imagí Docker. Místo toho se k sestavení imagí používá výpočetní cluster.

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

1. Je nutné použít následující šablonu Azure Resource Manager. Tato šablona umožňuje vašemu pracovnímu prostoru komunikovat s ACR.

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

Instance trezoru klíčů, která je přidružená k pracovnímu prostoru, se používá Azure Machine Learning k uložení následujících přihlašovacích údajů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Pokud chcete používat Azure Machine Learning možnosti experimentování s Azure Key Vault za virtuální sítí, použijte následující postup:

1. Přejít do trezoru klíčů, který je přidružený k pracovnímu prostoru.

   [![Trezor klíčů, který je přidružený k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stránce __Key Vault__ v levém podokně vyberte možnost __brány firewall a virtuální sítě__.

   ![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    - V části __Povolení přístupu z__vyberte __vybrané sítě__.
    - V části __virtuální sítě__vyberte __Přidat existující virtuální sítě__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní služby experimentování.
    - V části __umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall__, vyberte __Ano__.

   [![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Pokud chcete použít Azure Databricks ve virtuální síti s vaším pracovním prostorem, musí být splněné následující požadavky:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako cluster Azure Databricks.
> * Kromě __datacihly –__ veřejné podsítě a __datacihly__ , které používají Azure Databricks, je také nutná __výchozí__ podsíť vytvořená pro virtuální síť.

Konkrétní informace o použití Azure Databricks s virtuální sítí najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuální počítač nebo cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu.

Pokud chcete použít virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s vaším pracovním prostorem, použijte následující postup:

1. Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte cluster do virtuální sítě Azure. Další informace najdete v následujících článcích:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšiřování HDInsight pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pokud chcete Azure Machine Learning komunikovat s portem SSH na virtuálním počítači nebo v clusteru, nakonfigurujte položku zdroje pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. Pokud chcete povolit přenos z tohoto zdroje, proveďte následující akce:

    * V rozevíracím seznamu __zdroj__ vyberte možnost __značka služby__.

    * V rozevíracím seznamu __značka zdrojové služby__ vyberte možnost __AzureMachineLearning__.

    * V rozevíracím seznamu __rozsahy zdrojových portů__ vyberte __*__ .

    * V rozevíracím seznamu __cíl__ vyberte možnost __libovolný__.

    * V rozevíracím seznamu __rozsahy cílových portů__ vyberte __22__.

    * V části __protokol__vyberte __libovolný__.

    * V části __Akce__vyberte možnost __povoleno__.

   ![Příchozí pravidla pro experimentování na virtuálním počítači nebo clusteru HDInsight ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, přečtěte si část [omezení odchozího připojení z virtuální sítě](#limiting-outbound-from-vnet) .

1. Připojte virtuální počítač nebo cluster HDInsight k pracovnímu prostoru Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Další kroky

* [Nastavení prostředí trénování](how-to-set-up-training-targets.md)
* [Nastavení privátních koncových bodů](how-to-configure-private-link.md)
* [Kam se mají modely nasadit](how-to-deploy-and-where.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
