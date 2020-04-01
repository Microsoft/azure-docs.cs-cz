---
title: Kurz – konfigurace virtuálních sítí pro službu Azure AD Domain Services | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat podsíť virtuální sítě Azure nebo partnerský vztah v síti pro instanci Služby Azure Active Directory Domain Services pomocí portálu Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: af284e4c10487123c8c2a2105a25a2285ae0aa99
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474350"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Kurz: Konfigurace virtuální sítě pro instanci služby Azure Active Directory Domain Services

Chcete-li zajistit připojení uživatelům a aplikacím, spravovaná doména Azure Active Directory Domain Services (Azure AD DS) se nasadí do podsítě virtuální sítě Azure. Tato podsíť virtuální sítě by se měla používat jenom pro prostředky spravované domény poskytované platformou Azure. Při vytváření vlastních virtuálních počítačů a aplikací by se neměly nasazovat do stejné podsítě virtuální sítě. Místo toho byste měli vytvořit a nasadit aplikace do samostatné podsítě virtuální sítě nebo v samostatné virtuální síti, která je propojena s virtuální sítí Azure AD DS.

Tento kurz ukazuje, jak vytvořit a nakonfigurovat podsíť vyhrazené virtuální sítě nebo jak připojit jinou síť do virtuální sítě spravované domény služby Azure AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Seznamte se s možnostmi připojení virtuální sítě pro prostředky spojené s doménou do Služby Azure AD DS
> * Vytvoření rozsahu IP adres a další podsítě ve virtuální síti Azure AD DS
> * Konfigurace partnerského vztahu virtuální sítě do sítě, která je oddělená od Azure AD DS

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* K povolení služby Azure AD DS potřebujete oprávnění *globálního správce* ve vašem tenantovi Azure AD.
* K vytvoření požadovaných prostředků Azure AD DS potřebujete oprávnění *přispěvatele* ve vašem předplatném Azure.
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby první kurz [vytvoří a nakonfiguruje instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete instanci Azure AD DS pomocí portálu Azure. Chcete-li začít, nejprve se přihlaste na [portál Azure](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Možnosti připojení úlohy aplikace

V předchozím kurzu byla vytvořena spravovaná doména Azure AD DS, která používala některé výchozí možnosti konfigurace pro virtuální síť. Tyto výchozí možnosti vytvořily virtuální síť Azure a podsíť virtuální sítě. Řadiče domény Azure AD DS, které poskytují služby spravované domény, jsou připojené k této podsíti virtuální sítě.

Když vytvoříte a spustíte virtuální počítače, které je potřeba používat spravovanou doménu Azure AD DS, musí být k dispozici připojení k síti. Toto síťové připojení lze zajistit jedním z následujících způsobů:

* Vytvořte další podsíť virtuální sítě ve výchozí virtuální síti spravované domény Azure AD DS. Tato další podsíť je místo, kde můžete vytvořit a připojit virtuální počítače.
    * Vzhledem k tomu, že virtuální počítače jsou součástí stejné virtuální sítě, můžou automaticky provádět překlad názvů a komunikovat s řadiči domény Azure AD DS.
* Nakonfigurujte partnerský vztah virtuální sítě Azure z virtuální sítě spravované služby Azure AD DS do jedné nebo více samostatných virtuálních sítí. Tyto samostatné virtuální sítě jsou místo, kde můžete vytvořit a připojit virtuální počítače.
    * Při konfiguraci partnerského vztahu virtuální sítě je nutné také nakonfigurovat nastavení DNS tak, aby se překlad názvů používalo zpět k řadičům domény Azure AD DS.

Obvykle používáte pouze jednu z těchto možností připojení k síti. Volba je často na tom, jak chcete spravovat samostatné prostředky Azure. Pokud chcete spravovat Azure AD DS a připojené virtuální počítače jako jednu skupinu prostředků, můžete vytvořit další virtuální síť podsítě pro virtuální počítače. Pokud chcete oddělit správu Azure AD DS a pak všechny připojené virtuální počítače, můžete použít partnerský vztah virtuální sítě. Můžete také použít partnerský vztah virtuální sítě k zajištění připojení ke stávajícím virtuálním počítačům ve vašem prostředí Azure, které jsou připojené k existující virtuální síti.

V tomto kurzu stačí nakonfigurovat jednu z těchto možností připojení virtuální sítě.

Další informace o plánování a konfiguraci virtuální sítě naleznete v tématu [důležité informace o vytváření sítí pro službu Azure Active Directory Domain Services][aspekty sítě].

## <a name="create-a-virtual-network-subnet"></a>Vytvoření podsítě virtuální sítě

Ve výchozím nastavení virtuální síť Azure vytvořená se spravovanou doménou Azure AD DS obsahuje jednu podsíť virtuální sítě. Tato podsíť virtuální sítě by měla být používána jenom platformou Azure k poskytování služeb spravované domény. Pokud chcete vytvořit a používat vlastní virtuální počítače v této virtuální síti Azure, vytvořte další podsíť.

Pokud chcete vytvořit podsíť virtuální sítě pro virtuální počítače a úlohy aplikací, proveďte následující kroky:

1. Na webu Azure Portal vyberte skupinu prostředků spravované domény Azure AD DS, jako je *například myResourceGroup*. Ze seznamu prostředků zvolte výchozí virtuální síť, *například aadds-vnet*.
1. V levé nabídce okna virtuální sítě vyberte **Možnost Adresní prostor**. Virtuální síť je vytvořena s jedním adresním prostorem *10.0.2.0/24*, který používá výchozí podsíť.

    Přidejte do virtuální sítě další rozsah IP adres. Velikost tohoto rozsahu adres a skutečný rozsah IP adres, který chcete použít, závisí na jiných síťových prostředcích, které jsou již nasazeny. Rozsah IP adres by se neměl překrývat s existujícími rozsahy adres ve vašem prostředí Azure nebo v místním prostředí. Ujistěte se, že velikost rozsahu IP adres dostatečně velký pro počet virtuálních počítačů, které očekáváte nasazení do podsítě.

    V následujícím příkladu je přidán další rozsah IP adres *10.0.3.0/24.* Až budete připraveni, vyberte **Uložit**.

    ![Přidání dalšího rozsahu IP adres virtuální sítě na webu Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Dále v levé nabídce okna virtuální sítě vyberte **Podsítě**a pak zvolte **+ Podsíť** a přidejte podsíť.
1. Zadejte název podsítě, například *pracovní vytížení*. V případě potřeby aktualizujte **rozsah adres,** pokud chcete v předchozích krocích použít podmnožinu rozsahu IP adres nakonfigurovaného pro virtuální síť. Prozatím ponechte výchozí nastavení pro možnosti, jako je skupina zabezpečení sítě, směrovací tabulka, koncové body služby.

    V následujícím příkladu je vytvořena podsíť s názvem *úlohy,* která používá rozsah adres *IP 10.0.3.0/24:*

    ![Přidání další podsítě virtuální sítě na webu Azure Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Až budete připraveni, vyberte **OK**. Vytvoření podsítě virtuální sítě trvá několik okamžiků.

Když vytvoříte virtuální počítač, který potřebuje používat spravovanou doménu Azure AD DS, ujistěte se, že vyberete tuto podsíť virtuální sítě. Nevytvářejte virtuální virtuální sítě ve výchozí *podsíti aadds*. Pokud vyberete jinou virtuální síť, neexistuje žádné připojení k síti a překlad DNS k dosažení spravované domény Azure AD DS, pokud nenakonfigurujete partnerský vztah virtuální sítě.

## <a name="configure-virtual-network-peering"></a>Konfigurace partnerského vztahu virtuální sítě

Můžete mít existující virtuální síť Azure pro virtuální počítače nebo chcete zachovat virtuální síť spravované domény Azure AD DS oddělené. Chcete-li použít spravovanou doménu, virtuální počítače v jiných virtuálních sítích potřebují způsob, jak komunikovat s řadiči domény Azure AD DS. Toto připojení lze zajistit pomocí partnerského vztahu virtuální sítě Azure.

Díky partnerské síti Azure jsou dvě virtuální sítě propojené společně, bez nutnosti zařízení virtuální privátní sítě (VPN). Partnerský vztah v síti umožňuje rychle propojit virtuální sítě a definovat toky provozu v celém prostředí Azure. Další informace o partnerské síti najdete v tématu [přehled partnerského vztahu virtuální sítě Azure][peering-overview].

Pokud chcete virtuální síť převést do virtuální sítě spravované domény Azure AD DS, proveďte následující kroky:

1. Zvolte výchozí virtuální síť vytvořenou pro instanci Azure AD DS s názvem *aadds-vnet*.
1. V levé nabídce okna virtuální sítě vyberte **Partnerské partnerské partnerské partnerské partnerské partnerské partnerské okno**.
1. Chcete-li vytvořit partnerský vztah, vyberte **+ Přidat**. V následujícím příkladu je výchozí *síť aadds-vnet* propojena s virtuální sítí s názvem *myVnet*. Nakonfigurujte následující nastavení s vlastními hodnotami:

    * **Název partnerského vztahu z aadds-vnet do vzdálené virtuální sítě**: Popisný identifikátor obou sítí, jako je *například aadds-vnet-to-myvnet*
    * **Typ nasazení virtuální sítě**: *Resource Manager*
    * **Předplatné:** Předplatné virtuální sítě, do které chcete mít přístup, jako je *Azure*
    * **Virtuální síť**: Virtuální síť, do které chcete přejít, například *myVnet*
    * **Název partnerského vztahu z myVnet do aadds-vnet**: Popisný identifikátor obou sítí, například *myvnet-to-aadds-vnet*

    ![Konfigurace partnerského vztahu virtuální sítě na webu Azure Portal](./media/tutorial-configure-networking/create-peering.png)

    Ponechte všechny ostatní výchozí hodnoty pro přístup k virtuální síti nebo předávaný provoz, pokud nemáte specifické požadavky pro vaše prostředí, a pak vyberte **OK**.

1. Vytvoření partnerského vztahu ve virtuální síti Azure AD DS i ve virtuální síti, kterou jste vybrali, trvá několik okamžiků. Po připravenosti hlásí **stav partnerského vztahu** *Připojeno*, jak je znázorněno v následujícím příkladu:

    ![Úspěšně připojené partnerské sítě na webu Azure Portal](./media/tutorial-configure-networking/connected-peering.png)

Než virtuální počítače ve virtuální síti můžou používat spravovanou doménu Azure AD DS, nakonfigurujte servery DNS tak, aby umožňovaly správné překladnázvů.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurace serverů DNS ve virtuální síti s partnerským partnerem

Pro virtuální počítače a aplikace ve virtuální síti partnerských společností úspěšně mluvit se spravovanou doménou Azure AD DS, nastavení DNS musí být aktualizován. IP adresy řadičů domény Azure AD DS musí být nakonfigurovány jako servery DNS ve virtuální síti s partnerským vztahem. Existují dva způsoby konfigurace řadičů domény jako serverů DNS pro virtuální síť partnerského vztahu:

* Nakonfigurujte servery DNS virtuální sítě Azure tak, aby používaly řadiče domény Azure AD DS.
* Nakonfigurujte existující server DNS, který se používá ve virtuální síti partnerského vztahu, tak, aby používal podmíněné předávání DNS k přesměrování dotazů do spravované domény Azure AD DS. Tyto kroky se liší v závislosti na existujícím serveru DNS, který je používán.

V tomto kurzu nakonfigurujeme servery DNS virtuální sítě Azure tak, aby směrovaly všechny dotazy na řadiče domény Azure AD DS.

1. Na webu Azure Portal vyberte skupinu prostředků partnerské virtuální sítě, jako je *například myResourceGroup*. Ze seznamu prostředků zvolte partnerovou virtuální síť, například *myVnet*.
1. V levé nabídce okna virtuální sítě vyberte **servery DNS**.
1. Ve výchozím nastavení virtuální síť používá integrované servery DNS poskytované Azure. Zvolte, zda chcete použít **vlastní** servery DNS. Zadejte IP adresy pro řadiče domény Azure AD DS, které jsou obvykle *10.0.2.4* a *10.0.2.5*. Potvrďte tyto IP adresy v okně **Přehled** spravované domény Azure AD DS na portálu.

    ![Konfigurace serverů DNS virtuální sítě tak, aby používaly řadiče domény Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Až budete připraveni, vyberte **Uložit**. Aktualizace serverů DNS pro virtuální síť trvá několik okamžiků.
1. Pokud chcete použít aktualizované nastavení DNS na virtuální počítače, restartujte virtuální počítače připojené k virtuální síti s partnerským připojením.

Když vytvoříte virtuální počítač, který potřebuje používat spravovanou doménu Azure AD DS, ujistěte se, že vyberete tuto partnerská virtuální síť. Pokud vyberete jinou virtuální síť, neexistuje žádné připojení k síti a překlad DNS k dosažení spravované domény Azure AD DS.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Seznamte se s možnostmi připojení virtuální sítě pro prostředky spojené s doménou do Služby Azure AD DS
> * Vytvoření rozsahu IP adres a další podsítě ve virtuální síti Azure AD DS
> * Konfigurace partnerského vztahu virtuální sítě do sítě, která je oddělená od Azure AD DS

Chcete-li zobrazit tuto spravovanou doménu v akci, vytvořte a připojte virtuální počítač k doméně.

> [!div class="nextstepaction"]
> [Připojení virtuálního počítače se systémem Windows Server ke spravované doméně](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
