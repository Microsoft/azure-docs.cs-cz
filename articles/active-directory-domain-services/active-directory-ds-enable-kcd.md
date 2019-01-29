---
title: 'Azure Active Directory Domain Services: Povolit vynucené delegování protokolu kerberos | Dokumentace Microsoftu'
description: Povolit vynucené delegování protokolu kerberos na spravovaných domén Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 5569344a2df560036b99dea40c466302f5e6fe4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159269"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurace omezeného delegování protokolu Kerberos (KCD) ve spravované doméně
Mnoho aplikací se musí pro přístup k prostředkům v kontextu uživatele. Služba Active Directory podporuje mechanismus delegování protokolu Kerberos, která umožňuje tento případ použití volána. Kromě toho můžete omezit delegování tak, že je v kontextu uživatele přístupný pouze konkrétní prostředky. Spravované domény služby Azure AD Domain Services se liší od tradiční domén služby Active Directory, protože jejich bezpečněji uzamčen.

V tomto článku se dozvíte, jak nakonfigurovat omezené delegování protokolu Kerberos ve spravované doméně Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Omezené delegování protokolu Kerberos (KCD)
Delegování protokolu Kerberos umožňuje účet zosobnění jiného objektu zabezpečení (například uživatel) pro přístup k prostředkům. Vezměte v úvahu webovou aplikaci, která přistupuje k back endové webové rozhraní API v kontextu uživatele. V tomto příkladu webové aplikace (spuštěného v kontextu účtu služby nebo účet počítače) zosobňuje uživatele při přístupu k prostředku (back endové webové rozhraní API). Delegování protokolu Kerberos je nezabezpečené, protože neomezuje prostředky, které účet zosobnění přístup v kontextu uživatele.

Omezené delegování protokolu Kerberos (KCD) omezuje služby a prostředky, ke kterým lze určený server jednat jménem uživatele. Tradiční KCD vyžaduje oprávnění správce domény a nakonfigurujte účet domény pro službu a omezuje účet na jedinou doménu.

Tradiční KCD má také několik problémů s ním spojená. Ve starších operačních systémech Pokud správce domény konfiguroval KCD založené na účtu služby, neměl Správce služeb žádný užitečný způsob, jak zjistit, které front-endové služby delegované na služby prostředků vlastnil. A front-end služba, která mohla delegovat na službu prostředků, představuje možné místo pro útok. Pokud došlo k napadení server, který hostoval front-endové služby, je nastavený tak, aby delegoval na služby, služby prostředků může také dojít k ohrožení.

> [!NOTE]
> Na spravované doméně služby Azure AD Domain Services nemáte oprávnění správce domény. Proto **tradiční KCD založené na účtu nelze konfigurovat ve spravované doméně**. Použijte KCD založené na prostředcích, jak je popsáno v tomto článku. Tento mechanismus je také bezpečnější.
>
>

## <a name="resource-based-kcd"></a>KCD založené na prostředcích
Ze systému Windows Server 2012 a vyšší Správci služeb získají možnost nakonfigurovat omezené delegování pro příslušnou službu. Správce back-end služby v tomto modelu, můžete povolit nebo odepřít konkrétní front-endové služby pomocí KCD. Tento model se nazývá **založené na prostředcích KCD**.

Založené na prostředcích KCD je nakonfigurovat pomocí Powershellu. Můžete použít `Set-ADComputer` nebo `Set-ADUser` rutin, v závislosti na tom, jestli účet zosobnění je počítačový účet nebo účet uživatele účtu nebo služby.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurace založené na prostředcích KCD pro účet počítače na spravované doméně
Předpokládá, že jste do webové aplikace spuštěné v počítači "contoso100-webapp.contoso100.com". Je potřeba přístup k prostředku (webové rozhraní API a systémem "contoso100-api.contoso100.com") v kontextu uživatele domény. Zde je, jak byste nastavili KCD založené na prostředcích pro tento scénář:

1. [Vytvoření vlastní organizační jednotky](active-directory-ds-admin-guide-create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele v rámci spravované domény.
2. Připojte se k oba virtuální počítače (jedno spuštění webové aplikace a, jaká běží webové rozhraní API) do spravované domény. Vytvoření těchto účtů počítačů v rámci vlastní organizační jednotce.
3. Nyní nakonfigurujte založené na prostředcích KCD pomocí následujícího příkazu Powershellu:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Účty počítače pro webovou aplikaci a webového rozhraní API musí být ve vlastní organizační jednotce, kde máte oprávnění pro konfiguraci založené na prostředcích KCD. Nelze konfigurovat KCD založené na prostředcích pro účet počítače v kontejneru integrované "AAD DC počítače".
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurace založené na prostředcích KCD u určitého uživatelského účtu ve spravované doméně
Předpokládejme, že máte webovou aplikaci spuštěna jako účet služby "appsvc" a je nutné pro přístup k prostředku (webové rozhraní API spuštěna jako účet služby - "backendsvc") v kontextu uživatele domény. Zde je, jak byste nastavili KCD založené na prostředcích pro tento scénář.

1. [Vytvoření vlastní organizační jednotky](active-directory-ds-admin-guide-create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele v rámci spravované domény.
2. Připojte se k virtuální počítač spuštěný back-endový prostředek webového rozhraní API/do spravované domény. Vytvořte svůj účet počítače v rámci vlastní organizační jednotce.
3. Vytvoření účtu služby (například "appsvc') použít ke spuštění webové aplikace v rámci vlastní organizační jednotce.
4. Nyní nakonfigurujte založené na prostředcích KCD pomocí následujícího příkazu Powershellu:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Účet počítače pro back-endové webové rozhraní API a nutnosti účtu služby ve vlastní organizační jednotky, ve kterém máte oprávnění pro konfiguraci založené na prostředcích KCD. V kontejneru integrované: uživatel AAD DC"nelze konfigurovat KCD prostředků pro účet počítače v kontejneru integrované"AAD DC počítače"nebo pro uživatelské účty. Proto nelze použít uživatelské účty synchronizované se službou Azure AD k nastavení KCD založené na prostředcích.
>

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Omezené delegování přehled protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
