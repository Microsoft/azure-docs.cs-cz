---
title: 'Azure Active Directory Domain Services: Příručka pro správu | Dokumentace Microsoftu'
description: Vytvoření organizační jednotce (OU) v Azure AD Domain Services spravované domény
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: cd4964939aa9b9ebd96a2fb29e1a6f7bf422d477
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154824"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Vytvořit organizační jednotce (OU) ve spravované doméně Azure AD Domain Services
Spravované domény služby Azure AD Domain Services patří dvě předdefinované kontejnery nazývá "Kontejnery počítače AADDC" a "Uživatelé AADDC" v uvedeném pořadí. Kontejner 'Kontejnery počítače AADDC' obsahuje objekty počítače pro všechny počítače, které jsou připojené ke spravované doméně. Kontejner 'Uživatelé AADDC' obsahuje uživatele a skupiny v tenantovi Azure AD. V některých případech může být potřeba vytvořit účty služeb ve spravované doméně nasazování úloh. K tomuto účelu můžete vytvořit vlastní organizační jednotce (OU) ve spravované doméně a vytvořit účty služeb v rámci dané organizační jednotce. Tento článek ukazuje, jak vytvoření OU ve spravované doméně.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
K provádění úkolů uvedených v tomto článku, budete potřebovat:

1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ze kterého budete spravovat Azure AD Domain Services virtuální počítač připojený k doméně spravované domény. Pokud nemáte virtuální počítač, proveďte všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patřící do skupiny "Správci AAD DC"** ve vašem adresáři, chcete-li vytvořit vlastní organizační jednotky ve vaší spravované doméně.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Nainstalovat nástroje pro správu AD na virtuálním počítači připojeném k doméně pro vzdálenou správu
Spravované domény služby Azure AD Domain Services je možné spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory jako je správu Center Active Directory (ADAC) nebo Powershellu AD. Správci klientů nemají oprávnění k připojení k řadiči domény ve spravované doméně přes vzdálenou plochu. Správa spravované domény, nainstalujte funkci nástroje správy AD virtuálnímu počítači připojený ke spravované doméně. Přečtěte si článek s názvem [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md) pokyny.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Vytváření organizační jednotky ve spravované doméně
Teď, když jsou nainstalovány nástroje pro správu AD v doméně připojené k virtuálnímu počítači, můžeme použít tyto nástroje k vytvoření organizační jednotky ve spravované doméně. Proveďte následující kroky:

> [!NOTE]
> Pouze členové skupiny "Správci AAD DC" mají požadovaná oprávnění k vytvoření vlastní organizační jednotky. Ujistěte se, že provedete následující kroky jako uživatel, který patří do této skupiny.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět nástroje pro správu AD nainstalovaný na virtuálním počítači.

    ![Nástroje pro správu nainstalovaný na serveru](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klikněte na tlačítko **Centrum správy služby Active Directory**.

    ![Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Chcete-li zobrazit domény, klikněte na název domény v levém podokně (například "contoso100.com").

    ![Centrum ADAC – zobrazení domény](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Na pravé straně **úlohy** podokně klikněte na tlačítko **nový** pod uzlem název domény. V tomto příkladu klikneme **nový** pod uzlem 'contoso100(local)' na pravé straně **úlohy** podokně.

    ![Centrum ADAC – nové organizační jednotky](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Měli byste vidět možnost vytvořit organizační jednotku. Klikněte na tlačítko **organizační jednotka** ke spuštění **vytvořit organizační jednotku** dialogového okna.
6. V **vytvořit organizační jednotku** dialogové okno, zadejte **název** nové organizační jednotky. Zadejte krátký popis pro organizační jednotku. Můžete také nastavit **správce** pole pro organizační jednotku. Chcete-li vytvořit vlastní organizační jednotku, klikněte na tlačítko **OK**.

    ![Centrum ADAC – vytvoření organizační jednotky dialogu](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Nově vytvořený organizační jednotky by se měl zobrazit v AD pro správu System Center (ADAC).

    ![Centrum ADAC – vytvoření organizační jednotky](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Oprávnění a zabezpečení pro nově vytvořený organizační jednotky
Ve výchozím nastavení uživatele (člen skupiny "Správci AAD DC"), který vytvořil vlastní organizační jednotky uděleno oprávnění pro správu (Úplné řízení) nad organizační jednotku. Uživatel pak můžete pokračovat a udělit oprávnění k ostatním uživatelům nebo do skupiny "Správci AAD DC' podle potřeby. Jak je znázorněno na následujícím snímku obrazovky, že uživatel 'bob@domainservicespreview.onmicrosoft.com"kdo vytvořil nové organizační jednotky"MyCustomOU"je oprávnění k úplnému řízení nad ním.

 ![Centrum ADAC – zabezpečení nové organizační jednotky](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Poznámky k správě vlastní organizační jednotky
Teď, když jste vytvořili vlastní organizační jednotky, můžete pokračovat a vytvořit uživatelů, skupin, počítačů a účty služeb v této organizační jednotky. Uživatele nebo skupiny z "AADDC"OJ uživatele nelze přesunout do vlastní organizační jednotky.

> [!WARNING]
> Uživatelské účty, skupiny, účty služeb a objekty počítače, které vytvoříte v rámci vlastní organizační jednotky nejsou k dispozici ve vašem tenantovi Azure AD. Jinými slovy tyto objekty nezobrazovat pomocí Azure AD Graph API nebo v Uživatelském rozhraní služby Azure AD. Tyto objekty jsou k dispozici pouze ve vaší spravované doméně Azure AD Domain Services.
>
>

## <a name="related-content"></a>Související obsah
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konfigurace zásad skupiny ve spravované doméně](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centrum správy služby Active Directory: Začínáme](https://technet.microsoft.com/library/dd560651.aspx)
* [Podrobný průvodce účty služby](https://technet.microsoft.com/library/dd548356.aspx)
