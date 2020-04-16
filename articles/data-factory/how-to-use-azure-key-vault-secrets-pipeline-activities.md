---
title: Použití tajných kódů služby Azure Key Vault v aktivitách kanálu
description: Zjistěte, jak načíst uložená pověření z trezoru klíčů Azure a používat je během spuštění kanálu datové továrny.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: f2531ebfd8b1eafc04fa6eda660b0eec3d1147f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417081"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Použití tajných kódů služby Azure Key Vault v aktivitách kanálu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Můžete uložit přihlašovací údaje nebo tajné hodnoty v trezoru klíčů Azure a použít je během spuštění kanálu předat své aktivity.

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na identitě spravované z datové továrny.  Zjistěte, jak funguje ze [spravované identity pro Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) a ujistěte se, že vaše data factory má jeden přidružený.

## <a name="steps"></a>Kroky

1. Otevřete vlastnosti vaší datové továrny a zkopírujte hodnotu ID aplikace spravované identity.

    ![Hodnota spravované identity](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Otevřete zásady přístupu trezoru klíčů a přidejte oprávnění spravované identity do tajných kódů Získat a Seznam.

    ![Zásady přístupu trezoru klíčů](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Zásady přístupu trezoru klíčů](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klepněte na tlačítko **Přidat**a potom klepněte na tlačítko **Uložit**.

3. Přejděte na tajný klíč trezoru klíčů a zkopírujte tajný identifikátor.

    ![Tajný identifikátor](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Poznamenejte si tajný identifikátor URI, který chcete získat během spuštění kanálu datové továrny.

4. V kanálu Data Factory přidejte novou webovou aktivitu a nakonfigurujte ji následujícím způsobem.  

    |Vlastnost  |Hodnota  |
    |---------|---------|
    |Zabezpečený výstup     |True         |
    |zprostředkovatele identity     |[Vaše tajná hodnota identifikátoru URI]?api-version=7.0         |
    |Metoda     |GET         |
    |Authentication     |MSI         |
    |Prostředek        |https://vault.azure.net       |

    ![Webová aktivita](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Na konec tajného identifikátoru URI je nutné přidat **?api-version=7.0.**  

    > [!CAUTION]
    > Nastavte možnost Zabezpečený výstup na hodnotu true, abyste zabránili zaznamenání hodnoty tajného klíče ve formátu prostého textu.  Všechny další aktivity, které spotřebovávají tuto hodnotu by měl mít jejich zabezpečené vstupní možnost nastavena na hodnotu true.

5. Chcete-li použít hodnotu v jiné aktivitě, použijte následující výraz ** @activitykódu ("Web1").output.value**.

    ![Výraz kódu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat Azure Key Vault k ukládání přihlašovacích údajů pro úložiště dat a výpočetní prostředky, najdete [v tématu Store přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
