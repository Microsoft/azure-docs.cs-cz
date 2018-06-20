---
title: Vyřešte chyby neodpovídající directory pro existující spravované domény služby Azure AD Domain Services | Microsoft Docs
description: Rady pro pochopení a řešení chyb neodpovídající directory pro existující spravované domény služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: maheshu
ms.openlocfilehash: 7e7786ac36485a792e9c77b10925f01790f95ab1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218193"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Vyřešte chyby neodpovídající directory pro existující spravované domény služby Azure AD Domain Services
Máte existující spravované doméně služby Azure AD Domain Services. Když přejdete na portál Azure a zobrazení spravované doméně, zobrazí se následující chybová zpráva:

![Chyba neodpovídající adresáře](.\media\getting-started\mismatched-tenant-error.png)

Až do vyřešení chyba není možné spravovat toto spravované domény.


## <a name="whats-causing-this-error"></a>Co je příčinou této chyby?
K této chybě dojde, pokud vaší spravované domény a virtuální sítě je povolený v patří dva různé klienty Azure AD. Například máte spravované doménu "contoso.com" a bylo povoleno pro klienta Azure AD společnosti Contoso. Však virtuální síť Azure, ve kterém byl povolen spravované domény patří do Fabrikam - jiné klienta Azure AD.

Nový portál Azure (a konkrétně rozšíření Azure AD Domain Services) je založený na Azure Resource Manager. Moderní prostředí Azure Resource Manager vynutí se určitá omezení a poskytovat vyšší úroveň zabezpečení pro přístup na základě rolí k řízení (RBAC) k prostředkům. Povolení služby Azure AD Domain Services pro klienta služby Azure AD je citlivé operace, protože způsobí, že hodnoty hash přihlašovacích údajů k synchronizaci k spravované doméně. Tuto operaci, musíte být správcem klienta pro adresář. Kromě toho musí mít oprávnění správce přes virtuální síť, ve kterém povolíte spravované domény. Pro RBAC kontroly, které mají pracují konzistentně spravované doméně a virtuální síť musí náležet do stejné klienta Azure AD.

Stručně řečeno nelze povolit spravované domény pro klienta služby Azure AD "contoso.com" ve virtuální síti, které patří k předplatnému Azure vlastníkem jiného klienta Azure AD, fabrikam.com'. 

**Platná konfigurace**: V tomto scénáři nasazení spravované domény Contoso je povolený pro klientovi Contoso Azure AD. Spravované domény je vystaven ve virtuální síti, které patří k předplatnému Azure vlastníkem klientovi Contoso Azure AD. Spravované doméně jak virtuální sítě, tedy patřit do stejné klienta Azure AD. Tato konfigurace je platný a plně podporované.

![Konfiguraci platný klienta](./media/getting-started/valid-tenant-config.png)

**Konfiguraci neodpovídající klienta**: V tomto scénáři nasazení spravované domény Contoso je povolený pro klientovi Contoso Azure AD. Nicméně spravované doméně je zveřejněné ve virtuální síti, který patří k předplatnému Azure vlastníkem klientské Fabrikam Azure AD. Proto spravované doméně a virtuální síť patří do dva různé klienty Azure AD. Tato konfigurace je konfiguraci neodpovídající klienta a není podporována. Virtuální sítě je třeba přesunout na stejném klientovi Azure AD (Contoso) jako spravované domény. Najdete v článku [řešení](#resolution) podrobnosti.

![Konfiguraci neodpovídající klienta](./media/getting-started/mismatched-tenant-config.png)

Proto když spravované doméně a virtuální sítě je povolený v patří dva různé klienty Azure AD se zobrazí tato chyba.

V prostředí Resource Manager, platí následující pravidla:
- Adresář služby Azure AD může mít víc předplatných Azure.
- Předplatné Azure může mít několik prostředků, jako je například virtuální sítě.
- Jeden spravované doméně služby Azure AD Domain Services je povolený pro adresář služby Azure AD.
- Spravované doméně služby Azure AD Domain Services můžete povolit pro virtuální sítě patřící do jakéhokoli z předplatných Azure v rámci stejné klienta Azure AD.


## <a name="resolution"></a>Řešení
Máte dvě možnosti řešení chyba neodpovídající adresáře. Můžete:

- Klikněte **odstranit** tlačítko Odstranit existující spravované domény. Znovu vytvořte pomocí [portál Azure](https://portal.azure.com)tak, aby spravované domény a virtuální sítě je k dispozici v patří do adresáře služby Azure AD. Připojte všechny počítače dříve připojený k doméně odstraněné do nově vytvořené spravované domény.

- Přesuňte předplatné Azure, který obsahuje virtuální sítě do adresáře Azure AD, do které patří vaší spravované domény. Postupujte podle kroků v [přenos vlastnictví předplatného služby Azure na jiný účet](../billing/billing-subscription-transfer.md) článku.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Řešení potíží s Průvodce – Azure AD Domain Services](active-directory-ds-troubleshooting.md)
