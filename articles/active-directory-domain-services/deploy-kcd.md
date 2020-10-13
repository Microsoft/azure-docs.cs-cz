---
title: Omezené delegování protokolu Kerberos pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak povolit omezené delegování protokolu Kerberos založené na prostředku (KCD) ve spravované doméně Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 7f1640f74c3fca27e1d992f3e2cc538f6e2c7fef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722989"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurace omezeného delegování protokolu Kerberos (KCD) v Azure Active Directory Domain Services

Při spouštění aplikací může být potřeba, aby tyto aplikace měly přístup k prostředkům v kontextu jiného uživatele. Active Directory Domain Services (služba AD DS) podporuje mechanismus označovaný jako *delegování protokolu Kerberos* , který umožňuje použití tohoto případu použití. *Omezené* delegování protokolu Kerberos (KCD) následně sestaví na tomto mechanismu k definování konkrétních prostředků, ke kterým je možné přistupovat v kontextu uživatele.

Spravované domény Azure Active Directory Domain Services (Azure služba AD DS) jsou bezpečnějším způsobem uzamčené než tradiční místní služba AD DS prostředí, takže použijte bezpečnější KCD *založené na prostředcích* .

V tomto článku se dozvíte, jak nakonfigurovat omezené delegování protokolu Kerberos založené na prostředku ve spravované doméně Azure služba AD DS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete tyto prostředky:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows serveru, který je připojený k spravované doméně Azure služba AD DS.
    * V případě potřeby dokončete kurz [a vytvořte virtuální počítač s Windows serverem a připojte ho ke spravované doméně a][create-join-windows-vm] [nainstalujte nástroje pro správu služba AD DS][tutorial-create-management-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Přehled omezeného delegování protokolu Kerberos

Delegování protokolu Kerberos umožňuje jednomu účtu zosobnit jiný účet pro přístup k prostředkům. Například webová aplikace, která přistupuje k back-endové webové komponentě, se může při připojení k back-endu zosobnit jako jiný uživatelský účet. Delegování protokolu Kerberos je nezabezpečené, protože neomezuje prostředky, ke kterým má zosobněný účet přístup.

*Omezené* delegování protokolu Kerberos (KCD) omezuje služby nebo prostředky, které může zadaný server nebo aplikace připojit při zosobnění jiné identity. Tradiční KCD vyžaduje oprávnění správce domény ke konfiguraci účtu domény pro službu a omezuje účet tak, aby běžel v jedné doméně.

Tradiční KCD také obsahuje několik problémů. Například ve starších operačních systémech neměl správce služby žádný užitečný způsob, jak zjistit, které front-endové služby jsou delegovány službám prostředků, které vlastní. Všechny front-endové služby, které by mohly delegovat službu prostředků, byly potenciálního bodu útoku. Pokud došlo k ohrožení bezpečnosti serveru, který hostuje front-end službu nakonfigurovanou pro delegování na služby prostředků, mohly by taky dojít k ohrožení služeb prostředků.

Ve spravované doméně nemáte oprávnění správce domény. V důsledku toho se tradiční KCD založený na účtu nedá nakonfigurovat ve spravované doméně. Místo toho je možné použít KCD založené na prostředku, což je také bezpečnější.

### <a name="resource-based-kcd"></a>KCD na základě prostředků

Windows Server 2012 a novější poskytuje správcům služby možnost Konfigurace omezeného delegování pro jejich službu. Tento model se označuje jako KCD na základě prostředků. V rámci tohoto přístupu může správce back-end služeb povolit nebo odepřít konkrétní front-endové služby v používání KCD.

KCD na základě prostředků se konfiguruje pomocí PowerShellu. Rutiny [set-ADComputer][Set-ADComputer] nebo [set-ADUser][Set-ADUser] se používají v závislosti na tom, jestli je pro zosobněný účet počítačový účet nebo účet uživatele nebo účtu služby.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurace KCD na základě prostředků pro účet počítače

V tomto scénáři předpokládáme, že máte webovou aplikaci, která běží na počítači s názvem *Contoso-WebApp.aaddscontoso.com*.

Webová aplikace potřebuje přístup k webovému rozhraní API, které běží na počítači s názvem *Contoso-API.aaddscontoso.com* v kontextu doménových uživatelů.

Pro konfiguraci tohoto scénáře proveďte následující kroky:

1. [Vytvořte vlastní organizační jednotku](create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele ve spravované doméně.
1. [Doména se připojí k virtuálním počítačům][create-join-windows-vm], které používají webovou aplikaci, a tu, která spouští webové rozhraní API, do spravované domény. Vytvořte tyto účty počítačů ve vlastní organizační jednotce z předchozího kroku.

    > [!NOTE]
    > Účty počítačů pro webovou aplikaci a webové rozhraní API musí být ve vlastní organizační jednotce, kde máte oprávnění ke konfiguraci KCD založených na prostředcích. KCD na základě prostředků nejde nakonfigurovat pro účet počítače na integrovaném kontejneru *AAD DC Computers* .

1. Nakonec nakonfigurujte KCD založené na prostředku pomocí rutiny [set-ADComputer][Set-ADComputer] prostředí PowerShell.

    Z virtuálního počítače pro správu připojeného k doméně a přihlášený jako uživatelský účet, který je členem skupiny *Azure AD DC Administrators* , spusťte následující rutiny. Zadejte vlastní názvy počítačů podle potřeby:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurace KCD založených na prostředcích pro uživatelský účet

V tomto scénáři předpokládáme, že máte webovou aplikaci, která je spuštěná jako účet služby s názvem *appsvc*. Webová aplikace potřebuje přístup k webovému rozhraní API, které běží jako účet služby s názvem *backendsvc* v kontextu doménových uživatelů. Pro konfiguraci tohoto scénáře proveďte následující kroky:

1. [Vytvořte vlastní organizační jednotku](create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele ve spravované doméně.
1. [Doména – připojí virtuální počítače][create-join-windows-vm] , na kterých běží back-end webové rozhraní API nebo prostředek, do spravované domény. Vytvořte účet počítače v rámci vlastní organizační jednotky.
1. Vytvořte účet služby (například *appsvc*), který se používá ke spuštění webové aplikace v rámci vlastní organizační jednotky.

    > [!NOTE]
    > Účet počítače pro virtuální počítač s webovým rozhraním API a účet služby pro webovou aplikaci musí být ve vlastní organizační jednotce, kde máte oprávnění ke konfiguraci KCD založených na prostředku. Nemůžete nakonfigurovat KCD na základě prostředků pro účty na integrovaných *počítačích řadiče pro AAD* nebo na kontejnerech *uživatelů AAD* . To také znamená, že nemůžete pomocí uživatelských účtů synchronizovaných z Azure AD nastavit KCD na základě prostředků. Je potřeba vytvořit a použít účty služeb, které se konkrétně vytvořily v Azure služba AD DS.

1. Nakonec nakonfigurujte KCD založené na prostředku pomocí rutiny [set-ADUser][Set-ADUser] prostředí PowerShell.

    Z virtuálního počítače pro správu připojeného k doméně a přihlášený jako uživatelský účet, který je členem skupiny *Azure AD DC Administrators* , spusťte následující rutiny. Podle potřeby zadejte vlastní názvy služeb:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak delegování funguje v Active Directory Domain Services, najdete v tématu [Přehled omezeného delegování protokolu Kerberos][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)