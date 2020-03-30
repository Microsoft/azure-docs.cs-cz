---
title: Řešení Azure VMware od CloudSimple – zabezpečený privátní cloud
description: Popisuje, jak zabezpečit řešení Azure VMware pomocí cloudového privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565974"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Jak zabezpečit prostředí privátního cloudu

Definujte řízení přístupu na základě rolí (RBAC) pro cloudovou službu CloudSimple, portál CloudSimple a privátní cloud z Azure.  Uživatelé, skupiny a role pro přístup k vCenter privátního cloudu jsou určeny pomocí vmware semaforu.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC pro službu CloudSimple

Vytvoření služby CloudSimple vyžaduje roli **vlastníka** nebo **přispěvatele** v předplatném Azure.  Ve výchozím nastavení mohou všichni vlastníci a přispěvatelé vytvořit službu CloudSimple a přistupovat k portálu CloudSimple pro vytváření a správu privátních cloudů.  Pro každý region lze vytvořit pouze jednu službu CloudSimple.  Chcete-li omezit přístup na konkrétní správce, postupujte podle následujícího postupu.

1. Vytvoření služby CloudSimple v nové **skupině prostředků** na webu Azure Portal
2. Zadejte RBAC pro skupinu prostředků.
3. Nákup uzlů a použití stejné skupiny prostředků jako služba CloudSimple

Pouze uživatelé, kteří mají oprávnění **vlastníka** nebo **přispěvatele** ve skupině prostředků, uvidí službu CloudSimple a spustí portál CloudSimple.

Další informace o RBAC najdete [v tématu Co je řízení přístupu na základě rolí (RBAC) pro prostředky Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC pro privátní cloud vCenter

Při vytvoření `CloudOwner@cloudsimple.local` privátního cloudu se v doméně s připojovacím zabezpečení virtuálního centra vytvoří výchozí uživatel.  Uživatel CloudOwner má oprávnění pro správu vCenter. Další zdroje identit jsou přidány do vCenter SSO pro poskytování přístupu k různým uživatelům.  Předdefinované role a skupiny jsou nastaveny v centru, které lze použít k přidání dalších uživatelů.

### <a name="add-new-users-to-vcenter"></a>Přidání nových uživatelů do aplikace vCenter

1. [Eskalovat oprávnění](escalate-private-cloud-privileges.md) pro **cloudOwner\@cloudsimple.local** uživatele v privátním cloudu.
2. Přihlášení k virtuálnímu centru pomocí **cloudowner\@cloudsimple.local**
3. [Přidat uživatele s jedním přihlášením vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Přidání uživatelů do [skupin y jednotného přihlašování vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Další informace o předdefinovaných rolích a skupinách najdete v článku [CloudSimple Private Cloud, který se v článku vcentru VMware.](learn-private-cloud-permissions.md)

### <a name="add-new-identity-sources"></a>Přidání nových zdrojů identity

Můžete přidat další zprostředkovatele identit pro doménu vCenter SSO vašeho privátního cloudu.  Zprostředkovatelé identit poskytují ověřování a skupiny spřibíná vCenter poskytují autorizaci pro uživatele.

* [Službu Active Directory použijte jako poskytovatele identity](set-vcenter-identity.md) v programu Private Cloud vCenter.
* [Použití Azure AD jako poskytovatele identity](azure-ad.md) v privátním cloudu vCenter

1. [Eskalovat oprávnění](escalate-private-cloud-privileges.md) pro **cloudOwner\@cloudsimple.local** uživatele v privátním cloudu.
2. Přihlášení k virtuálnímu centru pomocí **cloudowner\@cloudsimple.local**
3. Přidejte uživatele z poskytovatele identity do [skupin y jednotného přihlašování vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Zabezpečená síť v prostředí privátního cloudu

Zabezpečení sítě privátního cloudového prostředí je řízeno zabezpečením přístupu k síti a řízením síťového provozu mezi prostředky.

### <a name="access-to-private-cloud-resources"></a>Přístup k prostředkům privátního cloudu

Privátní cloud vCenter a prostředky přístup je přes zabezpečené síťové připojení:

* **[ExpressRoute připojení](on-premises-connection.md)**. ExpressRoute poskytuje zabezpečené připojení s vysokou šířkou pásma a nízkou latencí z místního prostředí.  Pomocí připojení umožňuje vaše místní služby, sítě a uživatelé přístup k priviacloudvCenter.
* **[Brána VPN site-to-site](vpn-gateway.md)**. Vpn site-to-Site poskytuje přístup k prostředkům privátního cloudu z místního prostředí prostřednictvím zabezpečeného tunelu.  Určíte, které místní sítě mohou odesílat a přijímat síťový provoz do vašeho privátního cloudu.
* **[Brána VPN point-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Pro rychlý vzdálený přístup k privátnímu cloudu vCenter použijte připojení VPN z bodu na místo.

### <a name="control-network-traffic-in-private-cloud"></a>Řízení síťového provozu v privátním cloudu

Tabulky brány firewall a pravidla řídí síťový provoz v privátním cloudu.  Tabulka brány firewall umožňuje řídit síťový provoz mezi zdrojovou sítí nebo adresou IP a cílovou sítí nebo adresou IP na základě kombinace pravidel definovaných v tabulce.

1. Vytvořte [tabulku brány firewall](firewall.md#add-a-new-firewall-table).
2. [Přidejte pravidla](firewall.md#create-a-firewall-rule) do tabulky brány firewall.
3. [Připojte tabulku brány firewall k síti VLAN/podsíti](firewall.md#attach-vlans-subnet).
