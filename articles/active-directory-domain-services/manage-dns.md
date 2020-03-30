---
title: Správa služby DNS pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nainstalovat nástroje serveru DNS ke správě DNS pro spravovanou doménu služby Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613687"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Správa SLUŽBY DNS ve spravované doméně služby Azure AD Domain Services

Ve službě Azure Active Directory Domain Services (Azure AD DS) je klíčovou součástí DNS (Překlad názvů domén). Azure AD DS obsahuje server DNS, který poskytuje překlad názvů pro spravovanou doménu. Tento server DNS obsahuje integrované záznamy DNS a aktualizace klíčových součástí, které umožňují spuštění služby.

Při spouštění vlastních aplikací a služeb může být nutné vytvořit záznamy DNS pro počítače, které nejsou připojeny k doméně, nakonfigurovat virtuální IP adresy pro nástroje pro vyrovnávání zatížení nebo nastavit externí servery pro předávání DNS. Uživatelům, kteří patří do *skupiny Správci řadiče domény Řadičdomény AAD,* jsou udělena oprávnění správy DNS ve spravované doméně spravované službou Azure AD DS a můžou vytvářet a upravovat vlastní záznamy DNS.

V hybridním prostředí nejsou zóny DNS a záznamy nakonfigurované v místním prostředí služby AD DS synchronizovány s Azure AD DS. Chcete-li definovat a používat vlastní položky DNS, vytvořte záznamy na serveru Azure AD DS DNS nebo použijte podmíněné servery pro předávání, které odkazují na existující servery DNS ve vašem prostředí.

Tento článek ukazuje, jak nainstalovat nástroje dns serveru a potom pomocí konzoly DNS spravovat záznamy v Azure AD DS.

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

## <a name="install-dns-server-tools"></a>Instalace nástrojů serveru DNS

Chcete-li vytvořit a upravit záznamy DNS ve službě Azure AD DS, je třeba nainstalovat nástroje serveru DNS. Tyto nástroje lze nainstalovat jako funkci v systému Windows Server. Další informace o instalaci nástrojů pro správu do klienta systému Windows naleznete v tématu instalace [nástrojů pro vzdálenou správu serveru (RSAT)][install-rsat].

1. Přihlaste se k virtuálnímu počítači pro správu. Postup připojení pomocí portálu Azure najdete v [tématu Připojení k virtuálnímu počítači s Windows Server][connect-windows-server-vm].
1. Pokud **se Správce serveru** při přihlášení k virtuálnímu virtuálnímu virtuálnímu provozu ve výchozím nastavení neotevře, vyberte nabídku **Start** a pak zvolte Správce **serveru**.
1. V podokně *Řídicí panel* v okně **Správce serveru** vyberte Přidat role **a funkce**.
1. Na stránce **Před zahájením** *Průvodce*přidáním rolí a funkcí vyberte další položku **.**
1. U *typu instalace*ponechte zaškrtnutou možnost **instalace na základě rolí nebo funkcí** a vyberte **další**.
1. Na stránce **Výběr serveru** vyberte aktuální virtuální počítač z fondu serverů, například *myvm.aaddscontoso.com*, a pak vyberte **Další**.
1. Na stránce **Role serveru** klepněte na tlačítko **Další**.
1. Na stránce **Funkce** rozbalte uzel **Nástroje pro vzdálenou správu serveru** a potom rozbalte uzel Nástroje pro **správu rolí.** Vyberte funkci **Nástroje serveru DNS** ze seznamu nástrojů pro správu rolí.

    ![Zvolte instalaci nástrojů serveru DNS ze seznamu dostupných nástrojů pro správu rolí.](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na stránce **Potvrzení** vyberte **Instalovat**. Instalace nástrojů pro správu zásad skupiny může trvat minutu nebo dvě.
1. Po dokončení instalace prvku vyberte **Zavřít** a ukončete Průvodce **přidáním rolí a funkcí.**

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otevření konzoly pro správu DNS pro správu služby DNS

S nainstalovanými nástroji DNS Server můžete spravovat záznamy DNS ve spravované doméně Azure AD DS.

> [!NOTE]
> Chcete-li spravovat službu DNS ve spravované doméně Azure AD DS, musíte být přihlášeni k uživatelskému účtu, který je členem *skupiny Správci řadiče domény Řadiče domény AAD.*

1. Na úvodní obrazovce vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, včetně **dns** nainstalovaných v předchozí části. Výběrem **možnosti DNS** spusťte konzolu správy DNS.
1. V dialogovém okně **Připojit k serveru DNS** vyberte Následující **počítač**a zadejte název domény DNS spravované domény, například *aaddscontoso.com*:

    ![Připojení ke spravované doméně Azure AD DS v konzole DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. Konzola DNS se připojuje k zadané spravované doméně Azure AD DS. Rozbalte **zóny dopředných vyhledávání** nebo zóny **zpětného vyhledávání** a vytvořte požadované položky DNS nebo podle potřeby upravte existující záznamy.

    ![Konzola DNS - správa domény](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Při správě záznamů pomocí nástrojů serveru DNS se ujistěte, že neodstraníte nebo neupravujete předdefinované záznamy DNS, které používá Služba Azure AD DS. Vestavěné záznamy DNS zahrnují záznamy DNS domény, záznamy názvového serveru a další záznamy používané pro umístění řadiče domény. Pokud tyto záznamy změníte, služby domény budou ve virtuální síti přerušeny.

## <a name="next-steps"></a>Další kroky

Další informace o správě služby DNS naleznete v [článku nástroje DNS na technetu](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
