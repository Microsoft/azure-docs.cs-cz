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
ms.openlocfilehash: 0eaf48f57c3011222b71a63d703e1ccec7aca001
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692834"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Spravovat, nasazovat a sledovat modely pomocí služby Azure Machine Learning

V tomto článku najdete informace o tom, jak používat službu Azure Machine Learning spravovat životní cyklus vašich modelů. Služba Azure Machine Learning používá přístup Machine Learning operací (MLOps), což zvyšuje kvalitu a konzistence vašeho řešení pro strojové učení. Služba Azure Machine Learning poskytuje následující možnosti MLOps:

* Integrace s Azure kanály. Definujte průběžné integrace a nasazování pracovních postupů vašich modelů.
* Model registru, který udržuje několik verzí trénované modely.
* Ověření modelu. Automaticky ověřit trénované modely a vybrat optimální konfiguraci pro jejich nasazení do produkčního prostředí.
* Nasazení modelů jako webové služby v cloudu, místně nebo do zařízení IoT Edge.
* Sledování výkonu nasazený model, takže můžete zlepšit vylepšení v příští verzi modelu.

Poslechněte si o konceptech za MLOps a jak se vztahují ke službě Azure Machine Learning, podívejte se na následující video.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Integrace s Azure kanály

Kanály Azure slouží k vytvoření proces průběžné integrace, který trénovat modelu. V rámci typického scénáře když mezi odborníky přes Data kontroluje změny do úložiště Git pro projekt, spustí kanálu Azure spustit školení. Výsledky spuštění můžete prozkoumat pak zobrazíte trénovaného modelu charakteristiky výkonu. Můžete také vytvořit kanál, který se nasadí modelu jako webové služby.

[Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) usnadňuje práci s kanály Azure. Kanály Azure poskytuje následující vylepšení:

* Umožňuje výběr pracovního prostoru, při definování připojení služby.
* Umožňuje vydávání kanálů aktivaci trénované modely vytvořené v rámci kanálu školení.

Další informace o používání kanály Azure se službou Azure Machine Learning najdete v tématu [průběžnou integraci a nasazování modelů ML se Azure kanály](/azure/devops/pipelines/targets/azure-machine-learning) článku a [Azure Machine Learning Service MLOps](https://aka.ms/mlops) úložiště.

## <a name="convert-and-optimize-models"></a>Převod a optimalizaci modelů

Převod má váš model [otevřít Neural Network Exchange](https://onnx.ai) (ONNX) může zlepšit výkon. Převod na ONNX v průměru může přinést 2 x zvýšení výkonu.

Další informace o ONNX službou Azure Machine Learning, najdete v článku [vytvořit a zrychlit modelů ML](concept-onnx.md) článku.

## <a name="register-models"></a>Zaregistrujte modelů

Registrace modelu umožňuje ukládat a verzí vašich modelů v cloudu Azure, ve vašem pracovním prostoru. Model registru usnadňuje uspořádat a sledovat, trénované modely.

> [!TIP]
> Budete taky moct registrovat modelů trénovaných mimo službu Azure Machine Learning.
 
Registrované modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace, který se dá použít při vyhledávání pro modely můžete zadat také další metadata značky. Služba Azure Machine Learning podporuje všechny modely, které lze načíst s použitím Python verze 3.5.2 nebo vyšší.

Nelze odstranit modely, které se používají v aktivních nasazeních.

Další informace najdete v části registrace modelu z [nasazovat modely](how-to-deploy-and-where.md#registermodel).

Příklad registrace uložené ve formátu pickle modelu naleznete v tématu [kurzu: Trénování modelu klasifikace obrázků](tutorial-deploy-models-with-aml.md).

## <a name="package-and-debug-models"></a>Ladění a balíčku modelů

Před nasazením modelu do produkčního prostředí, je zabalená do image Dockeru. Ve většině případů vytvoření bitové kopie se stane automaticky na pozadí během nasazení. Pro pokročilé scénáře můžete ručně zadat image.

Pokud narazíte na problémy s nasazením, můžete nasadit na svoje místní vývojové prostředí pro řešení potíží a ladění.

Další informace najdete v tématu [nasazovat modely](how-to-deploy-and-where.md#registermodel) a [řešení potíží s nasazeními](how-to-troubleshoot-deployment.md).

## <a name="validate-and-profile-models"></a>Ověření a Profilovat modelů

Služba Azure Machine Learning můžete použít profilování k určení nastavení procesoru a paměti, ideální pro použití při nasazení modelu. Ověření modelu se stane jako součást tohoto procesu pomocí dat, která zadáte pro profilování procesu.

## <a name="use-models"></a>Použití modelů

Trénované modely strojového učení je možné nasadit jako webové služby v cloudu nebo místně na vašem vývojovém prostředí. Můžete také nasadit modely do zařízení Azure IoT Edge. Nasazení, můžete použít k odvozování CPU a GPU nebo pole programmable gate Array (FPGA). Můžete také použít modely z Power BI.

Při použití modelu jako webové služby nebo zařízení IoT Edge, zadejte následující položky:

* Modely, které slouží ke stanovení skóre pro data odeslaná do služeb a zařízení.
* Skriptu položka. Tento skript přijímá požadavky, používá k vyhodnocení dat a vrátí odpověď modely.
* Soubor prostředí conda, který popisuje závislosti, které vyžaduje skript modely a položka.
* Všechny další prostředky, jako jsou text, data a podobně, které jsou vyžadované modely a položka skriptu.

Tyto prostředky jsou zabalené do image Dockeru a nasadit jako webovou službu nebo modul IoT Edge.

Volitelně můžete dál optimalizovat nasazení můžete následující parametry:

* Povolte GPU: Slouží k povolení podpory GPU v image Dockeru. Obrázek musí použít na služby Microsoft Azure Services, jako je Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning Compute nebo Azure Virtual Machines.
* Navíc docker file kroky: Soubor, který obsahuje další kroky Dockeru pro spuštění při vytvoření image Dockeru.
* Základní image: Vlastní image chcete použít jako základní image. Pokud je velmi riskantní používat vlastní image, se službou Azure Machine Learning poskytuje základní image.

Je také zadat konfiguraci Cílová platforma nasazení. Například virtuální počítač typ dostupné paměti a počet jader při nasazování do služby Azure Kubernetes Service.

Když se vytvoří bitovou kopii, jsou také přidány součásti vyžadované pro služby Azure Machine Learning. Například prostředky potřebné pro spuštění webové služby a interakci s IoT Edge.

> [!NOTE]
> Nemůžete upravovat ani změnit webový server nebo IoT Edge komponenty použité v image Dockeru. Služba Azure Machine Learning používá konfigurace webového serveru a IoT Edge součásti, které jsou testovány a podporovány společností Microsoft.

### <a name="web-service"></a>Webová služba

Můžete použít modelů ve službě **webových služeb** následujícím cílových výpočetních prostředí:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Místní vývojové prostředí

Nasazení modelu jako webové služby, je nutné zadat následující položky:

* Model nebo skupiny stromů modelů.
* Závislosti, které vyžaduje použití modelu. Například skript, který přijímá požadavky a vyvolá model, závislosti systému conda, atd.
* Konfigurace nasazení, který popisuje, jak a kde nasadit model.

Další informace najdete v tématu [nasazovat modely](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Zařízení IoT Edge


Modely můžete použít se zařízeními IoT prostřednictvím **modulů Azure IoT Edge**. Moduly IoT Edge se nasadí do hardwarové zařízení, která umožňuje odvození nebo model vyhodnocení na zařízení.

Další informace najdete v tématu [nasazovat modely](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analýzy

Microsoft Power BI podporuje použití modelů strojového učení pro analýzu dat. Další informace najdete v tématu [integrace Azure Machine Learning v Power BI (Preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="monitor-and-collect-data"></a>Monitorování a shromažďování dat

Monitorování můžete pochopit, jaká data je odesíláno do modelu a předpovědi, které vrátí.

Tyto informace vám pomůže porozumět využití modelu. Shromážděná vstupní data můžou být taky užitečné v budoucích verzích trénování modelu.

Další informace najdete v tématu [povolení shromažďování dat modelu](how-to-enable-data-collection.md).

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde můžete nasadit modely](how-to-deploy-and-where.md) ve službě Azure Machine Learning. Příklad nasazení najdete v tématu [kurzu: Nasadit model klasifikace obrázků ve službě Azure Container Instances](tutorial-deploy-models-with-aml.md).

Zjistěte, jak vytvořit [průběžnou integraci a nasazování modelů ML se Azure kanály](/azure/devops/pipelines/targets/azure-machine-learning). 

Další informace o vytváření klientských aplikací a služeb, které [využívat model nasadit jako webovou službu](how-to-consume-web-service.md).
