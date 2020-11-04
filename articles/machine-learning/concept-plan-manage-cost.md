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
ms.openlocfilehash: e8554bf4385c7ddad540b8ba807988618fe80b53
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322986"
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

## <a name="estimate-costs"></a>Odhadněte náklady

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady ještě předtím, než vytvoříte prostředky v účtu Azure Machine Learning. Na levé straně vyberte **AI + Machine Learning** a pak vyberte **Azure Machine Learning** a začněte.  

Následující snímek obrazovky ukazuje odhad nákladů pomocí kalkulačky:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů v programu Azure kalkulačka":::

Když do svého pracovního prostoru přidáte nové prostředky, vraťte se do této kalkulačky a přidejte do něj stejný prostředek, abyste mohli aktualizovat odhadované náklady.

Další informace najdete v tématu [Azure Machine Learning ceny](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Získat nákladové výstrahy

Vytvářejte [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md) , které budou spravovat náklady, a vytvářejte [výstrahy](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md) , které automaticky upozorňují na nepravidelnosti útraty a rizika přetrávit. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. Rozpočty a výstrahy ale můžou mít omezené funkce pro správu jednotlivých nákladů na službu Azure, protože jsou navržené tak, aby sledovaly náklady na vyšší úrovni.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků s Azure Machine Learning se účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek žádosti. Jakmile Azure Machine Learning začne, náklady budou účtovány. Tyto náklady si prohlédněte v podokně [Analýza nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md) v Azure Portal.

Náklady můžete zobrazit v grafech a tabulkách v různých časových intervalech. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přepnutím na delší zobrazení v průběhu času můžete identifikovat trendy útraty a zjistit, kde došlo k nadměrnému útratě. Pokud jste vytvořili rozpočty, přečtěte si téma kde byly překročeny.  

Pro Machine Learning se nezobrazí samostatná oblast služby.  Místo toho uvidíte různé prostředky, které jste přidali do vašich Machine Learning pracovních prostorů. 

Všimněte si, že i když pracovní prostor Machine Learning sám nemá přímé náklady, budou se vám účtovat spravované výpočetní prostředky. Proto pokud jste přidali značky do pracovních prostorů, protože na ně neexistují žádné přímé náklady, bude podokno analýza nákladů odpovídat tomuto pracovnímu prostoru. Pro přesné řízení nákladů prostřednictvím značek budete muset označit přidružené výpočetní prostředky.  

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Použití Azure Machine Learning Compute Cluster (AmlCompute)

S neustále se měnícími se daty potřebujete rychlé a zjednodušené školení modelů a rekurze, aby bylo možné zajistit správné modely. Průběžné školení však přináší náklady, zejména pro modely obsáhlého učení na GPU. 

Azure Machine Learning uživatelé můžou použít cluster Managed Azure Machine Learning COMPUTE, označovaný také jako AmlCompute. AmlCompute podporuje celou řadu možností GPU a CPU. AmlCompute je interně hostovaný jménem vašeho předplatného, a to Azure Machine Learning. Přináší stejné zabezpečení na podnikové úrovni, dodržování předpisů a zásady správného řízení ve službě Azure IaaS Cloud Scale.

Vzhledem k tomu, že se tyto výpočetní fondy nacházejí v infrastruktuře Azure IaaS, můžete nasazovat, škálovat a spravovat školení se stejnými požadavky na zabezpečení a dodržování předpisů jako v ostatních částech vaší infrastruktury.  Tato nasazení se objeví ve vašem předplatném a řídí se pravidly zásad správného řízení. Přečtěte si další informace o [Azure Machine Learning COMPUTE](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Konfigurace školicích clusterů pro automatické škálování

Automatické škálování clusterů na základě požadavků vašich úloh pomáhá snižovat náklady, takže budete používat jenom to, co potřebujete.

Clustery AmlCompute jsou navržené tak, aby dynamicky škálovat na základě vašich úloh. Cluster se dá škálovat až na maximální počet konfigurovaných uzlů. Po dokončení každého spuštění bude cluster vydávat uzly a škálovat je podle nakonfigurovaného minimálního počtu uzlů.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Můžete také nakonfigurovat, jak dlouho je uzel nečinný, než se horizontální navýšení kapacity zmenší. Ve výchozím nastavení je doba nečinnosti, než bude horizontální navýšení kapacity nastavená na 120 sekund.

+ Pokud provedete méně iterativní experimenty, zkraťte tuto dobu na úsporu nákladů.
+ Pokud provádíte vysoce iterativní experimenty při vývoji a testování, možná budete muset čas prodloužit, abyste za každou změnu v školicím skriptu nebo prostředí nemuseli platit za konstantního škálování.

Clustery AmlCompute se dají nakonfigurovat pro vaše měnící se požadavky na úlohy v Azure Portal pomocí [třídy AMLCOMPUTE SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)s [rozhraními REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Nastavení kvót u prostředků

AmlCompute obsahuje [konfiguraci kvóty (nebo omezení)](how-to-manage-quotas.md#azure-machine-learning-compute). Tato kvóta vychází z řady virtuálních počítačů (například Dv2 Series, NCv3 Series) a pro každé předplatné se liší podle oblasti. Odběry začínají malým výchozím nastavením, které vám pomohou, ale pomocí tohoto nastavení můžete řídit množství Amlcompute prostředků, které mají být v předplatném k dispozici. 

Také nakonfigurujte [kvótu na úrovni pracovního prostoru podle rodiny virtuálních počítačů](how-to-manage-quotas.md#workspace-level-quotas)pro každý pracovní prostor v rámci předplatného. Díky tomu budete mít podrobnější kontrolu nad náklady, které může každý pracovní prostor způsobit a omezit určité rodiny virtuálních počítačů. 

Pokud chcete nastavit kvóty na úrovni pracovního prostoru, začněte v [Azure Portal](https://portal.azure.com).  V předplatném vyberte libovolný pracovní prostor a v levém podokně vyberte **využití a kvóty** . Pak vyberte kartu **Konfigurace kvót** pro zobrazení kvót. Pro nastavení kvóty potřebujete oprávnění v oboru předplatného, protože se jedná o nastavení, které ovlivňuje více pracovních prostorů.

## <a name="set-run-autotermination-policies"></a>Nastavit zásady autoukončovacího běhu 

V některých případech byste měli nakonfigurovat své školicí běhy, aby se omezila doba jejich trvání nebo jejich ukončení brzy. Například při použití integrovaného ladění parametrů Azure Machine Learning nebo automatizovaného strojového učení.

Tady je několik možností, které máte:
* Definujte parametr s názvem `max_run_duration_seconds` v RunConfiguration k řízení maximální doby, po kterou se může běh na výpočetním prostředí rozhodnout (buď místní, nebo vzdálený cloudový výpočet).
* V případě [ladění pomocí parametrů](how-to-tune-hyperparameters.md#early-termination)Definujte zásadu prvotního ukončení ze zásad Bandit, pozastavené zásady nebo zásadu výběru zkrácení. Chcete-li dále řídit Sweep parametrů, použijte parametry jako `max_total_runs` nebo `max_duration_minutes` .
* V případě [automatizovaného strojového učení](how-to-configure-auto-train.md#exit)nastavte podobné zásady ukončení pomocí  `enable_early_stopping` příznaku. K `iteration_timeout_minutes` `experiment_timeout_minutes` řízení maximální doby trvání běhu nebo pro celý experiment použijte také vlastnosti, jako je a.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Použití virtuálních počítačů s nízkou prioritou

Azure umožňuje používat nadbytečnou nevyužitou kapacitu jako Low-Priority virtuálních počítačů napříč sadami škálování virtuálních počítačů, službou Batch a službou Machine Learning. Tato přidělení jsou předem emptible, ale v porovnání s vyhrazenými virtuálními počítači se přidávají za nižší cenu. Obecně doporučujeme používat pro úlohy služby Batch Low-Priority virtuální počítače. Měli byste je také použít, pokud se přerušení budou obnovovat buď prostřednictvím opětovného odeslání (pro Batch Inferencing), nebo prostřednictvím restartu (pro školení s hloubkovým učením s kontrolními body).

Virtuální počítače s Low-Priority mají jedinou kvótu oddělenou od hodnoty vyhrazené kvóty, kterou používá rodina virtuálních počítačů. Přečtěte si [Další informace o kvótách AmlCompute](how-to-manage-quotas.md).

 Low-Priority virtuální počítače nefungují pro výpočetní instance, protože potřebují podporovat interaktivní poznámkové bloky.

## <a name="use-reserved-instances"></a>Použití rezervovaných instancí

Další možností, jak ušetřit peníze na výpočetní prostředky, je rezervovaná instance virtuálního počítače Azure. V rámci této nabídky se potvrdili na jednoleté nebo tříleté období. Tyto slevy dosahují až 72% sazeb za průběžné platby a vztahují se přímo na váš měsíční účet Azure.

Azure Machine Learning COMPUTE podporuje rezervované instance, které jsou v podstatě. Pokud si koupíte rezervovanou instanci na jeden nebo tři roky, automaticky použijeme slevu na vaše Azure Machine Learning spravované výpočetní prostředky.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:
* [Správa a zvýšení kvót prostředků](how-to-manage-quotas.md)
* [Správa nákladů pomocí analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Vytvořte Azure Machine Learning COMPUTE pomocí [sady SDK](how-to-create-attach-compute-cluster.md) nebo v [studiu](how-to-create-attach-compute-studio.md#amlcompute).