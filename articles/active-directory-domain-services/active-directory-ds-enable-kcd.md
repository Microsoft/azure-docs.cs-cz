---
title: 'Azure Active Directory Domain Services: Povolení vynuceného delegování protokolu kerberos | Microsoft Docs'
description: Povolení vynuceného delegování protokolu kerberos na spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 4c6e25972b47edf67dac8557e1925bb44463f4d6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331025"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Nakonfigurujte omezené delegování protokolu Kerberos (použitím KCD) na spravované doméně
Mnoho aplikací potřebovat přístup k prostředkům v kontextu uživatele. Služba Active Directory podporuje mechanismus delegování protokolu Kerberos, která umožňuje tento případ použití volat. Navíc můžete omezit delegování tak, aby se dalo přistupovat pouze konkrétní prostředky v kontextu uživatele. Spravované domény služby Azure AD Domain Services se liší od tradiční domén služby Active Directory, vzhledem k tomu, že jsou zamčené bezpečněji dolů.

Tento článek ukazuje, jak nakonfigurovat omezené delegování protokolu Kerberos na spravované doméně služby Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Omezené delegování protokolu Kerberos (použitím KCD)
Delegování protokolu Kerberos umožňuje účet zosobnění jiný objekt zabezpečení (například uživatel) pro přístup k prostředkům. Vezměte v úvahu webové aplikace, který přistupuje k back endové webové rozhraní API v kontextu uživatele. V tomto příkladu webové aplikace (spuštěn v kontextu účtu služby nebo účet počítače) zosobňuje uživatele při přístupu k prostředku (back endové webové rozhraní API). Delegování protokolu Kerberos je nezabezpečené, protože se neomezuje z prostředků, které vydává za jiný účet přístup v kontextu uživatele.

Omezené delegování protokolu Kerberos (použitím KCD) omezuje služby nebo prostředky, ke kterým může zadaný server fungovat jménem uživatele. Tradiční použitím KCD vyžaduje oprávnění správce domény nakonfigurovat účet domény pro službu a omezuje účet na jedinou doménu.

Tradiční použitím KCD má také několik problémů, které jsou s ním spojená. V dřívějších operačních systémech Pokud správce domény konfiguroval účet na základě použitím KCD pro službu, neměl Správce služeb nijak užitečné vědět, které front-endové služby delegované na služby prostředků vlastnil. A všechny front-endová služba, která mohla delegovat na službu prostředků reprezentována potenciální místo pro útok. Pokud serveru, který hostoval front-endové služby došlo k ohrožení, a že byl nakonfigurován pro delegoval na služby, služby prostředků může také dojít k ohrožení.

> [!NOTE]
> Na spravované doméně služby Azure AD Domain Services nemáte oprávnění správce domény. Proto **tradiční použitím KCD na základě účet nelze konfigurovat ve spravované doméně**. Použijte použitím KCD založené na prostředcích, jak je popsáno v tomto článku. Tento mechanismus je také bezpečnější.
>
>

## <a name="resource-based-kcd"></a>Použitím KCD založené na prostředcích
Správci služeb z Windows serveru 2012 a vyšší získáte možnost konfigurovat omezené delegování pro jejich službu. Správce služeb back-end v tomto modelu, můžete povolit nebo odepřít konkrétní front-endové služby pomocí použitím KCD. Tento model se označuje jako **založené na prostředcích použitím KCD**.

Na základě prostředků použitím KCD je nakonfigurován pomocí prostředí PowerShell. Můžete použít `Set-ADComputer` nebo `Set-ADUser` rutin, v závislosti na tom, zda je účet zosobnění účtu počítače nebo účet služby nebo účet uživatele.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurace založené na prostředcích použitím KCD pro účet počítače na spravované doméně
Předpokládejme, máte webovou aplikaci spuštěné v počítači "contoso100-webapp.contoso100.com'. Potřebuje pro přístup k prostředku (webového rozhraní API systémem "contoso100-api.contoso100.com') v kontextu uživatele domény. Zde je, jak by nastavit založené na prostředcích použitím KCD pro tento scénář:

1. [Vytvořit vlastní](active-directory-ds-admin-guide-create-ou.md). Můžete delegovat oprávnění ke správě tuto vlastní organizační jednotku pro uživatele v rámci spravované domény.
2. Připojte virtuální počítače (jedna spuštěno webové aplikace a jedna spuštěno webového rozhraní API) k spravované doméně. Vytvořte tyto účty počítačů v rámci vlastní organizační jednotce.
3. Nyní nakonfigurujte založené na prostředcích použitím KCD pomocí následujícího příkazu Powershellu:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Účty počítačů pro webovou aplikaci a webového rozhraní API musí být ve vlastní organizační jednotce, kde máte oprávnění ke konfiguraci použitím KCD založené na prostředcích. V předdefinované kontejneru 'AAD řadič domény počítače, nelze nakonfigurovat založené na prostředcích použitím KCD pro účet počítače.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurace založené na prostředcích použitím KCD pro uživatelský účet na spravované doméně
Předpokládejme, že máte webové aplikace spuštěna jako účet služby, appsvc' a musí se přístup k prostředku (web API spuštěna jako účet služby - 'backendsvc') v kontextu uživatele domény. Zde je, jak by nastavit založené na prostředcích použitím KCD pro tento scénář.

1. [Vytvořit vlastní](active-directory-ds-admin-guide-create-ou.md). Můžete delegovat oprávnění ke správě tuto vlastní organizační jednotku pro uživatele v rámci spravované domény.
2. Připojte virtuální počítač s back-endové webové rozhraní API nebo prostředků k spravované doméně. Vytvořte svůj účet počítače v rámci vlastní organizační jednotce.
3. Vytvoření účtu služby (například "appsvc"), které slouží ke spuštění webové aplikace v rámci vlastní organizační jednotce.
4. Nyní nakonfigurujte založené na prostředcích použitím KCD pomocí následujícího příkazu Powershellu:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Účet počítače pro back-endové webové rozhraní API a být ve vlastní organizační jednotky, ve které máte oprávnění ke konfiguraci na základě prostředků použitím KCD nutnost účtu služby. Nelze nakonfigurovat použitím založené na prostředcích KCD pro účet počítače v předdefinované kontejneru 'AAD řadič domény počítače, nebo pro uživatelské účty v předdefinované kontejneru 'AAD řadič domény uživatele'. Proto nelze použít uživatelské účty synchronizovány z Azure AD nastavit použitím KCD založené na prostředcích.
>

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Omezené delegování přehled protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
