---
title: Kurz – konfigurace virtuální sítě pro Azure AD Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak vytvořit a nakonfigurovat podsíť virtuální sítě Azure nebo partnerský vztah sítě pro Azure Active Directory Domain Services spravovanou doménu pomocí Azure Portal.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618412"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: Konfigurace virtuální sítě pro Azure Active Directory Domain Services spravovanou doménu

Aby se zajistilo připojení k uživatelům a aplikacím, spravovaná doména služby Azure Active Directory Domain Services (Azure služba AD DS) se nasadí do podsítě virtuální sítě Azure. Tato podsíť virtuální sítě by se měla používat jenom pro spravované prostředky domény poskytované platformou Azure.

Když vytváříte vlastní virtuální počítače a aplikace, neměli byste je nasazovat do stejné podsítě virtuální sítě. Místo toho byste měli své aplikace vytvářet a nasazovat do samostatné podsítě virtuální sítě nebo do samostatné virtuální sítě, která je v partnerském vztahu k virtuální síti Azure služba AD DS.

V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat vyhrazenou podsíť virtuální sítě nebo jak navázat jinou síť na virtuální síť spravované domény Azure služba AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pochopení možností připojení k virtuální síti pro prostředky připojené k doméně do Azure služba AD DS
> * Vytvoření rozsahu IP adres a další podsítě ve virtuální síti Azure služba AD DS
> * Konfigurace partnerského vztahu virtuálních sítí na síť, která je oddělená od Azure služba AD DS

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ke konfiguraci Azure služba AD DS potřebujete oprávnění *globálního správce* v TENANTOVI Azure AD.
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění *přispěvatele* v předplatném Azure.
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete spravovanou doménu pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Možnosti připojení úlohy aplikace

V předchozím kurzu se vytvořila spravovaná doména, která pro virtuální síť používala některé výchozí možnosti konfigurace. Tyto výchozí možnosti vytvořily virtuální síť Azure a podsíť virtuální sítě. Řadiče domény služba AD DS Azure, které poskytují spravované doménové služby, jsou připojené k této podsíti virtuální sítě.

Když vytváříte a spouštíte virtuální počítače, které potřebují používat spravovanou doménu, je nutné poskytnout síťové připojení. Připojení k síti lze zadat jedním z následujících způsobů:

* Vytvořte další podsíť virtuální sítě ve virtuální síti spravované domény. Tato další podsíť je místo, kde můžete vytvářet a připojovat své virtuální počítače.
    * Vzhledem k tomu, že virtuální počítače jsou součástí stejné virtuální sítě, můžou automaticky provádět překlad adres IP a komunikovat s služba AD DS řadiči domény Azure.
* Nakonfigurujte partnerský vztah Azure Virtual Network z virtuální sítě spravované domény na jednu nebo více samostatných virtuálních sítí. Tyto samostatné virtuální sítě jsou místo, kde můžete vytvářet a připojovat své virtuální počítače.
    * Při konfiguraci partnerského vztahu virtuálních sítí musíte taky nakonfigurovat nastavení DNS tak, aby se překlad IP adres používal zpátky na řadiče domény Azure služba AD DS.

Obvykle používáte jenom jednu z těchto možností připojení k síti. Tato volba často vychází z toho, jak chcete spravovat prostředky Azure odděleně.

* Pokud chcete spravovat Azure služba AD DS a připojené virtuální počítače jako jednu skupinu prostředků, můžete pro virtuální počítače vytvořit další podsíť virtuální sítě.
* Pokud chcete oddělit správu Azure služba AD DS a pak všechny připojené virtuální počítače, můžete použít partnerský vztah virtuálních sítí.
    * Můžete se také rozhodnout použít partnerský vztah virtuálních sítí k zajištění připojení ke stávajícím virtuálním počítačům v prostředí Azure, které jsou připojené k existující virtuální síti.

V tomto kurzu stačí nakonfigurovat jednu z těchto možností připojení k virtuální síti.

Další informace o tom, jak naplánovat a nakonfigurovat virtuální síť, najdete v tématu [požadavky na síť pro Azure Active Directory Domain Services][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Vytvoření podsítě virtuální sítě

Ve výchozím nastavení obsahuje virtuální síť Azure vytvořená ve spravované doméně jednu podsíť virtuální sítě. Tato podsíť virtuální sítě by měla být používána jenom platformou Azure k poskytování spravovaných doménových služeb. Pokud chcete vytvořit a používat vlastní virtuální počítače v této virtuální síti Azure, vytvořte další podsíť.

Pro vytvoření podsítě virtuální sítě pro virtuální počítače a úlohy aplikací proveďte následující kroky:

1. V Azure Portal vyberte skupinu prostředků vaší spravované domény, například *myResourceGroup*. V seznamu prostředků vyberte výchozí virtuální síť, například *aadds-VNet*.
1. V nabídce na levé straně okna virtuální síť vyberte **adresní prostor**. Virtuální síť se vytvoří s jedním adresním prostorem *10.0.2.0/24*, který se používá ve výchozí podsíti.

    Přidejte do virtuální sítě další rozsah IP adres. Velikost tohoto rozsahu adres a skutečný rozsah IP adres, které se mají použít, závisí na ostatních síťových prostředcích, které jsou už nasazené. Rozsah IP adres se nesmí překrývat s žádnými existujícími rozsahy adres ve vašem Azure nebo místním prostředí. Ujistěte se, že velikost rozsahu IP adres je dostatečně velká pro počet virtuálních počítačů, které chcete v podsíti nasadit.

    V následujícím příkladu se přidá další rozsah IP adres *10.0.3.0/24* . Až budete připraveni, vyberte **Uložit**.

    ![Přidejte do Azure Portal další rozsah IP adres virtuální sítě.](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. V nabídce vlevo v okně virtuální síť vyberte **podsítě** a pak vyberte **+ podsíť** a přidejte podsíť.
1. Zadejte název podsítě, například *úlohy*. V případě potřeby aktualizujte **Rozsah adres** , pokud chcete použít podmnožinu rozsahu IP adres nakonfigurovaného pro virtuální síť v předchozích krocích. Prozatím ponechte výchozí nastavení pro možnosti, jako je skupina zabezpečení sítě, směrovací tabulka, koncové body služby.

    V následujícím příkladu se vytvoří podsíť s názvem *úlohy* , která používá rozsah IP adres *10.0.3.0/24* :

    ![Přidejte do Azure Portal další podsíť virtuální sítě.](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Až budete připraveni, vyberte **OK**. Vytvoření podsítě virtuální sítě chvíli trvá.

Když vytváříte virtuální počítač, který potřebuje používat spravovanou doménu, ujistěte se, že jste vybrali tuto podsíť virtuální sítě. Nevytvářejte virtuální počítače ve výchozí *aadds-podsíti*. Pokud vyberete jinou virtuální síť, nebude k dispozici žádné připojení k síti a překlad DNS pro přístup ke spravované doméně, Pokud neprovedete konfiguraci partnerského vztahu virtuálních sítí.

## <a name="configure-virtual-network-peering"></a>Konfigurace partnerského vztahu virtuálních sítí

Je možné, že máte existující virtuální síť Azure pro virtuální počítače, nebo chcete, aby vaše virtuální síť spravované domény byla oddělená. Aby bylo možné používat spravovanou doménu, virtuální počítače v jiných virtuálních sítích potřebují způsob, jak komunikovat s řadiči domény služba AD DS Azure. Toto připojení se dá zadat pomocí partnerského vztahu virtuálních sítí Azure.

U partnerských vztahů virtuálních sítí Azure se vzájemně spojí dvě virtuální sítě, aniž by bylo nutné používat zařízení virtuální privátní sítě (VPN). Partnerský vztah k síti umožňuje rychle propojit virtuální sítě a definovat toky provozu napříč prostředím Azure.

Další informace o partnerském vztahu najdete v tématu [Přehled partnerských vztahů virtuálních sítí Azure][peering-overview].

Chcete-li vytvořit partnerský vztah k virtuální síti na spravované doméně, proveďte následující kroky:

1. Vyberte výchozí virtuální síť vytvořenou pro spravovanou doménu s názvem *aadds-VNet*.
1. V nabídce na levé straně okna virtuální síť vyberte **partnerské vztahy**.
1. Chcete-li vytvořit partnerský vztah, vyberte **+ Přidat**. V následujícím příkladu je výchozí *aadds-VNet* partnerský vztah k virtuální síti s názvem *myVnet*. Nakonfigurujte následující nastavení s vlastními hodnotami:

    * **Název partnerského vztahu z aadds-VNet do vzdálené virtuální sítě**: popisný identifikátor dvou sítí, jako je *aadds-VNet-to-myvnet*
    * **Typ nasazení virtuální sítě**: *Správce prostředků*
    * **Předplatné**: předplatné virtuální sítě, na kterou chcete vytvořit partnerský vztah, jako je *Azure* .
    * **Virtuální síť**: virtuální síť, na kterou chcete vytvořit partnerský vztah, například *myVnet*
    * **Název partnerského vztahu z myVnet do aadds-VNet**: popisný identifikátor dvou sítí, jako je například *myVnet-to-aadds-VNet*

    ![Konfigurace partnerského vztahu virtuálních sítí v Azure Portal](./media/tutorial-configure-networking/create-peering.png)

    Pokud nemáte specifické požadavky pro vaše prostředí, ponechte všechna ostatní výchozí nastavení přístupu k virtuální síti nebo přesměrovaného provozu, a pak vyberte **OK**.

1. Vytvoření partnerského vztahu pro virtuální síť Azure služba AD DS i virtuální síť, kterou jste vybrali, chvíli trvá. Až budete připraveni, zobrazí se zprávy o **stavu partnerského vztahu** *připojené*, jak je znázorněno v následujícím příkladu:

    ![Připojení partnerských sítí v Azure Portal bylo úspěšné.](./media/tutorial-configure-networking/connected-peering.png)

Než virtuální počítače v partnerské virtuální síti můžou používat spravovanou doménu, nakonfigurujte servery DNS tak, aby umožňovaly správné rozlišení názvů.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurace serverů DNS v partnerské virtuální síti

Aby virtuální počítače a aplikace v partnerské virtuální síti úspěšně komunikovaly se spravovanou doménou, musí se aktualizovat nastavení DNS. IP adresy řadičů domény služba AD DS Azure musí být nakonfigurované jako servery DNS v partnerské virtuální síti. Existují dva způsoby, jak nakonfigurovat řadiče domény jako servery DNS pro partnerský virtuální síť:

* Nakonfigurujte servery DNS virtuální sítě Azure tak, aby používaly řadiče domény Azure služba AD DS.
* Nakonfigurujte stávající server DNS používaný v partnerské virtuální síti tak, aby používal podmíněné předávání DNS pro přímé dotazy do spravované domény. Tyto kroky se liší v závislosti na používaném existujícím serveru DNS.

V tomto kurzu nakonfigurujeme servery DNS virtuální sítě Azure tak, aby směrovaly všechny dotazy na řadiče domény Azure služba AD DS.

1. V Azure Portal vyberte skupinu prostředků partnerské virtuální sítě, například *myResourceGroup*. V seznamu prostředků vyberte partnerský virtuální síť, například *myVnet*.
1. V nabídce na levé straně okna virtuální síť vyberte **servery DNS**.
1. Ve výchozím nastavení používá virtuální síť integrované servery DNS poskytované systémem Azure. Vyberte, chcete-li použít **vlastní** servery DNS. Zadejte IP adresy pro řadiče domény služba AD DS Azure, které jsou obvykle *10.0.2.4* a *10.0.2.5*. Potvrďte tyto IP adresy v okně **Přehled** vaší spravované domény na portálu.

    ![Konfigurace serverů DNS virtuální sítě pro použití řadičů domény Azure služba AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Až budete připraveni, vyberte **Uložit**. Aktualizace serverů DNS pro virtuální síť chvíli trvá.
1. Pokud chcete použít aktualizované nastavení DNS na virtuální počítače, restartujte virtuální počítače připojené k partnerské virtuální síti.

Když vytváříte virtuální počítač, který potřebuje používat spravovanou doménu, ujistěte se, že jste vybrali tuto partnerský virtuální síť. Pokud vyberete jinou virtuální síť, není k dispozici připojení k síti a překlad DNS pro přístup ke spravované doméně.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Pochopení možností připojení k virtuální síti pro prostředky připojené k doméně do Azure služba AD DS
> * Vytvoření rozsahu IP adres a další podsítě ve virtuální síti Azure služba AD DS
> * Konfigurace partnerského vztahu virtuálních sítí na síť, která je oddělená od Azure služba AD DS

Pokud chcete tuto spravovanou doménu zobrazit v akci, vytvořte virtuální počítač a připojte se k doméně.

> [!div class="nextstepaction"]
> [Připojení virtuálního počítače s Windows serverem k spravované doméně](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
