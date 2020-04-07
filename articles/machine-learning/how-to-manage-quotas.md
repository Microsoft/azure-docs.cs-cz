---
title: Správa zdrojů & kvótách
titleSuffix: Azure Machine Learning
description: Přečtěte si o kvótách na prostředky pro Azure Machine Learning a jak požádat o další kvóty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 530647c3d32b62f0cac250795ccce580b182fa92
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756604"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Správa a vyžádání kvót pro prostředky Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek obsahuje podrobnosti o předem nakonfigurovaných omezení prostředků Azure pro vaše předplatné. Zahrnuty jsou také pokyny, jak požádat o vylepšení kvót pro každý typ prostředku. Tato omezení jsou zavedena, aby se zabránilo překročení rozpočtu z důvodu podvodů a dodržovat omezení kapacity Azure.

Stejně jako u jiných služeb Azure existují omezení na určité prostředky spojené s Azure Machine Learning. Tyto limity se pohybují od omezení počtu pracovních prostorů až po omezení skutečného podkladového výpočetního výkonu, který se používá pro školení modelu nebo odvození/vyhodnocování. 

Při navrhování a škálování prostředků Azure Machine Learning pro produkční úlohy zvažte tato omezení. Například pokud váš cluster nedosáhne cílového počtu uzlů, pak jste pravděpodobně dosáhli limitu výpočetních jader Azure Machine Learning pro vaše předplatné. Pokud chcete zvýšit limit nebo kvótu nad výchozí limit, otevřete online žádost o zákaznickou podporu zdarma. Omezení nelze zvýšit nad maximální limit hodnotu zobrazenou v následujících tabulkách z důvodu omezení kapacity Azure. Pokud není žádný sloupec Maximální limit, zdroj nemá nastavitelná omezení.

## <a name="special-considerations"></a>Zvláštní aspekty

+ Kvóta je úvěrový limit, nikoli záruka kapacity. Pokud máte rozsáhlé kapacity, obraťte se na podporu Azure.

+ Vaše kvóta je sdílena ve všech službách ve vašich předplatných, včetně Azure Machine Learning. Jedinou výjimkou je Azure Machine Learning výpočetní prostředky, které má samostatnou kvótu od kvóty základní výpočetní. Při vyhodnocování potřeb kapacity nezapomeňte vypočítat využití kvóty ve všech službách.

+ Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze, průběžná platba a řada virtuálních počítačích, například Dv2, F, G a tak dále.

## <a name="default-resource-quotas"></a>Výchozí kvóty prostředků

Tady je rozpis limitů kvót podle různých typů prostředků v rámci předplatného Azure.

> [!IMPORTANT]
> Limity se mohou změnit. Nejnovější lze vždy nalézt na úrovni služby [kvóty dokumentu](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) pro všechny Azure.

### <a name="virtual-machines"></a>Virtuální počítače
Pro každé předplatné Azure existuje limit na počet virtuálních počítačů, které můžete mít napříč službami nebo samostatnými. Tento limit je na úrovni regionu jak na celkovém jádru, tak i na základě počtu rodin.

Jádra virtuálních strojů mají regionální celkový limit a regionální limit na velikost (Dv2, F atd.), které jsou vynuceny samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. V tomto předplatném by bylo možné nasadit 30 virtuálních počítačů řady A1, 30 virtuálních počítačů řady D1 nebo jejich kombinaci, která nepřekročí celkový počet 30 jader (například 10 virtuálních počítačů řady A1 a 20 virtuálních počítačů řady D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Podrobnější a aktuální seznam limitů kvót najdete v článku kvóty pro celý Azure [zde](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Pro Azure Machine Learning Compute je výchozí limit kvóty na počet jader a počet jedinečných výpočetních prostředků povolených pro oblast v předplatném. Tato kvóta je oddělená od kvóty jádra virtuálního počítače výše a základní limity nejsou sdíleny mezi dvěma typy prostředků, protože AmlCompute je spravovaná služba, která nasazuje prostředky v hostovaném modelu.

Dostupné zdroje:
+ Vyhrazená jádra pro oblast mají výchozí limit 24–300 v závislosti na typu nabídky předplatného s vyššími výchozími hodnotami pro typy nabídek EA a CSP.  Počet vyhrazených jader na jedno předplatné se dá zvýšit a pro každou rodinu virtuálních aplikací se liší. Některé specializované rodiny virtuálních zařízení, jako jsou řady NCv2, NCv3 nebo ND, začínají s výchozím nastavením nulových jader. Obraťte se na podporu Azure tím, že vyvolá žádost o kvótu k projednání možnosti zvýšení.

+ Jádra s nízkou prioritou na oblast mají výchozí limit 100 – 3000 v závislosti na typu nabídky předplatného s vyššími výchozími hodnotami pro typy nabídek EA a CSP. Počet jader s nízkou prioritou na jedno předplatné se může zvýšit a je to jedna hodnota napříč rodinami virtuálních aplikací. Obraťte se na podporu Azure diskutovat o možnosti zvýšení.

+ Clustery na oblast mají výchozí limit 200. Ty jsou sdíleny mezi trénovací cluster a výpočetní instance (která je považována za jeden uzel clusteru pro účely kvóty). Pokud chcete požádat o zvýšení nad rámec tohoto limitu, obraťte se na podporu Azure.

+ Existují další přísná omezení, která nelze po zásahu překročit.

| **Zdrojů** | **Maximální limit** |
| --- | --- |
| Maximální počet pracovních prostorů na skupinu prostředků | 800 |
| Maximální počet uzlů v jednom prostředku Azure Machine Learning Compute (AmlCompute) | 100 uzlů |
| Maximální počet procesů GPU MPI na uzel | 1-4 |
| Maximální počet pracovníků GPU na uzel | 1-4 |
| Maximální životnost úlohy | 90 dní<sup>1</sup> |
| Maximální životnost úlohy v uzlu s nízkou prioritou | 7 dní<sup>2</sup> |
| Maximální parametrservery na uzel | 1 |

<sup>1</sup> Maximální životnost se vztahuje k době spuštění spuštění a po jejím dokončení. Dokončené spuštění přetrvávají po neomezenou dobu; data pro spuštění, která nebyla dokončena během maximální životnosti, nejsou přístupná.
<sup>2</sup> Úlohy v uzlu s nízkou prioritou mohou být vystavovány, kdykoli dojde k omezení kapacity. Doporučujeme implementovat kontrolní body ve vaší práci.

### <a name="azure-machine-learning-pipelines"></a>Kanály Azure Machine Learning
Pro Azure Machine Learning kanály je omezení kvóty na počet kroků v kanálu a na počet spuštění na základě plánu publikovaných kanálů na oblast v předplatném.
- Maximální povolený počet kroků v kanálu je 30 000
- Maximální počet součtu spuštění založených na plánu a vyžádat objekt blob pro plány publikovaných kanálů aktivované blogem na jedno předplatné za měsíc je 100 000

> [!NOTE]
> Chcete-li tento limit zvýšit, obraťte se na [podporu společnosti Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Instance kontejnerů

Existuje také omezení počtu instancí kontejneru, které můžete střídat v daném časovém období (s rozsahem každou hodinu) nebo v rámci celého předplatného.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Podrobnější a aktuální seznam limitů kvót najdete v článku kvóty pro celý Azure [zde](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Počet účtů úložiště na oblast je omezen také v daném předplatném. Výchozí limit je 250 a zahrnuje účty standardu i úložiště Premium. Pokud v dané oblasti požadujete více než 250 účtů úložiště, požádejte o to prostřednictvím [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Tým Azure Storage zkontroluje váš obchodní případ a může schválit až 250 účtů úložiště pro danou oblast.


## <a name="workspace-level-quota"></a>Kvóta na úrovni pracovního prostoru

Abychom lépe spravovali přidělení prostředků pro Amlcompute mezi různými pracovními prostory, zavedli jsme funkci, která umožňuje distribuovat kvóty na úrovni předplatného (podle rodiny virtuálních počítačů) a konfigurovat je na úrovni pracovního prostoru. Výchozí chování je, že všechny pracovní prostory mají stejnou kvótu jako kvóta na úrovni předplatného pro všechny rodiny virtuálních počítače. S tím, jak se zvyšuje počet pracovních prostorů a úlohy s různou prioritou začínají sdílet stejné prostředky, však uživatelé chtějí způsob, jak lépe sdílet kapacitu a vyhnout se problémům s konflikty prostředků. Azure Machine Learning poskytuje řešení s nabídkou spravovaných výpočetních prostředků tím, že umožňuje uživatelům nastavit maximální kvótu pro konkrétní rodinu virtuálních počítačů v každém pracovním prostoru. To je analogické k distribuci kapacity mezi pracovní prostory a uživatelé se mohou rozhodnout také přerozdělit, aby řídili maximální využití. 

Pokud chcete nastavit kvóty na úrovni pracovního prostoru, přejděte do libovolného pracovního prostoru v předplatném a v levém podokně klikněte na **Využití + kvóty.** Pak vyberte kartu **Konfigurovat kvóty,** chcete-li kvóty zobrazit, rozbalte libovolnou rodinu virtuálních počítačů a nastavte limit kvóty pro libovolný pracovní prostor uvedený pod uvedenou rodinou virtuálních počítače. Nezapomeňte, že nelze nastavit zápornou hodnotu nebo hodnotu vyšší, než je kvóta na úrovni předplatného. Také, jak byste poznamenali, ve výchozím nastavení jsou všem pracovním prostorům přiřazena celá kvóta předplatného, aby bylo možné plně využít přidělenou kvótu.

[![Kvóta na úrovni pracovního prostoru Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Toto je pouze funkce edice Enterprise. Pokud máte v předplatném pracovní prostor Basic i Enterprise edition, můžete to použít pouze k nastavení kvót v pracovních prostorech Enterprise. Základní pracovní prostory budou mít i nadále kvótu na úrovni předplatného, což je výchozí chování.
>
> K nastavení kvóty na úrovni pracovního prostoru potřebujete oprávnění na úrovni předplatného. To je vynuceno tak, aby jednotliví vlastníci pracovního prostoru neupravovali nebo nezvyšovali své kvóty a začali zasahovat do prostředků vyhrazených pro jiný pracovní prostor. Proto správce předplatného je nejvhodnější pro přidělení a distribuci těchto kvót napříč pracovními prostory.



## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót

Zobrazení kvóty pro různé prostředky, jako jsou virtuální počítače, úložiště, síť, je snadné prostřednictvím portálu Azure.

1. V levém podokně vyberte **Všechny služby** a pak v yberte **Předplatná** v kategorii Obecné.

1. Ze seznamu předplatných vyberte předplatné, jehož kvótu hledáte.

   **Je upozornění**, konkrétně pro zobrazení kvóty Azure Machine Learning Compute. Jak bylo uvedeno výše, tato kvóta je oddělená od výpočetní kvóty ve vašem předplatném.

1. V levém podokně vyberte **službu Machine Learning** a pak vyberte libovolný pracovní prostor ze zobrazeného seznamu.

1. Na dalším okně v části **Podpora + řešení potíží** vyberte Využití + **kvóty,** abyste zobrazili aktuální limity kvót a využití.

1. Výběrem předplatného zobrazíte limity kvót. Nezapomeňte filtrovat do oblasti, která vás zajímá.

1. Nyní můžete přepínat mezi zobrazením úrovně předplatného a zobrazením na úrovni pracovního prostoru:
    + **Zobrazení předplatného:** To vám umožní zobrazit využití kvóty jádra podle rodiny virtuálních počítačů, rozšířit ji o pracovní prostor a dále ji rozšířit o skutečné názvy clusterů. Toto zobrazení je optimální pro rychlé získání podrobností o využití jádra pro konkrétní rodinu virtuálních počítače, abyste viděli rozdělení podle pracovních prostorů a dále podle základních clusterů pro každý z těchto pracovních prostorů. Obecná konvence v tomto zobrazení je (použití/kvóta), kde použití je aktuální počet navýšil jádra a kvóta je logický maximální počet jader, které prostředek lze škálovat. Pro každý **pracovní prostor**by kvóta byla kvóta na úrovni pracovního prostoru (jak je vysvětleno výše), která označuje maximální počet jader, na které můžete škálovat pro určitou rodinu virtuálních zařízení. Pro **cluster** podobně kvóta je ve skutečnosti jádra odpovídající maximální počet uzlů, které cluster uvažuje podle definice max_nodes vlastnost.

    + **Zobrazení pracovního prostoru:** To vám umožní zobrazit využití kvóty jádra podle workspace, rozšířit ji podle rodiny virtuálních počítačů a dále ji rozšířit o skutečné názvy clusterů. Toto zobrazení je optimální pro rychlé získání podrobností o využití jádra pro konkrétní pracovní prostor, abyste viděli rozdělení rodinami virtuálních počítače a dále základníclustery pro každou z těchto rodin.

## <a name="request-quota-increases"></a>Žádost o navýšení kvóty

Pokud chcete zvýšit limit nebo kvótu nad výchozí limit, [otevřete online žádost o zákaznickou podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) zdarma.

Limity nelze zvýšit nad maximální limitní hodnotu uvedenou v tabulkách. Pokud neexistuje žádný maximální limit, pak prostředek nemá nastavitelná omezení. [Tento](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) článek popisuje proces zvýšení kvóty podrobněji.

Při požadování zvýšení kvóty je třeba vybrat službu, kterou požadujete ke zvýšení kvóty, což mohou být služby, jako je kvóta služby Machine Learning, instance kontejnerů nebo kvóta úložiště. Kromě azure machine learning výpočetní prostředky, můžete kliknout na tlačítko **Požádat kvótu** při zobrazení kvóty následující výše uvedené kroky.

> [!NOTE]
> [Bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p) nemá nárok na omezení nebo zvýšení kvóty. Pokud máte [bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na předplatné [s průběžným platbou.](https://azure.microsoft.com/offers/ms-azr-0003p/) Další informace najdete [v tématu Upgrade bezplatné zkušební verze Azure na nejčastější platby](../billing/billing-upgrade-azure-subscription.md) a [nejčastější dotazy k bezplatné zkušební verzi](https://azure.microsoft.com/free/free-account-faq).
