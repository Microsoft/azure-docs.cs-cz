---
title: Oprava chyb neodpovídajícího adresáře ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, co znamená chyba neodpovídajícího adresáře a jak ji vyřešit ve službě Azure AD Domain Services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613380"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Řešení chyb neodpovídajícího adresáře pro existující spravované domény služby Azure AD Domain Services

Pokud spravovaná doména Služby Azure Active Directory Domain Services (Azure AD DS) zobrazuje chybu neodpovídajícího klienta, nemůžete spravovanou doménu spravovat, dokud nebude vyřešena. K této chybě dochází, pokud se základní virtuální síť Azure přesune do jiného adresáře Azure AD.

Tento článek vysvětluje, proč k chybě dochází a jak ji vyřešit.

## <a name="what-causes-this-error"></a>Co způsobuje tuto chybu?

Chyba neodpovídajícího adresáře se stane, když spravovaná doména a virtuální síť Azure AD DS patří do dvou různých klientů Azure AD. Například můžete mít spravovanou doménu Azure AD DS s názvem *aaddscontoso.com,* která běží v tenantovi Azure AD společnosti Contoso. Virtuální síť Azure pro spravovanou doménu je však součástí klienta Služby Azure AD společnosti Fabrikam.

Azure používá řízení přístupu na základě rolí (RBAC) k omezení přístupu k prostředkům. Když povolíte Azure AD DS v tenantovi Azure AD, budou se do spravované domény synchronizovat odhady přihlašovacích údajů. Tato operace vyžaduje, abyste byli správcem klienta pro adresář Azure AD a přístup k přihlašovacím údajům musí být řízen. Chcete-li nasadit prostředky do virtuální sítě Azure a řídit provoz, musíte mít oprávnění správce ve virtuální síti, ve které nasazujete Azure AD DS.

Aby RBAC fungoval konzistentně a zabezpečeně přístup ke všem prostředkům, které Azure AD DS používá, musí spravovaná doména a virtuální síť patřit do stejného klienta Azure AD.

V prostředí Správce prostředků platí následující pravidla:

- Adresář Azure AD může mít více předplatných Azure.
- Předplatné Azure může mít více prostředků, jako jsou virtuální sítě.
- Pro adresář Azure AD je povolená jedna spravovaná doména služby Azure AD Domain Services.
- Spravovaná doména služby Azure AD Domain Services může být povolená ve virtuální síti, která patří do některého z předplatných Azure v rámci stejného klienta Azure AD.

### <a name="valid-configuration"></a>Platná konfigurace

V následujícím příkladu scénáře nasazení je spravovaná doména Contoso Azure AD DS povolená v tenantovi Contoso Azure AD. Spravovaná doména se nasadí ve virtuální síti, která patří k předplatnému Azure vlastněném tenantem Contoso Azure AD. Spravovaná doména i virtuální síť patří do stejného klienta Azure AD. Tato příklad konfigurace je platná a plně podporována.

![Platná konfigurace klienta Azure AD DS se spravovanou doménou a součástí virtuální sítě stejného klienta Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Neodpovídající konfigurace klienta

V tomto příkladu scénáře nasazení je povolená spravovaná doména Contoso Azure AD DS v tenantovi Contoso Azure AD. Spravovaná doména se však nasadí ve virtuální síti, která patří k předplatnému Azure vlastněném klientem Služby Azure AD Společnosti Fabrikam. Spravovaná doména a virtuální síť patří dvěma různým klientům Azure AD. Tato ukázková konfigurace je neodpovídající klient a není podporována. Virtuální síť musí být přesunuta do stejného klienta Azure AD jako spravovaná doména.

![Neodpovídající konfigurace klienta](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Vyřešit chybu neodpovídajícího klienta

Následující dvě možnosti řeší chybu neodpovídajícího adresáře:

* [Odstraňte spravovanou doménu Azure AD DS](delete-aadds.md) ze svého stávajícího adresáře Azure AD. [Vytvořte náhradní spravovanou doménu Azure AD DS](tutorial-create-instance.md) ve stejném adresáři Azure AD jako virtuální síť, kterou chcete použít. Až budete připraveni, připojte všechny počítače, které byly dříve připojeny k odstraněné doméně, k obnovené spravované doméně.
* [Přesuňte předplatné Azure](../cost-management-billing/manage/billing-subscription-transfer.md) obsahující virtuální síť do stejného adresáře Azure AD jako spravovaná doména Azure AD DS.

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Azure AD DS najdete v [průvodci řešením potíží](troubleshoot.md).
