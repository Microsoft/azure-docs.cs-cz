---
title: 'Azure Active Directory Domain Services: Povolení synchronizace hodnot hash hesel | Microsoft Docs'
description: Začínáme se službou Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/02/2018
ms.author: maheshu
ms.openlocfilehash: 701fe17869d6e7744751d80330422ea47361b411
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216136"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Povolení synchronizace hodnot hash hesel do služby Azure Active Directory Domain Services
V předchozích úlohách jste povolili službu Azure Active Directory Domain Services pro tenanta služby Azure Active Directory (Azure AD). Další úlohou je povolení synchronizace hodnot hash hesel požadovaných pro ověřování protokolů NTLM (NT LAN Manager) a Kerberos do služby Azure AD Domain Services. Po nastavení synchronizace hodnot hash hesel se uživatelé můžou přihlásit ke spravované doméně s použitím podnikových přihlašovacích údajů.

Potřebný postup se liší pro uživatelské účty jenom cloudu a uživatelské účty synchronizované z místního adresáře pomocí služby Azure AD Connect. 

<br>
| **Typ uživatelského účtu** | **Požadovaný postup** |
| --- |---|
| **Cloudové uživatelské účty vytvořené ve službě Azure AD** |**&#x2713;** [Postupujte podle pokynů v tomto článku](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Uživatelské účty synchronizované z místního adresáře** |**&#x2713;** [Synchronizujte hodnot hash hesel pro uživatelské účty synchronizované z místní služby AD do spravované domény](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Možná bude nutné dokončit oba postupy.**
> Pokud je ve vašem tenantovi služby Azure AD kombinace uživatelů jenom cloudu a uživatelů z místní služby AD, musíte dokončit oba postupy.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Úkol 5: Povolení synchronizace hodnot hash hesel do spravované domény pro uživatelské účty jenom cloudu
K ověřování uživatelů ve spravované doméně služba Azure Active Directory Domain Services potřebuje hodnoty hash hesel ve formátu vhodném pro ověřování pomocí protokolů NTLM a Kerberos. Azure AD nebude generovat ani ukládat hodnoty hash hesel ve formátu požadovaném pro ověřování pomocí protokolů NTLM a Kerberos, dokud u svého tenanta nepovolíte službu Azure Active Directory Domain Services. Ze zřejmých bezpečnostních důvodů Azure AD také neukládá přihlašovací hesla jako nešifrovaný text. Proto v Azure AD neexistuje způsob, jak automaticky vygenerovat tyto hodnoty hash hesel protokolů NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

> [!NOTE]
> **Pokud má vaše organizace výhradně uživatelské účty jenom cloudu, všichni uživatelé, kteří potřebují používat službu Azure Active Directory Domain Services, si musí změnit heslo.** Uživatelský účet jenom cloudu je účet vytvořený v adresáři služby Azure AD pomocí webu Azure Portal nebo rutin Azure AD PowerShellu. Takové uživatelské účty se nesynchronizují z místního adresáře.
>
>

Tento proces změny hesla způsobí, že služba Azure AD vygeneruje hodnoty hash hesel vyžadované službou Azure Active Directory Domain Services pro ověřování pomocí protokolů Kerberos a NTLM. Můžete buď ukončit platnost hesel všech uživatelů v tenantovi, kteří potřebují používat službu Azure Active Directory Domain Services, nebo jim dát pokyny ke změně hesel.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Povolení generování hodnot hash hesel protokolů NTLM a Kerberos u uživatelského účtu jenom cloudu
Tyto pokyny je nutné poskytnout uživatelům, aby si mohli změnit hesla:

1. Přejděte na stránku s [přístupovým panelem Azure AD](http://myapps.microsoft.com) své organizace.

    ![Spusťte přístupový panel služby Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. V pravém horním rohu klikněte na své jméno a z nabídky vyberte **Profil**.

    ![Výběr profilu](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Na stránce **Profil** klikněte na **Změnit heslo**.

    ![Kliknutí na Změnit heslo](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Pokud se v okně s přístupovým panelem možnost **Změnit heslo** nezobrazí, ujistěte se, že je ve vaší organizaci nakonfigurovaná [správa hesel v Azure AD](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. Na stránce **Změnit heslo** zadejte stávající (staré) heslo, poté zadejte nové heslo a potvrďte je.

    ![Uživatel mění heslo](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Klikněte na **Odeslat**.

Několik minut poté, co si změníte heslo, je možné používat ve službě Azure Active Directory Domain Services nové heslo. Po přibližně 20 minutách se můžete přihlašovat k počítačům připojeným k spravované doméně pomocí nově změněného hesla.

## <a name="related-content"></a>Související obsah
* [Postup aktualizace vlastního hesla](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Začínáme se správou hesel v Azure AD](../active-directory/authentication/quickstart-sspr.md)
* [Povolení synchronizace hodnot hash hesel do služby Azure Active Directory Domain Services u synchronizovaného tenanta Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Správa spravované domény služby Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Připojení virtuálního počítače s Windows k spravované doméně služby Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Připojení virtuálního počítače se systémem Red Hat Enterprise Linux k spravované doméně služby Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
