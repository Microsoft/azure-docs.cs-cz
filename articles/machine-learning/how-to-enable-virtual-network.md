---
title: Zabezpečené experimenty a odvození ve virtuální síti
titleSuffix: Azure Machine Learning
description: Naučte se, jak zabezpečit experimenty/školicí úlohy a úlohy odvození a bodování v Azure Machine Learning v rámci Azure Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: 3dfdbc56456ea67c830d0e1e9785b9d0032bf2cc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988209"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Zabezpečení experimentů s Azure ML a odvození úloh v rámci Azure Virtual Network
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zabezpečit úlohy experimentování/školení a úlohy odvození a bodování v Azure Machine Learning v rámci Azure Virtual Network (VNET).

**Virtuální síť** funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. Virtuální síť Azure se taky můžete připojit k místní síti. Připojením sítí můžete bezpečně prosazovat modely a přistupovat k nasazeným modelům pro odvození.

Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky nebo [výpočetní cíle](concept-compute-target.md)se používají ke školení a nasazení modelů. Cíle lze vytvořit v rámci virtuální sítě. Můžete například použít Microsoft Data Science Virtual Machine k učení modelu a nasazení modelu do služby Azure Kubernetes Service (AKS). Další informace o virtuálních sítích najdete v tématu [Přehled služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Tento článek také poskytuje podrobné informace o *pokročilých nastaveních zabezpečení*, informace, které nejsou nutné pro základní nebo experimentální případy použití. Některé části tohoto článku obsahují informace o konfiguraci různých scénářů. Nemusíte doplňovat pokyny v daném pořadí nebo v celém rozsahu.

> [!TIP]
> Pokud to není konkrétně vyvoláno, budou se pomocí prostředků, jako jsou účty úložiště nebo cíle výpočtů ve virtuální síti, pracovat s oběma kanály strojového učení i s pracovními postupy bez kanálu, jako je spuštění skriptu.

> [!WARNING]
> Microsoft nepodporuje používání návrháře Azure Machine Learning ani automatizovaného strojového učení (od studia) s prostředky v rámci virtuální sítě.

## <a name="prerequisites"></a>Požadavky

+ [Pracovní prostor](how-to-manage-workspace.md)Azure Machine Learning.

+ Obecné praktické znalosti [služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a [sítě IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Již existující virtuální síť a podsíť, která se má použít s výpočetními prostředky.

## <a name="use-a-storage-account-for-your-workspace"></a>Použití účtu úložiště pro váš pracovní prostor

Pokud chcete použít účet úložiště Azure pro pracovní prostor ve virtuální síti, použijte následující postup:

1. Vytvořte výpočetní prostředek (například Machine Learning výpočetní instance nebo cluster) za virtuální sítí nebo připojte výpočetní prostředek k pracovnímu prostoru (například cluster HDInsight, virtuální počítač nebo cluster služby Azure Kubernetes). Výpočetní prostředek může být pro experimentování nebo nasazení modelu.

   Další informace najdete v částech [použití výpočetní služby Machine Learning](#amlcompute), [použití virtuálního počítače nebo clusteru HDInsight](#vmorhdi)a [používání služeb Azure Kubernetes](#aksvnet) v tomto článku.

1. V Azure Portal přejdete do úložiště, které je připojené k vašemu pracovnímu prostoru.

   [![úložiště, které je připojené k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stránce **Azure Storage** vyberte možnost __brány firewall a virtuální sítě__.

   ![Oblast brány firewall a virtuální sítě na stránce Azure Storage v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    - Vyberte __Vybrané sítě__.
    - V části __virtuální sítě__vyberte odkaz __Přidat existující virtuální síť__ . Tato akce přidá virtuální síť, ve které se nachází vaše výpočetní výkon (viz krok 1).

        > [!IMPORTANT]
        > Účet úložiště musí být ve stejné virtuální síti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

    - Zaškrtněte políčko __pro přístup k tomuto účtu úložiště udělit důvěryhodné služby Microsoftu__ .

    > [!IMPORTANT]
    > Při práci s Azure Machine Learning SDK musí být vaše vývojové prostředí schopné se připojit k účtu Azure Storage. Pokud je účet úložiště ve virtuální síti, musí brána firewall umožňovat přístup z IP adresy vývojového prostředí.
    >
    > Pokud chcete povolit přístup k účtu úložiště, přejděte na téma __brány firewall a virtuální sítě__ pro účet úložiště *z webového prohlížeče ve vývojovém klientovi*. Pak pomocí zaškrtávacího políčka __Přidat IP adresu klienta__ přidejte IP adresu klienta do __rozsahu adres__. Můžete také použít pole __Rozsah adres__ k ručnímu zadání IP adresy vývojového prostředí. Po přidání IP adresy pro klienta bude mít přístup k účtu úložiště pomocí sady SDK.

   [![podokně brány firewall a virtuální sítě v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Do virtuální sítě můžete umístit _výchozí účet úložiště_ pro Azure Machine Learning nebo _jiné než výchozí účty úložiště_ .
>
> Výchozí účet úložiště se automaticky zřídí při vytváření pracovního prostoru.
>
> U účtů úložiště, které nejsou výchozí, vám parametr `storage_account` ve [funkci`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umožňuje zadat vlastní účet úložiště podle ID prostředku Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Použít Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen 2 je sada funkcí pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage. Dá se použít k ukládání dat používaných ke vzdělávání modelů pomocí Azure Machine Learning. 

Pokud chcete použít Data Lake Storage Gen 2 uvnitř virtuální sítě pracovního prostoru Azure Machine Learning, použijte následující postup:

1. Vytvořte účet Azure Data Lake Storage Gen 2. Další informace najdete v tématu [Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Pomocí kroků 2-4 v předchozí části použijte [účet úložiště pro váš pracovní prostor](#use-a-storage-account-for-your-workspace)a vložte účet do virtuální sítě.

Pokud používáte Azure Machine Learning s Data Lake Storage Gen 2 v rámci virtuální sítě, postupujte podle následujících pokynů:

* Použijete-li sadu __SDK k vytvoření datové sady__a systém, který spouští kód, není __ve virtuální síti__, použijte parametr `validate=False`. Tento parametr přeskočí ověřování, které se nezdařilo, pokud systém není ve stejné virtuální síti jako účet úložiště. Další informace naleznete v tématu metoda [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) .

* Při použití Azure Machine Learning výpočetní instance nebo výpočetního clusteru ke školení modelu s datovou sadou se musí nacházet ve stejné virtuální síti jako účet úložiště.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Použití instance trezoru klíčů s vaším pracovním prostorem

Instance trezoru klíčů, která je přidružená k pracovnímu prostoru, se používá Azure Machine Learning k uložení následujících přihlašovacích údajů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Pokud chcete používat Azure Machine Learning možnosti experimentování s Azure Key Vault za virtuální sítí, použijte následující postup:

1. Přejít do trezoru klíčů, který je přidružený k pracovnímu prostoru.

   [![trezoru klíčů, který je přidružený k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stránce **Key Vault** v levém podokně vyberte možnost __brány firewall a virtuální sítě__.

   ![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    - V části __Povolení přístupu z__vyberte __vybrané sítě__.
    - V části __virtuální sítě__vyberte __Přidat existující virtuální sítě__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní služby experimentování.
    - V části __umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall__, vyberte __Ano__.

   [![části brány firewall a virtuální sítě v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Použít Výpočetní prostředky služby Machine Learning

> [!NOTE]
> Výpočetní instance (Preview) jsou v tuto chvíli dostupné jenom pro pracovní prostory s oblastí **střed USA – sever** nebo **Velká Británie – jih**, která už brzy podporuje další oblasti.
> Jednu z těchto oblastí použijte k vytvoření výpočetní instance, kterou je možné přidat do virtuální sítě.

Chcete-li použít výpočetní instanci nebo výpočetní cluster Azure Machine Learning ve virtuální síti, musí být splněny následující požadavky na síť:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Podsíť zadaná pro výpočetní instanci nebo cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu cílových virtuálních počítačů. Pokud podsíť nemá dostatek nepřiřazených IP adres, výpočetní cluster se částečně přidělí.
> * Zkontrolujte, jestli zásady zabezpečení nebo zámky v předplatném virtuální sítě nebo skupině prostředků omezují oprávnění ke správě virtuální sítě. Pokud máte v úmyslu zabezpečit virtuální síť omezením provozu, nechte některé porty pro výpočetní službu otevřené. Další informace najdete v části [požadované porty](#mlcports) .
> * Pokud hodláte do jedné virtuální sítě umístit víc výpočetních instancí nebo clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo víc vašich prostředků.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako Azure Machine Learning výpočetní instance nebo cluster. 

Instance Machine Learning COMPUTE nebo cluster automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť. Pro každou výpočetní instanci nebo cluster přiděluje služba následující prostředky:

* Jedna skupina zabezpečení sítě
* Jedna veřejná IP adresa
* Jeden nástroj pro vyrovnávání zatížení

Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) předplatného.


### <a id="mlcports"></a>Požadované porty

Výpočetní prostředky služby Machine Learning aktuálně používá službu Azure Batch k zřizování virtuálních počítačů v zadané virtuální síti. Podsíť musí umožňovat příchozí komunikaci ze služby Batch. Tato komunikace se používá k naplánování spuštění na Výpočetní prostředky služby Machine Learningch uzlech a ke komunikaci s Azure Storage a dalšími prostředky. Služba Batch přidá skupiny zabezpečení sítě (skupin zabezpečení sítě) na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz TCP na portech 29876 a 29877 ze __značky služby__ __BatchNodeManagement__.

    ![Příchozí pravidlo, které používá značku služby BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Volitelné Příchozí provoz TCP na portu 22 pro povolení vzdáleného přístupu. Tento port použijte pouze v případě, že se chcete připojit pomocí SSH na veřejné IP adrese.

- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

- Pro příchozí provoz TCP pro výpočetní instance na portu 44224 ze __značky služby__ __AzureMachineLearning__.

Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud NSG blokuje komunikaci s výpočetními uzly, služba COMPUTE nastaví stav výpočetních uzlů na nepoužitelné.

Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud však Zadaná podsíť má přidruženou skupin zabezpečení sítě nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení, jak je uvedeno výše.

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujících obrázcích:

[![příchozí pravidla NSG pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, použijte následující postup:

- Odmítne odchozí připojení k Internetu pomocí pravidel NSG.

- V případě __výpočetní instance__ nebo __výpočetního clusteru__omezte odchozí provoz na následující položky:
   - Azure Storage pomocí __označení služby__ __Storage. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Container Registry pomocí __označení služby__ __AzureContainerRegistry. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Machine Learning pomocí __označení služby__ __AzureMachineLearning__
   
- Pro __výpočetní instanci__přidejte také následující položky:
   - Azure Resource Manager pomocí __označení služby__ __AzureResourceManager__
   - Azure Active Directory pomocí __označení služby__ __azureactivedirectory selhala__

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujícím obrázku:

[![odchozích pravidel NSG pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Pokud plánujete používání výchozích imagí Docker poskytovaných Microsoftem a povolením spravovaných závislostí uživatelů, musíte také použít __značku služby__ __MicrosoftContainerRegistry. Region_Name__ (například MicrosoftContainerRegistry. EastUS).
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

### <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelování

Pokud používáte vynucené tunelování s Výpočetní prostředky služby Machine Learning, přidejte [uživatelsky definované trasy (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředky.

* Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch, v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Chcete-li získat seznam IP adres služby Batch, použijte jednu z následujících metod:

    * Stáhněte si [rozsahy IP adres Azure a značky služby](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v souboru `BatchNodeManagement.<region>`, kde `<region>` je vaše oblast Azure.

    * Informace si můžete stáhnout pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) . Následující příklad stáhne informace o IP adrese a odfiltruje informace o Východní USA 2 oblasti:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Odchozí provoz do Azure Storage se nesmí blokovat v místním síťovém zařízení. Konkrétně jsou adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`a `<account>.blob.core.windows.net`.

Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

![Příklad UDR pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Vytvoření výpočetního clusteru ve virtuální síti

Chcete-li vytvořit cluster Výpočetní prostředky služby Machine Learning, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor Azure Machine Learning.

1. V části __aplikace__ vyberte __COMPUTE__a pak vyberte __Přidat výpočetní__prostředky.

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, proveďte následující akce:

    a. V případě __Konfigurace sítě__vyberte __Upřesnit__.

    b. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.

    c. V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.

    d. V rozevíracím seznamu __podsíť__ vyberte podsíť, která se má použít.

   ![Nastavení virtuální sítě pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Cluster Výpočetní prostředky služby Machine Learning můžete vytvořit také pomocí sady Azure Machine Learning SDK. Následující kód vytvoří nový cluster Výpočetní prostředky služby Machine Learning v podsíti `default` virtuální sítě s názvem `mynetwork`:

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

## <a name="use-azure-databricks"></a>Použít Azure Databricks

Pokud chcete použít Azure Databricks ve virtuální síti s vaším pracovním prostorem, musí být splněné následující požadavky:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako cluster Azure Databricks.
> * Kromě __datacihly –__ veřejné podsítě a __datacihly__ , které používají Azure Databricks, je také nutná __výchozí__ podsíť vytvořená pro virtuální síť.

Konkrétní informace o použití Azure Databricks s virtuální sítí najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Použití virtuálního počítače nebo clusteru HDInsight

> [!IMPORTANT]
> Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu.

Pokud chcete použít virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s vaším pracovním prostorem, použijte následující postup:

1. Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte cluster do virtuální sítě Azure. Další informace najdete v těchto článcích:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšiřování HDInsight pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pokud chcete Azure Machine Learning komunikovat s portem SSH na virtuálním počítači nebo v clusteru, nakonfigurujte položku zdroje pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. Pokud chcete povolit přenos z tohoto zdroje, proveďte následující akce:

    * V rozevíracím seznamu __zdroj__ vyberte možnost __značka služby__.

    * V rozevíracím seznamu __značka zdrojové služby__ vyberte možnost __AzureMachineLearning__.

    * V rozevíracím seznamu __rozsahy zdrojových portů__ vyberte možnost __*__ .

    * V rozevíracím seznamu __cíl__ vyberte možnost __libovolný__.

    * V rozevíracím seznamu __rozsahy cílových portů__ vyberte __22__.

    * V části __protokol__vyberte __libovolný__.

    * V části __Akce__vyberte možnost __povoleno__.

   ![Příchozí pravidla pro experimentování na virtuálním počítači nebo clusteru HDInsight ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, přečtěte si část [omezení odchozího připojení z virtuální sítě](#limiting-outbound-from-vnet) .

1. Připojte virtuální počítač nebo cluster HDInsight k pracovnímu prostoru Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Použití služby Azure Kubernetes Service (AKS)

Pokud chcete do svého pracovního prostoru přidat AKS ve virtuální síti, použijte následující postup:

> [!IMPORTANT]
> Než začnete s následujícím postupem, postupujte podle pokynů v tématu [Konfigurace pokročilé sítě ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) postupy a naplánování IP adres pro váš cluster.
>
> Instance AKS a virtuální síť Azure musí být ve stejné oblasti. Pokud zabezpečujete Azure Storage účty používané pracovním prostorem ve virtuální síti, musí být ve stejné virtuální síti jako instance AKS.

1. V [Azure Portal](https://portal.azure.com)se ujistěte, že NSG, který řídí virtuální síť, má příchozí pravidlo, které je povolené pro Azure Machine Learning jako zdroj jako **zdroj**.

    [![Azure Machine Learning přidat výpočetní podokno](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Vyberte pracovní prostor Azure Machine Learning.

1. V části __aplikace__ vyberte __COMPUTE__a pak vyberte __Přidat výpočetní__prostředky.

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, proveďte následující akce:

    - V případě __Konfigurace sítě__vyberte __Upřesnit__.

    - V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.

    - V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.

    - V rozevíracím seznamu __podsíť__ vyberte podsíť.

    - Do pole __Rozsah adres služby Kubernetes__ zadejte rozsah adres služby Kubernetes. Tento rozsah adres používá pro definování IP adres, které jsou k dispozici pro cluster, rozsah IP adres zápisu CIDR (Classless Inter-Domain Routing). Nesmí se překrývat s žádnými rozsahy IP adres podsítě (například 10.0.0.0/16).

    - Do pole __IP adresa služby KUBERNETES DNS__ zadejte IP adresu služby DNS Kubernetes. Tato IP adresa se přiřadí službě DNS Kubernetes. Musí se nacházet v rozsahu adres služby Kubernetes (například 10.0.0.10).

    - Do pole __adresa mostu Docker__ zadejte adresu mostu Docker. Tato IP adresa je přiřazená k mostu Docker. Nesmí se jednat o žádné rozsahy IP adres podsítě ani rozsah adres služby Kubernetes (například 172.17.0.1/16).

   ![Azure Machine Learning: Výpočetní prostředky služby Machine Learning nastavení virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ujistěte se, že skupina NSG, která řídí virtuální síť, má pro koncový bod bodování povolené příchozí pravidlo zabezpečení, aby se mohlo volat mimo virtuální síť.
   > [!IMPORTANT]
   > Ponechte výchozí odchozí pravidla pro NSG. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![příchozí pravidlo zabezpečení](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Pomocí sady Azure Machine Learning SDK můžete také přidat službu Azure Kubernetes ve virtuální síti. Pokud už máte cluster AKS ve virtuální síti, připojte ho k pracovnímu prostoru, jak je popsáno v tématu [Jak nasadit do AKS](how-to-deploy-and-where.md). Následující kód vytvoří novou instanci AKS v podsíti `default` virtuální sítě s názvem `mynetwork`:

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

> [!IMPORTANT]
> Při vytváření clusteru služby Azure Kubernetes není možné povolit privátní IP adresu. Musí být povolená jako Aktualizace existujícího clusteru.

Následující fragment kódu ukazuje, jak **vytvořit nový cluster AKS**a pak ho aktualizovat tak, aby používal privátní IP/interní nástroj pro vyrovnávání zatížení:

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

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Obsah souboru `body.json`, na který odkazuje příkaz, je podobný následujícímu dokumentu JSON:

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
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

> [!NOTE]
> Nástroj pro vyrovnávání zatížení se v současné době nedá nakonfigurovat při provádění operace __připojení__ pro existující cluster. Musíte nejdřív připojit cluster a pak provést operaci aktualizace pro změnu nástroje pro vyrovnávání zatížení.

Další informace o používání interního nástroje pro vyrovnávání zatížení s AKS najdete v tématu [použití interního nástroje pro vyrovnávání zatížení se službou Azure Kubernetes Service](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Použít Azure Firewall

Při použití Azure Firewall musíte nakonfigurovat síťové pravidlo, které povoluje provoz do a z následujících adres:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Když přidáte pravidlo, nastavte __protokol__ na any a porty, které se mají `*`.

Další informace o konfiguraci pravidla sítě najdete v tématu [nasazení a konfigurace Azure firewall](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="next-steps"></a>Další kroky

* [Nastavení školicích prostředí](how-to-set-up-training-targets.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Bezpečné nasazení modelů pomocí protokolu SSL](how-to-secure-web-service.md)
