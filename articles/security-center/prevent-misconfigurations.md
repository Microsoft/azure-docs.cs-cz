---
title: Jak zabránit neoprávněným konfiguracím Azure Security Center
description: Přečtěte si, jak na stránkách s podrobnostmi doporučení použít možnosti "vymáhat" a "Odepřít" Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558183"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Jak zabránit chybným konfiguracím s využitím doporučení k vynucení nebo zamítnutí

Nezabezpečené konfigurace zabezpečení představují hlavní příčinu incidentů zabezpečení. Security Center nyní mají schopnost *zabránit* v neúspěšně konfiguracích nových prostředků s ohledem na konkrétní doporučení. 

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

- Přístup k účtům úložiště pomocí brány firewall a konfigurací virtuální sítě by měl být omezený.
- Mezipaměť Azure pro Redis by se měla nacházet v rámci virtuální sítě.
- Azure Cosmos DB účty by měly používat klíče spravované zákazníkem k šifrování neaktivních dat
- Azure Machine Learning pracovní prostory by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)
- Ve jarním cloudu Azure by se mělo používat vkládání ze sítě
- Cognitive Services účty by měly povolit šifrování dat pomocí klíče spravovaného zákazníkem (CMK).
- Měla by se vyhovět omezení procesoru a paměti kontejneru.
- Image kontejneru by se měly nasadit jenom z důvěryhodných registrů.
- Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)
- Je třeba zabránit kontejneru s eskalací oprávnění.
- Kontejnery sdílející závislé obory názvů hostitele by se měly vyhnout
- Kontejnery by měly naslouchat jenom povoleným portům.
- Neměnné (jen pro čtení) kořenový systém souborů by měl být vynutil pro kontejnery.
- Key Vault klíče by měly mít datum vypršení platnosti.
- Key Vault tajných kódů by mělo mít datum vypršení platnosti.
- Trezory klíčů by měly mít povolenou ochranu vyprázdnění
- Trezory klíčů by měly mít povolené obnovitelné odstranění.
- Pro kontejnery by se měly vyhovět aspoň privilegované možnosti pro Linux.
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Přepsání nebo zakázání profilu kontejnerů AppArmor by mělo být omezené.
- Měly by se vyhnout privilegovanému kontejneru
- Spuštění kontejnerů jako kořenový uživatel by se mělo vyhnout.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.
- Služby by měly naslouchat jenom povoleným portům.
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě.
- Používání hostitelských sítí a portů by se mělo omezit.
- Použití HostPath svazků připojení by se mělo omezit na známý seznam, aby se omezil přístup k uzlu z ohrožených kontejnerů.
- Doba platnosti certifikátů uložených v Azure Key Vault nesmí překročit 12 měsíců.
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.
- Firewall webových aplikací (WAF) by měla být povolená pro Application Gateway
- Služba WAF (firewall webových aplikací) by měla být povolená pro službu Azure front-dveří

Tato doporučení se dají použít s možností **vykonat** :

- Auditování na SQL serveru by mělo být povolené.
- Azure Backup by měly být povolené pro virtuální počítače
- Na vašich serverech SQL by měl být povolený Azure Defender pro SQL.
- Měly by být povolené diagnostické protokoly v Azure Stream Analytics.
- V účtech Batch by měly být povolené diagnostické protokoly.
- Měly by být povolené diagnostické protokoly v Data Lake Analytics.
- Měly by být povolené diagnostické protokoly v centru událostí.
- Měly by být povolené diagnostické protokoly v Key Vault.
- Měly by být povolené diagnostické protokoly v Logic Apps.
- Měly by být povolené diagnostické protokoly ve vyhledávacích službách.
- Měly by být povolené diagnostické protokoly v Service Bus.