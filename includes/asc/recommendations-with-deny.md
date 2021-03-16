---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/14/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: c142f6a00275b4b21b6bbc64e51a5c4d7d957258
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467987"
---
- Přístup k účtům úložiště s bránou firewall a konfigurací virtuální sítě by měl být omezený, protože účty úložiště by měly omezovat přístup k síti.
- Proměnné účtu Automation by měly být zašifrované proměnné účtu Automation.
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
- Pro Redis by měla být povolená jenom zabezpečená připojení k vaší Redis Cache službě Azure cache pro.
- Přepsání nebo zakázání profilu kontejnerů AppArmor by mělo být omezené.
- Měly by se vyhnout privilegovanému kontejneru
- Spuštění kontejnerů jako kořenový uživatel by se mělo vyhnout.
- Zabezpečený přenos do účtů úložiště by měl být povolený zabezpečený přenos do účtů úložiště.
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na Service Fabric EncryptAndSign clustery, které by měly mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů Service Fabric clustery by se měly používat jenom Azure Active Directory pro ověřování klientů.
- Služby by měly naslouchat jenom povoleným portům.
- Veřejný přístup k účtu úložiště by neměl být povolený.
- Účty úložiště by se měly migrovat do nových prostředků úložiště Azure Resource Manager prostředky úložiště by se měly migrovat na nové prostředky Azure Resource Manager
- Účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě.
- Používání hostitelských sítí a portů by se mělo omezit.
- Použití HostPath svazků připojení by se mělo omezit na známý seznam, aby se omezil přístup k uzlu z ohrožených kontejnerů.
- Doba platnosti certifikátů uložených v Azure Key Vault nesmí překročit 12 měsíců.
- Virtuální počítače by měly být migrovány do nových prostředků Azure Resource Managerch virtuálních počítačů, které by měly být migrovány do nových prostředků Azure Resource Manager
- Firewall webových aplikací (WAF) by měla být povolená pro Application Gateway
- Služba WAF (firewall webových aplikací) by měla být povolená pro službu Azure front-dveří

