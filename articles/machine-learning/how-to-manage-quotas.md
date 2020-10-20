---
title: Správa prostředků & kvót
titleSuffix: Azure Machine Learning
description: Přečtěte si o kvótách prostředků pro Azure Machine Learning a o tom, jak požádat o větší kvótu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 93cf29651110356023752f2377c9a0fc358982a7
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203040"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Správa & zvýšení kvót pro prostředky pomocí Azure Machine Learning

Azure používá omezení a kvóty k tomu, aby nedocházelo k překročení rozpočtu v důsledku podvodů, a k zajištění omezení kapacity Azure. Při škálování pro produkční úlohy Vezměte v úvahu tato omezení. V tomto článku se dozvíte o:

> [!div class="checklist"]
> + Výchozí omezení prostředků Azure vztahujících se na [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Zobrazení kvót a omezení
> + Vytváření kvót na úrovni pracovního prostoru
> + Zvýšení kvóty požadavků.
> + Privátní koncový bod a kvóty DNS.

Spolu se správou kvót můžete také zjistit, jak [plánovat & spravovat náklady na Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Zvláštní požadavky

+ Kvóta je úvěrový limit, nikoli záruka na kapacitu. Pokud máte velké nároky na kapacitu, obraťte se [na podporu Azure, která zvýší vaši kvótu](#request-quota-increases).

+ Kvóta se sdílí mezi všemi službami ve vašich předplatných, včetně Azure Machine Learning. Vypočítejte využití napříč všemi službami při vyhodnocování kapacity.
    + Azure Machine Learning COMPUTE je výjimka a má samostatnou kvótu od základní výpočetní kvóty. 

+ Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze, průběžné platby a řady virtuálních počítačů (VM), například Dv2, F, G atd.

## <a name="default-resource-quotas"></a>Výchozí kvóty prostředků

V této části se dozvíte o výchozích a maximálních limitech kvóty pro následující prostředky:

+ Virtuální počítače
+ Azure Machine Learning Compute
+ Azure Machine Learning kanály
+ Container Instances
+ Storage

> [!IMPORTANT]
> Omezení se mohou změnit. Nejnovější lze vždy najít v [dokumentu](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) kvót na úrovni služby pro všechny Azure.

### <a name="virtual-machines"></a>Virtuální počítače
Každé předplatné Azure má omezený počet virtuálních počítačů napříč všemi službami. Jádra virtuálních počítačů mají omezení na úrovni celého regionu a místní omezení na velikost řady (Dv2, F atd.). Obě omezení se vynutily samostatně.

Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. V tomto předplatném by bylo možné nasadit virtuální počítače s 30 a1 nebo 30 virtuálních počítačů D1 nebo kombinaci dvou, která nepřekračuje celkem 30 jader.

Omezení pro virtuální počítače nelze vyvolat nad hodnotu uvedenou v následující tabulce.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed) má omezení výchozí kvóty pro počet jader a počet jedinečných výpočetních prostředků v rámci předplatného. Tato kvóta je oddělená od kvóty jádra virtuálního počítače z předchozí části.

[Požádejte o zvýšení kvóty](#request-quota-increases) , aby se limity v této části zvýšily až do **maximálního limitu** uvedeného v tabulce.

Dostupné prostředky:
+ V závislosti na typu nabídky předplatného mají **vyhrazené jádra na oblast** výchozí limit 24-300.  Počet vyhrazených jader na předplatné se dá pro každou rodinu virtuálních počítačů zvýšit. Specializované rodiny virtuálních počítačů, jako jsou NCv2, NCv3 nebo řady ND, začínají výchozím nastavením s nulovými jádry.

+ **Jádra s nízkou prioritou na oblast** mají výchozí limit 100 – 3000 v závislosti na typu nabídky předplatného. Počet jader s nízkou prioritou na předplatné se dá zvýšit a v rámci rodiny virtuálních počítačů je jediná hodnota.

+ Počet **clusterů na oblast** má výchozí limit 200. Sdílí se mezi školicím clusterem a výpočetní instancí (což se pro účely kvót považuje za cluster s jedním uzlem).

Následující tabulka uvádí další omezení, která nelze překročit.

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| Pracovní prostory na skupinu prostředků | 800 |
| Uzly v prostředku s jednou Azure Machine Learning COMPUTE (AmlCompute) | uzly 100 |
| MPI procesy GPU na uzel | 1-4 |
| Počet pracovníků GPU na uzel | 1-4 |
| Doba života úlohy | 21 dnů<sup>1</sup> |
| Doba života úlohy na Low-Priority uzlu | 7 dnů<sup>2</sup> |
| Počet serverů parametrů na uzel | 1 |

<sup>1</sup> maximální doba života odkazuje na dobu mezi zahájením a dokončením spuštění. Dokončená spuštění budou trvalá po neomezenou dobu. Data pro spuštění nedokončená v rámci maximální doby života nejsou dostupná.
<sup>2</sup> úlohy na Low-Priority uzlu by mohly být přerušeny, kdykoli existuje omezení kapacity. Doporučujeme, abyste v úloze implementovali kontrolní body.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning kanály
[Azure Machine Learning kanály](concept-ml-pipelines.md) mají následující omezení.

| **Prostředek** | **Počtu** |
| --- | --- |
| Kroky v rámci kanálu | 30 000 |
| Pracovní prostory na skupinu prostředků | 800 |

### <a name="container-instances"></a>Instance kontejnerů

Další informace najdete v tématu [omezení Container Instances](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Účty služby Azure Storage mají limit 250 účtů úložiště na oblast v rámci jednoho předplatného. To zahrnuje účty Standard a Premium Storage.

Pokud chcete tento limit zvýšit, vytvořte žádost prostřednictvím [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Tým Azure Storage zkontroluje váš případ a může pro oblast schvalovat až 250 účtů úložiště.


## <a name="workspace-level-quota"></a>Kvóta na úrovni pracovního prostoru

Pomocí kvót na úrovni pracovního prostoru můžete spravovat Azure Machine Learning přidělování cílových výpočtů mezi několika [pracovními prostory](concept-workspace.md) ve stejném předplatném.

Ve výchozím nastavení všechny pracovní prostory sdílí stejnou kvótu jako kvóta na úrovni předplatného pro rodiny virtuálních počítačů. V rámci předplatného ale můžete nastavit maximální kvótu pro jednotlivé rodiny virtuálních počítačů v pracovních prostorech. To vám umožní sdílet kapacitu a vyhnout se problémům s kolize prostředků:

1. Přejděte do libovolného pracovního prostoru v rámci svého předplatného.
1. V levém podokně vyberte **využití a kvóty**.
1. Vyberte kartu **Konfigurace kvót** pro zobrazení kvót.
1. Rozbalte rodinu virtuálních počítačů.
1. Nastavte limit kvóty pro libovolný pracovní prostor uvedený v části Tato rodina virtuálních počítačů.

Nemůžete nastavit zápornou hodnotu ani hodnotu vyšší, než je kvóta úrovně předplatného.

[![Kvóta na úrovni pracovního prostoru Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Pro nastavení kvóty na úrovni pracovního prostoru potřebujete oprávnění na úrovni předplatného.

## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót

Pokud chcete zobrazit kvótu pro různé prostředky Azure, jako je Virtual Machines, úložiště, síť, použijte Azure Portal:

1. V levém podokně vyberte **všechny služby** a potom v kategorii Obecné vyberte **předplatná** .

2. V seznamu předplatných vyberte předplatné, jehož kvótu hledáte.

3. Pokud chcete zobrazit aktuální limity a využití kvóty, vyberte **využití a kvóty** . Pomocí filtrů vyberte poskytovatele a umístění. 

Kvóta Azure Machine Learning COMPUTE v předplatném se spravuje odděleně od jiných kvót Azure. 

1. V Azure Portal přejděte do svého pracovního prostoru **Azure Machine Learning** .

2. V levém podokně v **části Podpora a Poradce při potížích** vyberte **využití + kvóty** , abyste zobrazili aktuální limity a využití kvóty.

3. Vyberte předplatné, pro které chcete zobrazit omezení kvóty. Nezapomeňte filtrovat na oblast, do které vás zajímáte.

4. Můžete přepínat mezi zobrazením na úrovni předplatného a zobrazeními na úrovni pracovního prostoru.

## <a name="request-quota-increases"></a>Žádost o navýšení kvóty

Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, [otevřete online žádost o zákaznickou podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

Limity se nedají vyhodnotit nad maximální hodnotou limitu uvedenou v tabulkách výše. Pokud není maximální limit, nemůžete upravit limit prostředku.

Při žádosti o zvýšení kvóty vyberte službu, pro kterou požadujete zvýšit kvótu. Například Azure Machine Learning, Container Instances, úložiště atd. Pro Azure Machine Learning COMPUTE můžete vybrat tlačítko **kvóta žádosti** a při zobrazení kvóty podle výše uvedeného postupu.

> [!NOTE]
> [Bezplatné zkušební odběry](https://azure.microsoft.com/offers/ms-azr-0044p) nejsou způsobilé pro zvýšení limitu nebo kvóty. Pokud máte [bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na předplatné s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) . Další informace najdete v tématu [upgrade bezplatné zkušební verze Azure na](../billing/billing-upgrade-azure-subscription.md) Nejčastější dotazy týkající se průběžných plateb a  [bezplatné zkušební verze předplatného](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Zvýšení kvóty privátních a privátních koncových bodů DNS

Existují omezení pro počet privátních koncových bodů a privátních zón DNS, které je možné vytvořit v rámci předplatného.

I když Azure Machine Learning vytváří prostředky v rámci předplatného (zákazníka), existují scénáře, které vytvářejí prostředky v předplatném vlastněné společností Microsoft.

 V následujících scénářích možná budete muset požádat o povolení kvóty v předplatném vlastněné společností Microsoft:

* __Pracovní prostor s povoleným privátním propojením s klíčem spravovaným zákazníkem (CMK)__
* __Azure Container Registry pracovního prostoru za vaší virtuální sítí__
* __Připojení k vašemu pracovnímu prostoru pomocí privátního propojení s povoleným clusterem služby Azure Kubernetes__.

Pokud chcete požádat o povolení těchto scénářů, použijte následující postup:

1. [Vytvořte žádost o podporu Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) a v části __základy__ vyberte následující možnosti:

    | Pole | Výběr |
    | ----- | ----- |
    | Typ problému | Technické |
    | Služba | Moje služby V rozevíracím seznamu vyberte __Machine Learning__ . |
    | Typ problému | Nastavení pracovního prostoru, sada SDK a rozhraní příkazového řádku |
    | Podtyp problému | Žádost o povolení privátního koncového bodu a Privátní DNS zóny |

2. V části __Podrobnosti__ použijte pole __Popis__ k poskytnutí oblasti Azure, kterou chcete použít, a scénáře, který chcete použít. Pokud potřebujete požádat o zvýšení kvóty pro více předplatných, uveďte také ID předplatných v tomto poli.

3. Vyberte __vytvořit__ a vytvořte žádost.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Snímek obrazovky s požadavkem na zvýšení kvóty privátního koncového bodu a privátního serveru DNS":::

## <a name="next-steps"></a>Další kroky

+ [Plánování & správy nákladů Azure Machine Learning](concept-plan-manage-cost.md)
