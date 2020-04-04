---
title: Kerberos omezené delegování pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit delegování omezeného zabezpečení protokolu Kerberos založené na prostředcích (KCD) ve spravované doméně služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 07aa9ade25d1d986833b6da2f3907d07b752b662
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655430"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurace omezeného delegování protokolu Kerberos (KCD) ve službě Azure Active Directory Domain Services

Při spouštění aplikací může být potřeba, aby tyto aplikace měly přístup k prostředkům v kontextu jiného uživatele. Služba AD DS (Active Directory Domain Services) podporuje mechanismus nazvaný *delegování protokolu Kerberos,* který umožňuje tento případ použití. Kerberos *omezené delegování* (KCD) pak staví na tomto mechanismu definovat konkrétní prostředky, které lze přistupovat v kontextu uživatele. Spravované domény služby Azure Active Directory Domain Services (Azure AD DS) jsou bezpečněji uzamčené než tradiční místní prostředí služby AD DS, proto použijte bezpečnější disk KCD *založený na prostředcích.*

Tento článek ukazuje, jak nakonfigurovat delegování omezeného zabezpečení protokolu Kerberos založené na prostředcích ve spravované doméně Služby Azure AD DS.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento článek dokončit, potřebujete následující zdroje:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows Serveru, který je spojený se spravovanou doménou Azure AD DS.
    * V případě potřeby dokončete kurz [vytvoření virtuálního virtuálního uživatele se systémem Windows Server a připojte jej ke spravované doméně a][create-join-windows-vm] [nainstalujte nástroje pro správu služby AD DS][tutorial-create-management-vm].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Přehled delegování omezenou protokolem Kerberos

Delegování protokolu Kerberos umožňuje jednomu účtu zosobnit jiný účet pro přístup k prostředkům. Například webová aplikace, která přistupuje k webové součásti back-end, se může zosobnit jako jiný uživatelský účet při připojení back-end. Delegování protokolu Kerberos je nezabezpečené, protože neomezuje prostředky, ke zdrojům, ke které má účet zosobnění přístup.

Kerberos omezené delegování (KCD) omezuje služby nebo prostředky, které zadaný server nebo aplikace lze připojit při zosobnění jiné identity. Tradiční kcd vyžaduje oprávnění správce domény ke konfiguraci účtu domény pro službu a omezuje spuštění účtu v jedné doméně.

Tradiční KCD má také několik otázek. Například v dřívějších operačních systémech neměl správce služeb žádný užitečný způsob, jak zjistit, které front-endové služby delegovány na služby prostředků, které vlastnili. Všechny front-endové služby, které by mohly delegovat na službu prostředků byl potenciální bod útoku. Pokud byl ohrožen server, který hostoval front-endovou službu nakonfigurovanou pro delegování na služby prostředků, mohly by být ohroženy také služby prostředků.

Ve spravované doméně Azure AD DS nemáte oprávnění správce domény. V důsledku toho tradiční účet na základě KCD nelze nakonfigurovat ve službě Azure AD DS spravované domény. Místo toho lze použít kcd na bázi prostředků, což je také bezpečnější.

### <a name="resource-based-kcd"></a>KCD založené na zdrojích

Systém Windows Server 2012 a novější umožňuje správcům služeb konfigurovat omezené delegování pro svou službu. Tento model se označuje jako kcd na základě prostředků. S tímto přístupem může správce back-endové služby povolit nebo odepřít použití kcd určitým front-endovým službám.

KCD založené na prostředcích je konfigurováno pomocí prostředí PowerShell. Rutiny [Set-ADComputer][Set-ADComputer] nebo [Set-ADUser][Set-ADUser] se používají v závislosti na tom, zda je účet zosobnění účtem počítače nebo uživatelským účtem / účtem služby.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurace kcd KCD založené na prostředcích pro účet počítače

V tomto scénáři předpokládejme, že máte webovou aplikaci, která běží v počítači s názvem *contoso-webapp.aaddscontoso.com*. Webová aplikace potřebuje přístup k webovému rozhraní API, které běží v počítači s názvem *contoso-api.aaddscontoso.com* v kontextu uživatelů domény. Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

1. [Vytvořte vlastní ou .](create-ou.md) Oprávnění ke správě této vlastní individuální oužiny můžete delegovat uživatelům ve spravované doméně Azure AD DS.
1. [Připojení k virtuálním počítačům][create-join-windows-vm], jak ten, který spouští webovou aplikaci, a ten, který spouští webové rozhraní API, do spravované domény Azure AD DS. Vytvořte tyto účty počítačů ve vlastní hlavní výuce z předchozího kroku.

    > [!NOTE]
    > Účty počítače pro webovou aplikaci a webové rozhraní API musí být ve vlastní hlavní pracovní jednotky, kde máte oprávnění ke konfiguraci kcd založené na prostředcích. KCD na základě prostředků nelze nakonfigurovat pro účet počítače ve vestavěném kontejneru *Počítače řadiče domény AAD.*

1. Nakonec nakonfigurujte kcd založené na prostředcích pomocí rutiny [Prostředí Set-ADComputer][Set-ADComputer] PowerShell. Z vašeho virtuálního počítače pro správu připojovaný k doméně a přihlášen jako uživatelský účet, který je členem *skupiny správců Azure AD DC,* spusťte následující rutiny. Podle potřeby zadejte vlastní názvy počítačů:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurace kcd k cd založené na prostředcích pro uživatelský účet

V tomto scénáři předpokládejme, že máte webovou aplikaci, která běží jako účet služby s názvem *appsvc*. Webová aplikace potřebuje přístup k webovému rozhraní API, které běží jako účet služby s názvem *backendsvc* v kontextu uživatelů domény. Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

1. [Vytvořte vlastní ou .](create-ou.md) Oprávnění ke správě této vlastní individuální oužiny můžete delegovat uživatelům ve spravované doméně Azure AD DS.
1. [Připojení k virtuálním počítačům,][create-join-windows-vm] které spouštějí back-endové webové rozhraní API/prostředek do spravované domény Azure AD DS. Vytvořte svůj účet počítače v rámci vlastní hlavní výužné skupiny.
1. Vytvořte účet služby (například "appsvc"),který se používá ke spuštění webové aplikace v rámci vlastní hlavní okruh.

    > [!NOTE]
    > Účet počítače pro virtuální počítač webového rozhraní API a účet služby pro webovou aplikaci musí být opět ve vlastní hlavní pracovní hodnotě, kde máte oprávnění ke konfiguraci kcd založené na prostředcích. KCD k cd založené na prostředcích nelze konfigurovat pro účty ve *vestavěných kontejnerech počítačů AAD DC* nebo *AAD DC Users.* To také znamená, že nelze použít uživatelské účty synchronizované z Azure AD nastavit kcd založené na prostředcích. Musíte vytvořit a používat účty služeb speciálně vytvořené v Azure AD DS.

1. Nakonec nakonfigurujte kcd založené na prostředcích pomocí rutiny [prostředí Set-ADUser][Set-ADUser] PowerShell. Z vašeho virtuálního počítače pro správu připojovaný k doméně a přihlášen jako uživatelský účet, který je členem *skupiny správců Azure AD DC,* spusťte následující rutiny. Podle potřeby zadejte vlastní názvy služeb:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Další kroky

Další informace o fungování delegování ve službě Active Directory Domain Services naleznete v [tématu Přehled delegování s omezenou platností protokolu Kerberos][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
