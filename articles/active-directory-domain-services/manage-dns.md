---
title: Správa DNS pro Azure AD Domain Services | Microsoft Docs
description: Naučte se instalovat nástroje serveru DNS pro správu DNS pro Azure Active Directory Domain Services spravovanou doménu.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f47f112991a4fe2e5b245920db98e5ae7617161a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704931"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Správa DNS ve spravované doméně Azure AD Domain Services

V Azure Active Directory Domain Services (Azure služba AD DS) je klíčovou součástí služba DNS (rozlišení názvů domén). Azure služba AD DS zahrnuje server DNS, který poskytuje překlad adres IP pro spravovanou doménu. Tento server DNS obsahuje integrované záznamy DNS a aktualizace pro klíčové součásti, které umožňují spuštění služby.

Při spouštění vlastních aplikací a služeb možná budete muset vytvořit záznamy DNS pro počítače, které nejsou připojené k doméně, nakonfigurovat virtuální IP adresy pro nástroje pro vyrovnávání zatížení nebo nastavit externí servery DNS pro přeposílání. Uživatelům patřícím do skupiny *správci řadiče domény AAD* se udělují oprávnění ke správě DNS ve spravované doméně Azure služba AD DS a můžou vytvářet a upravovat vlastní záznamy DNS.

V hybridním prostředí se zóny DNS a záznamy nakonfigurované v místním prostředí služba AD DS nesynchronizují s Azure služba AD DS. Pokud chcete definovat a používat vlastní položky DNS, vytvořte záznamy na serveru DNS Azure služba AD DS nebo použijte podmíněné servery pro směrování, které odkazují na stávající servery DNS ve vašem prostředí.

V tomto článku se dozvíte, jak nainstalovat nástroje serveru DNS a pak použít konzolu DNS ke správě záznamů v Azure služba AD DS.

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
    * V případě potřeby dokončete kurz a [vytvořte virtuální počítač s Windows serverem a připojte ho ke spravované doméně][create-join-windows-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="install-dns-server-tools"></a>Instalace nástrojů serveru DNS

Pokud chcete vytvářet a upravovat záznamy DNS v Azure služba AD DS, musíte nainstalovat nástroje serveru DNS. Tyto nástroje je možné nainstalovat jako funkci Windows serveru. Další informace o tom, jak nainstalovat nástroje pro správu na klienta Windows, najdete v tématu Install [Nástroje pro vzdálenou správu serveru (RSAT)][install-rsat].

1. Přihlaste se ke svému VIRTUÁLNÍmu počítači pro správu. Postup, jak se připojit pomocí Azure Portal, najdete v tématu [připojení k virtuálnímu počítači s Windows serverem][connect-windows-server-vm].
1. Pokud se při přihlášení k virtuálnímu počítači ve výchozím nastavení **Správce serveru** neotevře, vyberte nabídku **Start** a pak zvolte **Správce serveru**.
1. V podokně *řídicí panel* v okně **Správce serveru** vyberte **Přidat role a funkce**.
1. Na stránce **než začnete** v *Průvodci přidáním rolí a funkcí*vyberte **Další**.
1. Pro *typ instalace*ponechte zaškrtnutou možnost instalace na základě **rolí nebo na základě funkcí** a vyberte **Další**.
1. Na stránce **Výběr serveru** zvolte aktuální virtuální počítač z fondu serverů, například *myvm.aadds.contoso.com*, a pak vyberte **Další**.
1. Na stránce **role serveru** klikněte na **Další**.
1. Na stránce **funkce** rozbalte uzel **Nástroje pro vzdálenou správu serveru** a potom rozbalte uzel **Nástroje pro správu rolí** . V seznamu nástrojů pro správu rolí vyberte funkci **Nástroje serveru DNS** .

    ![Vyberte instalaci nástrojů serveru DNS ze seznamu dostupných nástrojů pro správu rolí.](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na stránce **potvrzení** vyberte **nainstalovat**. Instalace nástrojů pro správu Zásady skupiny může trvat minutu nebo dvě.
1. Po dokončení instalace funkce vyberte **Zavřít** a ukončete průvodce **přidáním rolí a funkcí** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otevřete konzolu pro správu DNS pro správu DNS.

S nainstalovanými nástroji serveru DNS můžete spravovat záznamy DNS ve spravované doméně Azure služba AD DS.

> [!NOTE]
> Pokud chcete spravovat DNS ve spravované doméně služba AD DS Azure, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *Správci AAD řadiče domény* .

1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, včetně **DNS** nainstalovaného v předchozí části. Vyberte **DNS** a spusťte konzolu pro správu DNS.
1. V dialogovém okně **připojit k serveru DNS** vyberte **následující počítač**a potom zadejte název domény DNS spravované domény, například *aadds.contoso.com*:

    ![Připojení k spravované doméně Azure služba AD DS v konzole DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. Konzola DNS se připojí k zadané spravované doméně Azure služba AD DS. Rozbalením **zón dopředného vyhledávání** nebo **zón zpětného vyhledávání** vytvořte požadované položky DNS nebo podle potřeby upravte existující záznamy.

    ![Konzola DNS – Správa domény](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Při správě záznamů pomocí nástrojů serveru DNS se ujistěte, že neodstraníte ani neupravujete předdefinované záznamy DNS, které používá Azure služba AD DS. Mezi předdefinované záznamy DNS patří záznamy DNS domény, záznamy názvového serveru a další záznamy používané pro umístění řadiče domény. Pokud tyto záznamy upravíte, přeruší se služba Domain Services ve virtuální síti.

## <a name="next-steps"></a>Další kroky

Další informace o správě DNS najdete v článku věnovaném [nástrojům DNS na webu TechNet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
