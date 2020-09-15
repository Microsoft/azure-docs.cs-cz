---
title: Jak zabránit neoprávněným konfiguracím Azure Security Center
description: Přečtěte si, jak na stránkách s podrobnostmi doporučení použít možnosti "vymáhat" a "Odepřít" Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: ceafbe1fd1682fc5e92ab3048ed09866fb9ebbdf
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570453"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Zabránění neoprávněným konfiguracím s doporučeními pro vymáhání nebo zakazování

Nezabezpečené konfigurace zabezpečení představují hlavní příčinu incidentů zabezpečení. Security Center nyní mají schopnost *zabránit* v neúspěšně konfiguracích nových prostředků s ohledem na konkrétní doporučení. 

Tato funkce vám může přispět k zabezpečení vašich úloh a ke stabilizaci zabezpečeného skóre.

Vynucování zabezpečené konfigurace na základě konkrétního doporučení se nabízí ve dvou režimech:

- Pomocí efektu **odepření** Azure Policy můžete zastavit vytváření špatných prostředků.

- Pomocí možnosti **Vynutilit** můžete využít **DeployIfNotExist** efektu zásad Azure a automaticky opravovat prostředky, které nedodržují předpisy při tvorbě.
 
Tato možnost je k dispozici pro vybraná doporučení zabezpečení a nachází se v horní části stránky s podrobnostmi o prostředku.

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

- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.
- Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů Service Bus
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Proměnné účtu Automation by se měly šifrovat.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Auditování neomezeného síťového přístupu k účtům úložiště


Tato doporučení se dají použít s možností **vykonat** :

- Měly by být povolené diagnostické protokoly v Logic Apps.
- Měly by být povolené diagnostické protokoly v Data Lake Analytics.
- Měly by být povolené diagnostické protokoly v IoT Hub.
- V účtech Batch by měly být povolené diagnostické protokoly.
- Měly by být povolené diagnostické protokoly v Azure Stream Analytics.
- Měly by být povolené diagnostické protokoly v Service Bus.
- Měly by být povolené diagnostické protokoly ve vyhledávacích službách.
- Měly by být povolené diagnostické protokoly v centru událostí.
- Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.
- Měly by být povolené diagnostické protokoly v Key Vault.
- Auditování na SQL serveru by mělo být povolené.
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat



