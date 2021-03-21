---
title: 'ML Studio (Classic): migrace na službu Azure Machine Learning-Rebuild Web Service'
description: Opětovné sestavení webových služeb Studio (Classic) jako koncových bodů kanálu v Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565006"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Opětovné sestavení webové služby studia (Classic) v Azure Machine Learning

V tomto článku se naučíte, jak znovu sestavit webovou službu studia (Classic) jako **koncový bod** v Azure Machine Learning.

Pomocí koncových bodů kanálu Azure Machine Learning můžete vytvářet předpovědi, reškolovat modely nebo spouštět jakýkoli obecný kanál. Koncový bod REST umožňuje spouštět kanály z libovolné platformy. 

Tento článek je součástí studia (Classic) pro Azure Machine Learning řady migrace. Další informace o migraci na Azure Machine Learning najdete v [článku Přehled migrace](migrate-overview.md).

> [!NOTE]
> Tato řada migrace se zaměřuje na návrháře přetahování. Další informace o tom, jak programově nasazovat modely, najdete v tématu [nasazení modelů strojového učení v Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Pracovní prostor služby Azure Machine Learning. [Vytvořte pracovní prostor Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Kanál školení Azure Machine Learning. Další informace najdete v tématu [opětovné sestavení experimentu studia (Classic) v Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Koncový bod kanálu vs v reálném čase

Webové služby studia (Classic) byly nahrazeny **koncovými body** v Azure Machine Learning. Pomocí následující tabulky vyberte typ koncového bodu, který se má použít:

|Webová služba Studio (Classic)| Nahrazení Azure Machine Learning
|---|---|
|Webová služba žádosti nebo odpověď (předpověď v reálném čase)|Koncový bod v reálném čase|
|Webová služba Batch (předpověď dávky)|Koncový bod kanálu|
|Přeškolení webové služby (retraining)|Koncový bod kanálu| 


## <a name="deploy-a-real-time-endpoint"></a>Nasazení koncového bodu v reálném čase

V nástroji Studio (Classic) jste použili **webovou službu Request/reaguje** k nasazení modelu pro předpovědi v reálném čase. V Azure Machine Learning používáte **koncový bod v reálném čase**.

Existuje několik způsobů, jak nasadit model v Azure Machine Learning. Jedním z nejjednodušších způsobů je použít návrháře k automatizaci procesu nasazení. Pomocí následujících kroků nasaďte model jako koncový bod v reálném čase:

1. Svůj dokončený školicí kanál spouštějte aspoň jednou.
1. Po dokončení běhu vyberte v horní části plátna možnost **vytvořit odvození kanálu**  >  **odvození v reálném čase**.

    ![vytvořit kanál pro odvození v reálném čase](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Návrhář převede školicí kanál na kanál pro odvození v reálném čase. K podobným převodům dochází také v nástroji Studio (Classic).

    V Návrháři krok převodu také [zaregistruje vyškolený model do vašeho pracovního prostoru Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Vyberte **Odeslat** a spusťte tak kanál odvození v reálném čase a ověřte, jestli se úspěšně spustí.

1. Po ověření kanálu odvození vyberte **nasadit**.

1. Zadejte název koncového bodu a výpočetní typ.

    Následující tabulka popisuje možnosti COMPUTE nasazení v Návrháři:

    | Cílový výpočetní objekt | Použití | Description | Vytvoření |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Odvození v reálném čase|Nasazení ve velkém měřítku v produkčním prostředí. Rychlá doba odezvy a automatické škálování služby.| Uživatel byl vytvořen. Další informace najdete v tématu [vytváření výpočetních cílů](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|Testování a vývoj | Menší nároky na zatížení procesoru, které vyžadují méně než 48 GB paměti RAM.| Automaticky vytvořeno pomocí Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Testování koncového bodu v reálném čase

Po dokončení nasazení můžete zobrazit další podrobnosti a otestovat koncový bod:

1. Přejít na kartu **koncové body** .
1. Vyberte koncový bod.
1. Vyberte kartu **Test**.
    
    ![Snímek obrazovky s tlačítkem testovacího koncového bodu na kartě koncové body](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Publikování koncového bodu kanálu pro dávkovou předpověď nebo přeškolení

Svůj kanál školení můžete použít také k vytvoření **koncového bodu kanálu** namísto koncového bodu v reálném čase. Pomocí **koncových bodů kanálu** můžete provést předpověď dávky nebo přeškolení.

Koncové body kanálu nahrazují **koncové body spouštění dávky**  v studiu (Classic) a **přeškolují webové služby**.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Publikování koncového bodu kanálu pro předpověď dávky

Publikování koncového bodu předpovědi dávky se podobá koncovému bodu v reálném čase.

K publikování koncového bodu kanálu pro předpovědi dávky použijte následující postup:

1. Svůj dokončený školicí kanál spouštějte aspoň jednou.

1. Po dokončení běhu v horní části plátna vyberte **vytvořit** odvození kanálu odvození kanálu  >  .

    ![Snímek obrazovky znázorňující tlačítko vytvoření kanálu odvození na školicím kanálu](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Návrhář převede školicí kanál na kanál odvození dávky. K podobným převodům dochází také v nástroji Studio (Classic).

    V Návrháři tento krok také [zaregistruje vyškolený model do vašeho pracovního prostoru Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Výběrem **Odeslat** spusťte kanál odvození dávky a ověřte, že se úspěšně dokončilo.

1. Po ověření kanálu odvození vyberte možnost **publikovat**.
 
1. Vytvořte nový koncový bod kanálu nebo vyberte některý z existujících.
    
    Nový koncový bod kanálu vytvoří pro svůj kanál nový koncový bod REST. 

    Pokud vyberete existující koncový bod kanálu, nepřepisujte existující kanál. Místo toho Azure Machine Learning verze každého kanálu v koncovém bodu. Můžete určit, která verze se má spustit ve vašem volání REST. Výchozí kanál je nutné nastavit také v případě, že volání REST neurčuje verzi.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Publikování koncového bodu kanálu pro přeškolení

Pokud chcete publikovat koncový bod kanálu pro rekurzi, musíte už mít koncept kanálu, který bude vlakem připravovat model. Další informace o vytváření školicích kanálů najdete v tématu opětovné [sestavení experimentu studia (Classic)](migrate-rebuild-experiment.md).

Pokud chcete znovu použít svůj koncový bod kanálu pro přeškolení, musíte pro vstupní datovou sadu vytvořit **parametr kanálu** . To vám umožní dynamicky nastavit datovou sadu školení, abyste mohli svůj model přeškolit.

K publikování koncového bodu kanálu opětovného školení použijte následující postup:

1. Svůj školicí kanál spouštějte aspoň jednou.
1. Po dokončení běhu vyberte modul DataSet.
1. V podokně podrobností modulu vyberte **nastavit jako parametr kanálu**.
1. Zadejte popisný název, například "InputDataset".    

    ![Snímek obrazovky se zvýrazněním způsobu vytvoření parametru kanálu](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Tím se vytvoří parametr kanálu pro vstupní datovou sadu. Při volání koncového bodu kanálu pro školení můžete zadat novou datovou sadu pro revýuce modelu.

1. Vyberte **Publikovat**.

    ![Snímek obrazovky se zvýrazněným tlačítkem publikovat na školicím kanálu](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Volání koncového bodu kanálu z studia

Po vytvoření dávkového odvození dávky nebo přeškolení koncového bodu kanálu můžete svůj koncový bod volat přímo z prohlížeče.

1. Přejít na kartu **kanály** a vyberte **koncové body kanálu**.
1. Vyberte koncový bod kanálu, který chcete spustit.
1. Vyberte **Odeslat**.

    Po výběru **Odeslat** můžete zadat libovolné parametry kanálu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak znovu sestavit webovou službu studia (Classic) v Azure Machine Learning. Dalším krokem je [integrace webové služby s klientskými aplikacemi](migrate-rebuild-integrate-with-client-app.md).


Podívejte se na další články v řadě migrace pro Studio (Classic):

1. [Přehled migrace](migrate-overview.md)
1. [Migrujte datovou sadu](migrate-register-dataset.md).
1. [Znovu sestavte školicí kanál studia (Classic)](migrate-rebuild-experiment.md).
1. **Opětovné sestavení webové služby studia (Classic)**.
1. [Integrujte webovou službu Azure Machine Learning s klientskými aplikacemi](migrate-rebuild-integrate-with-client-app.md).
1. [Migrace spouštěného skriptu R](migrate-execute-r-script.md)
