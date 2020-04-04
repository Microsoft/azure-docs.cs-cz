---
title: Vytvoření a správa zásad skupiny ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak upravit předdefinované objekty zásad skupiny (GPO) a vytvořit vlastní zásady ve spravované doméně Služby Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 742d716ecdfff6ab67dedc281aa6134020f57add
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655035"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Správa zásad skupiny ve spravované doméně služby Azure AD Domain Services

Nastavení pro objekty uživatelů a počítačů ve službě Azure Active Directory Domain Services (Azure AD DS) se často spravují pomocí objektů zásad skupiny (GPO). Azure AD DS zahrnuje předdefinované objekty zásad skupinpro *kontejnery AADDC users* a *AADDC Computers.* Tyto předdefinované objekty Zásad skupiny můžete přizpůsobit tak, aby nakonfigurovaly zásady skupiny podle potřeby pro vaše prostředí. Členové *skupiny správců řadiče domény Azure AD DC* mají oprávnění pro správu zásad skupiny v doméně Azure AD DS a můžou také vytvářet vlastní objekty zásad skupiny a organizační jednotky (OU). Další informace o tom, co je zásady skupiny a jak fungují, najdete v [tématu Přehled zásad skupiny][group-policy-overview].

V hybridním prostředí nejsou zásady skupiny nakonfigurované v místním prostředí služby AD DS synchronizovány se službou Azure AD DS. Chcete-li definovat nastavení konfigurace pro uživatele nebo počítače ve službě Azure AD DS, upravte jeden z výchozích objektů zásad skupiny nebo vytvořte vlastní objekt zásad skupiny.

Tento článek ukazuje, jak nainstalovat nástroje pro správu zásad skupiny, potom upravit předdefinované objekty Zásad skupiny a vytvořit vlastní objekty Zásad skupiny.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows Serveru, který je spojený se spravovanou doménou Azure AD DS.
    * V případě potřeby dokončete kurz [vytvoření virtuálního virtuálního uživatele se systémem Windows Server a připojte ho ke spravované doméně][create-join-windows-vm].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.

> [!NOTE]
> Šablony pro správu zásad skupiny můžete použít zkopírováním nových šablon do pracovní stanice pro správu. Zkopírujte soubory *ADMX* `%SYSTEMROOT%\PolicyDefinitions` do souborů *ADML* specifických pro `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`dané `Language-CountryRegion` prostředí , kde odpovídá jazyku a oblasti souborů *ADML.*
>
> Do `\en-us` složky například zkopírujte anglickou verzi souborů *ADML* v angličtině.
>
> Případně můžete centrálně ukládat šablonu pro správu zásad skupiny na řadiče domény, které jsou součástí spravované domény Azure AD DS. Další informace naleznete v [tématu Vytvoření a správa šablon správy zásad central store pro skupiny v systému Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Instalace nástrojů pro správu zásad skupiny

Chcete-li vytvořit a nakonfigurovat objekt zásad skupiny (Objekt y zásad skupiny), je třeba nainstalovat nástroje pro správu zásad skupiny. Tyto nástroje lze nainstalovat jako funkci v systému Windows Server. Další informace o instalaci nástrojů pro správu do klienta systému Windows naleznete v tématu instalace [nástrojů pro vzdálenou správu serveru (RSAT)][install-rsat].

1. Přihlaste se k virtuálnímu počítači pro správu. Postup připojení pomocí portálu Azure najdete v [tématu Připojení k virtuálnímu počítači s Windows Server][connect-windows-server-vm].
1. **Správce serveru** by se měl otevřít ve výchozím nastavení při přihlášení k virtuálnímu virtuálnímu týmu. Pokud ne, vyberte v nabídce **Start** **Správce serveru**.
1. V podokně *Řídicí panel* v okně **Správce serveru** vyberte Přidat role **a funkce**.
1. Na stránce **Před zahájením** *Průvodce*přidáním rolí a funkcí vyberte další položku **.**
1. U *typu instalace*ponechte zaškrtnutou možnost **instalace na základě rolí nebo funkcí** a vyberte **další**.
1. Na stránce **Výběr serveru** vyberte aktuální virtuální počítač z fondu serverů, například *myvm.aaddscontoso.com*, a pak vyberte **Další**.
1. Na stránce **Role serveru** klepněte na tlačítko **Další**.
1. Na stránce **Funkce** vyberte funkci **Správa zásad skupiny.**

    ![Instalace správy zásad skupiny ze stránky Funkce](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na stránce **Potvrzení** vyberte **Instalovat**. Instalace nástrojů pro správu zásad skupiny může trvat minutu nebo dvě.
1. Po dokončení instalace prvku vyberte **Zavřít** a ukončete Průvodce **přidáním rolí a funkcí.**

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Otevření Konzoly pro správu zásad skupiny a úprava objektu

Výchozí objekty zásad skupiny (GPO) existují pro uživatele a počítače ve spravované doméně Azure AD DS. S nainstalovanou funkcí Správy zásad skupiny z předchozí části zobrazme a upravíme existující objekt zásad skupiny. V další části vytvoříte vlastní objekt gpo.

> [!NOTE]
> Chcete-li spravovat zásady skupiny ve spravované doméně Azure AD DS, musíte být přihlášeni k uživatelskému účtu, který je členem *skupiny Správci řadiče domény Řadiče domény AAD.*

1. Na úvodní obrazovce vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, včetně **správy zásad skupiny** nainstalované v předchozí části.
1. Chcete-li otevřít Konzolu pro správu zásad skupiny (GPMC), zvolte **Nástroj pro správu zásad skupiny**.

    ![Konzola pro správu zásad skupiny se otevře připravená k úpravám objektů zásad skupiny.](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Ve spravované doméně Azure AD DS existují dva předdefinované objekty zásad skupiny (GPO) – jeden pro kontejner *Počítače AADDC* a jeden pro kontejner *Uživatelů AADDC.* Tyto objekty zásad skupiny můžete přizpůsobit tak, aby podle potřeby nakonfigurovaly zásady skupiny ve spravované doméně Azure AD DS.

1. V konzole **správa zásad skupiny** rozbalte uzel **Doménová struktura: aaddscontoso.com.** Dále rozbalte uzly **domén.**

    Existují dva předdefinované kontejnery pro *počítače AADDC* a *uživatele AADDC*. Každý z těchto kontejnerů má výchozí gpo použít na ně.

    ![Vestavěné objekty Zásad skupiny použité pro výchozí kontejnery AADDC Computers a AADDC Users](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Tyto předdefinované objekty zásad skupiny lze přizpůsobit tak, aby nakonfigurovaly konkrétní zásady skupiny ve spravované doméně Azure AD DS. Vyberte jeden z objektů zásad skupiny, například *objekt zásad skupiny Počítačů AADDC*, a pak zvolte **Upravit...**.

    ![Zvolte možnost "Upravit" jeden z předdefinovaných objektů zásad skupiny.](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Otevře se nástroj Editor správy zásad skupiny, který umožňuje přizpůsobit objekt zásad skupiny, například *zásady obchodního vztahu*:

    ![Přizpůsobení příkazu GPO pro konfiguraci nastavení podle potřeby](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Až bude hotovo, zvolte **Soubor > Uložit,** chcete-li zásadu uložit. Počítače aktualizují zásady skupiny ve výchozím nastavení každých 90 minut a aplikují provedené změny.

## <a name="create-a-custom-group-policy-object"></a>Vytvoření vlastního objektu zásad skupiny

Chcete-li seskupit podobné nastavení zásad, často vytvoříte další objekty zásad skupiny namísto použití všech požadovaných nastavení v jednom výchozím objektu zásad skupiny. Pomocí služby Azure AD DS můžete vytvořit nebo importovat vlastní objekty zásad skupiny a propojit je s vlastní ou. Pokud potřebujete nejdřív vytvořit vlastní ouovou akci, přečtěte si hlavní [pracovní příležitost ve spravované doméně Azure AD DS](create-ou.md).

1. V konzole **správy zásad skupiny** vyberte vlastní organizační jednotku (OU), například *MyCustomOU*. Vyberte hlavní výuce vpravo a v této doméně zvolte **Vytvořit zdroj skupiny a propojte ho zde...**:

    ![Vytvoření vlastního objektu zásad skupiny v konzole pro správu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Zadejte název nového objektu gpo, například *Můj vlastní objekt gpo*, a vyberte **OK**. Volitelně můžete založit tento vlastní objekt zásad zásad zásad na existujícím objektu zásad zásad a sadě možností zásad.

    ![Zadejte název nového vlastního objektu gpo](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Vlastní objekt gpo je vytvořen a propojen s vlastní ou. Chcete-li nyní konfigurovat nastavení zásad, vyberte vpravo vlastní objekt zásad y gpo a zvolte **Upravit...**:

    ![Zvolte možnost "Upravit" vlastní objekt skupiny.](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Otevře se **Editor správy zásad skupiny,** který umožňuje přizpůsobit objekt zásad skupiny:

    ![Přizpůsobení příkazu GPO pro konfiguraci nastavení podle potřeby](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Až bude hotovo, zvolte **Soubor > Uložit,** chcete-li zásadu uložit. Počítače aktualizují zásady skupiny ve výchozím nastavení každých 90 minut a aplikují provedené změny.

## <a name="next-steps"></a>Další kroky

Další informace o dostupných nastaveních zásad skupiny, které lze konfigurovat pomocí Konzoly pro správu zásad skupiny, naleznete v [tématu Práce s položkami předvoleb zásad skupiny][group-policy-console].

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
