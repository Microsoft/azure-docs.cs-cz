---
title: Přeučování modelů Machine Learning Studio – Azure | Dokumentace Microsoftu
description: Zjistěte, jak model přetrénujete a aktualizovat webovou službu, která používá nově trénovaného modelu ve službě Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 57e3a444c9de951fdd974d98679908eb5a744ad1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099783"
---
# <a name="retrain-an-azure-machine-learning-studio-model"></a>Přeučování model ve službě Azure Machine Learning Studio
Jako součást procesu operacionalizace modelů strojového učení v Azure Machine Learning je váš model školení a uložen. Pak použijete ho k vytvoření prediktivní webové služby. Webová služba může být potom používán webové stránky, řídicí panely a mobilních aplikací. 

Obvykle nejsou statické modely, které vytvoříte pomocí služby Machine Learning. K dispozici nová data nebo když příjemce rozhraní API má svá vlastní data musí být retrained modelu. 

Přeškolení dochází často. S funkcí programový Přetrénování rozhraní API můžete prostřednictvím kódu programu přeučování modelu s použitím rozhraní Retraining API a aktualizovat webovou službu s nově trénovaného modelu. 

Tento dokument popisuje proces retraining a ukazuje, jak použít rozhraní Retraining API.

## <a name="why-retrain-defining-the-problem"></a>Proč přeučování: definování problému
Jako součást machine learningu procesu trénování model se trénuje pomocí sady data. Obvykle nejsou statické modely, které vytvoříte pomocí služby Machine Learning. K dispozici nová data nebo když příjemce rozhraní API má svá vlastní data musí být retrained modelu.

V těchto scénářích programových rozhraní API poskytuje pohodlný způsob, jak vám či příjemce vašich rozhraní API k vytvoření klienta, který můžete, na základě jednorázové nebo pravidelné přeučování modelu s použitím svá vlastní data. Potom mohou Vyhodnoťte výsledky přetrénování a aktualizovat rozhraní API webové služby pro použití nově trénovaného modelu.

> [!NOTE]
> Pokud máte existující výukového experimentu a nové webové služby, můžete chtít rezervovat obsloužených existující prediktivní webové služby nepostupujte návod uvedených v následující části.
> 
> 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
Tento proces zahrnuje následující součásti: experimentu A trénování a prediktivní Experiment publikovat jako webovou službu. Povolit přeučováním trénovaného modelu, musí být publikován výukový Experiment jako webové služby s výstupem trénovaného modelu. To umožňuje přístup k rozhraní API modelu pro přetrénování. 

Následující postup se vztahuje na nový a klasické webové služby:

Vytvoření počáteční prediktivní webové služby:

* Vytvořit výukový experiment
* Vytvoření experimentu prediktivními webovými
* Nasazení prediktivní webové služby

Přeučování webové služby:

* Aktualizace výukového experimentu umožňující přetrénování
* Nasazení retraining webové služby
* Pomocí kódu služba Batch Execution přeučování modelu

Názorný postup předchozí kroky, najdete v části [modelů Machine Learning Přeučování](retrain-models-programmatically.md).

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Pokud jste nasadili klasických webových služeb:

* Vytvořit nový koncový bod na prediktivní webové služby
* Získat adresu URL opravu a kódu
* Použijte adresu URL OPRAVIT tak, aby odkazovala nový koncový bod v modelu retrained 

Názorný postup předchozí kroky, najdete v části [Přeučování klasickou webovou službou](retrain-a-classic-web-service.md).

Pokud narazíte na potíže přetrénování klasická webová služba, přečtěte si téma [řešení potíží s přeučováním služby Azure Machine Learning klasickou webovou](troubleshooting-retraining-models.md).

Pokud je nasazení nové webové služby:

* Přihlaste se ke svému účtu Azure Resource Manageru
* Získat definice webové služby
* Export definice webové služby jako JSON.
* Aktualizovat odkaz na `ilearner` objektů blob JSON
* Import kódu JSON do definice webové služby
* Aktualizovat webovou službu pomocí definice nové webové služby

Názorný postup předchozí kroky, najdete v části [Přeučování nové webové služby pomocí rutin prostředí PowerShell pro Machine Learning Management](retrain-new-web-service-using-powershell.md).

Proces pro nastavení přeškolení pro klasickou webovou službou zahrnuje následující kroky:

![Přeškolení přehled procesu][1]

Proces pro nastavení přeškolení pro novou webovou službu zahrnuje následující kroky:

![Přeškolení přehled procesu][7]

## <a name="other-resources"></a>Další prostředky
* [Aktualizace Azure Machine Learning a přetrénování modelů s Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Vytvoření mnoha modelů Machine Learning a webové koncové body služby z jednoho experimentu pomocí prostředí PowerShell](create-models-and-endpoints-with-powershell.md)
* [AML Přetrénování modelů pomocí rozhraní API](https://www.youtube.com/watch?v=wwjglA8xllg) video ukazuje, jak programovém přeučení modelů Machine Learning vytvořených ve službě Azure Machine Learning pomocí Retraining API a Powershellu.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

