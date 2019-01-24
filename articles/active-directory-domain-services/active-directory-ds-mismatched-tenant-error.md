---
title: Řešení chyb adresáře pro existující spravované domény služby Azure AD Domain Services | Dokumentace Microsoftu
description: Pochopení a vyřešení chyb adresáře pro existující spravované domény služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: ergreenl
ms.openlocfilehash: 7f69214304161c091a4550aff770b6333656972c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844207"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Řešení chyb adresáře pro existující spravované domény služby Azure AD Domain Services
Máte existující spravovanou doménu služby Azure AD Domain Services. Přejděte na web Azure Portal a zobrazení spravované domény, zobrazí se následující chybová zpráva:

![Chyba adresáře](./media/getting-started/mismatched-tenant-error.png)

Dokud se problém nevyřeší, budete moct spravovat tuto spravovanou doménu.


## <a name="whats-causing-this-error"></a>Co je příčinou této chyby?
K této chybě dojde, když vaše spravovaná doména a virtuální sítě je povolený v patří do dvou různých tenantů Azure AD. Například máte spravované doménu "contoso.com" a byla povolená pro tenanta Azure AD společnosti Contoso. Však patří Fabrikam - jinou virtuální síť Azure, ve kterém se povolila spravovaná doména tenanta Azure AD.

Na novém portálu Azure (a konkrétně rozšíření Azure AD Domain Services) je postavená na Azure Resource Manageru. V moderním prostředí Azure Resource Manageru určitá omezení se vynucují poskytovat lepší zabezpečení a přístupu na základě rolí ovládací prvek k prostředkům (RBAC). Povolení služby Azure AD Domain Services pro tenanta služby Azure AD je citlivé operace, protože to způsobí, že hodnoty hash přihlašovacích údajů, které se mají synchronizovat do spravované domény. Tato operace vyžaduje, abyste byli správce tenanta pro adresář. Kromě toho musí mít oprávnění pro správu přes virtuální síť, ve kterém povolíte spravované domény. RBAC se kontroly pracují konzistentně spravovaná doména a virtuální sítě by měly patřit do stejného tenanta služby Azure AD.

Stručně řečeno nelze povolit spravovanou doménu pro tenanta služby Azure AD "contoso.com" ve virtuální síti, které patří k předplatnému Azure vlastněn jiným tenantem Azure AD "fabrikam.com". 

**Platná konfigurace**: V tomto scénáři nasazení spravované domény Contoso je povolena pro příslušného tenanta Azure AD Contoso. Spravovaná doména je přístupný ve virtuální síti, které patří k předplatnému Azure vlastněných tenantem Azure AD Contoso. Proto spravovaná doména i virtuální síť patřit do stejného tenanta služby Azure AD. Tato konfigurace je platný a plně podporovaná.

![Konfigurace platné tenanta](./media/getting-started/valid-tenant-config.png)

**Konfigurace neshod tenantů**: V tomto scénáři nasazení spravované domény Contoso je povolena pro příslušného tenanta Azure AD Contoso. Spravovaná doména je však přístupný ve virtuální síti, která patří k předplatnému Azure vlastněných tenantem Azure AD Fabrikam. Proto spravovaná doména a virtuální síť patří do dvou různých tenantů Azure AD. Tato konfigurace je konfigurace neshod tenantů a není podporována. Virtuální síť musí přesunout do stejného tenanta služby Azure AD (Contoso) jako spravované domény. Zobrazit [rozlišení](#resolution) podrobné informace.

![Konfigurace neshod tenantů](./media/getting-started/mismatched-tenant-config.png)

Proto když spravovaná doména a virtuální sítě je povolený v patří dva různé tenanty Azure AD se zobrazí tato chyba.

V Resource Manageru prostředí platí následující pravidla:
- Adresář Azure AD může mít více předplatných Azure.
- Předplatné Azure může obsahovat několik prostředků, jako jsou virtuální sítě.
- Pro adresář Azure AD je povolené jediné spravované doméně Azure AD Domain Services.
- Spravované domény služby Azure AD Domain Services je možné povolit ve virtuální síti náležející k jakémukoli z předplatných Azure v rámci stejného tenanta služby Azure AD.


## <a name="resolution"></a>Řešení
Máte dvě možnosti, jak vyřešit chybu adresáře. Můžete:

- Klikněte na tlačítko **odstranit** tlačítko Odstranit existující spravované domény. Znovu vytvořte pomocí [webu Azure portal](https://portal.azure.com), takže bude spravovaná doména a virtuální sítě je k dispozici v patří do adresáře Azure AD. Připojte všechny počítače, které dříve připojen k doméně odstraněné na nově vytvořenou spravovanou doménu.

- Přesuňte předplatné Azure obsahující virtuální sítě do adresáře Azure AD, ke kterému patří vaší spravované domény. Postupujte podle pokynů [přenos vlastnictví předplatného Azure na jiný účet](../billing/billing-subscription-transfer.md) článku.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Řešení potíží s Průvodce – Azure AD Domain Services](active-directory-ds-troubleshooting.md)
