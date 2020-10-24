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
ms.openlocfilehash: 000e43a7861bd155ebbd7175db96dd323731464b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518207"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Jak zabezpečit prostředí privátního cloudu

Definujte řízení přístupu na základě role (RBAC) pro službu CloudSimple, portál CloudSimple a privátní cloud z Azure.  Uživatelé, skupiny a role pro přístup k vCenter privátního cloudu se zadává pomocí jednotného přihlašování VMware.  

## <a name="azure-rbac-for-cloudsimple-service"></a>Služba Azure RBAC pro CloudSimple

Vytváření služby CloudSimple vyžaduje roli **vlastníka** nebo **přispěvatele** v předplatném Azure.  Ve výchozím nastavení mohou všichni vlastníci a přispěvatelé vytvořit službu CloudSimple a přistupovat k portálu CloudSimple pro vytváření a správu privátních cloudů.  Pro každou oblast se dá vytvořit jenom jedna služba CloudSimple.  Pokud chcete omezit přístup ke konkrétním správcům, postupujte podle níže uvedeného postupu.

1. Vytvoření služby CloudSimple v nové **skupině prostředků** v Azure Portal
2. Zadejte pro skupinu prostředků Azure RBAC.
3. Koupit uzly a použít stejnou skupinu prostředků jako služba CloudSimple

Pouze uživatelé, kteří mají oprávnění **vlastníka** nebo **přispěvatelé** ve skupině prostředků, uvidí službu CloudSimple a spustí portál CloudSimple.

Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC pro privátní cloud vCenter

`CloudOwner@cloudsimple.local`Při vytvoření privátního cloudu se v doméně VCENTER SSO vytvoří výchozí uživatel.  CloudOwner uživatel má oprávnění ke správě vCenter. Další zdroje identity se přidají do služby vCenter SSO pro udělení přístupu různým uživatelům.  Předdefinované role a skupiny se nastavují na vCenter, který se dá použít k přidání dalších uživatelů.

### <a name="add-new-users-to-vcenter"></a>Přidat nové uživatele do vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro **CloudOwner \@ cloudsimple. místní** uživatel na privátním cloudu.
2. Přihlaste se k vCenter pomocí **CloudOwner \@ cloudsimple. Local.**
3. [Přidat uživatele vCenter Single Sign-On](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Přidejte uživatele do [skupin s jednotným přihlašováním vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Další informace o předem definovaných rolích a skupinách najdete v článku o [modelu oprávnění privátního cloudu CloudSimple v článku o VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Přidat nové zdroje identity

Můžete přidat další zprostředkovatele identity pro doménu vCenter SSO vašeho privátního cloudu.  Zprostředkovatelé identit poskytují skupiny ověřování a vCenter SSO pro uživatele autorizaci.

* V privátním cloudu vCenter [použijte službu Active Directory jako zprostředkovatele identity](set-vcenter-identity.md) .
* [Použití Azure AD jako zprostředkovatele identity](azure-ad.md) v privátním cloudu vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro **CloudOwner \@ cloudsimple. místní** uživatel na privátním cloudu.
2. Přihlaste se k vCenter pomocí **CloudOwner \@ cloudsimple. Local.**
3. Přidejte uživatele z poskytovatele identity do [skupin s jednotným přihlašováním vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Zabezpečení sítě v prostředí privátního cloudu

Zabezpečení sítě v prostředí privátního cloudu je řízeno pomocí zabezpečení přístupu k síti a řízení síťového provozu mezi prostředky.

### <a name="access-to-private-cloud-resources"></a>Přístup k prostředkům privátního cloudu

Přístup k vCenter a prostředkům privátního cloudu je přes zabezpečené síťové připojení:

* **[ExpressRoute připojení](on-premises-connection.md)**. ExpressRoute zajišťuje zabezpečené připojení s vysokou šířkou pásma a nízkou latencí z místního prostředí.  Použití připojení umožňuje vašim místním službám, sítím a uživatelům přístup k vašemu privátnímu cloudu vCenter.
* **[Brána VPN typu Site-to-site](vpn-gateway.md)**. Síť Site-to-Site VPN poskytuje přístup k prostředkům privátního cloudu z místního prostředí prostřednictvím zabezpečeného tunelového propojení.  Určíte, které místní sítě mohou odesílat a přijímat síťový provoz do privátního cloudu.
* **[Brána VPN typu Point-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Použijte připojení VPN typu Point-to-site pro rychlý vzdálený přístup k vašemu privátnímu cloudu vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Řízení síťového provozu v privátním cloudu

Tabulky a pravidla brány firewall řídí síťový provoz v privátním cloudu.  Tabulka brány firewall umožňuje řídit síťový provoz mezi zdrojovou nebo IP adresou a cílovou sítí nebo IP adresou založenou na kombinaci pravidel definovaných v tabulce.

1. Vytvořte [tabulku brány firewall](firewall.md#add-a-new-firewall-table).
2. [Přidejte pravidla](firewall.md#create-a-firewall-rule) do tabulky brány firewall.
3. [Připojte tabulku brány firewall k síti VLAN nebo podsíti](firewall.md#attach-vlans-subnet).
