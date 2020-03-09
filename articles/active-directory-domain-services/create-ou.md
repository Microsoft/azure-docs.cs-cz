---
title: Vytvoření organizační jednotky (OU) v Azure AD Domain Services | Microsoft Docs
description: Naučte se, jak vytvořit a spravovat vlastní organizační jednotku (OU) ve spravované doméně Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 7abbdf03e85f425f65a45e6640b82529c2b9c84f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614065"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Vytvoření organizační jednotky (OU) ve Azure AD Domain Services spravované doméně

Organizační jednotky (OU) v Active Directory Domain Services (služba AD DS) umožňují logicky seskupovat objekty, jako jsou uživatelské účty, účty služeb nebo účty počítačů. Pak můžete přiřadit správce ke konkrétním organizačním jednotkám a použít zásady skupiny k vystavování cílových nastavení konfigurace.

Spravované domény Azure služba AD DS zahrnují dva předdefinované *AADDC počítače* a *AADDC uživatele*. Organizační jednotka *počítačů s AADDC* obsahuje objekty počítače pro všechny počítače, které jsou připojené ke spravované doméně. Organizační jednotka *uživatelů AADDC* zahrnuje uživatele a skupiny synchronizované v rámci TENANTA Azure AD. Když vytváříte a spouštíte úlohy, které používají Azure služba AD DS, možná budete muset vytvořit účty služeb pro aplikace, které se budou ověřovat sami. K organizování těchto účtů služeb často vytváříte vlastní organizační jednotku ve spravované doméně Azure služba AD DS a pak vytvoříte účty služeb v této organizační jednotce.

V hybridním prostředí se organizační jednotky vytvořené v místním prostředí služba AD DS nesynchronizují se službou Azure služba AD DS. Spravované domény Azure služba AD DS používají strukturu ploché organizační jednotky. Všechny uživatelské účty a skupiny se ukládají do kontejneru *AADDC Users* , a to i v případě, že se synchronizují z různých místních domén nebo doménových struktur, a to i v případě, že jste nakonfigurovali hierarchickou strukturu organizační jednotky.

V tomto článku se dozvíte, jak vytvořit organizační jednotku ve spravované doméně Azure služba AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte instanci Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows serveru, který je připojený k spravované doméně Azure služba AD DS.
    * V případě potřeby dokončete kurz a [vytvořte virtuální počítač pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Vlastní doporučení a omezení organizační jednotky

Když vytvoříte vlastní organizační jednotky ve spravované doméně Azure služba AD DS, získáte další flexibilitu správy uživatelů a používání zásad skupiny. V porovnání s místním služba AD DS prostředí existují určitá omezení a požadavky při vytváření a správě vlastní struktury organizační jednotky v Azure služba AD DS:

* Chcete-li vytvořit vlastní organizační jednotky, uživatelé musí být členy skupiny *AAD DC Administrators* .
* Uživatel, který vytváří vlastní organizační jednotku, má prostřednictvím této organizační jednotky oprávnění správce (úplné řízení) a je vlastníkem prostředku.
    * Ve výchozím nastavení má skupina *AAD DC Administrators* také úplnou kontrolu nad vlastní organizační jednotkou.
* Vytvoří se výchozí organizační jednotka pro *uživatele AADDC* , která obsahuje všechny synchronizované uživatelské účty z vašeho TENANTA Azure AD.
    * Nemůžete přesunout uživatele nebo skupiny z organizační jednotky *AADDC Users* do vlastních organizačních jednotek, které vytvoříte. Do vlastních organizačních jednotek se dají přesunout jenom uživatelské účty nebo prostředky vytvořené ve spravované doméně Azure služba AD DS.
* Uživatelské účty, skupiny, účty služeb a objekty počítačů, které vytvoříte v části vlastní organizační jednotky, nejsou k dispozici ve vašem tenantovi Azure AD.
    * Tyto objekty se nezobrazují pomocí rozhraní Microsoft Graph API ani v uživatelském rozhraní Azure AD; jsou k dispozici pouze ve spravované doméně Azure služba AD DS.

## <a name="create-a-custom-ou"></a>Vytvoření vlastní organizační jednotky

Pokud chcete vytvořit vlastní organizační jednotku, použijte nástroje pro správu služby Active Directory z virtuálního počítače připojeného k doméně. Centrum správy služby Active Directory umožňuje zobrazovat, upravovat a vytvářet prostředky ve spravované doméně Azure služba AD DS, včetně organizačních jednotek.

> [!NOTE]
> Pokud chcete vytvořit vlastní organizační jednotku ve spravované doméně služba AD DS Azure, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *Správci AAD DC* .

1. Přihlaste se ke svému VIRTUÁLNÍmu počítači pro správu. Postup, jak se připojit pomocí Azure Portal, najdete v tématu [připojení k virtuálnímu počítači s Windows serverem][connect-windows-server-vm].
1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, které byly nainstalovány v tomto kurzu, aby bylo možné [vytvořit virtuální počítač pro správu][tutorial-create-management-vm].
1. Chcete-li vytvořit a spravovat organizační jednotky, vyberte **Centrum správy služby Active Directory** ze seznamu nástrojů pro správu.
1. V levém podokně vyberte spravovanou doménu Azure služba AD DS, například *aaddscontoso.com*. Zobrazí se seznam existujících organizačních jednotek a prostředků:

    ![Vyberte spravovanou doménu Azure služba AD DS v Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. Podokno **úlohy** se zobrazuje na pravé straně Centrum správy služby Active Directory. V části doména, jako je *aaddscontoso.com*, vyberte **Nový > organizační jednotka**.

    ![Vyberte možnost vytvoření nové organizační jednotky v Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. V dialogovém okně **vytvořit organizační jednotku** zadejte **název** nové organizační jednotky, například *MyCustomOu*. Zadejte krátký popis organizační jednotky, jako je například *vlastní organizační jednotka pro účty služeb*. V případě potřeby můžete také nastavit pole **spravované podle** pro organizační jednotku. Pokud chcete vytvořit vlastní organizační jednotku, vyberte **OK**.

    ![Vytvoření vlastní organizační jednotky z Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. Zpět v Centrum správy služby Active Directory je vlastní organizační jednotka nyní uvedená a je k dispozici pro použití:

    ![Vlastní organizační jednotka dostupná pro použití v Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Další kroky

Další informace o používání nástrojů pro správu nebo vytváření a používání účtů služeb najdete v následujících článcích:

* [Centrum správy služby Active Directory: Začínáme](https://technet.microsoft.com/library/dd560651.aspx)
* [Podrobný průvodce účty služby](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
