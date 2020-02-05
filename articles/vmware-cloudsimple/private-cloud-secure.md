---
title: Řešení Azure VMware (AVS) – zabezpečený privátní cloud pro službu AVS
description: Popisuje, jak zabezpečit privátní cloud řešení Azure VMware (AVS).
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020076"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Jak zabezpečit prostředí privátního cloudu služby AVS

Definujte řízení přístupu na základě role (RBAC) pro službu AVS, portál funkce AVS a privátní cloud služby AVS z Azure. Uživatelé, skupiny a role pro přístup k vCenter privátního cloudu služby AVS jsou zadáni pomocí jednotného přihlašování VMware. 

## <a name="rbac-for-avs-service"></a>RBAC pro službu AVS

Vytvoření služby AVS vyžaduje roli **vlastníka** nebo **přispěvatele** v předplatném Azure. Ve výchozím nastavení mohou všichni vlastníci a přispěvatelé vytvořit službu AVS a přistupovat k portálu AVS pro vytváření a správu privátních cloudů služby AVS. Pro každou oblast se dá vytvořit jenom jedna služba AVS. Pokud chcete omezit přístup ke konkrétním správcům, postupujte podle níže uvedeného postupu.

1. Vytvoření služby AVS v nové **skupině prostředků** v Azure Portal
2. Zadejte RBAC pro skupinu prostředků.
3. Koupit uzly a použít stejnou skupinu prostředků jako službu AVS

Pouze uživatelé, kteří mají oprávnění **vlastníka** nebo **přispěvatelé** ve skupině prostředků, uvidí službu AVS a spustí portál AVS.

Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role (RBAC) pro prostředky Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC pro funkci AVS privátní cloud vCenter

Výchozí uživatel `CloudOwner@AVS.local` se v doméně vCenter jednotného přihlašování vytvoří při vytvoření privátního cloudu služby AVS. CloudOwner uživatel má oprávnění ke správě vCenter. Další zdroje identity se přidají do služby vCenter SSO pro udělení přístupu různým uživatelům. Předdefinované role a skupiny se nastavují na vCenter, který se dá použít k přidání dalších uživatelů.

### <a name="add-new-users-to-vcenter"></a>Přidat nové uživatele do vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro **CloudOwner@AVS.local** uživatele v privátním cloudu služby AVS
2. Přihlášení k vCenter pomocí **CloudOwner@AVS.local**
3. [Přidejte uživatele s jednotným přihlašováním vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Přidejte uživatele do [skupin s jednotným přihlašováním vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Další informace o předem definovaných rolích a skupinách najdete v článku o [modelu oprávnění privátního cloudu služby AVS v článku VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Přidat nové zdroje identity

Můžete přidat další zprostředkovatele identity pro doménu vCenter SSO vašeho privátního cloudu služby AVS. Zprostředkovatelé identit poskytují skupiny ověřování a vCenter SSO pro uživatele autorizaci.

* [Použijte službu Active Directory jako zprostředkovatele identity v rámci služby](set-vcenter-identity.md) AVS privátní cloud vCenter.
* [Použití Azure AD jako zprostředkovatele identity v rámci](azure-ad.md) služby AVS privátní cloud vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro **CloudOwner@AVS.local** uživatele v privátním cloudu služby AVS
2. Přihlášení k vCenter pomocí **CloudOwner@AVS.local**
3. Přidejte uživatele z poskytovatele identity do [skupin s jednotným přihlašováním vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Zabezpečení sítě v prostředí privátního cloudu služby AVS

Zabezpečení sítě prostředí privátního cloudu ve funkci AVS se řídí zabezpečením přístupu k síti a řízením síťového provozu mezi prostředky.

### <a name="access-to-avs-private-cloud-resources"></a>Přístup k prostředkům privátního cloudu pro funkci AVS

Přístup k prostředkům vCenter privátního cloudu a přístup k prostředkům je přes zabezpečené síťové připojení:

* **[ExpressRoute připojení](on-premises-connection.md)** . ExpressRoute zajišťuje zabezpečené připojení s vysokou šířkou pásma a nízkou latencí z místního prostředí. Použití připojení umožňuje vašim místním službám, sítím a uživatelům přístup k serveru služby AVS Private Cloud vCenter.
* **[Brána VPN typu Site-to-site](vpn-gateway.md)** . Síť Site-to-Site VPN poskytuje přístup k prostředkům privátního cloudu služby AVS z místního prostředí prostřednictvím zabezpečeného tunelového propojení. Určíte, které místní sítě mohou odesílat a přijímat síťové přenosy do privátního cloudu služby AVS.
* **[Brána VPN typu Point-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Připojení VPN typu Point-to-site slouží k rychlému vzdálenému přístupu ke službě AVS privátního cloudu vCenter.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Řízení síťového provozu v privátním cloudu AVS

Tabulky a pravidla brány firewall řídí síťový provoz v privátním cloudu funkce AVS. Tabulka brány firewall umožňuje řídit síťový provoz mezi zdrojovou nebo IP adresou a cílovou sítí nebo IP adresou založenou na kombinaci pravidel definovaných v tabulce.

1. Vytvořte [tabulku brány firewall](firewall.md#add-a-new-firewall-table).
2. [Přidejte pravidla](firewall.md#create-a-firewall-rule) do tabulky brány firewall.
3. [Připojení tabulky brány firewall k síti VLAN/podsíti] (firewall. MD # Attach-vlanssubnet.
