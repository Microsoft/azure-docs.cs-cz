---
title: Správa DNS pro Azure AD Domain Services | Microsoft Docs
description: Naučte se, jak nainstalovat nástroje serveru DNS pro správu DNS a vytvořit podmíněné servery pro přeposílání pro Azure Active Directory Domain Services spravovanou doménu.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619347"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Správa DNS a vytvoření podmíněného předávaného serveru ve Azure Active Directory Domain Services spravované doméně

V Azure Active Directory Domain Services (Azure služba AD DS) je klíčovou součástí služba DNS (rozlišení názvů domén). Azure služba AD DS zahrnuje server DNS, který poskytuje překlad adres IP pro spravovanou doménu. Tento server DNS obsahuje integrované záznamy DNS a aktualizace pro klíčové součásti, které umožňují spuštění služby.

Při spouštění vlastních aplikací a služeb možná budete muset vytvořit záznamy DNS pro počítače, které nejsou připojené k doméně, nakonfigurovat virtuální IP adresy pro nástroje pro vyrovnávání zatížení nebo nastavit externí servery DNS pro přeposílání. Uživatelům patřícím do skupiny *správci řadiče domény AAD* se udělují oprávnění ke správě DNS ve spravované doméně Azure služba AD DS a můžou vytvářet a upravovat vlastní záznamy DNS.

V hybridním prostředí se do spravované domény nesynchronizují zóny DNS a záznamy nakonfigurované v jiných oborech názvů DNS, například v místním prostředí služba AD DS. Pokud chcete vyřešit pojmenované prostředky v jiných oborech názvů DNS, vytvořte a používejte podmíněné servery pro přeposílání, které odkazují na existující servery DNS ve vašem prostředí.

V tomto článku se dozvíte, jak nainstalovat nástroje serveru DNS a pak pomocí konzoly DNS spravovat záznamy a vytvářet v Azure služba AD DS podmíněné servery pro směrování.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Připojení z vaší virtuální sítě Azure služba AD DS do umístění, kde jsou hostované vaše jiné obory názvů DNS.
    * Toto připojení se dá poskytnout pomocí [Azure ExpressRoute][expressroute] nebo připojení [Azure VPN Gateway][vpn-gateway] .
* Virtuální počítač pro správu Windows serveru, který je připojený ke spravované doméně.
    * V případě potřeby dokončete kurz a [vytvořte virtuální počítač s Windows serverem a připojte ho ke spravované doméně][create-join-windows-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="install-dns-server-tools"></a>Instalace nástrojů serveru DNS

Chcete-li vytvářet a upravovat záznamy DNS ve spravované doméně, je nutné nainstalovat nástroje serveru DNS. Tyto nástroje je možné nainstalovat jako funkci Windows serveru. Další informace o tom, jak nainstalovat nástroje pro správu na klienta Windows, najdete v tématu Install [Nástroje pro vzdálenou správu serveru (RSAT)][install-rsat].

1. Přihlaste se ke svému VIRTUÁLNÍmu počítači pro správu. Postup, jak se připojit pomocí Azure Portal, najdete v tématu [připojení k virtuálnímu počítači s Windows serverem][connect-windows-server-vm].
1. Pokud se při přihlášení k virtuálnímu počítači ve výchozím nastavení **Správce serveru** neotevře, vyberte nabídku **Start** a pak zvolte **Správce serveru**.
1. V podokně *řídicí panel* v okně **Správce serveru** vyberte **Přidat role a funkce**.
1. Na stránce **než začnete** v *Průvodci přidáním rolí a funkcí* vyberte **Další**.
1. Pro *typ instalace* ponechte zaškrtnutou možnost instalace na základě **rolí nebo na základě funkcí** a vyberte **Další**.
1. Na stránce **Výběr serveru** zvolte aktuální virtuální počítač z fondu serverů, například *myvm.aaddscontoso.com*, a pak vyberte **Další**.
1. Na stránce **role serveru** klikněte na **Další**.
1. Na stránce **funkce** rozbalte uzel **Nástroje pro vzdálenou správu serveru** a potom rozbalte uzel **Nástroje pro správu rolí** . V seznamu nástrojů pro správu rolí vyberte funkci **Nástroje serveru DNS** .

    ![Vyberte instalaci nástrojů serveru DNS ze seznamu dostupných nástrojů pro správu rolí.](./media/manage-dns/install-dns-tools.png)

1. Na stránce **potvrzení** vyberte **nainstalovat**. Instalace nástrojů serveru DNS může trvat minutu nebo dvě.
1. Po dokončení instalace funkce vyberte **Zavřít** a ukončete průvodce **přidáním rolí a funkcí** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otevřete konzolu pro správu DNS pro správu DNS.

S nainstalovanými nástroji serveru DNS můžete spravovat záznamy DNS ve spravované doméně.

> [!NOTE]
> Pokud chcete spravovat DNS ve spravované doméně, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *Správci AAD řadiče domény* .

1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, včetně **DNS** nainstalovaného v předchozí části. Vyberte **DNS** a spusťte konzolu pro správu DNS.
1. V dialogovém okně **připojit k serveru DNS** vyberte **následující počítač** a potom zadejte název domény DNS spravované domény, například *aaddscontoso.com*:

    ![Připojení ke spravované doméně v konzole DNS](./media/manage-dns/connect-dns-server.png)

1. Konzola DNS se připojí k zadané spravované doméně. Rozbalením **zón dopředného vyhledávání** nebo **zón zpětného vyhledávání** vytvořte požadované položky DNS nebo podle potřeby upravte existující záznamy.

    ![Konzola DNS – Správa domény](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Při správě záznamů pomocí nástrojů serveru DNS se ujistěte, že neodstraníte ani neupravujete předdefinované záznamy DNS, které používá Azure služba AD DS. Mezi předdefinované záznamy DNS patří záznamy DNS domény, záznamy názvového serveru a další záznamy používané pro umístění řadiče domény. Pokud tyto záznamy upravíte, přeruší se služba Domain Services ve virtuální síti.

## <a name="create-conditional-forwarders"></a>Vytváření služeb pro podmíněné dopředné

Zóna DNS v Azure služba AD DS by měla obsahovat jenom zónu a záznamy pro samotnou spravovanou doménu. V spravované doméně nevytvářejte další zóny, aby se vyřešily pojmenované prostředky v jiných oborech názvů DNS. Místo toho ve spravované doméně použijte podmíněné servery pro předání a sdělte tak serveru DNS, na který se má přejít, aby se daly přeložit adresy pro tyto prostředky.

Podmíněný Server pro dodávání je možnost konfigurace serveru DNS, která umožňuje definovat doménu DNS, například *contoso.com*, pro přeposílání dotazů na. Místo místního serveru DNS se při pokusu o překlad dotazů na záznamy v této doméně předají dotazy DNS na konfigurovanou službu DNS pro tuto doménu. Tato konfigurace zajistí, že se vrátí správné záznamy DNS, protože nevytvoříte místní zónu DNS s duplicitními záznamy ve spravované doméně, aby odrážely tyto prostředky.

Chcete-li vytvořit podmíněný Server pro směrování ve spravované doméně, proveďte následující kroky:

1. Vyberte zónu DNS, například *aaddscontoso.com*.
1. Vyberte **podmíněné dopředné** a pak klikněte pravým tlačítkem a vyberte **nové podmíněné dopředné...**
1. Zadejte svoji jinou **doménu DNS**, třeba *contoso.com*, a pak zadejte IP adresy serverů DNS pro daný obor názvů, jak je znázorněno v následujícím příkladu:

    ![Přidání a konfigurace podmíněného dodávání pro server DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Zaškrtněte políčko pro **uložení tohoto podmíněného dopředné služby ve službě Active Directory a replikaci následujícím způsobem, a** pak vyberte možnost pro *všechny servery DNS v této doméně*, jak je znázorněno v následujícím příkladu:

    ![Konzola DNS – vybere všechny servery DNS v této doméně.](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Pokud je podmíněné přeposílání Uloženo v *doménové struktuře* namísto *domény*, podmíněný předávací modul se nezdařil.

1. Chcete-li vytvořit podmíněný Server pro směrování, vyberte možnost **OK**.

Překlad názvů prostředků v jiných oborech názvů z virtuálních počítačů připojených ke spravované doméně by se teď měl vyřešit správně. Dotazy na doménu DNS nakonfigurované v podmíněném předávání jsou předány relevantním serverům DNS.

## <a name="next-steps"></a>Další kroky

Další informace o správě DNS najdete v článku věnovaném [nástrojům DNS na webu TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh