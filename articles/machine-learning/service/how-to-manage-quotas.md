---
title: Správa a žádosti o kvóty pro službu Azure Machine Learning
description: Tato příručka vysvětluje různé kvóty pro prostředky Azure Machine Learning a postup zobrazení a požádat o navýšení kvóty.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997807"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Správa a požádat o kvóty pro prostředky Azure

Jako s ostatními službami Azure, se vztahují omezení určitých prostředků ve službě Azure Machine Learning. Tato omezení v rozsahu od limit počtu pracovních prostorů můžete vytvořit pro omezení na skutečné spotřebované výpočetní prostředky, který získá používá vašich modelů pro trénování a odvozování. Tento článek obsahuje další podrobnosti o předem nakonfigurované limity pro různé prostředky, které jsou pro vaše předplatné Azure a také obsahuje vylepšení žádost o kvótu pro každý typ prostředku a nápomocných odkazů.

Mějte tyto kvóty při navrhování a škálování vašich prostředků Azure ML. Například pokud váš cluster nemá přístup do cílového počtu uzlů, které jste zadali, pak může dosáhli jste limitu jádra služby Batch AI pro vaše předplatné. Pokud budete chtít spustit úlohy v produkčním prostředí v Batch AI, budete muset zvýšit nejméně jeden z kvót nad výchozí. Pokud chcete zvýšit limit nebo kvóty nad výchozí omezení, otevřete žádost o podporu online zákazníků bez poplatků. Omezení nelze zvýšit nad maximální mezní hodnoty uvedené v následujících tabulkách. Pokud neexistuje žádný sloupec maximálního limitu, prostředek nemá nastavitelná omezení. 

## <a name="special-considerations"></a>Zvláštní upozornění

+ Kvóta je kreditního limitu záruku kapacity. Pokud máte velkou kapacitu, obraťte se na podporu Azure.

+ Kvótu se sdílí ve všech službách, s výjimkou služby Batch AI ve vašem předplatném Azure Machine Learning, která je právě jeden z nich. Je potřeba Vypočítat využití kvót ve všech službách při vyhodnocení potřeb kapacity.

+ Výchozí omezení se liší podle typu kategorie nabídky, jako jsou bezplatné zkušební verze, s průběžnými platbami a řady, jako je Dv2, F, G a tak dále.

## <a name="default-resource-quotas"></a>Výchozí kvóty prostředků

Tady je rozpis kvóty podle různých typů prostředků v rámci vašeho předplatného Azure. 

> [!Important]
> Omezení se můžou změnit. Nejnovější informace vždy najdete dosáhlo kvóty úrovni služby [dokumentu](https://docs.microsoft.com/azure/azure-subscription-service-limits/) pro všechno, co Azure.

### <a name="virtual-machines"></a>Virtuální počítače 
Platí omezení na počet virtuálních počítačů, které můžete zřídit v předplatném Azure do služby nebo v samostatné způsobem. Toto omezení je na úrovni oblasti, jak na celkový počet jader a také na základě za řady.

Je důležité zdůraznit, že počet jader virtuálního počítače mají regionální celkový limit a podle omezení velikosti řady (Dv2, F atd.), se vynucují samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. V tomto předplatném by bylo možné nasadit 30 virtuálních počítačů řady A1, 30 virtuálních počítačů řady D1 nebo jejich kombinaci, která nepřekročí celkový počet 30 jader (například 10 virtuálních počítačů řady A1 a 20 virtuálních počítačů řady D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Podrobnější a aktuální seznam kvóty, najdete v článku kvóta Azure celou [tady](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1).

### <a name="batch-ai-clusters"></a>clustery Batch AI
Batch AI je výchozí limit kvóty na počtu jader a počet clusterů jednu oblast v rámci předplatného. Kvóty služby batch AI je oddělená od výše uvedených kvótu jader virtuálních počítačů a základní omezení nejsou aktuálně sdílen mezi typy dvou prostředků.

K dispozici prostředky:
+ Počet vyhrazených jader na oblasti mají výchozí limit 10 až 24.  Je možné zvýšit počet vyhrazených jader na předplatné služby Batch AI. Požádejte podporu Azure o zvýšení možnosti.

+ Počet jader s nízkou prioritou na oblasti mají výchozí limit 10 až 24.  Je možné zvýšit počet jader s nízkou prioritou na předplatné služby Batch AI. Požádejte podporu Azure o zvýšení možnosti.

+ Clustery v jedné oblasti mají výchozí limit 20 a maximálního počtu 200. Pokud chcete nad tento limit zvýšit, kontaktujte podporu Azure.

Podrobnější a aktuální seznam kvóty, najdete v článku kvóta Azure celou [tady](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits).

### <a name="container-instances"></a>Instance kontejnerů

Platí omezení na počet instancí kontejneru, který můžete aktivovat v daném časovém období (každou hodinu s rozsahem) nebo napříč celé předplatné.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Podrobnější a aktuální seznam kvóty, najdete v článku kvóta Azure celou [tady](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Úložiště
Platí omezení na počet účtů úložiště na oblast i v rámci daného předplatného. Výchozí limit je 200 a zahrnuje účty úložiště úrovně Premium i Standard. Pokud požadujete víc než 200 účtů úložiště v dané oblasti, vytvořte žádost prostřednictvím [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Tým Azure Storage bude váš obchodní případ podívá a může schválit až 250 účtů úložiště pro příslušnou oblast.


## <a name="find-your-quotas"></a>Najít svoje kvóty

Zobrazení vaší kvóty pro různé prostředky, jako jsou virtuální počítače, úložiště, sítě, je snadné prostřednictvím webu Azure portal.

1. V levém podokně vyberte **všechny služby** a pak vyberte **předplatná** v obecné kategorii.

1. Seznam předplatných vyberte předplatné, jehož kvóty, které hledáte.

   **Je výstrahou**, speciálně pro zobrazení této kvóty služby Batch AI. Jak je uvedeno výše, tato kvóta je oddělené od kvóta výpočetních prostředků v rámci předplatného. 
   Pro služby Batch AI, po výběru **všechny služby**, vyhledejte služby Batch AI a otevřete službu.

1. V části **nastavení**vyberte **využití a kvóty** zobrazíte aktuální kvóty a využití.

1. Vyberte předplatné, chcete-li zobrazit kvóty. Mějte na paměti můžete filtrovat služby a oblasti, které vás zajímají.


## <a name="request-quota-increases"></a>Požádat o zvýšení kvóty

Pokud chcete zvýšit limit nebo kvóty nad výchozí omezení [otevřete žádost o online zákaznickou podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez poplatků. 

Omezení nelze zvýšit nad maximální mezní hodnoty uvedené v tabulkách. Pokud neexistuje žádné omezení maximální, prostředek nemá nastavitelná omezení. [To](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) článek se týká procesu zvýšení kvóty podrobněji.

Pokud se požaduje zvýšení kvóty, budete muset vyberte službu požadujete navýšení kvóty, které by mohly být služeb, jako je Azure ml kvóty, kvóty služby Batch AI nebo kvóty úložiště. 

> [!NOTE]
> [Bezplatné předplatné zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p) se nevztahuje možnost zvýšení limitu nebo kvóty. Pokud máte [bezplatnou zkušební verzi předplatného](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) předplatného. Další informace najdete v tématu [Upgrade bezplatné zkušební verze Azure na průběžné platby](../../billing/billing-upgrade-azure-subscription.md) a [nejčastější dotazy k bezplatné zkušební předplatné](https://azure.microsoft.com/free/free-account-faq).
