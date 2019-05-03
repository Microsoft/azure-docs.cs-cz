---
title: 'MLOps: Správa, nasazení a monitorování modelů ML'
titleSuffix: Azure Machine Learning service
description: 'Další informace o použití služby Azure Machine Learning pro MLOps: nasadit, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí služby Azure Machine Learning, na místním počítači nebo z jiných zdrojů.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 18a80af8422e30ce3e87395449fca7b5f6a73762
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025009"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Spravovat, nasazovat a sledovat modely pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak pomocí služby Azure Machine Learning nasadit, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí Azure Machine Learning na místním počítači nebo z jiných zdrojů. 

Následující diagram znázorňuje kompletního nasazení pracovního postupu: [![Pracovní postup nasazení pro Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

MLOps / pracovní postup nasazení zahrnuje následující kroky:
1. **Zaregistrujte model** v registru hostované ve vašem pracovním prostoru služby Azure Machine Learning
1. **Použití** modelu ve webové službě v cloudu na zařízení IoT a analýzy pomocí Power BI.
1. **Monitorování a shromažďování dat**
1. **Aktualizace** nasazení používat novou bitovou kopii.

Každý krok lze provést, samostatně nebo jako součást jediného příkazu. Kromě toho můžete vytvořit **pracovních postupů CI/CD** jak je znázorněno na tomto obrázku.

[!["Služba azure Machine Learning průběžné integrace a nasazování (CI/CD) cyklu.](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.youtube.com/embed/0MaHb070H_8]

## <a name="step-1-register-model"></a>Krok 1: Registrace modelu

Registrace modelu umožňuje ukládat a verzí vašich modelů v cloudu Azure, ve vašem pracovním prostoru. Model registru usnadňuje uspořádat a sledovat, trénované modely.
 
Registrované modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace, který se dá použít při vyhledávání pro modely můžete zadat také další metadata značky. Služba Azure Machine Learning podporuje všechny modely, které lze načíst s použitím Python verze 3.5.2 nebo vyšší.

Nelze odstranit modely, které se používají v aktivních nasazeních.

Další informace najdete v části registrace modelu z [nasazovat modely](how-to-deploy-and-where.md#registermodel).

Příklad registrace uložené ve formátu pickle modelu naleznete v tématu [kurzu: Trénování modelu klasifikace obrázků](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Krok 2: Použití modelu

Modely strojového učení může sloužit jako webovou službu, na zařízení IoT Edge, nebo pro analýzu ze služeb, jako je Power BI.

### <a name="web-service"></a>Webová služba

Můžete použít modelů ve službě **webových služeb** následujícím cílových výpočetních prostředí:

* Instance kontejneru Azure
* Azure Kubernetes Service

Nasazení modelu jako webové služby, musíte poskytnout následující:

* Model nebo skupiny stromů modelů.
* Závislosti, které vyžaduje použití modelu. Například skript, který přijímá požadavky a vyvolá model, závislosti systému conda, atd.
* Konfigurace nasazení, který popisuje, jak a kde nasadit model.

Další informace najdete v tématu [nasazovat modely](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Zařízení IoT Edge

Modely můžete použít se zařízeními IoT prostřednictvím **modulů Azure IoT Edge**. Moduly IoT Edge se nasadí do hardwarové zařízení, která povolí odvozování na zařízení.

Další informace najdete v tématu [nasazovat modely](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analýzy

Microsoft Power BI podporuje použití modelů strojového učení pro analýzu dat. Další informace najdete v tématu [integrace Azure Machine Learning v Power BI (Preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Krok 3: Modely monitorování a shromažďování dat

Monitorování můžete pochopit, jaká data je odesíláno do modelu a předpovědi, které vrátí.

Tyto informace vám pomůže porozumět využití modelu. Shromážděná vstupní data můžou být taky užitečné v budoucích verzích trénování modelu.

Další informace najdete v tématu [povolení shromažďování dat modelu](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Krok 4: Aktualizace nasazení

Nasazení musí být explicitně aktualizovat. Další informace najdete v tématu aktualizovat část [nasazovat modely](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde můžete nasadit modely](how-to-deploy-and-where.md) ve službě Azure Machine Learning. Příklad nasazení najdete v tématu [kurzu: Nasadit model klasifikace obrázků ve službě Azure Container Instances](tutorial-deploy-models-with-aml.md).

Další informace o vytváření klientských aplikací a služeb, které [využívat model nasadit jako webovou službu](how-to-consume-web-service.md).
