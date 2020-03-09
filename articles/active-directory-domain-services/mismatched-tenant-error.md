---
title: Opravte chyby neodpovídajících adresářů v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, co znamená chyba neshody adresáře a jak ho vyřešit v Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613380"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Řešení chyb neodpovídajících adresářů pro existující Azure AD Domain Services spravované domény

Pokud spravovaná doména Azure Active Directory Domain Services (Azure služba AD DS) zobrazuje neshodnou chybu tenanta, nemůžete spravovat spravovanou doménu, dokud nebude vyřešená. K této chybě dojde, pokud je základní virtuální síť Azure přesunuta do jiného adresáře služby Azure AD.

Tento článek vysvětluje, proč k chybě dochází a jak ho vyřešit.

## <a name="what-causes-this-error"></a>Co způsobuje tuto chybu?

Neshodná Chyba adresáře se stane, když spravovaná doména a virtuální síť Azure služba AD DS patří do dvou různých tenantů Azure AD. Můžete mít například spravovanou doménu Azure služba AD DS s názvem *aaddscontoso.com* , která běží v TENANTOVI Azure AD společnosti Contoso. Virtuální síť Azure pro spravovanou doménu je ale součástí tenanta Azure AD společnosti Fabrikam.

Azure používá řízení přístupu na základě role (RBAC) k omezení přístupu k prostředkům. Pokud povolíte Azure služba AD DS v tenantovi Azure AD, hodnoty hash přihlašovacích údajů se synchronizují do spravované domény. Tato operace vyžaduje, abyste byli správcem tenanta pro adresář služby Azure AD a aby bylo možné řídit přístup k přihlašovacím údajům. K nasazení prostředků do virtuální sítě Azure a řízení provozu musíte mít ve virtuální síti, ve které nasazujete Azure služba AD DS, oprávnění správce.

Aby RBAC fungovalo konzistentně a zabezpečenému přístupu ke všem prostředkům, které Azure služba AD DS používá, musí Tato spravovaná doména a virtuální síť patřit do stejného tenanta služby Azure AD.

V prostředí Správce prostředků platí následující pravidla:

- Adresář služby Azure AD může mít několik předplatných Azure.
- Předplatné Azure může mít několik prostředků, jako jsou virtuální sítě.
- Pro adresář služby Azure AD je povolená jediná Azure AD Domain Services spravovaná doména.
- Ve virtuální síti, která patří do jednoho nebo více předplatných Azure v rámci stejného tenanta služby Azure AD, může být povolená Azure AD Domain Services spravovaná doména.

### <a name="valid-configuration"></a>Platná konfigurace

V následujícím ukázkovém scénáři nasazení je v tenantovi Azure AD ve společnosti Contoso povolená spravovaná doména contoso Azure služba AD DS. Spravovaná doména je nasazená ve virtuální síti, která patří k předplatnému Azure, které vlastní tenant služby Azure AD společnosti Contoso. Spravovaná doména i virtuální síť patří do stejného tenanta služby Azure AD. Tato příklad konfigurace je platná a plně podporovaná.

![Platná konfigurace tenanta Azure služba AD DS se spravovanou doménou a částí virtuální sítě stejného tenanta Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Neshoda konfigurace tenanta

V tomto ukázkovém scénáři nasazení je v tenantovi Azure AD ve společnosti Contoso povolená spravovaná doména contoso Azure služba AD DS. Spravovaná doména se ale nasadí ve virtuální síti, která patří do předplatného Azure, které vlastní tenant služby Azure AD společnosti Fabrikam. Spravovaná doména a virtuální síť patří do dvou různých tenantů Azure AD. Tato příklad konfigurace nesouhlasí tenanta a není podporovaný. Virtuální síť musí být přesunutá do stejného tenanta Azure AD jako spravovaná doména.

![Neshoda konfigurace tenanta](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Vyřešit chybu neshody tenanta

Následující dvě možnosti vyřeší chybu neodpovídajícího adresáře:

* Odstraňte ze stávajícího adresáře Azure AD [spravovanou doménu azure služba AD DS](delete-aadds.md) . Ve stejném adresáři Azure AD jako virtuální síť, kterou chcete použít, [vytvořte náhradní azure služba AD DS spravovanou doménu](tutorial-create-instance.md) . Až budete připraveni, připojte všechny počítače dříve připojené k odstraněné doméně k znovu vytvořené spravované doméně.
* [Přesuňte předplatné Azure](../cost-management-billing/manage/billing-subscription-transfer.md) obsahující virtuální síť do stejného adresáře služby Azure AD, jako je spravovaná doména Azure služba AD DS.

## <a name="next-steps"></a>Další kroky

Další informace o řešení problémů se službou Azure služba AD DS najdete v [Průvodci odstraňováním potíží](troubleshoot.md).
