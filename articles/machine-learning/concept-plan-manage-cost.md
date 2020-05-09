---
title: Plánování a správa nákladů
titleSuffix: Azure Machine Learning
description: Plánování a Správa nákladů pro Azure Machine Learning s analýzou nákladů v Azure Portal. Když sestavíte modely strojového učení, získáte další informace – tipy pro úsporu nákladů, které vám umožní snížit náklady.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 2161a9e4460526113aaf89609b72250a09fc6af3
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891205"
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

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. 

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Odhadované náklady

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady ještě předtím, než vytvoříte prostředky v účtu Azure Machine Learning. Na levé straně vyberte **AI + Machine Learning**a pak vyberte **Azure Machine Learning** a začněte.  

Následující snímek obrazovky ukazuje odhad nákladů pomocí kalkulačky:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů v programu Azure kalkulačka":::

Když do svého pracovního prostoru přidáte nové prostředky, vraťte se do této kalkulačky a přidejte do něj stejný prostředek, abyste mohli aktualizovat odhadované náklady.

I když je edice Enterprise ve verzi Preview, neexistuje žádný příplatek na ML. Až bude edice Enterprise všeobecně dostupná, bude mít příplatek Machine Learning (za účelem školení a Inferencing).  Další podrobnosti [Azure Machine Learning cenách](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Získat nákladové výstrahy

Vytvářejte [rozpočty](../cost-management/tutorial-acm-create-budgets.md) , které budou spravovat náklady, a vytvářejte [výstrahy](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) , které automaticky upozorňují na nepravidelnosti útraty a rizika přetrávit. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. Rozpočty a výstrahy ale můžou mít omezené funkce pro správu jednotlivých nákladů na službu Azure, protože jsou navržené tak, aby sledovaly náklady na vyšší úrovni.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků s Azure Machine Learning se účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek žádosti. Jakmile Azure Machine Learning začne, náklady budou účtovány. Tyto náklady si prohlédněte v podokně [Analýza nákladů](../cost-management/quick-acm-cost-analysis.md) v Azure Portal.

Zobrazení nákladů v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního, předchozího měsíce a roku. Prohlédněte si také náklady na rozpočty a předpokládané náklady. Přepínání na delší zobrazení v průběhu času vám pomůže identifikovat trendy útraty a zjistit, kde se mohlo vyhovět. Pokud jste vytvořili rozpočty, přečtěte si téma kde byly překročeny.  

Pro Machine Learning se nezobrazí samostatná oblast služby.  Místo toho uvidíte různé prostředky, které jste přidali do vašich Machine Learning pracovních prostorů.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Použití Azure Machine Learning Compute Cluster (AmlCompute)

S neustále se měnícími se daty potřebujete rychlé a zjednodušené školení modelů a rekurze, aby bylo možné zajistit správné modely. Průběžné školení však přináší náklady, zejména pro modely obsáhlého učení na GPU. 

Azure Machine Learning uživatelé můžou použít cluster Managed Azure Machine Learning COMPUTE, označovaný také jako AmlCompute. AmlCompute podporuje celou řadu možností GPU a CPU. AmlCompute se interně hostuje v rámci vašeho předplatného Azure Machine Learning, ale poskytuje stejné zabezpečení na podnikové úrovni, dodržování předpisů a zásad správného řízení ve službě Azure IaaS Cloud Scale.

Vzhledem k tomu, že se tyto výpočetní fondy nacházejí v infrastruktuře Azure IaaS, můžete nasazovat, škálovat a spravovat školení se stejnými požadavky na zabezpečení a dodržování předpisů jako v ostatních částech vaší infrastruktury.  Tato nasazení se objeví ve vašem předplatném a řídí se pravidly zásad správného řízení. Přečtěte si další informace o [Azure Machine Learning COMPUTE](how-to-set-up-training-targets.md#amlcompute).

## <a name="configure-training-clusters-for-autoscaling"></a>Konfigurace školicích clusterů pro automatické škálování

Automatické škálování clusterů na základě požadavků vašich úloh pomáhá snižovat náklady, takže budete používat jenom to, co potřebujete. Clustery AmlCompute jsou navržené tak, aby se dynamicky škálují na základě požadavků vašich úloh. Cluster se dá škálovat až na maximální počet zřízených uzlů a v rámci kvóty určené pro předplatné. Po dokončení každého spuštění bude cluster vydávat uzly a automatické škálování podle stanoveného minimálního počtu uzlů.

Kromě nastavení minimálního a maximálního počtu uzlů můžete upravit dobu nečinnosti uzlu, než se horizontální navýšení kapacity rozvýší. Ve výchozím nastavení je doba nečinnosti, než bude horizontální navýšení kapacity nastavená na 120 sekund.

+ Pokud provedete méně iterativní experimenty, zkraťte tuto dobu na úsporu nákladů. 
+ Pokud provádíte vysoce iterativní experimenty při vývoji a testování, možná ho budete muset napravit, abyste za každou změnu v školicím skriptu nebo prostředí neplatíte za konstantní škálování nahoru a dolů.

Clustery AmlCompute se dají nakonfigurovat pro vaše měnící se požadavky na úlohy v Azure Portal pomocí [třídy AMLCOMPUTE SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) [AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)s [rozhraními REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Nastavení kvót u prostředků

Podobně jako u jiných výpočetních prostředků Azure zahrnuje AmlCompute s vlastní [kvótou (nebo omezenou) konfigurací](how-to-manage-quotas.md#azure-machine-learning-compute). Tato kvóta vychází z řady virtuálních počítačů (například Dv2 Series, NCv3 Series) a pro každé předplatné se liší podle oblasti. Odběry začínají malým výchozím nastavením, které vám pomohou, ale pomocí tohoto nastavení můžete řídit množství Amlcompute prostředků, které mají být v předplatném k dispozici. 

Také nakonfigurujte [kvótu na úrovni pracovního prostoru podle rodiny virtuálních počítačů](how-to-manage-quotas.md#workspace-level-quota)pro každý pracovní prostor v rámci předplatného. To vám umožní podrobnější kontrolu nad náklady, které může každý pracovní prostor způsobit, a omezit určité rodiny virtuálních počítačů. 

Pokud chcete nastavit kvóty na úrovni pracovního prostoru, začněte v [Azure Portal](https://portal.azure.com).  V předplatném vyberte libovolný pracovní prostor a v levém podokně vyberte **využití a kvóty** . Pak vyberte kartu **Konfigurace kvót** pro zobrazení kvót. K nastavení této kvóty potřebujete oprávnění v oboru předplatného, protože se jedná o nastavení, které ovlivňuje více pracovních prostorů.

## <a name="set-run-auto-termination-policies"></a>Nastavení zásad automatického ukončení pro spuštění 

Nakonfigurujte své školicí běhy tak, aby se omezily jejich doba trvání, nebo je můžete kdykoli ukončit v případě určitých podmínek zvlášť, zejména pokud používáte integrované ladění parametrů Azure Machine Learning nebo automatizované Machine Learning možnosti. 

Tady je několik možností, které máte:
* Definujte parametr s názvem `max_run_duration_seconds` v RunConfiguration k řízení maximální doby, po kterou se může běh na výpočetním prostředí rozhodnout (buď místní, nebo vzdálený cloudový výpočet).
* V případě [ladění pomocí parametrů](how-to-tune-hyperparameters.md#early-termination)Definujte zásadu prvotního ukončení ze zásad Bandit, medián zastavení zásad nebo zásadu výběru zkrácení. Kromě toho také používejte parametry, jako je `max_total_runs` nebo `max_duration_minutes` , k dalšímu řízení různých Sweep parametrů.
* V případě [automatizovaného strojového učení](how-to-configure-auto-train.md#exit)nastavte podobné zásady ukončení `enable_early_stopping` pomocí příznaku. K řízení maximální doby trvání `iteration_timeout_minutes` běhu `experiment_timeout_minutes` nebo pro celý experiment použijte také vlastnosti, jako je a.

## <a name="use-low-priority-vms"></a>Použití virtuálních počítačů s nízkou prioritou

Azure umožňuje používat nadbytečnou nevyužitou kapacitu jako virtuální počítače s nízkou prioritou napříč sadami Virtual Machine Scale Sets, službou Batch a službou Machine Learning. Tato přidělení jsou předem emptible, ale v porovnání s vyhrazenými virtuálními počítači se přidávají za nižší cenu. Obecně doporučujeme používat virtuální počítače s nízkou prioritou pro úlohy služby Batch nebo místa, kde jsou přerušení obnovitelné buď prostřednictvím opětovného odeslání (pro Batch Inferencing), nebo prostřednictvím restartu (pro školení obsáhlého učení s kontrolními body).

Virtuální počítače s nízkou prioritou mají jedinou kvótu oddělenou od hodnoty vyhrazené kvóty, kterou používá rodina virtuálních počítačů. Přečtěte si [Další informace o kvótách AmlCompute](how-to-manage-quotas.md).

Prioritu virtuálního počítače nastavíte některým z těchto způsobů:

* V nástroji Studio při vytváření virtuálního počítače vyberte možnost **Nízká priorita** .

* V sadě Python SDK nastavte `vm_priority` atribut v konfiguraci zřizování.  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* Pomocí rozhraní příkazového řádku nastavte `vm-priority`:

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 Virtuální počítače s nízkou prioritou nefungují pro výpočetní instance, protože potřebují podporovat interaktivní poznámkové bloky. 

## <a name="use-reserved-instances"></a>Použití rezervovaných instancí

Rezervovaná instance virtuálního počítače Azure poskytuje další způsob, jak získat obrovský úsporu výpočetních prostředků tím, že se potvrzování na jednoleté nebo tříleté období. Tyto slevy dosahují až 72% sazeb za průběžné platby a vztahují se přímo na váš měsíční účet Azure.

Azure Machine Learning COMPUTE podporuje rezervované instance, které jsou v podstatě. Takže pokud jste si zakoupili rezervovanou instanci na jeden nebo tři roky, automaticky použijeme tuto slevu rezervované instance na spravované výpočetní prostředky, která se používá v rámci Azure Machine Learning bez nutnosti dalšího nastavení od konce.


## <a name="next-steps"></a>Další kroky

Další informace:
* [Správa a zvýšení kvót prostředků](how-to-manage-quotas.md)
* [Správa nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* [Azure Machine Learning výpočetní](how-to-set-up-training-targets.md#amlcompute)prostředky.
