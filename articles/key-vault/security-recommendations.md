---
title: Doporučení zabezpečení pro Azure Key Vault
description: Doporučení zabezpečení pro Azure Key Vault. Implementace těchto pokynů vám pomůže splnit vaše povinnosti zabezpečení, jak je popsáno v našem modelu sdílení zodpovědnosti.
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 5aa3ebc602396bc2d7f83150c66535039b1389e6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171339"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Doporučení zabezpečení pro Azure Key Vault

Tento článek obsahuje doporučení zabezpečení pro Azure Key Vault. Implementace těchto doporučení vám pomůže splnit vaše povinnosti zabezpečení, jak je popsáno v našem sdíleném modelu zodpovědnosti. Další informace o tom, co společnost Microsoft splní zodpovědností poskytovatele služeb, najdete v článku [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Některá doporučení obsažená v tomto článku můžete automaticky monitorovat pomocí Azure Security Center. Azure Security Center je první linií obrany při ochraně vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Pak vám poskytne doporučení, jak je řešit.

- Další informace o Azure Security Center doporučeních najdete v tématu [doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md).
- Informace o Azure Security Center najdete v [Azure Security Center?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře | Security Center |
|-|----|--|
|Povolit obnovitelné odstranění | [Obnovitelné odstranění](key-vault-ovw-soft-delete.md) umožňuje obnovit odstraněné trezory a objekty trezoru. |  - |
| Omezení přístupu k datům trezoru  | Sledujte princip nejnižších oprávnění a omezte, kteří členové vaší organizace mají přístup k datům trezoru. |  - |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Omezte počet uživatelů s přístupem přispěvatele. | Pokud má uživatel oprávnění přispěvatele k rovině správy trezoru klíčů, může uživatel sami udělit přístup k rovině dat nastavením zásady přístupu Key Vault. Měli byste přesně řídit, kdo má roli přispěvatele přístup k vašim trezorům klíčů. Zajistěte, aby k trezorům mohli přistupovat a spravovat jenom uživatelé s potřebou přístupu oprávněným osobám. Můžete číst [zabezpečený přístup k trezoru klíčů](key-vault-secure-your-key-vault.md) . | - |

## <a name="monitoring"></a>Sledování

| Doporučení | Komentáře | Security Center |
|-|----|--|
 Měly by být povolené diagnostické protokoly v Key Vault. | Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. | [Ano](../security-center/security-center-identity-access.md) |
| Omezit, kdo má přístup k protokolům trezoru klíčů Azure | [Protokoly Key Vault](key-vault-logging.md) ukládají informace o aktivitách provedených ve vašem trezoru, jako je vytváření nebo odstraňování trezorů, klíčů, tajných kódů a jejich použití při vyšetřování. |  - |

## <a name="networking"></a>Sítě

| Doporučení | Komentáře | Security Center |
|-|----|--|
|Omezit vystavení sítě | Přístup k síti by měl být omezený na virtuální sítě používané řešeními, které vyžadují přístup k trezoru. Zkontrolujte informace o [koncových bodech služby virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Další kroky

Obraťte se na poskytovatele aplikace a zjistěte, zda jsou k dispozici další požadavky na zabezpečení. Další informace o vývoji zabezpečených aplikací najdete v [dokumentaci k zabezpečení vývoje](../security/fundamentals/abstract-develop-secure-apps.md).
