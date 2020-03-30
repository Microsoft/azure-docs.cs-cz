---
title: Řešení Azure VMware od CloudSimple – nastavení brány VPN
description: Popisuje, jak nastavit bránu VPN z bodu na místo a bránu VPN site-to-site a vytvořit připojení mezi místní sítí a privátním cloudem CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279491"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Nastavení bran VPN v síti CloudSimple

Brány VPN umožňují připojení k síti CloudSimple z místní sítě a z klientského počítače vzdáleně. Připojení VPN mezi místní sítí a sítí CloudSimple poskytuje přístup k virtuálnímu centru a úlohám ve vašem privátním cloudu. CloudSimple podporuje privátní sítě k webu a brány VPN point-to-site.

## <a name="vpn-gateway-types"></a>Typy brány VPN

* Připojení **VPN site-to-site** umožňuje nastavit úlohy privátního cloudu pro přístup k místním službám. Místní službu Active Directory můžete také použít jako zdroj identity pro ověřování v programu Private Cloud vCenter.  V současné době je podporován pouze typ **VPN založený na zásadách.**
* Připojení **VPN z bodu na místo** je nejjednodušší způsob, jak se připojit k privátnímu cloudu z počítače. Připojení VPN z bodu na místo slouží ke vzdálenému připojení k privátnímu cloudu. Informace o instalaci klienta pro připojení VPN bodu na místo naleznete v [tématu Konfigurace připojení VPN k privátnímu cloudu](set-up-vpn.md).

V oblasti můžete vytvořit jednu bránu VPN point-to-site a jednu bránu VPN site-to-site.

## <a name="automatic-addition-of-vlansubnets"></a>Automatické přidávání Sítí VLAN/podsítí

CloudSimple VPN brány poskytují zásady pro přidávání VLAN/podsítí do bran VPN.  Zásady umožňují zadat různá pravidla pro správu sítí VLAN/podsítí a uživatelem definovaných sítí VLAN/podsítí.  Pravidla pro správu vsítích VLAN/podsítí platí pro všechny nové privátní cloudy, které vytvoříte.  Pravidla pro uživatelem definované sítě VLAN/podsítě umožňují automaticky přidávat všechny nové sítě VLAN/podsítě do existujících nebo nových privátních cloudů Pro bránu VPN mezi lokalitami, definujete zásady pro každé připojení.

Zásady přidávání sítí VLAN/podsítí do bran VPN se vztahují jak na brány VPN mezi lokalitami, tak na brány VPN z bodu na místo.

## <a name="automatic-addition-of-users"></a>Automatické přidávání uživatelů

Brána VPN point-to-site umožňuje definovat zásady automatického sčítání pro nové uživatele. Ve výchozím nastavení mají všichni vlastníci a přispěvatelé předplatného přístup k portálu CloudSimple.  Uživatelé jsou vytvořeny pouze při spuštění portálu CloudSimple poprvé.  Výběr automaticky **přidat** pravidla umožňuje každému novému uživateli přístup k síti CloudSimple pomocí připojení VPN bodu na místo.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Nastavení brány VPN mezi lokalitami

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md) a vyberte **možnost Síť**.
2. Vyberte **bránu VPN**.
3. Klepněte na **položku Nová brána VPN**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V **části Konfigurace brány**zadejte následující nastavení a klepněte na tlačítko **Další**.

    * Jako typ brány vyberte **SÍŤ VPN typu Site-to-Site.**
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde se vaše služba CloudSimple nasazuje.
    * Volitelně povolte vysokou dostupnost.

    ![Vytvořit bránu VPN mezi lokalitami](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Povolení vysoké dostupnosti vyžaduje, aby vaše místní zařízení VPN podporovalo připojení ke dvěma IP adresám. Tuto možnost nelze po nasazení brány VPN zakázat.

5. Vytvořte první připojení z místní sítě a klepněte na tlačítko **Další**.

    * Zadejte název pro identifikaci připojení.
    * Pro ip adresu druhé strany zadejte veřejnou IP adresu místní brány VPN.
    * Zadejte identifikátor partnera místní brány VPN.  Identifikátor partnera je obvykle veřejná IP adresa místní brány VPN.  Pokud jste na bránu nakonfigurovali konkrétní identifikátor, zadejte jej.
    * Zkopírujte sdílený klíč, který chcete použít pro připojení z místní brány VPN.  Chcete-li změnit výchozí sdílený klíč a zadat nový, klepněte na ikonu úprav.
    * V **případě místních předpon**zadejte místní předpony CIDR, které budou přistupovat k síti CloudSimple.  Při vytváření připojení můžete přidat více předpon CIDR.

    ![Vytvořit připojení brány VPN mezi lokalitami](media/create-vpn-gateway-s2s-connection.png)

6. Povolte sítě VLAN/podsítě v privátní cloudové síti, ke které se bude přistupovat z místní sítě, a klepněte na tlačítko **Další**.

    * Chcete-li přidat správu sítě VLAN/podsítě, povolte **možnost Přidat správu sítí VLAN/podsítí privátních cloudů**.  Podsíť pro správu je vyžadována pro podsítě vMotion a vSAN.
    * Chcete-li přidat podsítě vMotion, povolte **možnost Přidat vPohybovou síť privátních cloudů**.
    * Chcete-li přidat podsítě vSAN, povolte **přidat podsíť vSAN privátních cloudů**.
    * Vyberte nebo zrušte výběr konkrétních vlano.

    ![Vytvoření připojení](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Zkontrolujte nastavení a klepněte na tlačítko **Odeslat**.

    ![Kontrola a vytvoření brány VPN site-to-Site](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Vytvořit bránu VPN point-to-site

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md) a vyberte **možnost Síť**.
2. Vyberte **bránu VPN**.
3. Klepněte na **položku Nová brána VPN**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V **části Konfigurace brány**zadejte následující nastavení a klepněte na tlačítko **Další**.

    * Jako typ brány vyberte **bod na web VPN.**
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde se vaše služba CloudSimple nasazuje.
    * Zadejte podsíť klienta pro bránu typu Point-to-Site.  Při připojení budou z podsítě klienta uvedeny adresy DHCP.

5. V **poli Připojení/Uživatel**zadejte následující nastavení a klepněte na tlačítko **Další**.

    * Chcete-li automaticky povolit všem současným i budoucím uživatelům přístup k privátnímu cloudu prostřednictvím brány Point-to-Site, vyberte **Automaticky přidat všechny uživatele**. Když vyberete tuto možnost, budou automaticky vybráni všichni uživatelé v seznamu uživatelů. Automatickou volbu můžete přepsat zrušením výběru jednotlivých uživatelů v seznamu.
    * Chcete-li vybrat jednotlivé uživatele, zaškrtněte políčka v seznamu uživatelů.

6. Část Sítě VLAN/podsítě umožňuje určit správu a uživatelské sítě VLAN/podsítě pro bránu a připojení.

    * **Možnosti automatického přidání** nastavují globální zásady brány. Nastavení platí pro aktuální bránu. Nastavení lze přepsat v oblasti **Vybrat.**
    * Vyberte **Přidat správu sítí VLAN/podsítí privátních cloudů**. 
    * Chcete-li přidat všechny uživatelem definované sítě VLAN/podsítě, klepněte na tlačítko **Přidat uživatelem definované sítě VLAN/podsítě**.
    * Nastavení **výběru** přepíše globální nastavení v části **Automaticky přidat**.

7. Chcete-li zkontrolovat nastavení, klepněte na tlačítko **Další.** Kliknutím na ikony úprav proveďte změny.
8. Kliknutím na **Vytvořit** vytvořte bránu VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Podsíť klienta a protokoly pro bránu VPN bodu na lokalitu

Brána VPN point-to-site umožňuje připojení TCP a UDP.  Vyberte protokol, který chcete použít při připojení z počítače, výběrem konfigurace Protokolu TCP nebo UDP.

Nakonfigurovaná podsíť klienta se používá pro klienty TCP i UDP.  Předpona CIDR je rozdělena do dvou podsítí, jedné pro TCP a jedné pro klienty UDP. Zvolte masku předpony na základě počtu uživatelů VPN, kteří se připojí souběžně.  

V následující tabulce je uveden počet souběžných klientských připojení pro masku předpony.

| Maska předpony | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Počet souběžných připojení TCP | 124 | 60 | 28 | 12 | 4 |
| Počet souběžných připojení UDP | 124 | 60 | 28 | 12 | 4 |

Chcete-li se připojit pomocí technologie Point-to-Site VPN, přečtěte si informace [o připojení ke clouduJednoduché pomocí sítě POINT-to-Site VPN](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
