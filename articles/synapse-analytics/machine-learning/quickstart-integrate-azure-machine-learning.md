---
title: 'Rychlý Start: propojení pracovního prostoru Azure Machine Learning'
description: Propojení pracovního prostoru Synapse k pracovnímu prostoru Azure Machine Learning
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 141cc47adb398cbd5730fbe33bcd90fece809ed3
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91543462"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Rychlý Start: vytvoření nové propojené služby Azure Machine Learning v synapse

V tomto rychlém startu připojíte pracovní prostor synapse Analytics k pracovnímu prostoru Azure Machine Learning. Propojení těchto pracovních prostorů vám umožní využít Azure Machine Learning z různých prostředí v synapse.

Například tento odkaz na pracovní prostor Azure Machine Learning umožňuje tyto prostředí:

- Spusťte kanály Azure Machine Learning jako krok v kanálech synapse. Další informace najdete v tématu [spuštění kanálů Azure Machine Learning](/azure/data-factory/transform-data-machine-learning-service).

- Naobohacení dat pomocí předpovědi díky zavedení modelu Machine Learning z registru modelu Azure Machine Learning a určení skóre modelu v synapse fondech SQL. Další podrobnosti najdete v tématu [kurz: Průvodce bodování modelu Machine Learning pro synapse fondy SQL serveru](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
- [Pracovní prostor synapse Analytics](../get-started-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být **přispěvatelem dat objektů BLOB úložiště** adls Gen2 systému souborů, se kterými pracujete.
- [Pracovní prostor Azure Machine Learning](/azure/machine-learning/how-to-manage-workspace).
- Potřebujete oprávnění (nebo požádat někoho, kdo má oprávnění) k vytvoření instančního objektu a tajného klíče, který můžete použít k vytvoření propojené služby. Všimněte si, že tento instanční objekt musí mít přiřazenou roli Přispěvatel v pracovní prostor Azure Machine Learning.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

V tomto kroku se vytvoří nový instanční objekt. Pokud chcete použít existující instanční objekt, můžete tento krok přeskočit.
1. Otevřete Azure Portal. 

1. Přejít na **Azure Active Directory**  ->  **Registrace aplikací**.

1. Klikněte na **Nová registrace**. Pak postupujte podle pokynů v uživatelském rozhraní a zaregistrujte novou aplikaci.

1. Po zaregistrování aplikace. Vygenerujte tajný klíč pro aplikaci. Přejít na **Your application**  ->  **certifikát aplikace & tajný klíč**. Kliknutím na **Přidat tajný klíč klienta** vygenerujte tajný klíč. Ponechte tajný klíč v bezpečí a použije se později.

   ![Generovat tajný kód](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Vytvořte instanční objekt pro aplikaci. Přejděte na **Your application**  ->  **Přehled** aplikace a pak klikněte na **vytvořit instanční objekt**. V některých případech se tento instanční objekt automaticky vytvoří.

   ![Vytvoření instančního objektu](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Přidejte instanční objekt jako Přispěvatel v pracovním prostoru Azure Machine Learning. Všimněte si, že tato akce vyžaduje, aby byla vlastníkem skupiny prostředků, do které patří pracovní prostor Azure Machine Learning.

   ![Přiřadit roli přispěvatele](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Vytvoření propojené služby

1. V pracovním prostoru synapse, kde chcete vytvořit novou propojenou službu Azure Machine Learning, přejít na **Management**  ->  **propojenou službu**Správa a vytvořte novou propojenou službu s typem "Azure Machine Learning".

   ![Vytvořit propojenou službu](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Vyplňte formulář:

   - ID instančního objektu: Toto je **ID aplikace (klienta)** aplikace.
  
     > [!NOTE]
     > Nejedná se o název aplikace. Toto ID můžete najít na stránce Přehled v aplikaci. Mělo by se jednat o dlouhý řetězec podobný tomuto: "81707eac-ab38-406u-8f6c-10ce76a568d5".

   - Klíč instančního objektu: tajný klíč, který jste vygenerovali v předchozí části.

3. Klikněte na **Test připojení** a ověřte, jestli je konfigurace správná. Pokud test připojení projde, klikněte na **Uložit**.

   Pokud se test připojení nezdařil, ujistěte se, že je ID instančního objektu a tajný kód správné, a zkuste to znovu.

## <a name="next-steps"></a>Další kroky

- [Kurz: Průvodce bodování modelu Machine Learning – fond SQL](tutorial-sql-pool-model-scoring-wizard.md)
- [Funkce Machine Learning ve službě Azure synapse Analytics (verze Preview pro pracovní prostory)](what-is-machine-learning.md)
