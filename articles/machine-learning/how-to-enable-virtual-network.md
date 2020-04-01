---
title: Bezpečné experimenty a odvození ve virtuální síti
titleSuffix: Azure Machine Learning
description: Zjistěte, jak zabezpečit úlohy experimentování/školení a odvození/vyhodnocování úloh v Azure Machine Learning v rámci virtuální sítě Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/13/2020
ms.openlocfilehash: 6e300bbec097201b33f0c576db91c2ca720fb921
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437358"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Zabezpečené úlohy experimentování a odvození Azure ML v rámci virtuální sítě Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zabezpečit úlohy experimentování a školení a odvození/vyhodnocování úloh v Azure Machine Learning v rámci virtuální sítě Azure (vnet).

**Virtuální síť** funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného internetu. Virtuální síť Azure můžete taky připojit k místní síti. Připojením k sítím můžete bezpečně trénovat své modely a přistupovat k nasazeným modelům pro odvození.

Azure Machine Learning závisí na jiných službách Azure pro výpočetní prostředky. Výpočetní prostředky nebo [výpočetní cíle](concept-compute-target.md)se používají k trénování a nasazování modelů. Cíle lze vytvořit v rámci virtuální sítě. Například můžete použít Microsoft Data Science Virtual Machine trénovat model a pak nasadit model do služby Azure Kubernetes Service (AKS). Další informace o virtuálních sítích najdete [v tématu Přehled virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Tento článek také obsahuje podrobné informace o *pokročilých nastavení zabezpečení*, informace, které nejsou nezbytné pro základní nebo experimentální případy použití. Některé části tohoto článku poskytují informace o konfiguraci pro různé scénáře. Pokyny nemusíte vyplňovat v uvedeném pořadí ani v plném rozsahu.

> [!TIP]
> Pokud není konkrétně volána, pomocí prostředků, jako jsou účty úložiště nebo výpočetní cíle uvnitř virtuální sítě bude fungovat s kanály strojového učení a non-kanálpracovních postupů, jako je například spuštění skriptu.

> [!WARNING]
> Microsoft nepodporuje použití návrháře Azure Machine Learning nebo automatizované strojové učení (ze studia) s prostředky uvnitř virtuální sítě.

## <a name="prerequisites"></a>Požadavky

+ Pracovní [prostor](how-to-manage-workspace.md)Azure Machine Learning .

+ Obecné pracovní znalosti [služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i IP [sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Již existující virtuální síť a podsíť, která se použije s výpočetními prostředky.

## <a name="use-a-storage-account-for-your-workspace"></a>Použití účtu úložiště pro pracovní prostor

Pokud chcete pro pracovní prostor ve virtuální síti použít účet úložiště Azure, použijte následující kroky:

1. Vytvořte výpočetní prostředek (například výpočetní instanci Machine Learning nebo cluster) za virtuální sítí nebo připojte výpočetní prostředek k pracovnímu prostoru (například cluster HDInsight, virtuální počítač nebo cluster služby Azure Kubernetes). Výpočetní prostředek může být pro experimentování nebo nasazení modelu.

   Další informace najdete [v tématu použití výpočetního strojového učení](#amlcompute), použití [virtuálního počítače nebo clusteru HDInsight](#vmorhdi)a [použití služby Azure Kubernetes Service](#aksvnet) v tomto článku.

1. Na webu Azure Portal přejděte do úložiště, které je připojené k vašemu pracovnímu prostoru.

   [![Úložiště připojené k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stránce **Úložiště Azure** vyberte brány firewall a __virtuální sítě__.

   ![Oblast "Firewally a virtuální sítě" na stránce Úložiště Azure na webu Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stránce __Brány firewall a virtuální sítě__ proveďte následující akce:
    - Vyberte __Vybrané sítě__.
    - V části __Virtuální sítě__vyberte Přidat existující propojení __virtuální sítě.__ Tato akce přidá virtuální síť, kde se nachází vaše výpočetní prostředky (viz krok 1).

        > [!IMPORTANT]
        > Účet úložiště musí být ve stejné virtuální síti a podsíti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

    - Zaškrtněte políčko __Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště.__

    > [!IMPORTANT]
    > Při práci s Sadou Azure Machine Learning SDK, vaše vývojové prostředí musí být možné připojení k účtu úložiště Azure. Pokud je účet úložiště uvnitř virtuální sítě, brána firewall musí povolit přístup z ip adresy vývojového prostředí.
    >
    > Chcete-li povolit přístup k účtu úložiště, navštivte __brány firewall a virtuální sítě__ pro účet úložiště z *webového prohlížeče na vývojovém klientovi*. Potom pomocí zaškrtávacího políčka __Přidat adresu IP klienta__ přidejte ip adresu klienta do __rozsahu adres__. Pole __ROZSAH ADRES__ můžete také použít k ručnímu zadání adresy IP vývojového prostředí. Po přidání IP adresy pro klienta může přístup k účtu úložiště pomocí sady SDK.

   [![Podokno "Firewally a virtuální sítě" na webu Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Můžete umístit výchozí _účet úložiště_ pro Azure Machine Learning nebo _jiné než výchozí účty úložiště_ ve virtuální síti.
>
> Výchozí účet úložiště se automaticky zřídí při vytváření pracovního prostoru.
>
> Pro účty úložiště, které `storage_account` nejsou výchozí, parametr ve [ `Workspace.create()` funkci](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umožňuje zadat vlastní účet úložiště podle ID prostředků Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Použití Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen 2 je sada funkcí pro analýzu velkých objemů dat, která je postavená na úložišti objektů blob Azure. Slouží k ukládání dat používaných k trénování modelů pomocí Azure Machine Learning. 

Pokud chcete používat Data Lake Storage Gen 2 uvnitř virtuální sítě vašeho pracovního prostoru Azure Machine Learning, použijte následující kroky:

1. Vytvořte účet Azure Data Lake Storage gen 2. Další informace najdete [v tématu Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Pomocí kroků 2-4 v předchozí části [použijte účet úložiště pro pracovní prostor](#use-a-storage-account-for-your-workspace), chcete-li účet umístit do virtuální sítě.

Při použití Azure Machine Learning s Úložiště dat Lake Gen 2 uvnitř virtuální sítě, použijte následující pokyny:

* Pokud pomocí sady __SDK vytvoříte datovou sadu__a systém s kódem není `validate=False` ve virtuální __síti__, použijte parametr. Tento parametr přeskočí ověření, které se nezdaří, pokud systém není ve stejné virtuální síti jako účet úložiště. Další informace naleznete v [metodě from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* Při použití Azure Machine Learning Compute Instance nebo výpočetního clusteru k trénování modelu pomocí datové sady musí být ve stejné virtuální síti jako účet úložiště.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Použití instance trezoru klíčů s pracovním prostorem

Instance trezoru klíčů, která je přidružená k pracovnímu prostoru, používá Azure Machine Learning k ukládání následujících přihlašovacích údajů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla do instancí úložiště kontejnerů Azure
* Připojovací řetězce k úložištím dat

Pokud chcete používat možnosti experimentování Azure Machine Learning s Azure Key Vault za virtuální sítí, použijte následující kroky:

1. Přejděte do trezoru klíčů, který je přidružen k pracovníploše.

   [![Trezor klíčů, který je přidružený k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stránce **Trezor klíčů** vyberte v levém podokně brány firewall a __virtuální sítě__.

   ![Oddíl Brány firewall a virtuální sítě v podokně Trezor klíčů](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stránce __Brány firewall a virtuální sítě__ proveďte následující akce:
    - V části __Povolit přístup z__vyberte __Vybrané sítě__.
    - V části __Virtuální sítě__vyberte Přidat existující __virtuální sítě__ a přidejte virtuální síť, ve které se nacházejí vaše experimentovací výpočty.
    - V části __Povolit důvěryhodným službám společnosti Microsoft obejít tuto bránu firewall__vyberte __ano__.

   [![Oddíl Brány firewall a virtuální sítě v podokně Trezor klíčů](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Použití výpočetního výkonu strojového učení

Pokud chcete použít výpočetní instanci Azure Machine Learning nebo výpočetní cluster ve virtuální síti, musí být splněny následující požadavky na síť:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Podsíť určená pro výpočetní instanci nebo cluster musí mít dostatek nepřiřazených IP adres, aby se přizpůsobila počtu cílových virtuálních počítačů. Pokud podsíť nemá dostatek nepřiřazených ADRES IP, bude částečně přidělen výpočetní cluster.
> * Zkontrolujte, zda vaše zásady zabezpečení nebo zámky na předplatné virtuální sítě nebo skupiny prostředků omezit oprávnění ke správě virtuální sítě. Pokud máte v plánu zabezpečit virtuální síť omezením provozu, nechte některé porty otevřené pro výpočetní službu. Další informace naleznete v části [Požadované porty.](#mlcports)
> * Pokud se chystáte umístit více výpočetních instancí nebo clusterů do jedné virtuální sítě, možná budete muset požádat o zvýšení kvóty pro jeden nebo více prostředků.
> * Pokud jsou účty úložiště Azure pro pracovní prostor také zabezpečené ve virtuální síti, musí být ve stejné virtuální síti jako výpočetní instance nebo clusterAzure Machine Learning. 

> [!TIP]
> Výpočetní instance machine learningu nebo cluster automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť. Pro každou výpočetní instanci nebo cluster služba přiděluje následující prostředky:
> 
> * Jedna skupina zabezpečení sítě
> * Jedna veřejná IP adresa
> * Jeden odvykač zatížení
> 
> Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) předplatného.


### <a name="required-ports"></a><a id="mlcports"></a>Požadované porty

Machine Learning Compute aktuálně používá službu Azure Batch k zřizování virtuálních počítačů v zadané virtuální síti. Podsíť musí umožňovat příchozí komunikaci ze služby Batch. Tuto komunikaci použijete k plánování spuštění na výpočetních uzlech Machine Learning a ke komunikaci s Azure Storage a dalšími prostředky. Služba Batch přidává skupiny zabezpečení sítě (NSG) na úrovni síťových rozhraní , které jsou připojeny k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí přenosy Protokolu TCP na portech 29876 a 29877 z __výrobního čísla__ __BatchNodeManagement__.

    ![Příchozí pravidlo, které používá značku služby BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Nepovinné) Příchozí přenos protokolu TCP na portu 22 pro povolení vzdáleného přístupu. Tento port použijte pouze v případě, že se chcete připojit pomocí protokolu SSH ve veřejné IP adrese.

- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

- Pro výpočetní instance příchozí přenosy TCP na portu 44224 z __výrobního čísla__ __AzureMachineLearning__.

Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud skupina nsg blokuje komunikaci s výpočetními uzly, výpočetní služba nastaví stav výpočetních uzlů na nepoužitelný.

Není nutné zadávat skupiny zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupiny zabezpečení sítě. Pokud však zadaná podsíť přidružená skupiny zabezpečení sítě nebo brána firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení, jak bylo uvedeno výše.

Konfigurace pravidla nsg na webu Azure portal se zobrazí na následujících obrázcích:

[![Příchozí pravidla nsg pro výpočetní výkon strojového učení](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Odchozí pravidla nsg pro výpočetní výkon strojového učení](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup virtuální sítě, použijte následující kroky:

- Odepřít odchozí připojení k internetu pomocí pravidel sítě zabezpečení sítě.

- U __instance výpočetního prostředí__ nebo __výpočetního clusteru__omezte odchozí provoz na následující položky:
   - Azure Storage pomocí __výrobního tagu__ __Storage.RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Container Registry, pomocí __service tag__ __azurecontainerregistru.název_regionu__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Machine Learning pomocí __servisního tagu__ __AzureMachineLearning__
   - Správce prostředků Azure pomocí __výrobního tagu__ __AzureResourceManager__
   - Azure Active Directory pomocí __servisní značky__ __AzureActiveDirectory__

Konfigurace pravidla nsg na webu Azure portal se zobrazí na následujícím obrázku:

[![Odchozí pravidla nsg pro výpočetní výkon strojového učení](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Pokud plánujete používat výchozí imitace Dockeru poskytované společností Microsoft a povolit závislosti spravované uživatelem, musíte také použít výrobní číslo __microsoftcontainerregistry.Region_Name__ (například MicrosoftContainerRegistry.EastUS). __Service Tag__
>
> Tato konfigurace je potřebná, pokud máte kód podobný následujícím úryvkům jako součást trénovacích skriptů:
>
> __RunConfig školení__
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
> __Školení odhadu__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelové propojení

Pokud používáte vynucené tunelové propojení s výpočetními strojovými učeními, přidejte [uživatelem definované trasy (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředek.

* Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch v oblasti, kde existují vaše prostředky. Tyto udrumožňují službě Batch komunikovat s výpočetními uzly pro plánování úkolů. Chcete-li získat seznam IP adres služby Batch, použijte jednu z následujících metod:

    * Stáhněte si [rozsahy IP azure a značky služeb](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v souboru `BatchNodeManagement.<region>`, kde `<region>` je vaše oblast Azure.

    * Ke stažení informací použijte [rozhraní příkazového příkazové konto Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Následující příklad stáhne informace o adrese IP a odfiltruje informace pro oblast VÝCHODNÍ USA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Odchozí provoz do Služby Azure Storage nesmí blokovat místní síťové zařízení. Konkrétně adresy URL jsou ve `<account>.table.core.windows.net`tvaru `<account>.queue.core.windows.net`, `<account>.blob.core.windows.net`a .

Když přidáte UDR, definujte postup pro každou související předponu IP dávky a nastavte __další typ směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR na webu Azure Portal:

![Příklad udr pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

Další informace najdete [v tématu Vytvoření fondu Azure Batch ve virtuální síti](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Vytvoření výpočetního clusteru ve virtuální síti

Chcete-li vytvořit výpočetní cluster Machine Learning Compute, použijte následující kroky:

1. Přihlaste se do [centra Azure Machine Learning a](https://ml.azure.com/)vyberte předplatné a pracovní prostor.

1. Vlevo vyberte __Vypočítat.__

1. Vyberte __tréninkové clustery__ ze středu __+__ a pak vyberte .

1. V dialogovém okně __Nový trénovací cluster__ rozbalte část __Upřesnit nastavení.__

1. Chcete-li nakonfigurovat tento výpočetní prostředek tak, aby používal virtuální síť, proveďte v části __Konfigurace virtuální sítě__ následující akce:

    1. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.
    1. V rozevíracím seznamu __Virtuální síť__ vyberte virtuální síť, která podsíť obsahuje.
    1. V rozevíracím seznamu __Podsíť__ vyberte podsíť, kterou chcete použít.

   ![Nastavení virtuální sítě pro machine learning compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Výpočetní cluster Machine Learning můžete také vytvořit pomocí sady Azure Machine Learning SDK. Následující kód vytvoří nový cluster Machine `default` Learning Compute v podsíti virtuální sítě s názvem `mynetwork`:

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

Po dokončení procesu vytváření trénování modelu pomocí clusteru v experimentu. Další informace najdete [v tématu Výběr a použití výpočetního cíle pro školení](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Použití datových cihel Azure

Pokud chcete používat Azure Databricks ve virtuální síti s vaším pracovním prostorem, musí být splněny následující požadavky:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Pokud jsou účty úložiště Azure pro pracovní prostor také zabezpečené ve virtuální síti, musí být ve stejné virtuální síti jako cluster Azure Databricks.
> * Kromě __podsítí databricks-private__ a __databricks-public__ používanou azure databricks je také __vyžadována výchozí__ podsíť vytvořená pro virtuální síť.

Konkrétní informace o používání Azure Databricks s virtuální sítí najdete [v tématu Nasazení Datových cihel Azure ve virtuální síti Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Použití virtuálního počítače nebo clusteru HDInsight

> [!IMPORTANT]
> Azure Machine Learning podporuje jenom virtuální počítače, na kterých běží Ubuntu.

Pokud chcete používat virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s pracovním prostorem, použijte následující kroky:

1. Vytvořte cluster virtuálního počítače nebo HDInsight pomocí portálu Azure nebo rozhraní příkazového příkazu Konstatování Azure a vložte cluster do virtuální sítě Azure. Další informace najdete v těchto článcích:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšíření HDInsightu pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Chcete-li povolit Azure Machine Learning ke komunikaci s portem SSH na virtuálním počítači nebo clusteru, nakonfigurujte zdrojovou položku pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. Chcete-li povolit provoz z tohoto zdroje, proveďte následující akce:

    * V rozevíracím seznamu __Zdroj__ vyberte __Číslo servisu__.

    * V rozevíracím seznamu __Zdrojová značka služby__ vyberte __AzureMachineLearning__.

    * V rozevíracím seznamu __Rozsahy zdrojových portů__ vyberte položku __*__.

    * V rozevíracím seznamu __Cíl__ vyberte __Any__.

    * V rozevíracím seznamu __Rozsahy cílových portů__ vyberte __možnost 22__.

    * V části __Protokol__vyberte __any__.

    * V části __Akce__vyberte __Povolit__.

   ![Příchozí pravidla pro experimentování na clusteru virtuálních zařízení nebo HDInsight v rámci virtuální sítě](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace naleznete v výchozích pravidlech zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup virtuální sítě, přečtěte si článek Omezit odchozí připojení z části [virtuální sítě.](#limiting-outbound-from-vnet)

1. Připojte cluster virtuálního počítače nebo HDInsight do pracovního prostoru Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro trénování modelu](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Použití služby Azure Kubernetes (AKS)

Pokud chcete přidat AKS ve virtuální síti do pracovního prostoru, použijte následující kroky:

> [!IMPORTANT]
> Než začnete s následujícím postupem, postupujte podle požadavků v [konfiguraci pokročilých sítí ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) a naplánujte adresování IP adres pro váš cluster.
>
> Instance AKS a virtuální síť Azure musí být ve stejné oblasti. Pokud zabezpečíte účty úložiště Azure používané pracovníprostor ve virtuální síti, musí být ve stejné virtuální síti jako instance AKS.

> [!WARNING]
> Azure Machine Learning nepodporuje pomocí služby Azure Kubernetes, která má povolenou privátní vazbu.

1. Přihlaste se do [centra Azure Machine Learning a](https://ml.azure.com/)vyberte předplatné a pracovní prostor.

1. Vlevo vyberte __Vypočítat.__

1. Vyberte __Odvozovat clustery__ ze středu __+__ a pak vyberte .

1. V dialogovém okně __Nový odvozený cluster__ vyberte v části __Konfigurace sítě__možnost __Upřesnit__ .

1. Chcete-li nakonfigurovat tento výpočetní prostředek tak, aby používal virtuální síť, proveďte následující akce:

    1. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.
    1. V rozevíracím seznamu __Virtuální síť__ vyberte virtuální síť, která podsíť obsahuje.
    1. V rozevíracím seznamu __Podsíť__ vyberte podsíť.
    1. Do pole __Rozsah adres služby Kubernetes__ zadejte rozsah adres služby Kubernetes. Tento rozsah adres používá rozsah IP adres zápisu beztřídní mezidoménami (CIDR) k definování IP adres, které jsou pro cluster k dispozici. Nesmí se překrývat s rozsahy IP podsítě (například 10.0.0.0/16).
    1. Do pole __IP adresy služby Kubernetes__ zadejte IP adresu služby Kubernetes DNS. Tato IP adresa je přiřazena službě Kubernetes DNS. Musí být v rozsahu adres služby Kubernetes (například 10.0.0.10).
    1. Do pole __Adresa mostu Dockeru__ zadejte adresu mostu Dockeru. Tato IP adresa je přiřazena dockeru Bridge. Nesmí být v žádné rozsahy IP podsítě nebo rozsah adres služby Kubernetes (například 172.17.0.1/16).

   ![Azure Machine Learning: Nastavení výpočetní virtuální sítě Machine Learning Compute](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ujistěte se, že skupina nsg, která řídí virtuální síť má příchozí pravidlo zabezpečení povoleno pro koncový bod vyhodnocování tak, aby jej lze volat mimo virtuální síť.
   > [!IMPORTANT]
   > Ponechte výchozí odchozí pravidla pro soubor zabezpečení zabezpečení zabezpečení zabezpečení. Další informace naleznete v výchozích pravidlech zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Pravidlo příchozího zabezpečení](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Azure Machine Learning SDK můžete taky použít k přidání služby Azure Kubernetes ve virtuální síti. Pokud již máte cluster AKS ve virtuální síti, připojte jej k pracovnímu prostoru, jak je popsáno v [jak nasadit do AKS](how-to-deploy-and-where.md). Následující kód vytvoří novou instanci `default` AKS v podsíti virtuální sítě s názvem `mynetwork`:

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

Po dokončení procesu vytváření můžete spustit odvození nebo vyhodnocování modelu v clusteru AKS za virtuální sítí. Další informace naleznete v tématu [Jak nasadit do AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Použití privátních IP služeb se službou Azure Kubernetes

Ve výchozím nastavení je nasazením AKS přiřazena veřejná IP adresa. Při použití AKS uvnitř virtuální sítě, můžete použít privátní IP adresu místo. Privátní IP adresy jsou přístupné pouze zevnitř virtuální sítě nebo propojených sítí.

Privátní IP adresa je povolena konfigurací služby AKS tak, aby používala _interní systém vyrovnávání zatížení_. 

> [!IMPORTANT]
> Při vytváření clusteru služby Azure Kubernetes nelze povolit privátní IP adresu. Musí být povolena jako aktualizace existujícího clusteru.

Následující fragment kódu ukazuje, jak **vytvořit nový cluster AKS**a potom jej aktualizovat tak, aby používal privátní správce zatížení IP/interní:

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
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
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

Obsah souboru, `body.json` na který odkazuje příkaz, je podobný následujícímu dokumentu JSON:

```json
{ 
    "location": "<region>", 
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
> V současné době nelze konfigurovat vyrovnávání zatížení při provádění operace __připojení__ na existující cluster. Nejprve je nutné připojit cluster a potom provést operaci aktualizace pro změnu systému vyrovnávání zatížení.

Další informace o použití interního systému pro vyrovnávání zatížení s AKS najdete [v tématu Použití interního vyvažovače zatížení se službou Azure Kubernetes Service](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Použití brány Azure Firewall

Při používání brány Azure Firewall je nutné nakonfigurovat síťové pravidlo, které umožní provoz na a z následujících adres:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Při přidávání pravidla nastavte __protokol__ na libovolné `*`a porty na .

Další informace o konfiguraci síťového pravidla najdete v tématu [Nasazení a konfigurace brány Azure Firewall](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="use-azure-container-registry"></a>Použití služby Azure Container Registry

> [!IMPORTANT]
> Azure Container Registry (ACR) lze umístit do virtuální sítě, ale musíte splnit následující požadavky:
>
> * Váš pracovní prostor Azure Machine Learning musí být edice Enterprise. Informace o inovaci naleznete v [tématu Upgrade na edici Enterprise .](how-to-manage-workspace.md#upgrade)
> * Váš registr kontejnerů Azure musí být verze Premium . Další informace o upgradu naleznete v [tématu Změna skum](/azure/container-registry/container-registry-skus#changing-skus).
> * Váš Registr kontejnerů Azure musí být ve stejné virtuální síti a podsíti jako účet úložiště a výpočetní cíle používané pro školení nebo odvození.
> * Pracovní prostor Azure Machine Learning musí obsahovat [výpočetní cluster Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).
>
>     Když acr je za virtuální síť, Azure Machine Learning nelze použít k přímému vytváření ibi Dockeru. Místo toho výpočetní cluster se používá k sestavení bitové kopie.

1. Pokud chcete najít název registru kontejnerů Azure pro váš pracovní prostor, použijte jednu z následujících metod:

    __portál Azure__

    V části přehled vašeho pracovního prostoru hodnota __registru__ odkazy na Azure Container Registry.

    ![Azure Container Registry pro pracovní prostor](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __Azure CLI__

    Pokud jste [nainstalovali rozšíření Machine Learning pro Azure CLI](reference-azure-machine-learning-cli.md), můžete použít `az ml workspace show` příkaz k zobrazení informací o pracovním prostoru.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Tento příkaz vrátí hodnotu podobnou . `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` Poslední část řetězce je název registru kontejnerů Azure pro pracovní prostor.

1. Chcete-li omezit přístup k virtuální síti, použijte postup v části [Konfigurace přístupu k síti pro registr](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Při přidávání virtuální sítě vyberte virtuální síť a podsíť pro vaše prostředky Azure Machine Learning.

1. Azure Machine Learning Python SDK slouží ke konfiguraci výpočetního clusteru pro vytváření ibi dockerů. Následující fragment kódu ukazuje, jak to provést:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Váš účet úložiště, výpočetní cluster a Azure Container Registry musí být ve stejné podsíti virtuální sítě.
    
    Další informace naleznete v odkazu na metodu [update().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none-)

1. Pokud používáte privátní odkaz pro pracovní prostor Azure Machine Learning a umístit registr kontejnerů Azure pro váš pracovní prostor ve virtuální síti, musíte také použít následující šablonu Azure Resource Manager. Tato šablona umožňuje vašemu pracovnímu prostoru komunikovat s ACR přes soukromé propojení.

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

## <a name="next-steps"></a>Další kroky

* [Nastavení prostředí trénování](how-to-set-up-training-targets.md)
* [Kam se mají modely nasadit](how-to-deploy-and-where.md)
* [Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
