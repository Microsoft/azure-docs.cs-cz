---
title: Plánování a správa nákladů
titleSuffix: Azure Machine Learning
description: Plánování a Správa nákladů pro Azure Machine Learning s analýzou nákladů v Azure Portal. Přečtěte si další informace a tipy pro úspory nákladů, které vám umožní snížit náklady při sestavování modelů ML.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 6f4a8e4b8cbc35dca9f48bbec84e9023e82f6a84
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501631"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Plánování a Správa nákladů na Azure Machine Learning

Tento článek popisuje, jak plánovat a spravovat náklady na Azure Machine Learning. Nejprve pomocí cenové kalkulačky Azure naplánujte náklady před přidáním jakýchkoli prostředků. Pak při přidávání prostředků Azure si Projděte odhadované náklady. Nakonec použijte při práci s modelem spravované Azure Machine Learning výpočetní clustery, které vám umožní ušetřit náklady.

Po zahájení práce s prostředky Azure Machine Learning použijte funkce pro správu nákladů a nastavte rozpočty a sledujte náklady. Přečtěte si také předpovědi předpokládaných nákladů a Identifikujte trendy útraty a Identifikujte oblasti, kde můžete chtít pracovat.

Pochopení, že náklady na Azure Machine Learning jsou jenom částí měsíčních nákladů na faktuře Azure. Pokud používáte další služby Azure, účtuje se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran. Tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure Machine Learning. Až budete obeznámeni se správou nákladů na Azure Machine Learning, použijte podobné metody pro správu nákladů na všechny služby Azure používané ve vašem předplatném.

Při výuce modelů strojového učení použijte spravované Azure Machine Learning výpočetní clustery, abyste mohli využít více tipů pro úsporu nákladů:

* Konfigurace školicích clusterů pro automatické škálování
* Nastavení kvót pro vaše předplatné a pracovní prostory
* Nastavení zásad ukončení pro váš školicí běh
* Použití virtuálních počítačů s nízkou prioritou
* Použití rezervované instance virtuálního počítače Azure

## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. 

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Odhad nákladů před použitím Azure Machine Learning

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete odhadnout náklady ještě předtím, než vytvoříte prostředky v účtu Azure Machine Learning. Na levé straně vyberte **AI + Machine Learning** a pak vyberte **Azure Machine Learning** a začněte.  

Následující snímek obrazovky ukazuje odhad nákladů pomocí kalkulačky:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů v programu Azure kalkulačka":::

Když do svého pracovního prostoru přidáte nové prostředky, vraťte se do této kalkulačky a přidejte do něj stejný prostředek, abyste mohli aktualizovat odhadované náklady.

Další informace najdete v tématu [Azure Machine Learning ceny](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Princip úplného fakturačního modelu pro Azure Machine Learning

Azure Machine Learning běží na infrastruktuře Azure, která při nasazení nového prostředku nasazuje náklady společně s Azure Machine Learning. Je důležité pochopit, že další infrastruktura by mohla navýšit náklady. Pokud provedete změny nasazených prostředků, musíte tyto náklady spravovat. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Náklady, které obvykle nabíhají Azure Machine Learning

Když vytváříte prostředky pro pracovní prostor Azure Machine Learning, vytvoří se taky prostředky pro jiné služby Azure. Jsou to tyto:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Účet Basic
* [Azure Block BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (pro obecné účely V1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Náklady se můžou po odstranění prostředku časově rozlišit.

Když odstraníte pracovní prostor Azure Machine Learning v Azure Portal nebo pomocí rozhraní příkazového řádku Azure CLI, dál existují následující prostředky. Nadále se účtují náklady, dokud je neodstraníte.

* Azure Container Registry
* Blob Storage blokování Azure
* Key Vault
* Application Insights

Pokud chcete odstranit pracovní prostor spolu s těmito závislými prostředky, použijte sadu SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Pokud vytvoříte Azure Kubernetes Service (AKS) v pracovním prostoru nebo pokud k pracovnímu prostoru připojíte nějaké výpočetní prostředky, musíte je odstranit samostatně v [Azure Portal](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Použití předplacených kreditů Azure s Azure Machine Learning

Za poplatky za Azure Machine Learning můžete platit pomocí kreditu služby Azure (dříve nazývaného peněžního závazku). Předplatné Azure ale nemůžete použít k platbám za poplatky za produkty a služby třetích stran, včetně těch, které jsou Azure Marketplace.


## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Například finanční týmy mohou analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Další způsoby správy a snížení nákladů na Azure Machine Learning

Tyto tipy použijte k získání nákladů na výpočetní prostředky machine learningu.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Použití Azure Machine Learning Compute Cluster (AmlCompute)

S neustále se měnícími se daty potřebujete rychlé a zjednodušené školení modelů a rekurze, aby bylo možné zajistit správné modely. Průběžné školení však přináší náklady, zejména pro modely obsáhlého učení na GPU. 

Azure Machine Learning uživatelé můžou použít cluster Managed Azure Machine Learning COMPUTE, označovaný také jako AmlCompute. AmlCompute podporuje celou řadu možností GPU a CPU. AmlCompute je interně hostovaný jménem vašeho předplatného, a to Azure Machine Learning. Přináší stejné zabezpečení na podnikové úrovni, dodržování předpisů a zásady správného řízení ve službě Azure IaaS Cloud Scale.

Vzhledem k tomu, že se tyto výpočetní fondy nacházejí v infrastruktuře Azure IaaS, můžete nasazovat, škálovat a spravovat školení se stejnými požadavky na zabezpečení a dodržování předpisů jako v ostatních částech vaší infrastruktury.  Tato nasazení se objeví ve vašem předplatném a řídí se pravidly zásad správného řízení. Přečtěte si další informace o [Azure Machine Learning COMPUTE](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Konfigurace školicích clusterů pro automatické škálování

Automatické škálování clusterů na základě požadavků vašich úloh pomáhá snižovat náklady, takže budete používat jenom to, co potřebujete.

Clustery AmlCompute jsou navržené tak, aby dynamicky škálovat na základě vašich úloh. Cluster se dá škálovat až na maximální počet konfigurovaných uzlů. Po dokončení každého spuštění bude cluster vydávat uzly a škálovat je podle nakonfigurovaného minimálního počtu uzlů.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Můžete také nakonfigurovat, jak dlouho je uzel nečinný, než se horizontální navýšení kapacity zmenší. Ve výchozím nastavení je doba nečinnosti, než bude horizontální navýšení kapacity nastavená na 120 sekund.

+ Pokud provedete méně iterativní experimenty, zkraťte tuto dobu na úsporu nákladů.
+ Pokud provádíte vysoce iterativní experimenty při vývoji a testování, možná budete muset čas prodloužit, abyste za každou změnu v školicím skriptu nebo prostředí nemuseli platit za konstantního škálování.

Clustery AmlCompute se dají nakonfigurovat pro vaše měnící se požadavky na úlohy v Azure Portal pomocí [třídy AMLCOMPUTE SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)s [rozhraními REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Nastavení kvót u prostředků

AmlCompute obsahuje [konfiguraci kvóty (nebo omezení)](how-to-manage-quotas.md#azure-machine-learning-compute). Tato kvóta vychází z řady virtuálních počítačů (například Dv2 Series, NCv3 Series) a pro každé předplatné se liší podle oblasti. Odběry začínají malým výchozím nastavením, které vám pomohou, ale pomocí tohoto nastavení můžete řídit množství Amlcompute prostředků, které mají být v předplatném k dispozici. 

Také nakonfigurujte [kvótu na úrovni pracovního prostoru podle rodiny virtuálních počítačů](how-to-manage-quotas.md#workspace-level-quotas)pro každý pracovní prostor v rámci předplatného. Díky tomu budete mít podrobnější kontrolu nad náklady, které může každý pracovní prostor způsobit a omezit určité rodiny virtuálních počítačů. 

Pokud chcete nastavit kvóty na úrovni pracovního prostoru, začněte v [Azure Portal](https://portal.azure.com).  V předplatném vyberte libovolný pracovní prostor a v levém podokně vyberte **využití a kvóty** . Pak vyberte kartu **Konfigurace kvót** pro zobrazení kvót. Pro nastavení kvóty potřebujete oprávnění v oboru předplatného, protože se jedná o nastavení, které ovlivňuje více pracovních prostorů.

### <a name="set-run-autotermination-policies"></a>Nastavit zásady autoukončovacího běhu 

V některých případech byste měli nakonfigurovat své školicí běhy, aby se omezila doba jejich trvání nebo jejich ukončení brzy. Například při použití integrovaného ladění parametrů Azure Machine Learning nebo automatizovaného strojového učení.

Tady je několik možností, které máte:
* Definujte parametr s názvem `max_run_duration_seconds` v RunConfiguration k řízení maximální doby, po kterou se může běh na výpočetním prostředí rozhodnout (buď místní, nebo vzdálený cloudový výpočet).
* V případě [ladění pomocí parametrů](how-to-tune-hyperparameters.md#early-termination)Definujte zásadu prvotního ukončení ze zásad Bandit, pozastavené zásady nebo zásadu výběru zkrácení. Chcete-li dále řídit Sweep parametrů, použijte parametry jako `max_total_runs` nebo `max_duration_minutes` .
* V případě [automatizovaného strojového učení](how-to-configure-auto-train.md#exit)nastavte podobné zásady ukončení pomocí  `enable_early_stopping` příznaku. K `iteration_timeout_minutes` `experiment_timeout_minutes` řízení maximální doby trvání běhu nebo pro celý experiment použijte také vlastnosti, jako je a.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Použití virtuálních počítačů s nízkou prioritou

Azure umožňuje používat nadbytečnou nevyužitou kapacitu jako Low-Priority virtuálních počítačů napříč sadami škálování virtuálních počítačů, službou Batch a službou Machine Learning. Tato přidělení jsou předem emptible, ale v porovnání s vyhrazenými virtuálními počítači se přidávají za nižší cenu. Obecně doporučujeme používat pro úlohy služby Batch Low-Priority virtuální počítače. Měli byste je také použít, pokud se přerušení budou obnovovat buď prostřednictvím opětovného odeslání (pro Batch Inferencing), nebo prostřednictvím restartu (pro školení s hloubkovým učením s kontrolními body).

Virtuální počítače s Low-Priority mají jedinou kvótu oddělenou od hodnoty vyhrazené kvóty, kterou používá rodina virtuálních počítačů. Přečtěte si [Další informace o kvótách AmlCompute](how-to-manage-quotas.md).

 Low-Priority virtuální počítače nefungují pro výpočetní instance, protože potřebují podporovat interaktivní poznámkové bloky.

### <a name="use-reserved-instances"></a>Použití rezervovaných instancí

Další možností, jak ušetřit peníze na výpočetní prostředky, je rezervovaná instance virtuálního počítače Azure. V rámci této nabídky se potvrdili na jednoleté nebo tříleté období. Tyto slevy dosahují až 72% sazeb za průběžné platby a vztahují se přímo na váš měsíční účet Azure.

Azure Machine Learning COMPUTE podporuje rezervované instance, které jsou v podstatě. Pokud si koupíte rezervovanou instanci na jeden nebo tři roky, automaticky použijeme slevu na vaše Azure Machine Learning spravované výpočetní prostředky.


## <a name="next-steps"></a>Další kroky

- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .