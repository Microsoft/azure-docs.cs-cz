---
title: Jak zabránit neoprávněným konfiguracím Azure Security Center
description: Přečtěte si, jak na stránkách s podrobnostmi doporučení použít možnosti "vymáhat" a "Odepřít" Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: fabbd255f64e5614ae052c9d7c8d65fc35d45856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600492"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Jak zabránit chybným konfiguracím s využitím doporučení k vynucení nebo zamítnutí

Nezabezpečené konfigurace zabezpečení představují hlavní příčinu incidentů zabezpečení. Security Center má teď možnost pomáhat *zabránit* neúspěšně se konfigurací nových prostředků s ohledem na konkrétní doporučení. 

Tato funkce vám může přispět k zabezpečení vašich úloh a ke stabilizaci zabezpečeného skóre.

Vynucování zabezpečené konfigurace na základě konkrétního doporučení se nabízí ve dvou režimech:

- Pomocí efektu **odepření** Azure Policy můžete zastavit vytváření špatných prostředků.
- Pomocí možnosti **Vynutilit** můžete využít **DeployIfNotExist** efektu zásad Azure a automaticky opravovat prostředky, které nedodržují předpisy při tvorbě.

To najdete v horní části stránky s podrobnostmi o prostředcích pro vybraná doporučení zabezpečení (viz [doporučení s možnostmi odepřít/vyhovět](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Zabránit vytváření prostředků

1. Otevřete doporučení, které vaše nové prostředky musí splňovat, a v horní části stránky vyberte tlačítko **Odepřít** .

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Stránka doporučení s zvýrazněným tlačítkem odepřít":::

    Otevře se podokno konfigurace se seznamem možností oboru. 

1. Nastavte obor tak, že vyberete příslušné předplatné nebo skupinu pro správu.

    > [!TIP]
    > Chcete-li změnit jedno předplatné, můžete použít tři tečky na konci řádku, nebo pomocí zaškrtávacích políček vybrat více předplatných nebo skupin a pak vybrat možnost **změnit na odepřít**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Nastavení oboru pro odepření Azure Policy":::


## <a name="enforce-a-secure-configuration"></a>Vynutilit zabezpečenou konfiguraci

1. Otevřete doporučení, které nasadíte nasazení šablony pro Pokud nové prostředky nevyhovují, a v horní části stránky vyberte tlačítko **vykonat** .

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Stránka doporučení s zvýrazněným tlačítkem pro vymáhání":::

    Otevře se podokno konfigurace se všemi možnostmi konfigurace zásad. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Vynutilit možnosti konfigurace":::

1. Nastavte rozsah, název přiřazení a další relevantní možnosti.

1. Vyberte **Zkontrolovat a vytvořit**.

## <a name="recommendations-with-denyenforce-options"></a>Doporučení s možnostmi odepřít/vyhovět

Tato doporučení se dají použít s možností **Zakázat** :

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Tato doporučení se dají použít s možností **vykonat** :

- Auditování na SQL serveru by mělo být povolené.
- Azure Backup by měly být povolené pro virtuální počítače
- Na vašich serverech SQL by měl být povolený Azure Defender pro SQL.
- V clusterech by měl být nainstalovaný a povolený Azure Policy doplněk pro Kubernetes.
- Měly by být povolené diagnostické protokoly v Azure Stream Analytics.
- V účtech Batch by měly být povolené diagnostické protokoly.
- Měly by být povolené diagnostické protokoly v Data Lake Analytics.
- Měly by být povolené diagnostické protokoly v centru událostí.
- Měly by být povolené diagnostické protokoly v Key Vault.
- Měly by být povolené diagnostické protokoly v Logic Apps.
- Měly by být povolené diagnostické protokoly ve vyhledávacích službách.
- Měly by být povolené diagnostické protokoly v Service Bus.
