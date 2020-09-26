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
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: a25dcc187c1bb172106a3972c1cb57dfd473bc2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322372"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Správa & zvýšení kvót pro prostředky pomocí Azure Machine Learning


V tomto článku se dozvíte o předem konfigurovaných omezeních prostředků Azure pro vaše předplatné [Azure Machine Learning](overview-what-is-azure-ml.md) a o tom, jaké kvóty můžete spravovat. Tato omezení jsou zavedena, aby nedocházelo k překročení rozpočtu v důsledku podvodů, a k zajištění omezení kapacity Azure. 

Stejně jako u ostatních služeb Azure platí omezení pro určité prostředky spojené s Azure Machine Learning. Tato omezení omezují rozsah od limitu na počet [pracovních prostorů](concept-workspace.md) , který se má omezit na skutečné základní výpočetní prostředky, které se používají pro školení modelů nebo odvozování a bodování. 

Při návrhu a škálování prostředků Azure Machine Learning pro produkční úlohy zvažte Tato omezení. Pokud například váš cluster nedosáhne cílového počtu uzlů, možná jste dosáhli limitu Azure Machine Learning výpočetních jader pro vaše předplatné. Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, otevřete online žádost o zákaznickou podporu zdarma. Mezní hodnoty se nedají vyhodnotit nad maximální hodnotou limitu uvedenou v následujících tabulkách z důvodu omezení kapacity Azure. Pokud neexistují žádné sloupce maximálního limitu, prostředek nemá nastavitelný limit.


Spolu se správou kvót můžete také zjistit, jak [plánovat & spravovat náklady na Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Zvláštní požadavky

+ Kvóta je úvěrový limit, nikoli záruka na kapacitu. Pokud máte velké nároky na kapacitu, obraťte se na podporu Azure. Můžete také [zvýšit svoje kvóty](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

+ Vaše kvóta se sdílí mezi všemi službami ve vašich předplatných, včetně Azure Machine Learning. Jedinou výjimkou je Azure Machine Learning výpočetní prostředí, které má samostatnou kvótu od základní výpočetní kvóty. Počítejte s tím, že při vyhodnocování vašich potřeb kapacity budete počítat využití kvót napříč všemi službami.

+ Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze, průběžné platby a série virtuálních počítačů, například Dv2, F, G atd.

## <a name="default-resource-quotas"></a>Výchozí kvóty prostředků

Tady je rozpis omezení kvót podle různých typů prostředků v rámci vašeho předplatného Azure.

> [!IMPORTANT]
> Omezení se mohou změnit. Nejnovější lze vždy najít v [dokumentu](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) kvót na úrovni služby pro všechny Azure.

### <a name="virtual-machines"></a>Virtuální počítače
U každého předplatného Azure se omezuje počet virtuálních počítačů v rámci služeb nebo samostatně. V jádrech virtuálních počítačů je omezení regionálního celkového počtu a rozsah řady podle velikosti (Dv2, F atd.), které jsou samostatně vynutily. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. V tomto předplatném by bylo možné nasadit 30 virtuálních počítačů řady A1, 30 virtuálních počítačů řady D1 nebo jejich kombinaci, která nepřekročí celkový počet 30 jader (například 10 virtuálních počítačů řady A1 a 20 virtuálních počítačů řady D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
U [Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed)je pro počet jader a počet jedinečných výpočetních prostředků v rámci předplatného povolená výchozí omezení. Tato kvóta je oddělená od výše uvedené kvóty virtuálních počítačů a omezení jádra nejsou sdílená mezi těmito dvěma typy prostředků, protože AmlCompute je spravovaná služba, která nasazuje prostředky v hostovaném modelu.

Dostupné prostředky:
+ Vyhrazená jádra na oblast mají výchozí omezení 24-300 v závislosti na typu nabídky předplatného s vyšším počtem výchozích hodnot pro typy nabídek EA a CSP.  Počet vyhrazených jader na předplatné se dá zvýšit a pro každou rodinu virtuálních počítačů se liší. Některé specializované rodiny virtuálních počítačů, jako jsou NCv2, NCv3 nebo ND Series, začínají výchozím nastavením s nulovými jádry. Kontaktujte podporu Azure tím, že si vyžádáte žádost o kvótu a prodiskutujte možnosti zvýšení.

+ Jádra s nízkou prioritou na oblast mají výchozí limit 100 – 3000 v závislosti na typu nabídky předplatného s vyššími výchozími hodnotami pro nabídky EA a CSP. Počet jader s nízkou prioritou na předplatné se dá zvýšit a v rámci rodiny virtuálních počítačů je jediná hodnota. Požádejte podporu Azure o diskuzi o možnostech zvýšení.

+ Počet clusterů na oblast má výchozí limit 200. Sdílí se mezi školicím clusterem a výpočetní instancí (což se pro účely kvót považuje za cluster s jedním uzlem). Pokud chcete požádat o zvýšení nad rámec tohoto limitu, obraťte se na podporu Azure.

+ K dispozici jsou i další striktní omezení, která nelze překročit.

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| Maximální počet pracovních prostorů na skupinu prostředků | 800 |
| Maximální počet uzlů v prostředku s jednou Azure Machine Learning COMPUTE (AmlCompute) | uzly 100 |
| Maximální počet MPI procesů GPU na uzel | 1-4 |
| Maximální počet pracovníků GPU na uzel | 1-4 |
| Maximální doba života úlohy | 21 dnů<sup>1</sup> |
| Maximální doba života úlohy na uzlu s nízkou prioritou | 7 dnů<sup>2</sup> |
| Maximální počet serverů parametrů na uzel | 1 |

<sup>1</sup> maximální doba života označuje čas spuštění a jeho dokončení. Dokončená spuštění trvalá po neomezenou dobu; data pro spuštění nedokončená v rámci maximální doby života nejsou dostupná.
<sup>2</sup> úlohy v uzlu s nízkou prioritou by mohly být přerušeny, kdykoli existuje omezení kapacity. V rámci úlohy doporučujeme implementovat kontrolní body.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning kanály
U [Azure Machine Learningch kanálů](concept-ml-pipelines.md)existuje kvótový limit počtu kroků v kanálu a počtu spuštění publikovaných kanálů na základě plánu na oblast v rámci předplatného.
- Maximální počet kroků povolených v kanálu je 30 000.
- Maximální počet spuštění založených na plánech a přijetí změn objektů BLOB pro plány publikovaných kanálů publikovaných na blogu v rámci předplatného za měsíc je 100 000

### <a name="container-instances"></a>Instance kontejnerů

Existuje také omezení počtu instancí kontejnerů, které můžete v daném časovém období aktivovat (v rozsahu hodin) nebo napříč celým předplatným.
Omezení najdete v tématu [omezení Container Instances](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
V daném předplatném je limit počtu účtů úložiště i jednotlivých oblastí. Výchozí limit je 250 a zahrnuje účty Standard a Premium Storage. Pokud v dané oblasti potřebujete více než 250 účtů úložiště, vytvořte žádost prostřednictvím [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Tým Azure Storage zkontroluje váš firemní případ a může pro danou oblast schvalovat až 250 účtů úložiště.


## <a name="workspace-level-quota"></a>Kvóta na úrovni pracovního prostoru

Abychom mohli lépe spravovat přidělení prostředků pro Azure Machine Learning výpočetní cíle (Amlcompute) mezi různými [pracovními prostory](concept-workspace.md), zavedli jsme funkci, která vám umožní distribuovat kvóty na úrovni předplatného (pomocí rodiny virtuálních počítačů) a nakonfigurovat je na úrovni pracovního prostoru. Výchozím chováním je, že všechny pracovní prostory mají stejnou kvótu jako kvóta na úrovni předplatného pro jakoukoli rodinu virtuálních počítačů. Jak se ale zvyšuje počet pracovních prostorů a zatížení s proměnlivou prioritou začíná sdílet stejné prostředky, chtějí uživatelé způsob, jak lépe sdílet kapacitu a vyhnout se problémům s kolize prostředků. Azure Machine Learning poskytuje řešení s využitím spravované výpočetní nabídky tím, že umožňuje uživatelům nastavit maximální kvótu pro konkrétní rodinu virtuálních počítačů v každém pracovním prostoru. To je obdobou distribuce kapacity mezi pracovními prostory a uživatelé si můžou vybrat, jestli se má vyhradit k maximálnímu využití. 

Pokud chcete nastavit kvóty na úrovni pracovního prostoru, přejděte na libovolný pracovní prostor ve vašem předplatném a v levém podokně klikněte na **používání a kvóty** . Pak vyberte kartu **Konfigurace kvót** pro zobrazení kvót, rozbalte libovolnou rodinu virtuálních počítačů a nastavte limit kvóty pro libovolný pracovní prostor uvedený v této rodině virtuálních počítačů. Pamatujte, že nemůžete nastavit zápornou hodnotu nebo vyšší hodnotu než kvóta úrovně předplatného. Stejně tak, jak byste měli pozor, se ve výchozím nastavení pro všechny pracovní prostory přiřadí celá kvóta předplatného, aby bylo umožněno plné využití přidělené kvóty.

[![Kvóta na úrovni pracovního prostoru Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Pro nastavení kvóty na úrovni pracovního prostoru potřebujete oprávnění na úrovni předplatného. Tím se vynutilo, že jednotliví vlastníci pracovního prostoru neupravují ani nezvyšují své kvóty a začnou encroaching na prostředky, které jsou pro jiný pracovní prostor vyhrazené. Správce předplatného se proto nejlépe hodí k přidělování a distribuci těchto kvót napříč pracovními prostory.



## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót

Azure Machine Learning výpočetní kvótu předplatného se spravuje odděleně od jiných kvót prostředků Azure. Chcete-li zobrazit tuto kvótu, je třeba přejít k podrobnostem Machine Learning Services.  

1. V levém podokně vyberte **Machine Learning Service** a pak vyberte libovolný pracovní prostor ze seznamu.

2. V dalším okně vyberte v části **Podpora a Poradce při potížích** možnost **využití + kvóty** , abyste zobrazili aktuální limity a využití kvóty.

3. Vyberte předplatné, pro které chcete zobrazit omezení kvóty. Nezapomeňte filtrovat na oblast, do které vás zajímáte.

4. Nyní můžete přepínat mezi zobrazením na úrovni předplatného a zobrazením na úrovni pracovního prostoru:
    + **Zobrazení odběru:** Díky tomu můžete zobrazit využití základní kvóty podle rodiny virtuálních počítačů, rozšířit je podle pracovního prostoru a dále je rozšířit o skutečné názvy clusterů. Toto zobrazení je optimální pro rychlé získání podrobností o základním využití konkrétní rodiny virtuálních počítačů, aby se zobrazilo rozdělení v pracovních prostorech a dále podle základních clusterů pro každý z těchto pracovních prostorů. Obecná konvence v tomto zobrazení je (využití/kvóta), kde využití je aktuální počet jader s horizontálním škálováním a kvóta představuje logický maximální počet jader, na které se může prostředek škálovat. U každého **pracovního prostoru**by kvóta byla kvóta na úrovni pracovního prostoru (jak je vysvětleno výše), která označuje maximální počet jader, na které se dá pro konkrétní rodinu virtuálních počítačů škálovat. Pro **cluster** podobně je kvóta ve skutečnosti jádry, které odpovídají maximálnímu počtu uzlů, které může cluster škálovat, aby byl definovaný vlastností max_nodes.
    
    + **Zobrazení pracovního prostoru:** Díky tomu můžete zobrazit využití základních kvót podle pracovního prostoru, rozšíříte je pomocí rodiny virtuálních počítačů a dále je rozšíříte pomocí skutečných názvů clusterů. Toto zobrazení je optimální pro rychlé získání podrobností o základním použití pro konkrétní pracovní prostor, aby se mohla zobrazit zaregistrované v rámci rodin virtuálních počítačů a dále podkladové clustery pro každou z těchto rodin.

Zobrazení kvóty pro různé další prostředky Azure, jako je Virtual Machines, úložiště, síť, je snadno Azure Portal.

1. V levém podokně vyberte **všechny služby** a potom v kategorii Obecné vyberte **předplatná** .

2. V seznamu předplatných vyberte předplatné, jehož kvótu hledáte.

3. Pokud chcete zobrazit aktuální limity a využití kvóty, vyberte **využití a kvóty** . Pomocí filtrů vyberte poskytovatele a umístění. 

## <a name="request-quota-increases"></a>Žádost o navýšení kvóty

Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, [otevřete online žádost o zákaznickou podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) zdarma.

Mezní hodnoty se nedají vyhodnotit nad maximální hodnotou limitu zobrazenou v tabulkách. Pokud není maximální omezení, prostředek nemá nastavitelný limit. [Projděte si podrobné pokyny, jak zvýšit vaši kvótu](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Při žádosti o zvýšení kvóty musíte vybrat službu, pro kterou požadujete zvýšit kvótu, což by mohlo být služby, například Machine Learning kvóta služby, instance kontejnerů nebo kvóta úložiště. Kromě Azure Machine Learning COMPUTE můžete kliknout na tlačítko **kvóta žádosti** a při zobrazení kvóty podle výše uvedeného postupu.

> [!NOTE]
> [Bezplatné zkušební odběry](https://azure.microsoft.com/offers/ms-azr-0044p) nejsou způsobilé pro zvýšení limitu nebo kvóty. Pokud máte [bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na předplatné s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) . Další informace najdete v tématu [upgrade bezplatné zkušební verze Azure na](../billing/billing-upgrade-azure-subscription.md) Nejčastější dotazy týkající se průběžných plateb a  [bezplatné zkušební verze předplatného](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Zvýšení kvóty privátních a privátních koncových bodů DNS

Existují omezení pro počet privátních koncových bodů a privátních zón DNS, které je možné vytvořit v rámci předplatného. I když Azure Machine Learning vytváří prostředky v rámci předplatného (zákazníka), existují scénáře, které vytvářejí prostředky v předplatném vlastněné společností Microsoft. V následujících scénářích možná budete muset požádat o povolení kvóty v předplatném vlastněné společností Microsoft:

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
    | Podtyp problému | Problém se zřízením nebo správou pracovního prostoru |

2. V části __Podrobnosti__ použijte pole __Popis__ k poskytnutí oblasti Azure, kterou chcete použít, a scénáře, který chcete použít. Pokud potřebujete požádat o zvýšení kvóty pro více předplatných, uveďte také ID předplatných v tomto poli.

3. K vytvoření žádosti použijte __vytvořit__ .

## <a name="next-steps"></a>Další kroky

+ [Plánování & správy nákladů Azure Machine Learning](concept-plan-manage-cost.md)
