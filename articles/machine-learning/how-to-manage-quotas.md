---
title: Správa prostředků a kvót
titleSuffix: Azure Machine Learning
description: Přečtěte si o kvótách a omezeních pro prostředky Azure Machine Learning a o tom, jak se zvýší kvóta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: SimranArora904
ms.author: siarora
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: aa25a81068b25b7b8696a7472eb04542e8bb1a60
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878948"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Správa a zvýšení kvót pro prostředky pomocí Azure Machine Learning

Azure používá omezení a kvóty k tomu, aby se předešlo překročení rozpočtu kvůli podvodům a aby bylo možné akceptovat omezení kapacity Azure. Při škálování pro produkční úlohy Vezměte v úvahu tato omezení. V tomto článku se dozvíte o:

> [!div class="checklist"]
> + Výchozí omezení prostředků Azure vztahujících se na [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Vytváření kvót na úrovni pracovního prostoru.
> + Zobrazení kvót a omezení
> + Požaduje se zvýšení kvóty.
> + Privátní koncový bod a kvóty DNS.

Spolu se správou kvót můžete zjistit, jak [plánovat a spravovat náklady na Azure Machine Learning](concept-plan-manage-cost.md) nebo se dozvíte o [omezeních služby v Azure Machine Learning](resource-limits-quotas-capacity.md).

## <a name="special-considerations"></a>Zvláštní požadavky

+ Kvóta je úvěrový limit, nikoli záruka na kapacitu. Pokud máte velké nároky na kapacitu, obraťte se [na podporu Azure, která zvýší vaši kvótu](#request-quota-increases).

+ Kvóta se sdílí mezi všemi službami ve vašich předplatných, včetně Azure Machine Learning. Vypočítejte využití napříč všemi službami při vyhodnocování kapacity.
 
  Azure Machine Learning COMPUTE je výjimka. Má samostatnou kvótu než základní výpočetní kvóta. 

+ Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze, průběžné platby a řady virtuálních počítačů (například Dv2, F a G).

## <a name="default-resource-quotas"></a>Výchozí kvóty prostředků

V této části se dozvíte o výchozích a maximálních limitech kvóty pro následující prostředky:

+ Prostředky Azure Machine Learning
  + Azure Machine Learning COMPUTE
  + Azure Machine Learning kanály
+ Virtuální počítače
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> Omezení se mohou změnit. Nejnovější informace najdete v tématu  [omezení služby v Azure Machine Learning](resource-limits-quotas-capacity.md).



### <a name="azure-machine-learning-assets"></a>Prostředky Azure Machine Learning
Následující omezení prostředků platí pro jednotlivé pracovní prostory. 

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| Datové sady | 10 milionů |
| Běží | 10 milionů |
| Modely | 10 milionů|
| Artifacts | 10 milionů |

Maximální **Doba běhu** je navíc 30 dnů a maximální počet **zaznamenaných metrik na spuštění** je 1 000 000.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Služba [Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed) má výchozí omezení pro počet jader (dělené jednotlivými RODINAMI virtuálních počítačů a kumulativních celkových jader) a také počet jedinečných výpočetních prostředků povolených pro jednotlivé oblasti v rámci předplatného. Tato kvóta se odliší od kvóty jádra virtuálního počítače uvedené v předchozí části, protože se vztahuje pouze na spravované výpočetní prostředky Azure Machine Learning.

[Požádat o zvýšení kvóty](#request-quota-increases) pro zvýšení limitů pro různé kvóty jader virtuálních počítačů, celkový počet základních kvót a prostředků předplatného v této části.

Dostupné prostředky:
+ V závislosti na typu nabídky předplatného mají **vyhrazené jádra na oblast** výchozí limit 24 až 300. Počet vyhrazených jader na předplatné můžete pro každou rodinu virtuálních počítačů zvýšit. Specializované rodiny virtuálních počítačů, jako jsou NCv2, NCv3 nebo řady ND, začínají výchozím nastavením s nulovými jádry.

+ V závislosti na typu nabídky předplatného mají **jádra s nízkou prioritou na oblast** výchozí omezení 100 až 3 000. Počet jader s nízkou prioritou na předplatné se dá zvýšit a v rámci rodiny virtuálních počítačů je jediná hodnota.

+ Počet **clusterů na oblast** má výchozí limit 200. Tyto jsou sdíleny mezi školicím clusterem a výpočetní instancí. (Výpočetní instance se považuje za cluster s jedním uzlem pro účely kvóty.)

> [!TIP]
> Další informace o tom, kterou rodinu virtuálních počítačů požádat o zvýšení kvóty, najdete [v Azure na velikostech virtuálních počítačů](../virtual-machines/sizes.md). U rodin virtuálních počítačů GPU se například v názvu rodiny začíná znakem "N" (např. NCv3 Series)

V následující tabulce jsou uvedena další omezení platformy. Pokud si chcete vyžádat výjimku, obraťte se na tým produktu AzureML prostřednictvím lístku **technické** podpory.

| **Prostředek nebo akce** | **Maximální omezení** |
| --- | --- |
| Pracovní prostory na skupinu prostředků | 800 |
| Uzly v nastavení **clusteru** s jednou Azure Machine Learning výpočetním prostředím (AmlCompute) jako fond s podporou nekomunikace (tj. nejde spustit úlohy MPI) | uzly 100, ale konfigurovatelné až 65000 uzlů |
| Uzly v jednom kroku paralelního spuštění **běží** na clusteru Azure Machine Learning COMPUTE (AmlCompute). | 100 uzlů, ale konfigurovatelné až 65000 uzlů, pokud je váš cluster nastavený na škálování podle výše |
| Uzly v nastavení **clusteru** s jednou Azure Machine Learning COMPUTE (AmlCompute) jako fond s povoleným komunikací | uzly 300, ale konfigurovatelné až 4000 uzlů |
| Uzly v nastavení **clusteru** s jednou Azure Machine Learning COMPUTE (AmlCompute) jako fondem s povolenou komunikací na rodině virtuálních počítačů s POVOLENOu RDMA | uzly 100 |
| Uzly v jednom MPI se **spouštějí** v clusteru s Azure Machine Learning COMPUTE (AmlCompute). | 100 uzlů, ale lze zvýšit na 300 uzlů |
| MPI procesy GPU na uzel | 1-4 |
| Počet pracovníků GPU na uzel | 1-4 |
| Doba života úlohy | 21 dnů<sup>1</sup> |
| Doba života úlohy v uzlu s nízkou prioritou | 7 dnů<sup>2</sup> |
| Počet serverů parametrů na uzel | 1 |

<sup>1</sup> maximální doba života je doba mezi tím, kdy se spuštění spustí a kdy se dokončí. Dokončená spuštění budou trvalá po neomezenou dobu. Data pro spuštění nedokončená v rámci maximální doby života nejsou dostupná.
<sup>2</sup> úlohy na uzlu s nízkou prioritou se můžou kdykoli považovat za omezení kapacity. Doporučujeme, abyste v úloze implementovali kontrolní body.

#### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning kanály
[Azure Machine Learning kanály](concept-ml-pipelines.md) mají následující omezení.

| **Prostředek** | **Počtu** |
| --- | --- |
| Kroky v rámci kanálu | 30 000 |
| Pracovní prostory na skupinu prostředků | 800 |

### <a name="virtual-machines"></a>Virtuální počítače
Každé předplatné Azure má omezený počet virtuálních počítačů napříč všemi službami. Jádra virtuálních počítačů mají omezení regionálního součtu a místní omezení na každou velikost řady. Obě omezení se vynutily samostatně.

Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. V tomto předplatném by bylo možné nasadit virtuální počítače s 30 a1 nebo 30 virtuálních počítačů D1 nebo kombinaci dvou, která nepřekračuje celkem 30 jader.

Omezení pro virtuální počítače nad rámec hodnot zobrazených v následující tabulce nelze vyvolat.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

Další informace najdete v tématu [omezení Container Instances](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits).

### <a name="storage"></a>Storage
Azure Storage má limit 250 účtů úložiště na oblast v rámci jednoho předplatného. Tento limit zahrnuje účty úložiště úrovně Standard i Premium.

Pokud chcete tento limit zvýšit, vytvořte žádost prostřednictvím [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Tým Azure Storage zkontroluje váš případ a může pro oblast schvalovat až 250 účtů úložiště.


## <a name="workspace-level-quotas"></a>Kvóty na úrovni pracovního prostoru

Pomocí kvót na úrovni pracovního prostoru můžete spravovat Azure Machine Learning přidělování cílových výpočtů mezi několika [pracovními prostory](concept-workspace.md) ve stejném předplatném.

Ve výchozím nastavení všechny pracovní prostory sdílí stejnou kvótu jako kvóta na úrovni předplatného pro rodiny virtuálních počítačů. V rámci předplatného ale můžete nastavit maximální kvótu pro jednotlivé rodiny virtuálních počítačů v pracovních prostorech. To vám umožní sdílet kapacitu a vyhnout se problémům s kolize prostředků.

1. Přejít na libovolný pracovní prostor ve vašem předplatném.
1. V levém podokně vyberte **využití a kvóty**.
1. Vyberte kartu **Konfigurace kvót** pro zobrazení kvót.
1. Rozbalte rodinu virtuálních počítačů.
1. Nastavte limit kvóty pro libovolný pracovní prostor uvedený v části Tato rodina virtuálních počítačů.

Nemůžete nastavit zápornou hodnotu ani vyšší hodnotu, než je kvóta na úrovni předplatného.

[![Snímek obrazovky zobrazující kvótu na úrovni pracovního prostoru Azure Machine Learning.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> K nastavení kvóty na úrovni pracovního prostoru potřebujete oprávnění na úrovni předplatného.

## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót

Pokud chcete zobrazit kvótu pro různé prostředky Azure, jako jsou virtuální počítače, úložiště nebo síť, použijte Azure Portal:

1. V levém podokně vyberte **všechny služby** a potom v kategorii **Obecné** vyberte **předplatná** .

2. V seznamu předplatných vyberte předplatné, jehož kvótu hledáte.

3. Pokud chcete zobrazit aktuální limity a využití kvóty, vyberte **využití a kvóty** . Pomocí filtrů vyberte poskytovatele a umístění. 

Kvótu Azure Machine Learning COMPUTE pro vaše předplatné můžete spravovat odděleně od jiných kvót Azure: 

1. V Azure Portal přejdete do svého pracovního prostoru **Azure Machine Learning** .

2. V levém podokně v části Podpora a **řešení potíží** vyberte **využití + kvóty** , abyste zobrazili aktuální limity a využití kvóty.

3. Vyberte předplatné, pro které chcete zobrazit omezení kvóty. Vyfiltrujte si oblast, do které vás zajímáte.

4. Můžete přepínat mezi zobrazením na úrovni předplatného a zobrazením na úrovni pracovního prostoru.

## <a name="request-quota-increases"></a>Žádost o navýšení kvóty

Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, [otevřete online žádost o zákaznickou podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

Omezení nad maximální hodnoty uvedenými v předchozích tabulkách nelze vyvolat. Pokud není maximální limit, nemůžete upravit limit prostředku.

Když požadujete zvýšení kvóty, vyberte službu, na kterou jste se přihlásili. Vyberte například Azure Machine Learning, Container Instances nebo úložiště. V případě Azure Machine Learning COMPUTE můžete vybrat tlačítko **kvóta žádosti** a při zobrazení kvóty v předchozích krocích.

> [!NOTE]
> [Bezplatné zkušební odběry](https://azure.microsoft.com/offers/ms-azr-0044p) nejsou způsobilé pro zvýšení limitu nebo kvóty. Pokud máte bezplatné zkušební předplatné, můžete upgradovat na předplatné s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) . Další informace najdete v tématu [upgrade bezplatné zkušební verze Azure na](../cost-management-billing/manage/upgrade-azure-subscription.md) Nejčastější dotazy týkající se průběžných plateb a [bezplatného účtu Azure](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Navýšení kvóty privátních koncových bodů a privátního DNS

Existují omezení pro počet privátních koncových bodů a privátních zón DNS, které můžete vytvořit v rámci předplatného.

Azure Machine Learning vytvoří prostředky v rámci předplatného (zákazníka), ale některé scénáře vytvoří prostředky v předplatném vlastněné společností Microsoft.

 V následujících scénářích možná budete muset požádat o povolení kvóty v předplatném vlastněné společností Microsoft:

* Pracovní prostor povolený pomocí privátního propojení Azure s klíčem spravovaným zákazníkem (CMK)
* Azure Container Registry pro pracovní prostor za vaší virtuální sítí
* Připojení clusteru Azure Kubernetes Service s podporou služby Private Link k vašemu pracovnímu prostoru

Pokud chcete požádat o povolení těchto scénářů, použijte následující postup:

1. [Vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) a v části __základy__ vyberte následující možnosti:

    | Pole | Výběr |
    | ----- | ----- |
    | Typ problému | **Technické** |
    | Služba | **Moje služby** Pak v rozevíracím seznamu vyberte __Machine Learning__ . |
    | Typ problému | **Konfigurace a zabezpečení v pracovním prostoru** |
    | Podtyp problému | **Žádost o povolení privátního koncového bodu a Privátní DNS zóny** |

2. V části __Podrobnosti__ použijte pole __Popis__ k poskytnutí oblasti Azure a scénáře, který plánujete použít. Pokud potřebujete zvýšit kvótu pro více předplatných, v tomto poli uveďte ID předplatných.

3. Vyberte __vytvořit__ a vytvořte žádost.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Snímek obrazovky s požadavkem na zvýšení kvóty privátního koncového bodu a privátního serveru DNS":::

## <a name="next-steps"></a>Další kroky

+ [Plánování a Správa nákladů na Azure Machine Learning](concept-plan-manage-cost.md)
+ [Omezení služby v Azure Machine Learning](resource-limits-quotas-capacity.md)