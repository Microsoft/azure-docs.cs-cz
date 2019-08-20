---
title: Řešení chyb neodpovídajících adresářů v Azure AD Domain Services | Microsoft Docs
description: Pochopení a vyřešení chyb neodpovídajících adresářů pro existující Azure AD Domain Services spravované domény
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 4978f7b782271daff996807172a24103bd8d9860
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617285"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Řešení chyb neodpovídajících adresářů pro existující Azure AD Domain Services spravované domény
Máte existující Azure AD Domain Services spravovanou doménu. Když přejdete na Azure Portal a zobrazíte spravovanou doménu, zobrazí se tato chybová zpráva:

![Chyba neshody adresáře](./media/getting-started/mismatched-tenant-error.png)

Tuto spravovanou doménu nelze spravovat, dokud nebude chyba vyřešena.


## <a name="whats-causing-this-error"></a>Co je příčinou této chyby?
Tato chyba je způsobená tím, že vaše spravovaná doména a virtuální síť, ve které je povolená, patří do dvou různých tenantů Azure AD. Máte například spravovanou doménu s názvem "contoso.com" a byla povolena pro tenanta Azure AD společnosti Contoso. Virtuální síť Azure, ve které je spravovaná doména povolená, ale patří společnosti Fabrikam – jinému tenantovi služby Azure AD.

Nové Azure Portal (a konkrétně Azure AD Domain Services rozšíření) jsou postavené na Azure Resource Manager. V moderních Azure Resource Manager prostředí se vynutila určitá omezení, která poskytují lepší zabezpečení a řízení přístupu na základě rolí (RBAC) k prostředkům. Povolení Azure AD Domain Services pro tenanta služby Azure AD je citlivá operace, protože způsobí synchronizaci hodnot hash přihlašovacích údajů do spravované domény. Tato operace vyžaduje, abyste byli správcem tenanta pro daný adresář. Kromě toho musíte mít oprávnění správce pro virtuální síť, ve které jste povolili spravovanou doménu. Aby kontroly RBAC pracovaly konzistentně, měla by spravovaná doména a virtuální síť patřit do stejného tenanta Azure AD.

V krátkém případě nemůžete povolit spravovanou doménu pro tenanta Azure AD ' contoso.com ' ve virtuální síti patřící do předplatného Azure, jehož vlastníkem je jiný tenant Azure AD ' fabrikam.com '. 

**Platná konfigurace**: V tomto scénáři nasazení je spravovaná doména contoso povolená pro tenanta služby Azure AD společnosti Contoso. Spravovaná doména je zveřejněná ve virtuální síti patřící do předplatného Azure, které vlastní tenant Azure AD společnosti Contoso. Proto spravovaná doména i virtuální síť patří do stejného tenanta služby Azure AD. Tato konfigurace je platná a plně podporovaná.

![Platná konfigurace tenanta](./media/getting-started/valid-tenant-config.png)

**Neshoda konfigurace tenanta**: V tomto scénáři nasazení je spravovaná doména contoso povolená pro tenanta služby Azure AD společnosti Contoso. Spravovaná doména se ale zveřejňuje ve virtuální síti, která patří k předplatnému Azure, které patří do tenanta Azure AD společnosti Fabrikam. Proto spravovaná doména a virtuální síť patří do dvou různých tenantů Azure AD. Tato konfigurace není shodná s konfigurací tenanta a není podporována. Virtuální síť musí být přesunutá do stejného tenanta Azure AD (tj. contoso) jako spravovaná doména. Podrobnosti najdete v části [řešení](#resolution) .

![Neshoda konfigurace tenanta](./media/getting-started/mismatched-tenant-config.png)

Proto pokud spravovaná doména a virtuální síť, ve které je povolena, patří do dvou různých tenantů Azure AD, zobrazí se tato chyba.

V prostředí Správce prostředků platí následující pravidla:
- Adresář služby Azure AD může mít několik předplatných Azure.
- Předplatné Azure může mít několik prostředků, jako jsou virtuální sítě.
- Pro adresář služby Azure AD je povolená jediná Azure AD Domain Services spravovaná doména.
- Ve virtuální síti, která patří do jednoho nebo více předplatných Azure v rámci stejného tenanta služby Azure AD, může být povolená Azure AD Domain Services spravovaná doména.


## <a name="resolution"></a>Řešení
K dispozici máte dvě možnosti, jak vyřešit chybu neodpovídajícího adresáře. Možná:

- Kliknutím na tlačítko **Odstranit** odstraníte existující spravovanou doménu. Znovu vytvořte pomocí [Azure Portal](https://portal.azure.com), aby byla spravovaná doména a virtuální síť dostupná v rámci patřící do adresáře služby Azure AD. Připojte všechny počítače, které jste dříve připojili k odstraněné doméně, do nově vytvořené spravované domény.

- Přesuňte předplatné Azure obsahující virtuální síť do adresáře služby Azure AD, ke kterému vaše spravovaná doména patří. Postupujte podle kroků v článku [přenos vlastnictví předplatného Azure na jiný účet](../billing/billing-subscription-transfer.md) .


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](tutorial-create-instance.md)
* [Průvodce odstraňováním potíží – Azure AD Domain Services](troubleshoot.md)
