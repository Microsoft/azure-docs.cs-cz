---
title: 'Kurz: předpoklady pro Cognitive Services ve službě Azure synapse Analytics'
description: Přečtěte si, jak nakonfigurovat předpoklady pro používání Cognitive Services v Azure synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936689"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Kurz: předpoklady pro použití Cognitive Services ve službě Azure synapse Analytics

V tomto kurzu se dozvíte, jak nastavit požadavky pro bezpečné používání Azure Cognitive Services ve službě Azure synapse Analytics.

Tento kurz zahrnuje:
> [!div class="checklist"]
> - Vytvořte prostředek Cognitive Services, jako je Analýza textu nebo detektor anomálií.
> - Uložte ověřovací klíč, abyste Cognitive Services prostředky jako tajné klíče v Azure Key Vault a nakonfigurovali přístup pro pracovní prostor Azure synapse Analytics.
> - Vytvořte propojenou službu Azure Key Vault v pracovním prostoru Azure synapse Analytics.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště Azure Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být *přispěvatelem dat objektů BLOB úložiště* Azure Data Lake Storage Gen2 systému souborů, se kterým pracujete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) zahrnuje mnoho typů služeb. V kurzech Azure synapse jsou dva příklady Analýza textu a anomálie.

V Azure Portal můžete vytvořit prostředek [Analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) :

![Snímek obrazovky zobrazující Analýza textu na portálu s tlačítkem vytvořit](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Prostředek [detektoru anomálií](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) můžete vytvořit v Azure Portal:

![Snímek obrazovky znázorňující detektor anomálií na portálu s tlačítkem vytvořit.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Vytvoření trezoru klíčů a konfigurace tajných klíčů a přístupu

1. Vytvořte [Trezor klíčů](https://ms.portal.azure.com/#create/Microsoft.KeyVault) v Azure Portal.
2. Přejděte na   >  **zásady přístupu** Key Vault a udělte oprávnění [MSI v pracovním prostoru Azure synapse](../security/synapse-workspace-managed-identity.md) ke čtení tajných kódů z Azure Key Vault.

   > [!NOTE]
   > Ujistěte se, že se změny zásad ukládají. Tento krok je snadno neúspěšný.

   ![Snímek obrazovky zobrazující výběry pro přidání zásady přístupu](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Přejít na prostředek Cognitive Services. Můžete například přejít na   >  **klíče a koncový bod** detektoru anomálií. Pak zkopírujte kterýkoli ze dvou klíčů do schránky.

4.   >  Pro vytvoření nového tajného klíče použijte Key Vault **tajný klíč** . Zadejte název tajného kódu a pak vložte klíč z předchozího kroku do pole **hodnota** . Nakonec vyberte **vytvořit**.

   ![Snímek obrazovky zobrazující výběry pro vytvoření tajného klíče](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Nezapomeňte si nebo si poznamenejte tento tajný název. Použijete ji později při připojení k Cognitive Services z Azure synapse studia.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Vytvoření propojené služby Azure Key Vault v Azure synapse

1. Otevřete pracovní prostor ve službě Azure synapse Studio. 
2. Přejít na **Správa**  >  **propojených služeb**. Vytvořte propojenou službu **Azure Key Vault** tak, že přejdete na Trezor klíčů, který jste právě vytvořili. 
3. Ověřte připojení tak, že vyberete tlačítko **Test připojení** . Pokud je připojení zelený, vyberte **vytvořit** a potom kliknutím na **publikovat vše** uložte změnu.

![Snímek obrazovky, který zobrazuje Azure Key Vault jako novou propojenou službu.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Teď můžete pokračovat v jednom z kurzů pro používání prostředí Azure Cognitive Services v Azure synapse studiu.

## <a name="next-steps"></a>Další kroky

- [Kurz: analýza mínění s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: detekce anomálií s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL ve službě Azure synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Možnosti strojového učení ve službě Azure Synapse Analytics](what-is-machine-learning.md)