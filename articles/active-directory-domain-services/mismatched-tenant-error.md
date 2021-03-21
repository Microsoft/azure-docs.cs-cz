---
title: Opravte chyby neodpovídajících adresářů v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, co znamená chyba neshody adresáře a jak ho vyřešit v Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619296"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Řešení chyb neodpovídajících adresářů pro existující Azure Active Directory Domain Services spravované domény

Pokud spravovaná doména Azure Active Directory Domain Services (Azure služba AD DS) zobrazuje neshodnou chybu tenanta, nemůžete spravovat spravovanou doménu, dokud nebude vyřešená. K této chybě dojde, pokud je základní virtuální síť Azure přesunuta do jiného adresáře služby Azure AD.

Tento článek vysvětluje, proč k chybě dochází a jak ho vyřešit.

## <a name="what-causes-this-error"></a>Co způsobuje tuto chybu?

Neshodná Chyba adresáře se stane, když spravovaná doména a virtuální síť Azure služba AD DS patří do dvou různých tenantů Azure AD. Můžete mít například spravovanou doménu s názvem *aaddscontoso.com* , která běží v TENANTOVI Azure AD společnosti Contoso. Virtuální síť Azure pro spravovanou doménu je ale součástí tenanta Azure AD společnosti Fabrikam.

Řízení přístupu na základě role Azure (Azure RBAC) slouží k omezení přístupu k prostředkům. Pokud povolíte Azure služba AD DS v tenantovi Azure AD, hodnoty hash přihlašovacích údajů se synchronizují do spravované domény. Tato operace vyžaduje, abyste byli správcem tenanta pro adresář služby Azure AD a aby bylo možné řídit přístup k přihlašovacím údajům.

Chcete-li nasadit prostředky do virtuální sítě Azure a řídit provoz, musíte mít oprávnění správce ve virtuální síti, ve které nasadíte spravovanou doménu.

Aby mohla služba Azure RBAC pracovat konzistentně a zabezpečeným přístupem ke všem prostředkům, které Azure služba AD DS používá, musí Tato spravovaná doména a virtuální síť patřit do stejného tenanta Azure AD.

Pro nasazení platí následující pravidla:

- Adresář služby Azure AD může mít několik předplatných Azure.
- Předplatné Azure může mít několik prostředků, jako jsou virtuální sítě.
- Pro adresář služby Azure AD je povolena jedna spravovaná doména.
- Spravovaná doména se dá povolit ve virtuální síti, která patří do libovolného předplatného Azure v rámci stejného tenanta Azure AD.

### <a name="valid-configuration"></a>Platná konfigurace

V následujícím příkladu scénáře nasazení je spravovaná doména contoso povolená v tenantovi služby Azure AD společnosti Contoso. Spravovaná doména je nasazená ve virtuální síti, která patří k předplatnému Azure, které vlastní tenant služby Azure AD společnosti Contoso.

Spravovaná doména i virtuální síť patří do stejného tenanta služby Azure AD. Tato příklad konfigurace je platná a plně podporovaná.

![Platná konfigurace tenanta Azure služba AD DS se spravovanou doménou a částí virtuální sítě stejného tenanta Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Neshoda konfigurace tenanta

V tomto ukázkovém scénáři se v tenantovi Azure AD ve společnosti Contoso povolí spravovaná doména contoso. Spravovaná doména se ale nasadí ve virtuální síti, která patří do předplatného Azure, které vlastní tenant služby Azure AD společnosti Fabrikam.

Spravovaná doména a virtuální síť patří do dvou různých tenantů Azure AD. Tato příklad konfigurace nesouhlasí tenanta a není podporovaný. Virtuální síť musí být přesunutá do stejného tenanta Azure AD jako spravovaná doména.

![Neshoda konfigurace tenanta](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Vyřešit chybu neshody tenanta

Následující dvě možnosti vyřeší chybu neodpovídajícího adresáře:

* Nejdřív [odstraňte spravovanou doménu](delete-aadds.md) ze stávajícího adresáře služby Azure AD. Pak [vytvořte náhradní spravovanou doménu](tutorial-create-instance.md) ve stejném adresáři služby Azure AD jako virtuální síť, kterou chcete použít. Až budete připraveni, připojte všechny počítače dříve připojené k odstraněné doméně k znovu vytvořené spravované doméně.
* [Přesuňte předplatné Azure](../cost-management-billing/manage/billing-subscription-transfer.md) obsahující virtuální síť do stejného adresáře služby Azure AD, jako je spravovaná doména.

## <a name="next-steps"></a>Další kroky

Další informace o řešení problémů se službou Azure služba AD DS najdete v [Průvodci odstraňováním potíží](troubleshoot.md).
