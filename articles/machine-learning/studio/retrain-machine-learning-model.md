---
title: Přeučování strojového učení modelu | Microsoft Docs
description: Zjistěte, jak aktualizovat webovou službu, která používá nově trénovaného modelu v Azure Machine Learning a přeučování modelu.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: f02bf54cc8941d18c866183c9b7d12bbe7222d2b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-a-machine-learning-model"></a>Přeučování strojového učení modelu
Jako součást procesu operationalization modelů machine learning v Azure Machine Learning je váš model vycvičena a uložit. Potom ji používáte k vytvoření predicative webové služby. Webové služby, pak mohou být využívány v weby, řídicí panely a mobilní aplikace. 

Obvykle nejsou statické modely, které vytvoříte pomocí Machine Learning. Jakmile nová data k dispozici, nebo když příjemce rozhraní API má svá vlastní data musí být retrained modelu. 

Retraining může dojít, často. S funkcí programové Přeučení rozhraní API můžete prostřednictvím kódu programu přeučování modelu s použitím rozhraní Retraining API a aktualizovat webovou službu s nově naučeného modelu. 

Tento dokument popisuje proces retraining a ukazuje, jak použít i rozhraní Retraining API.

## <a name="why-retrain-defining-the-problem"></a>Proč přeučování: definování problému
Jako součást strojového učení proces školení cvičení modelu pomocí datové sadě. Obvykle nejsou statické modely, které vytvoříte pomocí Machine Learning. Jakmile nová data k dispozici, nebo když příjemce rozhraní API má svá vlastní data musí být retrained modelu.

V těchto scénářích poskytuje programovací rozhraní API pohodlný způsob, jak povolit jste nebo příjemci vašich rozhraní API pro vytvoření klienta, který může na základě jednorázové nebo regulární přeučování modelu s použitím svá vlastní data. Potom se můžete vyhodnotit výsledky retraining a aktualizovat webového rozhraní API služby použít nově trained model.

> [!NOTE]
> Pokud máte existující výukový Experiment a nové webové služby, můžete projít obsloužených existující prediktivní webovou službu místo následující postupy uvedené v následující části.
> 
> 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
Proces zahrnuje následující součásti: A výukový Experiment a prediktivní Experiment publikované jako webovou službu. Povolit retraining z modulu trained model, je nutné výukový Experiment publikovat jako webovou službu s výstup modulu trained model. To umožňuje retraining API přístup k modelu. 

Následující postup platí pro nové a Classic webové služby:

Vytvoření počáteční prediktivní webové služby:

* Vytvoření experimentu školení
* Vytvoření experimentu prediktivní web
* Nasadit prediktivní webové služby

Přeučování webové služby:

* Aktualizace výukový experiment umožňující retraining
* Nasazení retraining webové služby
* Pomocí služby Batch provádění kódu přeučování modelu

Návod, podle předchozích kroků, najdete v části [Machine Learning Přeučování modelů prostřednictvím kódu programu](retrain-models-programmatically.md).

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

Pokud jste nasadili Classic webové služby:

* Vytvořte nový koncový bod na prediktivní webové služby
* Získat adresu PATCH URL a kódu
* Použijte adresu URL OPRAVIT tak, aby odkazoval nový koncový bod v retrained modelu 

Návod, podle předchozích kroků, najdete v části [Přeučování Classic webové služby](retrain-a-classic-web-service.md).

Pokud narazíte na problémy retraining Classic webové služby, přečtěte si téma [řešení potíží s retraining Azure Machine Learning Classic webové služby](troubleshooting-retraining-models.md).

Pokud jste nasadili do nové webové služby:

* Přihlaste se k účtu Azure Resource Manager
* Získat definice webové služby
* Exportovat jako JSON definice webové služby
* Aktualizovat odkaz na `ilearner` objektu blob ve formátu JSON
* Import kódu JSON do definice webové služby
* Aktualizovat webovou službu pomocí nové definice webové služby

Návod, podle předchozích kroků, najdete v části [Přeučování nové webové službě pomocí rutin prostředí PowerShell správu Machine Learning](retrain-new-web-service-using-powershell.md).

Proces pro nastavení retraining Classic webové služby zahrnuje následující kroky:

![Retraining přehled procesu][1]

Proces pro nastavení retraining pro nové webové služby zahrnuje následující kroky:

![Retraining přehled procesu][7]

## <a name="other-resources"></a>Další prostředky
* [Modely retraining a aktualizaci Azure Machine Learning s Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Vytvořit mnoho modely Machine Learning a webové koncové body služby z jednoho experimentu pomocí prostředí PowerShell](create-models-and-endpoints-with-powershell.md)
* [AML Přeučení modelů pomocí rozhraní API](https://www.youtube.com/watch?v=wwjglA8xllg) video ukazuje, jak přeučování modelů Machine Learning v Azure Machine Learning vytvořit pomocí rozhraní Retraining API a prostředí PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

