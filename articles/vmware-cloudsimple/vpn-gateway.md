---
title: Řešení Azure VMware podle CloudSimple – nastavení brány VPN
description: Popisuje, jak nastavit bránu VPN typu Point-to-site a bránu VPN typu Site-to-site a vytvořit připojení mezi místní sítí a privátním cloudem CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91871996"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Nastavení bran sítě VPN v síti CloudSimple

Brány VPN vám umožňují připojit se k CloudSimple síti z místní sítě a z klientského počítače vzdáleně. Připojení VPN mezi vaší místní sítí a vaší sítí CloudSimple zajišťuje přístup k vCenter a úlohám ve vašem privátním cloudu. CloudSimple podporuje sítě VPN typu Site-to-site i brány VPN typu Point-to-site.

## <a name="vpn-gateway-types"></a>Typy bran VPN

* Připojení **VPN typu Site-to-site** umožňuje nastavit vaše úlohy privátního cloudu pro přístup k místním službám. K ověřování do vašeho privátního cloudu vCenter můžete použít také místní službu Active Directory jako zdroj identity.  V současné době je podporován pouze typ **sítě VPN založený na zásadách** .
* Připojení **VPN typu Point-to-site** je nejjednodušší způsob, jak se připojit k privátnímu cloudu z vašeho počítače. Pomocí připojení VPN typu Point-to-site se můžete vzdáleně připojit k privátnímu cloudu. Informace o instalaci klienta pro připojení VPN typu Point-to-site najdete v tématu [Konfigurace připojení VPN k privátnímu cloudu](set-up-vpn.md).

V oblasti můžete vytvořit jednu bránu VPN typu Point-to-site a jednu bránu VPN typu Site-to-site.

## <a name="automatic-addition-of-vlansubnets"></a>Automatické přidání VLAN/podsítí

Brány VPN CloudSimple poskytují zásady pro přidávání sítí VLAN a podsítí do bran VPN.  Zásady umožňují zadat různá pravidla pro VLAN/podsítě pro správu a uživatelem definované sítě VLAN/podsítě.  Pravidla pro síť VLAN pro správu/podsítě se vztahují na všechny nové privátní cloudy, které vytvoříte.  Pravidla pro uživatelem definované sítě VLAN/podsítě umožňují automaticky přidat všechny nové sítě VLAN nebo podsítě do stávajícího nebo nového privátního cloudu pro bránu VPN typu Site-to-site. Tyto zásady definujete pro každé připojení.

Zásady pro přidávání sítí VLAN a podsítí do bran VPN se vztahují na VPN typu Site-to-site i na brány VPN typu Point-to-site.

## <a name="automatic-addition-of-users"></a>Automatické přidávání uživatelů

Brána sítě VPN typu Point-to-site umožňuje definovat zásady automatického přidávání pro nové uživatele. Ve výchozím nastavení mají všichni vlastníci a přispěvatelé předplatného přístup k portálu CloudSimple.  Uživatelé jsou vytvořeni pouze v případě, že je portál CloudSimple spuštěn poprvé.  Výběrem možnosti **automaticky přidat** pravidla umožníte každému novému uživateli přístup k síti CloudSimple pomocí připojení VPN typu Point-to-site.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Nastavení brány VPN typu Site-to-site

1. [Přejděte na portál CloudSimple](access-cloudsimple-portal.md) a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. Klikněte na **nový VPN Gateway**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V části **Konfigurace brány**zadejte následující nastavení a klikněte na **Další**.

    * Jako typ brány vyberte **VPN typu Site-to-site** .
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde je vaše služba CloudSimple nasazená.
    * Volitelně můžete povolit vysokou dostupnost.

    ![Vytvoření brány VPN typu Site-to-site](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Povolení vysoké dostupnosti vyžaduje, aby vaše místní zařízení VPN podporovalo připojení ke dvěma IP adresám. Po nasazení služby VPN Gateway nelze tuto možnost zakázat.

5. Vytvořte první připojení z místní sítě a klikněte na **Další**.

    * Zadejte název pro identifikaci připojení.
    * V poli IP adresa partnerského uzlu zadejte veřejnou IP adresu místní brány VPN.
    * Zadejte identifikátor partnerského vztahu místní brány VPN.  Identifikátor partnerského vztahu je obvykle veřejná IP adresa vaší místní brány VPN.  Pokud jste v bráně nakonfigurovali určitý identifikátor, zadejte identifikátor.
    * Zkopírujte sdílený klíč, který se použije pro připojení z místní brány VPN.  Pokud chcete změnit výchozí sdílený klíč a zadat nový, klikněte na ikonu Upravit.
    * **V části místní předpony**zadejte místní předpony CIDR, které budou přistupovat k CloudSimple síti.  Při vytváření připojení můžete přidat několik předpon CIDR.

    ![Vytvoření připojení brány VPN typu Site-to-site](media/create-vpn-gateway-s2s-connection.png)

6. Povolte síť VLAN nebo podsítě ve vaší síti privátního cloudu, ke kterým bude mít přistup z místní sítě, a klikněte na **Další**.

    * Chcete-li přidat síť VLAN nebo podsíť pro správu, povolte možnost **Přidat sítě VLAN pro správu/podsítě privátních cloudů**.  Pro podsítě vMotion a síti vSAN se vyžaduje podsíť pro správu.
    * Pokud chcete přidat podsítě vMotion, povolte možnost **Přidat vMotion síť privátních cloudů**.
    * Pokud chcete přidat podsítě síti vSAN, povolte **Přidání podsítě síti vSAN privátních cloudů**.
    * Vyberte nebo zrušte výběr konkrétních sítí VLAN.

    ![Vytvoření připojení](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Zkontrolujte nastavení a klikněte na **Odeslat**.

    ![Kontrola a vytvoření brány VPN typu Site-to-site](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Vytvoření brány VPN typu Point-to-site

1. [Přejděte na portál CloudSimple](access-cloudsimple-portal.md) a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. Klikněte na **nový VPN Gateway**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V části **Konfigurace brány**zadejte následující nastavení a klikněte na **Další**.

    * Jako typ brány vyberte **síť VPN typu Point-to-site** .
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde je vaše služba CloudSimple nasazená.
    * Zadejte podsíť klienta pro bránu Point-to-site.  Adresy DHCP se budou předávat z klientské podsítě, když se připojíte.

5. V poli **připojení/uživatel**zadejte následující nastavení a klikněte na tlačítko **Další**.

    * Pokud chcete všem současným a budoucím uživatelům automaticky dovolit přístup k privátnímu cloudu prostřednictvím brány Point-to-site, vyberte **automaticky přidat všechny uživatele**. Když vyberete tuto možnost, automaticky se vyberou všichni uživatelé v seznamu uživatelů. Automatickou možnost můžete přepsat tak, že zrušíte výběr jednotlivých uživatelů v seznamu.
    * Chcete-li vybrat jednotlivé uživatele, zaškrtněte políčka v seznamu uživatelů.

6. V části sítě VLAN a podsítě můžete zadat správu a sítě VLAN a uživatele pro bránu a připojení.

    * Možnosti **automaticky přidat** můžete nastavit globální zásady pro bránu. Nastavení platí pro aktuální bránu. Nastavení lze přepsat v oblasti **výběru** .
    * Vyberte **Přidat sítě VLAN pro správu/podsítě privátních cloudů**. 
    * Pokud chcete přidat všechny sítě VLAN a podsítě definované uživatelem, klikněte na  **Přidat uživatelsky definované sítě VLAN/podsítě**.
    * Nastavení **Vybrat** přepíše globální nastavení v části **automaticky přidat**.

7. Kliknutím na **Další** zkontrolujte nastavení. Kliknutím na ikony úprav proveďte požadované změny.
8. Kliknutím na **vytvořit** vytvořte bránu VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Podsíť klienta a protokoly pro bránu VPN typu Point-to-site

Brána VPN typu Point-to-site umožňuje připojení TCP a UDP.  Vyberte protokol, který se má použít, když se připojíte z počítače výběrem konfigurace TCP nebo UDP.

Nakonfigurovaná podsíť klienta se používá pro klienty TCP i UDP.  Předpona CIDR je rozdělená na dvě podsítě, jednu pro TCP a jednu pro klienty UDP. Vyberte masku předpony na základě počtu uživatelů sítě VPN, kteří se budou připojovat souběžně.  

Následující tabulka uvádí počet souběžných připojení klientů pro masku předpony.

| Maska předpony | za 24 | za 25 | za 26 | /27 | za 28 |
|-------------|-----|-----|-----|-----|-----|
| Počet souběžných připojení TCP | 124 | 60 | 28 | 12 | 4 |
| Počet souběžných připojení UDP | 124 | 60 | 28 | 12 | 4 |

Informace o připojení pomocí sítě VPN typu Point-to-site najdete v tématu [připojení k CloudSimple pomocí sítě VPN typu Point-to-site](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
