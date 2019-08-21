---
title: Řešení Azure VMware podle CloudSimple – zabezpečení privátního cloudu
description: Popisuje, jak zabezpečit řešení Azure VMware pomocí CloudSimple privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 39f451e94f2a825e69425f71aceda5f34de7eeb5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642640"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Jak zabezpečit prostředí privátního cloudu

Definujte řízení přístupu na základě role (RBAC) pro službu CloudSimple, portál CloudSimple a privátní cloud z Azure.  Uživatelé, skupiny a role pro přístup k vCenter privátního cloudu se zadává pomocí jednotného přihlašování VMware.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC pro službu CloudSimple

Vytváření služby CloudSimple vyžaduje roli **vlastníka** nebo **přispěvatele** v předplatném Azure.  Ve výchozím nastavení mohou všichni vlastníci a přispěvatelé vytvořit službu CloudSimple a přistupovat k portálu CloudSimple pro vytváření a správu privátních cloudů.  Pro každou oblast se dá vytvořit jenom jedna služba CloudSimple.  Pokud chcete omezit přístup ke konkrétním správcům, postupujte podle níže uvedeného postupu.

1. Vytvoření služby CloudSimple v nové **skupině prostředků** v Azure Portal
2. Zadejte RBAC pro skupinu prostředků.
3. Koupit uzly a použít stejnou skupinu prostředků jako služba CloudSimple

Pouze uživatelé, kteří mají oprávnění **vlastníka** nebo **přispěvatelé** ve skupině prostředků, uvidí službu CloudSimple a spustí portál CloudSimple.

Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role (RBAC) pro prostředky Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC pro privátní cloud vCenter

Při vytvoření privátního cloudu se v doméně vCenter SSO vytvoří výchozí uživatel `CloudOwner@cloudsimple.local` .  CloudOwner uživatel má oprávnění ke správě vCenter.   Další zdroje identity se přidají do služby vCenter SSO pro udělení přístupu různým uživatelům.  Předdefinované role a skupiny se nastavují na vCenter, který se dá použít k přidání dalších uživatelů.

### <a name="add-new-users-to-vcenter"></a>Přidat nové uživatele do vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro *CloudOwner@cloudsimple.local* uživatele v privátním cloudu.
2. Přihlášení k vCenter pomocí *CloudOwner@cloudsimple.local*
3. [Přidejte uživatele s jednotným přihlašováním vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Přidejte uživatele do [skupin](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)s jednotným přihlašováním vCenter.

Další informace o předem definovaných rolích a skupinách najdete v článku o [modelu oprávnění privátního cloudu CloudSimple v článku o VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Přidat nové zdroje identity

Můžete přidat další zprostředkovatele identity pro doménu vCenter SSO vašeho privátního cloudu.  Zprostředkovatelé identit poskytují skupiny ověřování a vCenter SSO pro uživatele autorizaci.

* V privátním cloudu vCenter [použijte službu Active Directory jako zprostředkovatele identity](set-vcenter-identity.md) .
* [Použití Azure AD jako zprostředkovatele identity](azure-ad.md) v privátním cloudu vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro *CloudOwner@cloudsimple.local* uživatele v privátním cloudu.
2. Přihlášení k vCenter pomocí *CloudOwner@cloudsimple.local*
3. Přidejte uživatele z poskytovatele identity do skupin s jednotným přihlašováním [vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Zabezpečení sítě v prostředí privátního cloudu

Zabezpečení sítě v prostředí privátního cloudu je řízeno pomocí zabezpečení přístupu k síti a řízení síťového provozu mezi prostředky.

### <a name="access-to-private-cloud-resources"></a>Přístup k prostředkům privátního cloudu

Přístup k vCenter a prostředkům privátního cloudu je přes zabezpečené síťové připojení:

* **[ExpressRoute připojení](on-premises-connection.md)** . ExpressRoute zajišťuje zabezpečené připojení s vysokou šířkou pásma a nízkou latencí z místního prostředí.  Použití připojení umožňuje vašim místním službám, sítím a uživatelům přístup k vašemu privátnímu cloudu vCenter.
* **[Brána VPN typu Site-to-site](vpn-gateway.md)** . Síť Site-to-Site VPN poskytuje přístup k prostředkům privátního cloudu z místního prostředí prostřednictvím zabezpečeného tunelového propojení.  Určíte, které místní sítě mohou odesílat a přijímat síťový provoz do privátního cloudu.
* **[Brána VPN typu Point-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Použijte připojení VPN typu Point-to-site pro rychlý vzdálený přístup k vašemu privátnímu cloudu vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Řízení síťového provozu v privátním cloudu

Tabulky a pravidla brány firewall řídí síťový provoz v privátním cloudu.  Tabulka brány firewall umožňuje řídit síťový provoz mezi zdrojovou nebo IP adresou a cílovou sítí nebo IP adresou založenou na kombinaci pravidel definovaných v tabulce.

1. Vytvořte [tabulku brány firewall](firewall.md#add-a-new-firewall-table).
2. [Přidejte pravidla](firewall.md#create-a-firewall-rule) do tabulky brány firewall.
3. [Připojení tabulky brány firewall k síti VLAN/podsíti] (firewall. MD # Attach-vlanssubnet.
