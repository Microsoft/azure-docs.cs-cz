---
title: 'Azure Active Directory Domain Services: Příručka pro správu | Microsoft Docs'
description: Vytvoření organizační jednotce (OU) v doménách spravovaných Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 49123a47d90970d1a99276521b5aa1d516f509ab
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587585"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Vytvoření organizační jednotce (OU) na spravované doméně služby Azure AD Domain Services
Azure AD Domain Services spravovaných domén obsahovat dvě předdefinované kontejnery označované jako 'AADDC počítače' a 'AADDC uživatele'. Kontejner 'AADDC počítače, má objektů počítače pro všechny počítače, které jsou připojeny k spravované doméně. Kontejner 'AADDC uživatele' obsahuje uživatele a skupiny v klientovi Azure AD. V některých případech může být nutné vytvořit účty služeb na spravované domény k nasazení úloh. Pro tento účel můžete vytvořit vlastní organizační jednotce (OU) ve spravované doméně a vytvořte účty služby v rámci dané organizační jednotce. Tento článek ukazuje, jak vytvořit organizační jednotku ve vaší spravované domény.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:

1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Virtuální počítač připojený k doméně ze kterého můžete spravovat Azure AD Domain Services spravované domény. Pokud nemáte virtuálního počítače, postupujte podle všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patří do skupiny "Správci AAD řadič domény,** ve vašem adresáři, chcete-li vytvořit vlastní organizační jednotku na vaší spravované domény.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Nainstalujte nástroje pro správu AD na virtuální počítač připojený k doméně pro vzdálenou správu
Spravované domény služby Azure AD Domain Services můžete spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory jako správy Center Active Directory (ADAC) nebo AD PowerShell. Správci klientů nemají oprávnění pro připojení k řadiče domény na spravované domény přes vzdálenou plochu. Ke správě spravované doméně, nainstalujte na virtuální počítač připojený k spravované doméně funkce nástroje pro správu AD. Přečtěte si článek s názvem [spravovat spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md) pokyny.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Vytvořit organizační jednotku na spravované doméně
Jsou teď nainstalované nástroje pro správu AD v doméně virtuálního počítače, můžeme použít tyto nástroje vytvořit organizační jednotku na spravované domény. Proveďte následující kroky:

> [!NOTE]
> Pouze členové skupiny 'Administrators AAD řadič domény, má požadovaná oprávnění k vytvoření vlastní organizační jednotce. Ujistěte se, že provedete následující kroky jako uživatel, který patří do této skupiny.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět nástroje pro správu AD nainstalované na virtuálním počítači.

    ![Nástroje pro správu nainstalován na serveru](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klikněte na tlačítko **Centrum správy služby Active Directory**.

    ![Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Chcete-li zobrazit domény, klikněte na název domény v levém podokně (například "contoso100.com").

    ![Centrum ADAC - zobrazení domény](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Na pravé straně **úlohy** podokně klikněte na tlačítko **nový** pod uzlem název domény. V tomto příkladu jsme klikněte na **nový** pod uzlem 'contoso100(local)' na pravé straně **úlohy** podokně.

    ![Centrum ADAC - nové organizační jednotky](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Měli byste vidět možnost vytvořit organizační jednotku. Klikněte na tlačítko **organizační jednotky** ke spuštění **vytvořit organizační jednotku** dialogové okno.
6. V **vytvořit organizační jednotku** dialogové okno, zadejte **název** nové organizační jednotky. Zadejte krátký popis pro organizační jednotku. Můžete také nastavit **správce** pole pro organizační jednotku. Chcete-li vytvořit vlastní organizační jednotku, klikněte na tlačítko **OK**.

    ![Centrum ADAC - vytvořit organizační jednotku dialogové okno](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Nově vytvořený organizační jednotky by se měla zobrazit v AD správy Center (ADAC).

    ![Centrum ADAC - vytvořit organizační jednotku](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Oprávnění zabezpečení pro nově vytvořený organizační jednotky
Ve výchozím nastavení je uživatele (člen skupiny 'AAD řadič domény Administrators'), který vlastní organizační jednotce udělena oprávnění pro správu (Úplné řízení) přes organizační jednotku. Uživatele můžete pokračovat a udělit oprávnění ostatním uživatelům nebo do skupiny "Administrators AAD řadič domény, podle potřeby. Jak je vidět na následujícím snímku obrazovky uživatele 'bob@domainservicespreview.onmicrosoft.com, který vytvořil nové organizační jednotky, MyCustomOU' je oprávnění k úplnému řízení nad ním.

 ![Centrum ADAC - nového zabezpečení organizační jednotky](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Poznámky k správě vlastní organizační jednotky
Teď, když jste vytvořili vlastní organizační jednotky, můžete ihned začít a vytvořit uživatele, skupiny, počítače a účty služby v této organizační jednotce. Uživatelé nebo skupiny nelze přesunout z 'AADDC 'oj uživatelé vlastní organizačními jednotkami.

> [!WARNING]
> Uživatelské účty, skupiny, účty služeb a objekty počítače, které vytvoříte, vlastní organizačních jednotek nejsou k dispozici v klientovi služby Azure AD. Jinými slovy tyto objekty nezobrazovat nahoru pomocí rozhraní API služby Azure AD Graph nebo v uživatelském rozhraní Azure AD. Tyto objekty jsou k dispozici pouze ve vaší spravované doméně služby Azure AD Domain Services.
>
>

## <a name="related-content"></a>Související obsah
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konfigurace zásad skupiny na spravované doméně](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centrum správy služby Active Directory: Začínáme](https://technet.microsoft.com/library/dd560651.aspx)
* [Podrobný průvodce účty služby](https://technet.microsoft.com/library/dd548356.aspx)
