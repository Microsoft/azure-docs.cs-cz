---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 02/16/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: fce7ba090d5aa86c85452ca41ce606d2fa52a6ee
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100645308"
---
- Přístup k účtům úložiště pomocí brány firewall a konfigurací virtuální sítě by měl být omezený.
- Proměnné účtu Automation by se měly šifrovat.
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
- Veřejný přístup k účtu úložiště by neměl být povolený.
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě.
- Používání hostitelských sítí a portů by se mělo omezit.
- Použití HostPath svazků připojení by se mělo omezit na známý seznam, aby se omezil přístup k uzlu z ohrožených kontejnerů.
- Doba platnosti certifikátů uložených v Azure Key Vault nesmí překročit 12 měsíců.
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.
- Firewall webových aplikací (WAF) by měla být povolená pro Application Gateway
- Služba WAF (firewall webových aplikací) by měla být povolená pro službu Azure front-dveří

