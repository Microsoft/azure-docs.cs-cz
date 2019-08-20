---
title: 'Azure Active Directory Domain Services: Povolit omezené delegování protokolu Kerberos | Microsoft Docs'
description: Povolit omezené delegování protokolu Kerberos ve Azure Active Directory Domain Services spravovaných doménách
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612944"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurace omezeného delegování protokolu Kerberos (KCD) ve spravované doméně
Mnoho aplikací potřebuje přístup k prostředkům v kontextu uživatele. Služba Active Directory podporuje mechanismus označovaný jako delegování protokolu Kerberos, který umožňuje použití tohoto případu. Dále můžete omezit delegování tak, aby přístup k určitým prostředkům byl v kontextu uživatele možné. Azure AD Domain Services spravované domény se liší od tradičních domén služby Active Directory, protože jsou bezpečněji uzamčené.

V tomto článku se dozvíte, jak nakonfigurovat omezené delegování protokolu Kerberos ve Azure AD Domain Services spravované doméně.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Omezené delegování protokolu Kerberos (KCD)
Delegování protokolu Kerberos umožňuje účtu zosobnit jiný objekt zabezpečení (například uživatel) pro přístup k prostředkům. Vezměte v úvahu webovou aplikaci, která přistupuje k back-endovému webovému rozhraní API v kontextu uživatele. V tomto příkladu webová aplikace (spuštěná v kontextu účtu služby nebo počítače/počítače) zosobňuje uživatele při přístupu k prostředku (back-endové webové rozhraní API). Delegování protokolu Kerberos je nezabezpečené, protože neomezuje prostředky, ke kterým má zosobněný účet přístup v kontextu uživatele.

Omezené delegování protokolu Kerberos (KCD) omezuje služby a prostředky, na které může zadaný server jednat jménem uživatele. Tradiční KCD vyžaduje oprávnění správce domény ke konfiguraci účtu domény pro službu a omezuje účet na jednu doménu.

K tradičním KCD je také přidruženo několik problémů. Pokud správce domény pro službu nakonfiguroval účet KCD založený na účtu, měl správce služby ve starších operačních systémech žádný užitečný způsob, jak zjistit, které front-endové služby jsou delegované službám prostředků, které vlastní. A všechny front-endové služby, které by mohly delegovat na službu prostředků, reprezentované potenciálním bodem útoku. Pokud došlo k ohrožení zabezpečení serveru, který hostoval front-end službu a byl nakonfigurován pro delegování na služby prostředků, mohlo by dojít k ohrožení zabezpečení služeb prostředků.

> [!NOTE]
> Ve spravované doméně Azure AD Domain Services nemáte oprávnění správce domény. Proto **na spravované doméně nejde nakonfigurovat tradiční KCD založený na účtu**. Použijte KCD podle prostředků, jak je popsáno v tomto článku. Tento mechanismus je také bezpečnější.
>
>

## <a name="resource-based-kcd"></a>KCD na základě prostředků
Od systému Windows Server 2012 a vyšších správců služeb získá možnost nakonfigurovat omezené delegování pro svou službu. V tomto modelu může správce back-end služeb povolit nebo odepřít konkrétní front-endové služby z používání KCD. Tento model se označuje jako **KCD na základě prostředků**.

KCD na základě prostředků se konfiguruje pomocí PowerShellu. Rutiny `Set-ADComputer` nebo `Set-ADUser` se používají v závislosti na tom, jestli se jedná o počítačový účet nebo účet uživatele nebo účtu služby.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurace KCD založených na prostředku pro účet počítače ve spravované doméně
Předpokládejme, že máte webovou aplikaci spuštěnou v počítači "contoso-webapp.contoso.com". Musí mít přístup k prostředku (webové rozhraní API běžící v ' contoso-api.contoso.com ') v kontextu doménových uživatelů. Tady je postup, jak byste pro tento scénář nastavili KCD založenou na prostředku:

1. [Vytvořte vlastní organizační jednotku](create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele ve spravované doméně.
2. Připojte se k virtuálním počítačům (tu, která spouští webovou aplikaci, a webovým rozhraním API) do spravované domény. Vytvořte tyto účty počítačů v rámci vlastní organizační jednotky.
3. Teď nakonfigurujte KCD podle prostředků pomocí následujícího příkazu PowerShellu:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Účty počítačů pro webovou aplikaci a webové rozhraní API musí být ve vlastní organizační jednotce, kde máte oprávnění ke konfiguraci KCD založených na prostředcích. KCD na základě prostředků nelze nakonfigurovat pro účet počítače v integrovaném kontejneru AAD DC Computers.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurace KCD založených na prostředcích pro uživatelský účet ve spravované doméně
Předpokládejme, že máte webovou aplikaci spuštěnou jako účet služby ' appsvc ' a ta musí mít přístup k prostředku (webové rozhraní API běžící jako účet služby-' backendsvc ') v kontextu doménových uživatelů. Tady je postup, jak byste pro tento scénář nastavili KCD založenou na prostředku.

1. [Vytvořte vlastní organizační jednotku](create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele ve spravované doméně.
2. Připojte se k virtuálnímu počítači, na kterém běží back-end webové rozhraní API nebo prostředek, do spravované domény. Vytvořte účet počítače v rámci vlastní organizační jednotky.
3. Vytvořte účet služby (například ' appsvc '), který se používá ke spuštění webové aplikace v rámci vlastní organizační jednotky.
4. Teď nakonfigurujte KCD podle prostředků pomocí následujícího příkazu PowerShellu:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Účet počítače webového rozhraní API back-endu i účet služby musí být ve vlastní organizační jednotce, kde máte oprávnění ke konfiguraci KCD založených na prostředku. KCD na základě prostředků pro účet počítače se nedá nakonfigurovat v předdefinovaném kontejneru AAD DC Computers nebo u uživatelských účtů v integrovaném kontejneru AAD DC Users. Proto nemůžete pomocí uživatelských účtů synchronizovaných z Azure AD nastavit KCD na základě prostředků.
>

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](tutorial-create-instance.md)
* [Přehled omezeného delegování protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
