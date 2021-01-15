---
title: 'Kurz: předpoklady pro Cognitive Services v Azure synapse'
description: Kurz pro konfiguraci požadavků pro použití Cognitive Services ve službě Azure synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222169"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Kurz: předpoklady pro použití Cognitive Services ve službě Azure synapse

V tomto kurzu se dozvíte, jak nastavit požadavky pro bezpečné využití Cognitive Services v Azure synapse.

Tento kurz zahrnuje:
> [!div class="checklist"]
> - Vytvořte prostředky Cognitive Services. Například Analýza textu nebo detektor anomálií.
> - Uložte ověřovací klíč pro Cognitive Services prostředky jako tajné klíče v Azure Key trezoru a nakonfigurujte přístup pro pracovní prostor Azure synapse.
> - Vytvořte propojenou službu trezoru klíčů Azure v pracovním prostoru Azure synapse Analytics.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být **přispěvatelem dat objektů BLOB úložiště** adls Gen2 systému souborů, se kterými pracujete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) zahrnuje mnoho různých typů služeb. Níže jsou uvedeny některé příklady, které se používají v kurzech synapse.

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií
Vytvořte [detektor anomálií](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) v Azure Portal.

![Vytvoření detektoru anomálií](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Vytvoření prostředku Analýza textu
Vytvoří prostředek [Analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) v Azure Portal.

![Vytváření analýz textu](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Vytvoření Key Vault a konfigurace tajných klíčů a přístupu

1. Vytvoří [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) v Azure Portal.
2. Přejděte na **zásady přístupu Key Vault >** a udělte oprávnění MSI v [pracovním prostoru Azure synapse](../security/synapse-workspace-managed-identity.md) ke čtení tajných kódů z Azure Key Vault.

>Ujistěte se, že se změny zásad ukládají. Tento krok je snadno neúspěšný.

![Přidat zásady přístupu](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Přejít na prostředek služby rozpoznávání, například **anomálie detektor-> Keys a koncový bod**, zkopírujte oba klíče do schránky.

4. Chcete-li vytvořit nový tajný klíč, otevřete **> tajného klíče Key Vault** . Zadejte název tajného kódu a pak vložte klíč z předchozího kroku do pole "value" (hodnota). Nakonec klikněte na **Vytvořit**.

![Vytvořit tajný kód](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Nezapomeňte si nebo si poznamenejte tento tajný název. Později ji budete používat při připojování k Cognitive Services z Azure synapse studia.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Vytvoření propojené služby Azure datatrezoru v Azure synapse

1. Otevřete pracovní prostor ve službě Azure synapse Studio. Přejděte na **Správa-> propojené služby**. Vytvořte propojenou službu AB "Azure Key Vault", která odkazuje na Key Vault jsme právě vytvořili. Pak ověřte připojení kliknutím na tlačítko Test připojení a zkontrolujte, jestli je zelený. Pokud cokoli funguje, klikněte na tlačítko "vytvořit" a potom klikněte na publikovat vše. tím uložíte změny.
![Propojená služba](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Nyní můžete pokračovat v jednom z kurzů pro používání prostředí Azure Cognitive Services v Azure synapse studiu.

## <a name="next-steps"></a>Další kroky

- [Kurz: analýza mínění s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: detekce anomálií s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL ve službě Azure synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Funkce Machine Learning ve službě Azure synapse Analytics](what-is-machine-learning.md)