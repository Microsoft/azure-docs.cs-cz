---
title: Správa prostředků & kvót
titleSuffix: Azure Machine Learning
description: Přečtěte si o kvótách prostředků pro Azure Machine Learning a o tom, jak požádat o větší kvótu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399160"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Správa a požádat o kvóty pro prostředky Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek poskytuje podrobné informace o předkonfigurovaných omezeních prostředků Azure pro vaše předplatné. K dispozici jsou také pokyny k vyžádání vylepšení kvóty pro jednotlivé typy prostředků. Tato omezení jsou umístěny v místě, aby se zabránilo rozpočtu překračující z důvodu možných podvodů a případném dalším sdílení dodržovat omezení kapacity Azure.

Stejně jako u ostatních služeb Azure platí omezení pro určité prostředky spojené s Azure Machine Learning. Tato omezení omezují rozsah od limitu na počet pracovních prostorů, který se má omezit na skutečné základní výpočetní prostředky, které se používají pro školení modelů nebo odvozování a bodování. 

Při návrhu a škálování prostředků Azure Machine Learning pro produkční úlohy zvažte Tato omezení. Pokud například váš cluster nedosáhne cílového počtu uzlů, možná jste dosáhli limitu Azure Machine Learning výpočetních jader pro vaše předplatné. Pokud chcete zvýšit limit nebo kvóty nad výchozí omezení, otevřete žádost o podporu online zákazníků bez poplatků. Omezení nelze zvýšit nad maximální mezní hodnoty uvedené v následujících tabulkách z důvodu omezení kapacity Azure. Pokud neexistuje žádný sloupec maximálního limitu, prostředek nemá nastavitelná omezení.

## <a name="special-considerations"></a>Zvláštní upozornění

+ Kvóta je kreditního limitu záruku kapacity. Pokud máte velkou kapacitu, obraťte se na podporu Azure.

+ Vaše kvóta se sdílí mezi všemi službami ve vašich předplatných, včetně Azure Machine Learning. Jedinou výjimkou je Azure Machine Learning výpočetní prostředí, které má samostatnou kvótu od základní výpočetní kvóty. Je potřeba Vypočítat využití kvót ve všech službách při vyhodnocení potřeb kapacity.

+ Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze, průběžné platby a série virtuálních počítačů, například Dv2, F, G atd.

## <a name="default-resource-quotas"></a>Výchozí kvóty prostředků

Tady je rozpis kvóty podle různých typů prostředků v rámci vašeho předplatného Azure.

> [!IMPORTANT]
> Omezení se můžou změnit. Nejnovější lze vždy najít v [dokumentu](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) kvót na úrovni služby pro všechny Azure.

### <a name="virtual-machines"></a>Virtuální počítače
U každého předplatného Azure existuje omezení počtu virtuálních počítačů, které můžete mít v rámci služeb nebo samostatně. Toto omezení je na úrovni oblasti, jak na celkový počet jader a také na základě za řady.

V jádrech virtuálních počítačů je omezení regionálního celkového počtu a rozsah řady podle velikosti (Dv2, F atd.), které jsou samostatně vynutily. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. V tomto předplatném by bylo možné nasadit 30 virtuálních počítačů řady A1, 30 virtuálních počítačů řady D1 nebo jejich kombinaci, která nepřekročí celkový počet 30 jader (například 10 virtuálních počítačů řady A1 a 20 virtuálních počítačů řady D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Podrobnější a aktuální seznam limitů [kvót najdete v článku o](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)kvótě v rámci Azure.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Pro Azure Machine Learning Compute je výchozí limit kvóty na počtu jader a počet jednu oblast v rámci předplatného jedinečný výpočetní prostředky. Tato kvóta je oddělená od výše uvedené kvóty virtuálních počítačů a omezení jádra nejsou sdílená mezi těmito dvěma typy prostředků, protože AmlCompute je spravovaná služba, která nasazuje prostředky v hostovaném modelu.

K dispozici prostředky:
+ Vyhrazená jádra na oblast mají výchozí omezení 24-300 v závislosti na typu nabídky předplatného s vyšším počtem výchozích hodnot pro typy nabídek EA a CSP.  Počet vyhrazených jader na předplatné se dá zvýšit a pro každou rodinu virtuálních počítačů se liší. Některé specializované rodiny virtuálních počítačů, jako jsou NCv2, NCv3 nebo ND Series, začínají výchozím nastavením s nulovými jádry. Kontaktujte podporu Azure tím, že si vyžádáte žádost o kvótu a prodiskutujte možnosti zvýšení.

+ Jádra s nízkou prioritou na oblast mají výchozí limit 100 – 3000 v závislosti na typu nabídky předplatného s vyššími výchozími hodnotami pro nabídky EA a CSP. Počet jader s nízkou prioritou na předplatné se dá zvýšit a v rámci rodiny virtuálních počítačů je jediná hodnota. Požádejte podporu Azure o zvýšení možnosti.

+ Počet clusterů na oblast má výchozí limit 200. Sdílí se mezi školicím clusterem a výpočetní instancí (což se pro účely kvót považuje za cluster s jedním uzlem). Pokud chcete nad tento limit zvýšit, kontaktujte podporu Azure.

+ K dispozici je * * dalších striktních omezení, která nelze po dosažení limitu překročit.

| **Prostředek** | **Maximální limit** |
| --- | --- |
| Maximální pracovních prostorů na skupinu prostředků | 800 |
| Maximální počet uzlů v jediném prostředku Azure Machine Learning Compute (AmlCompute) | 100 uzlů |
| Maximální GPU MPI zpracovává jeden uzel | 1 – 4 |
| Maximální pracovních procesů GPU na uzlu | 1 – 4 |
| Maximální doba života | 90 dnů<sup>1</sup> |
| Maximální doba života úlohy na uzlu s nízkou prioritou | 7 dnů<sup>2</sup> |
| Servery parametr maximálního počtu uzlů | 1 |

<sup>1</sup> maximální doba života označuje čas spuštění a jeho dokončení. Dokončené běhy zachovávají po neomezenou dobu; data pro spuštění se nedokončí v rámci maximální doby života není přístupný.
<sup>2</sup> úlohy v uzlu s nízkou prioritou by mohly být přerušeny, kdykoli existuje omezení kapacity. V rámci úlohy doporučujeme implementovat kontrolní body.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning kanály
U Azure Machine Learningch kanálů existuje kvótový limit počtu kroků v kanálu a počtu spuštění publikovaných kanálů na základě plánu na oblast v rámci předplatného.
- Maximální počet kroků povolených v kanálu je 30 000.
- Maximální počet spuštění založených na plánech a přijetí změn objektů BLOB pro plány publikovaných kanálů publikovaných na blogu v rámci předplatného za měsíc je 100 000

> [!NOTE]
> Pokud chcete tento limit zvýšit, kontaktujte [Podpora Microsoftu](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Instance kontejneru

Platí omezení na počet instancí kontejneru, který můžete aktivovat v daném časovém období (každou hodinu s rozsahem) nebo napříč celé předplatné.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Podrobnější a aktuální seznam limitů [kvót najdete v článku o](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)kvótě v rámci Azure.

### <a name="storage"></a>Úložiště
Platí omezení na počet účtů úložiště na oblast i v rámci daného předplatného. Výchozí limit je 250 a zahrnuje účty Standard a Premium Storage. Pokud v dané oblasti potřebujete více než 250 účtů úložiště, vytvořte žádost prostřednictvím [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Tým Azure Storage bude váš obchodní případ podívá a může schválit až 250 účtů úložiště pro příslušnou oblast.


## <a name="workspace-level-quota"></a>Kvóta na úrovni pracovního prostoru

Abychom mohli lépe spravovat přidělení prostředků pro Amlcompute mezi různými pracovními prostory, představili jsme funkci, která vám umožní distribuovat kvóty na úrovni předplatného (pomocí rodiny virtuálních počítačů) a nakonfigurovat je na úrovni pracovního prostoru. Výchozím chováním je, že všechny pracovní prostory mají stejnou kvótu jako kvóta na úrovni předplatného pro jakoukoli rodinu virtuálních počítačů. Jak se ale zvyšuje počet pracovních prostorů a zatížení s proměnlivou prioritou začíná sdílet stejné prostředky, chtějí uživatelé způsob, jak lépe sdílet kapacitu a vyhnout se problémům s kolize prostředků. Azure Machine Learning poskytuje řešení s využitím spravované výpočetní nabídky tím, že umožňuje uživatelům nastavit maximální kvótu pro konkrétní rodinu virtuálních počítačů v každém pracovním prostoru. To je obdobou distribuce kapacity mezi pracovními prostory a uživatelé si můžou vybrat, jestli se má vyhradit k maximálnímu využití. 

Pokud chcete nastavit kvóty na úrovni pracovního prostoru, přejděte na libovolný pracovní prostor ve vašem předplatném a v levém podokně klikněte na **používání a kvóty** . Pak vyberte kartu **Konfigurace kvót** pro zobrazení kvót, rozbalte libovolnou rodinu virtuálních počítačů a nastavte limit kvóty pro libovolný pracovní prostor uvedený v této rodině virtuálních počítačů. Pamatujte, že nemůžete nastavit zápornou hodnotu nebo vyšší hodnotu než kvóta úrovně předplatného. Stejně tak, jak byste měli pozor, se ve výchozím nastavení pro všechny pracovní prostory přiřadí celá kvóta předplatného, aby bylo umožněno plné využití přidělené kvóty.

[kvóta na úrovni pracovního prostoru ![Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Toto je jenom funkce edice Enterprise. Pokud v předplatném máte pracovní prostor Basic i Enterprise Edition, můžete ho použít jenom v pracovních prostorech vaší organizace. Vaše základní pracovní prostory budou i nadále mít kvótu na úrovni předplatného, což je výchozí chování.
>
> Pro nastavení kvóty na úrovni pracovního prostoru potřebujete oprávnění na úrovni předplatného. Tím se vynutilo, že jednotliví vlastníci pracovního prostoru neupravují ani nezvyšují své kvóty a začnou encroaching na prostředky, které jsou pro jiný pracovní prostor vyhrazené. Správce předplatného se proto nejlépe hodí k přidělování a distribuci těchto kvót napříč pracovními prostory.



## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót

Zobrazení vaší kvóty pro různé prostředky, jako jsou virtuální počítače, úložiště, sítě, je snadné prostřednictvím webu Azure portal.

1. V levém podokně vyberte **všechny služby** a potom v kategorii Obecné vyberte **předplatná** .

1. Seznam předplatných vyberte předplatné, jehož kvóty, které hledáte.

   K **dispozici je upozornění**, konkrétně pro zobrazení Azure Machine Learning kvóty Compute. Jak je uvedeno výše, tato kvóta je oddělené od kvóta výpočetních prostředků v rámci předplatného.

1. V levém podokně vyberte **službu Machine Learning Service** a pak vyberte libovolný pracovní prostor ze seznamu zobrazených

1. V dalším okně vyberte v části **Podpora a Poradce při potížích** možnost **využití + kvóty** , abyste zobrazili aktuální limity a využití kvóty.

1. Vyberte předplatné, chcete-li zobrazit kvóty. Mějte na paměti pro filtrování do oblasti, které vás zajímají.

1. Nyní můžete přepínat mezi zobrazením na úrovni předplatného a zobrazením na úrovni pracovního prostoru:
    + **Zobrazení odběru:** Díky tomu můžete zobrazit využití základní kvóty podle rodiny virtuálních počítačů, rozšířit je podle pracovního prostoru a dále je rozšířit o skutečné názvy clusterů. Toto zobrazení je optimální pro rychlé získání podrobností o základním využití konkrétní rodiny virtuálních počítačů, aby se zobrazilo rozdělení v pracovních prostorech a dále podle základních clusterů pro každý z těchto pracovních prostorů. Obecná konvence v tomto zobrazení je (využití/kvóta), kde využití je aktuální počet jader s horizontálním škálováním a kvóta představuje logický maximální počet jader, na které se může prostředek škálovat. U každého **pracovního prostoru**by kvóta byla kvóta na úrovni pracovního prostoru (jak je vysvětleno výše), která označuje maximální počet jader, na které se dá pro konkrétní rodinu virtuálních počítačů škálovat. Pro **cluster** podobně je kvóta ve skutečnosti jádry, které odpovídají maximálnímu počtu uzlů, které může cluster škálovat, aby byl definovaný vlastností max_nodes.

    + **Zobrazení pracovního prostoru:** Díky tomu můžete zobrazit využití základních kvót podle pracovního prostoru, rozšíříte je pomocí rodiny virtuálních počítačů a dále je rozšíříte pomocí skutečných názvů clusterů. Toto zobrazení je optimální pro rychlé získání podrobností o základním použití pro konkrétní pracovní prostor, aby se mohla zobrazit zaregistrované v rámci rodin virtuálních počítačů a dále podkladové clustery pro každou z těchto rodin.

## <a name="request-quota-increases"></a>Žádost o navýšení kvóty

Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, [otevřete online žádost o zákaznickou podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) zdarma.

Mezní hodnoty se nedají vyhodnotit nad maximální hodnotou limitu zobrazenou v tabulkách. Pokud není maximální omezení, prostředek nemá nastavitelný limit. [Tento](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) článek popisuje proces zvýšení kvóty podrobněji.

Pokud se požaduje zvýšení kvóty, budete muset vyberte službu požadujete navýšení kvóty, které by mohly být služeb, jako je kvóty služby Machine Learning, Container instances nebo kvóty úložiště. Kromě Azure Machine Learning COMPUTE můžete kliknout na tlačítko **kvóta žádosti** a při zobrazení kvóty podle výše uvedeného postupu.

> [!NOTE]
> [Bezplatné zkušební odběry](https://azure.microsoft.com/offers/ms-azr-0044p) nejsou způsobilé pro zvýšení limitu nebo kvóty. Pokud máte [bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na předplatné s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) . Další informace najdete v tématu [upgrade bezplatné zkušební verze Azure na](../billing/billing-upgrade-azure-subscription.md) Nejčastější dotazy týkající se průběžných plateb a [bezplatné zkušební verze předplatného](https://azure.microsoft.com/free/free-account-faq).
