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
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 1e3546239dfcfd4c6ef23ad16f3340f34f958901
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723210"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Vytvoření organizační jednotky (OU) ve Azure Active Directory Domain Services spravované doméně

Organizační jednotky (OU) v spravované doméně Active Directory Domain Services (služba AD DS) umožňují logicky seskupovat objekty, jako jsou uživatelské účty, účty služeb nebo účty počítačů. Pak můžete přiřadit správce ke konkrétním organizačním jednotkám a použít zásady skupiny k vystavování cílových nastavení konfigurace.

Spravované domény Azure služba AD DS zahrnují následující dva předdefinované organizační jednotky:

* *AADDC počítače* – obsahuje objekty počítačů pro všechny počítače, které jsou připojené ke spravované doméně.
* *AADDC Users* – obsahuje uživatele a skupiny synchronizované v rámci TENANTA Azure AD.

Když vytváříte a spouštíte úlohy, které používají Azure služba AD DS, možná budete muset vytvořit účty služeb pro aplikace, které se budou ověřovat sami. K uspořádání těchto účtů služeb často vytvoříte vlastní organizační jednotku ve spravované doméně a pak vytvoříte účty služeb v rámci této organizační jednotky.

V hybridním prostředí se organizační jednotky vytvořené v místním prostředí služba AD DS nesynchronizují se spravovanými doménami. Spravované domény používají strukturu ploché organizační jednotky. Všechny uživatelské účty a skupiny se ukládají do kontejneru *AADDC Users* , a to i v případě, že se synchronizují z různých místních domén nebo doménových struktur, a to i v případě, že jste nakonfigurovali hierarchickou strukturu organizační jednotky.

V tomto článku se dozvíte, jak vytvořit organizační jednotku ve spravované doméně.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows serveru, který je připojený k spravované doméně Azure služba AD DS.
    * V případě potřeby dokončete kurz a [vytvořte virtuální počítač pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Vlastní doporučení a omezení organizační jednotky

Při vytváření vlastních organizačních jednotek ve spravované doméně získáte další flexibilitu správy uživatelů a používání zásad skupiny. V porovnání s místním služba AD DS prostředí existují určitá omezení a požadavky při vytváření a správě vlastní struktury organizační jednotky ve spravované doméně:

* Chcete-li vytvořit vlastní organizační jednotky, uživatelé musí být členy skupiny *AAD DC Administrators* .
* Uživatel, který vytváří vlastní organizační jednotku, má prostřednictvím této organizační jednotky oprávnění správce (úplné řízení) a je vlastníkem prostředku.
    * Ve výchozím nastavení má skupina *AAD DC Administrators* také úplnou kontrolu nad vlastní organizační jednotkou.
* Vytvoří se výchozí organizační jednotka pro *uživatele AADDC* , která obsahuje všechny synchronizované uživatelské účty z vašeho TENANTA Azure AD.
    * Nemůžete přesunout uživatele nebo skupiny z organizační jednotky *AADDC Users* do vlastních organizačních jednotek, které vytvoříte. Do vlastních organizačních jednotek lze přesunout pouze uživatelské účty nebo prostředky vytvořené ve spravované doméně.
* Uživatelské účty, skupiny, účty služeb a objekty počítačů, které vytvoříte v části vlastní organizační jednotky, nejsou k dispozici ve vašem tenantovi Azure AD.
    * Tyto objekty se nezobrazují pomocí rozhraní Microsoft Graph API ani v uživatelském rozhraní Azure AD; jsou k dispozici pouze ve spravované doméně.

## <a name="create-a-custom-ou"></a>Vytvoření vlastní organizační jednotky

Pokud chcete vytvořit vlastní organizační jednotku, použijte nástroje pro správu služby Active Directory z virtuálního počítače připojeného k doméně. Centrum správy služby Active Directory umožňuje zobrazit, upravit a vytvořit prostředky ve spravované doméně, včetně organizačních jednotek.

> [!NOTE]
> Chcete-li vytvořit vlastní organizační jednotku ve spravované doméně, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *AAD DC Administrators* .

1. Přihlaste se ke svému VIRTUÁLNÍmu počítači pro správu. Postup, jak se připojit pomocí Azure Portal, najdete v tématu [připojení k virtuálnímu počítači s Windows serverem][connect-windows-server-vm].
1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, které byly nainstalovány v tomto kurzu, aby bylo možné [vytvořit virtuální počítač pro správu][tutorial-create-management-vm].
1. Chcete-li vytvořit a spravovat organizační jednotky, vyberte **Centrum správy služby Active Directory** ze seznamu nástrojů pro správu.
1. V levém podokně vyberte spravovanou doménu, například *aaddscontoso.com*. Zobrazí se seznam existujících organizačních jednotek a prostředků:

    ![V Centrum správy služby Active Directory vyberte spravovanou doménu.](./media/create-ou/create-ou-adac-overview.png)

1. Podokno **úlohy** se zobrazuje na pravé straně Centrum správy služby Active Directory. V části doména, jako je *aaddscontoso.com*, vyberte **Nový > organizační jednotka**.

    ![Vyberte možnost vytvoření nové organizační jednotky v Centrum správy služby Active Directory](./media/create-ou/create-ou-adac-new-ou.png)

1. V dialogovém okně **vytvořit organizační jednotku** zadejte **název** nové organizační jednotky, například *MyCustomOu*. Zadejte krátký popis organizační jednotky, jako je například *vlastní organizační jednotka pro účty služeb*. V případě potřeby můžete také nastavit pole **spravované podle** pro organizační jednotku. Pokud chcete vytvořit vlastní organizační jednotku, vyberte **OK**.

    ![Vytvoření vlastní organizační jednotky z Centrum správy služby Active Directory](./media/create-ou/create-ou-dialog.png)

1. Zpět v Centrum správy služby Active Directory je vlastní organizační jednotka nyní uvedená a je k dispozici pro použití:

    ![Vlastní organizační jednotka dostupná pro použití v Centrum správy služby Active Directory](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Další kroky

Další informace o používání nástrojů pro správu nebo vytváření a používání účtů služeb najdete v následujících článcích:

* [Centrum správy služby Active Directory: Začínáme](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Podrobný průvodce účty služby](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm