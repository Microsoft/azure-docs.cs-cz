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
ms.openlocfilehash: 837d62784a56ad0f17471cca5a660819d4a83e12
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926761"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Použití tajných kódů služby Azure Key Vault v aktivitách kanálu

Přihlašovací údaje nebo tajné hodnoty můžete ukládat do Azure Key Vault a použít je během provádění kanálu, aby je bylo možné předat k vašim aktivitám.

## <a name="prerequisites"></a>Předpoklady

Tato funkce závisí na spravované identitě objektu pro vytváření dat.  Zjistěte, jak funguje ze [spravované identity pro Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) a ujistěte se, že je k datové továrně přidružená jedna.

## <a name="steps"></a>Kroky

1. Otevřete vlastnosti datové továrny a zkopírujte hodnotu ID aplikace spravované identity.

    ![Spravovaná hodnota identity](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Otevřete zásady přístupu trezoru klíčů a přidejte spravovaná oprávnění identity pro získání a výpis tajných kódů.

    ![Zásady přístupu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Zásady přístupu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klikněte na **Přidat**a pak na **Uložit**.

3. Přejděte do Key Vault tajného klíče a zkopírujte tajný identifikátor.

    ![Tajný identifikátor](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Poznamenejte si identifikátor URI vašeho tajného kódu, který chcete získat během spuštění kanálu služby Data Factory.

4. V kanálu Data Factory přidejte novou aktivitu webu a nakonfigurujte ji následujícím způsobem.  

    |Vlastnost  |Hodnota  |
    |---------|---------|
    |Zabezpečený výstup     |Pravda         |
    |Adresa URL     |[Vaše tajná hodnota identifikátoru URI]? API-Version = 7.0         |
    |Metoda     |GET         |
    |Ověření     |MSI         |
    |Prostředek        |https://vault.azure.net       |

    ![Aktivita webu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Je nutné přidat **rozhraní API-Version = 7.0** na konec vašeho TAJNÉho identifikátoru URI.  

    > [!CAUTION]
    > Nastavte možnost zabezpečený výstup na hodnotu true, pokud chcete zabránit tomu, aby se tajná hodnota přihlásila do prostého textu.  Všechny další aktivity, které tuto hodnotu využívají, by měly mít možnost zabezpečeného vstupu nastavenou na hodnotu true.

5. Chcete-li použít hodnotu v jiné aktivitě, použijte následující výraz kódu **@activity("Web"). Output. Value)** .

    ![Výraz kódu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat Azure Key Vault k ukládání přihlašovacích údajů pro úložiště a výpočetní prostředky, najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
