---
title: Vytvoření a Správa zásad skupiny v Azure AD Domain Services | Microsoft Docs
description: Naučte se, jak upravit předdefinované objekty zásad skupiny (GPO) a vytvořit vlastní zásady ve Azure Active Directory Domain Services spravované doméně.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: c5ee162a78ac5e8c3080cd33c89fcee507ecfe92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704850"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Správa Zásady skupiny ve spravované doméně Azure Active Directory Domain Services

Nastavení pro objekty uživatelů a počítačů v Azure Active Directory Domain Services (Azure služba AD DS) se často spravují pomocí objektů Zásady skupiny (objekty zásad skupiny). Azure služba AD DS obsahuje integrované objekty zásad skupiny pro kontejnery *uživatelů AADDC* a *počítačů AADDC* . Tyto předdefinované objekty zásad skupiny můžete přizpůsobit tak, aby Zásady skupiny podle potřeby pro vaše prostředí. Členové skupiny *Správci domény Azure AD* mají zásady skupiny oprávnění správce v doméně Azure služba AD DS a můžou také vytvářet vlastní objekty zásad skupiny a organizační jednotky (OU). Další informace o tom, co Zásady skupiny a jak funguje, najdete v článku [Zásady skupiny přehled][group-policy-overview].

V hybridním prostředí se zásady skupiny nakonfigurované v místních služba AD DS prostředí nesynchronizují s Azure služba AD DS. Pokud chcete definovat nastavení konfigurace pro uživatele nebo počítače v Azure služba AD DS, upravte jeden z výchozích objektů zásad skupiny nebo vytvořte vlastní objekt zásad skupiny.

V tomto článku se dozvíte, jak nainstalovat nástroje pro správu Zásady skupiny a pak upravit předdefinované objekty zásad skupiny a vytvořit vlastní objekty zásad skupiny.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows serveru, který je připojený k spravované doméně Azure služba AD DS.
    * V případě potřeby dokončete kurz a [vytvořte virtuální počítač s Windows serverem a připojte ho ke spravované doméně][create-join-windows-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

> [!NOTE]
> Pomocí Zásady skupiny Šablony pro správu můžete zkopírovat nové šablony do pracovní stanice pro správu. Zkopírujte soubory *. admx* do `%SYSTEMROOT%\PolicyDefinitions` a zkopírujte soubory. *ADML* specifické pro národní prostředí do `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , kde `Language-CountryRegion` odpovídá jazyku a oblasti souborů *. adml* .
>
> Zkopírujte například anglickou USA verzi souborů *. adml* do `\en-us` složky.
>
> Případně můžete centrálně Uložit Zásady skupiny šablonu pro správu na řadičích domény, které jsou součástí spravované domény. Další informace najdete v tématu [Vytvoření a Správa centrálního úložiště pro Zásady skupiny šablony pro správu v systému Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Instalace nástrojů pro správu Zásady skupiny

Pokud chcete vytvořit a nakonfigurovat objekt Zásady skupiny (GPO), musíte nainstalovat nástroje pro správu Zásady skupiny. Tyto nástroje je možné nainstalovat jako funkci Windows serveru. Další informace o tom, jak nainstalovat nástroje pro správu na klienta Windows, najdete v tématu Install [Nástroje pro vzdálenou správu serveru (RSAT)][install-rsat].

1. Přihlaste se ke svému VIRTUÁLNÍmu počítači pro správu. Postup, jak se připojit pomocí Azure Portal, najdete v tématu [připojení k virtuálnímu počítači s Windows serverem][connect-windows-server-vm].
1. Když se přihlásíte k virtuálnímu počítači, **Správce serveru** by se měly otevřít ve výchozím nastavení. Pokud ne, v nabídce **Start** vyberte **Správce serveru**.
1. V podokně *řídicí panel* v okně **Správce serveru** vyberte **Přidat role a funkce**.
1. Na stránce **než začnete** v *Průvodci přidáním rolí a funkcí*vyberte **Další**.
1. Pro *typ instalace*ponechte zaškrtnutou možnost instalace na základě **rolí nebo na základě funkcí** a vyberte **Další**.
1. Na stránce **Výběr serveru** zvolte aktuální virtuální počítač z fondu serverů, například *myvm.aaddscontoso.com*, a pak vyberte **Další**.
1. Na stránce **role serveru** klikněte na **Další**.
1. Na stránce **funkce** vyberte funkci **správy Zásady skupiny** .

    ![Instalace ' Zásady skupiny Management ' ze stránky funkce](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na stránce **potvrzení** vyberte **nainstalovat**. Instalace nástrojů pro správu Zásady skupiny může trvat minutu nebo dvě.
1. Po dokončení instalace funkce vyberte **Zavřít** a ukončete průvodce **přidáním rolí a funkcí** .

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Otevření Konzola pro správu zásad skupiny a úprava objektu

Pro uživatele a počítače ve spravované doméně existují výchozí objekty zásad skupiny (GPO). Pomocí funkce správy Zásady skupiny nainstalované v předchozí části si můžeme zobrazit a upravit existující objekt zásad skupiny. V další části vytvoříte vlastní objekt zásad skupiny.

> [!NOTE]
> Chcete-li spravovat zásady skupiny ve spravované doméně, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *AAD DC Administrators* .

1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, včetně **správy Zásady skupiny** nainstalovanou v předchozí části.
1. Chcete-li otevřít konzolu Konzola pro správu zásad skupiny (GPMC), vyberte možnost **Zásady skupiny Správa**.

    ![Konzola pro správu zásad skupiny se otevře jako připravená pro úpravu objektů zásad skupiny.](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Existují dva předdefinované Zásady skupiny objekty (GPO) ve spravované doméně – jeden pro kontejner *počítače AADDC* a jeden pro kontejner *AADDC Users* . Tyto objekty zásad skupiny můžete přizpůsobit tak, aby podle potřeby v spravované doméně nakonfigurovaly zásady skupiny.

1. V konzole **pro správu Zásady skupiny** rozbalte uzel **doménová struktura: aaddscontoso.com** . Dále rozbalte uzly **domény** .

    Pro *počítače s AADDC* a *uživatele AADDC*existují dva předdefinované kontejnery. U každého z těchto kontejnerů je použit výchozí objekt zásad skupiny.

    ![Předdefinované objekty zásad skupiny použité pro výchozí kontejnery AADDC počítače a AADDC Users](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Tyto předdefinované objekty zásad skupiny je možné přizpůsobit tak, aby v spravované doméně nakonfigurovaly konkrétní zásady skupiny. Pravým tlačítkem vyberte jeden z objektů zásad skupiny (GPO), třeba *AADDC počítače*, a pak zvolte **Upravit...**.

    ![Vyberte možnost upravit jeden z vestavěných objektů zásad skupiny.](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Otevře se nástroj Editor pro správu zásad skupiny, který umožňuje přizpůsobení objektu zásad skupiny (GPO), jako jsou například *Zásady účtů*:

    ![Snímek obrazovky Editor pro správu zásad skupiny.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Až budete hotovi, vyberte **soubor > Uložit** a zásadu uložte. Počítače se ve výchozím nastavení aktualizují Zásady skupiny každých 90 minut a použijí změny, které jste provedli.

## <a name="create-a-custom-group-policy-object"></a>Vytvořit vlastní objekt Zásady skupiny

Pokud chcete seskupit podobná nastavení zásad, často se místo použití všech požadovaných nastavení v jednom výchozím objektu zásad skupiny vytvoří další objekty zásad skupiny. Pomocí Azure služba AD DS můžete vytvářet nebo importovat vlastní objekty zásad skupiny a propojit je s vlastní organizační jednotkou. Pokud potřebujete nejdřív vytvořit vlastní organizační jednotku, přečtěte si téma [Vytvoření vlastní organizační jednotky ve spravované doméně](create-ou.md).

1. V konzole **pro správu Zásady skupiny** vyberte vlastní organizační jednotku (OU), například *MyCustomOU*. Klikněte pravým tlačítkem myši na organizační jednotku a zvolte možnost **vytvořit objekt zásad skupiny v této doméně a propojit jej sem...**:

    ![Vytvoření vlastního objektu zásad skupiny v konzole pro správu Zásady skupiny](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Zadejte název nového objektu zásad skupiny, třeba *můj vlastní objekt zásad skupiny*, a pak vyberte **OK**. Volitelně můžete tento vlastní objekt zásad skupiny založit na existujícím objektu zásad skupiny a sadě možností zásad.

    ![Zadejte název nového vlastního objektu zásad skupiny.](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Vlastní objekt zásad skupiny se vytvoří a propojí se s vlastní organizační jednotkou. Pokud teď chcete nakonfigurovat nastavení zásad, vyberte vlastní objekt zásad skupiny a zvolte **Upravit...**:

    ![Vyberte možnost Upravit vlastní objekt zásad skupiny.](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Otevře se **Editor pro správu zásad skupiny** , abyste mohli přizpůsobit objekt zásad skupiny:

    ![Přizpůsobením objektu zásad skupiny nakonfigurujte nastavení podle potřeby.](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Až budete hotovi, vyberte **soubor > Uložit** a zásadu uložte. Počítače se ve výchozím nastavení aktualizují Zásady skupiny každých 90 minut a použijí změny, které jste provedli.

## <a name="next-steps"></a>Další kroky

Další informace o dostupných nastaveních Zásady skupiny, která můžete nakonfigurovat pomocí Konzola pro správu zásad skupiny, najdete v tématu [work with zásady skupiny Item Preferences][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
