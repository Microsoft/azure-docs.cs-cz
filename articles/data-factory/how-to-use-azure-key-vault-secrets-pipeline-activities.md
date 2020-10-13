---
title: Použití tajných kódů služby Azure Key Vault v aktivitách kanálu
description: Naučte se, jak načíst uložené přihlašovací údaje z trezoru klíčů Azure a použít je během spuštění kanálu služby Data Factory.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 5a662119d9ccf95eac23785c5fe9a787da882531
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537391"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Použití tajných kódů služby Azure Key Vault v aktivitách kanálu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Přihlašovací údaje nebo tajné hodnoty můžete ukládat do Azure Key Vault a použít je během provádění kanálu, aby je bylo možné předat k vašim aktivitám.

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na spravované identitě objektu pro vytváření dat.  Zjistěte, jak funguje ze [spravované identity pro Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) a ujistěte se, že je k datové továrně přidružená jedna.

## <a name="steps"></a>Postup

1. Otevřete vlastnosti datové továrny a zkopírujte hodnotu ID aplikace spravované identity.

    ![Spravovaná hodnota identity](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Otevřete zásady přístupu trezoru klíčů a přidejte spravovaná oprávnění identity pro získání a výpis tajných kódů.

    ![Snímek obrazovky zobrazující stránku "zásady přístupu" se zvýrazněnou akcí přidat zásadu přístupu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Zásady přístupu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klikněte na **Přidat**a pak na **Uložit**.

3. Přejděte do Key Vault tajného klíče a zkopírujte tajný identifikátor.

    ![Tajný identifikátor](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Poznamenejte si identifikátor URI vašeho tajného kódu, který chcete získat během spuštění kanálu služby Data Factory.

4. V kanálu Data Factory přidejte novou aktivitu webu a nakonfigurujte ji následujícím způsobem.  

    |Vlastnost  |Hodnota  |
    |---------|---------|
    |Zabezpečený výstup     |Ano         |
    |URL     |[Vaše tajná hodnota identifikátoru URI]? API-Version = 7.0         |
    |Metoda     |GET         |
    |Authentication     |MSI         |
    |Prostředek        |https://vault.azure.net       |

    ![Aktivita webu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Je nutné přidat **rozhraní API-Version = 7.0** na konec vašeho TAJNÉho identifikátoru URI.  

    > [!CAUTION]
    > Nastavte možnost zabezpečený výstup na hodnotu true, pokud chcete zabránit tomu, aby se tajná hodnota přihlásila do prostého textu.  Všechny další aktivity, které tuto hodnotu využívají, by měly mít možnost zabezpečeného vstupu nastavenou na hodnotu true.

5. Chcete-li použít hodnotu v jiné aktivitě, použijte následující výraz kódu ** @activity (' WEB1 '). Output. Value**.

    ![Výraz kódu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat Azure Key Vault k ukládání přihlašovacích údajů pro úložiště a výpočetní prostředky, najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
