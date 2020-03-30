---
title: Povolení služby Profil uživatele Služby SharePoint pomocí služby Azure AD DS | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat spravovanou doménu služby Azure Active Directory Domain Services pro podporu synchronizace profilů pro SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613865"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Konfigurace služby Azure Active Directory Domain Services pro podporu synchronizace profilů uživatelů pro SharePoint Server

SharePoint Server obsahuje službu pro synchronizaci profilů uživatelů. Tato funkce umožňuje, aby byly profily uživatelů uloženy v centrálním umístění a přístupné na více sharepointových webech a farmách. Chcete-li nakonfigurovat službu profilů uživatelů sharepointového serveru, musí být příslušná oprávnění udělena ve spravované doméně služby Azure Active Directory Domain Services (Azure AD DS). Další informace naleznete [v tématu synchronizace profilů uživatelů na sharepointovém serveru](https://technet.microsoft.com/library/hh296982.aspx).

Tento článek ukazuje, jak nakonfigurovat Azure AD DS tak, aby služba synchronizace profilu uživatele SharePoint Server.

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows Serveru, který je spojený se spravovanou doménou Azure AD DS.
    * V případě potřeby dokončete kurz [a vytvořte virtuální virtuální virtuální montovny pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.
* Účet služby SharePoint pro službu synchronizace profilů uživatelů.
    * V případě potřeby najdete [v tématu Plánování účtů pro správu a služby na SharePoint Serveru][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Přehled účtů služeb

Ve spravované doméně Azure AD DS existuje skupina zabezpečení s názvem **Účty služby Řadič domény řadiče domény AAD** jako součást organizační jednotky *Uživatelé* (OU). Členům této skupiny zabezpečení jsou delegována následující oprávnění:

- **Oprávnění Replikovat změny adresáře** v kořenovém adresáři DSE.
- **Oprávnění Replikovat změny adresáře** `cn=configuration` v názvovém kontextu *konfigurace* (kontejneru).

Skupina zabezpečení **Účty služby AAD DC** je také členem integrované skupiny **Pre-Windows 2000 Compatible Access**.

Po přidání do této skupiny zabezpečení je účtu služby pro službu synchronizace profilů uživatelů sharepointového serveru udělena požadovaná oprávnění ke správnému fungování.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Povolení podpory synchronizace profilů uživatelů sharepointového serveru

Účet služby pro SharePoint Server potřebuje odpovídající oprávnění k replikaci změn v adresáři a k tomu, aby synchronizace profilu uživatele sharepointového serveru fungovala správně. Chcete-li tato oprávnění poskytnout, přidejte účet služby používaný pro synchronizaci profilů uživatelů služby SharePoint do **skupiny Účty služby Řadič domény AAD.**

Z vašeho virtuálního počítače pro správu Azure AD DS proveďte následující kroky:

> [!NOTE]
> Chcete-li upravit členství ve skupině spravované službou Azure AD DS, musíte být přihlášeni k uživatelskému účtu, který je členem *skupiny Správci řadiče domény Řadič domény AAD.*

1. Na úvodní obrazovce vyberte **Nástroje pro správu**. Seznam dostupných nástrojů pro správu je zobrazen, které byly nainstalovány v kurzu [k vytvoření virtuálního virtuálního virtuálního uživatele pro správu][tutorial-create-management-vm].
1. Chcete-li spravovat členství ve skupině, vyberte ze seznamu nástrojů pro správu **Centrum správy služby Active Directory.**
1. V levém podokně zvolte spravovanou doménu Azure AD DS, například *aaddscontoso.com*. Zobrazí se seznam existujících vou s a prostředků.
1. Vyberte **ouuživatele** a pak zvolte skupinu *zabezpečení Účty služby Řadič domény AAD.*
1. Vyberte **Členové**, pak zvolte **Přidat...**.
1. Zadejte název účtu služby SharePoint a vyberte **OK**. V následujícím příkladu se účet služby SharePoint nazývá *spadmin*:

    ![Přidání účtu služby SharePoint do skupiny zabezpečení Účty služby AAD DC](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Další kroky

Další informace naleznete v [tématu Udělení oprávnění služby Active Directory Domain Services pro synchronizaci profilů na sharepointovém serveru](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
