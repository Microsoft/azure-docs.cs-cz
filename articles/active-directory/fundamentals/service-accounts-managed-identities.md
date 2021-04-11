---
title: Zabezpečení spravovaných identit v Azure Active Directory
description: Vysvětlení, jak najít, posoudit a zvýšit zabezpečení spravovaných identit.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b08231f254ea47fc3c9d65de42966301bd3378f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640060"
---
# <a name="securing-managed-identities"></a>Zabezpečení spravovaných identit

Vývojáři jsou často napadeni správou tajných klíčů a přihlašovacích údajů používaných k zabezpečení komunikace mezi různými službami. Spravované identity jsou identity zabezpečené Azure Active Directory (Azure AD) vytvořené za účelem poskytování identit pro prostředky Azure.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Výhody používání spravovaných identit pro prostředky Azure

Následující výhody využívají spravované identity:

* Nemusíte spravovat přihlašovací údaje. U spravovaných identit jsou přihlašovací údaje plně spravované, otočené a chráněné pomocí Azure. Identity se automaticky dodávají a odstraňují s prostředky Azure. Spravované identity umožňují prostředkům Azure komunikovat se všemi službami, které podporují ověřování Azure AD.

* Žádná z nich (včetně globálního správce) nemá přístup k přihlašovacím údajům, takže je nepůjde v kódu omylem zneniknout, například do kódu.

## <a name="when-to-use-managed-identities"></a>Kdy použít spravované identity?

Spravované identity se nejlépe používají pro komunikaci mezi službami, které podporují ověřování Azure AD. 

Zdrojový systém požaduje přístup k cílové službě. Libovolný prostředek Azure může být zdrojový systém. Například virtuální počítač Azure, instance funkce Azure a instance služby Azure App Services podporují spravované identity.

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Jak funguje ověřování a autorizace

Se spravovanými identitami může zdrojový systém získat token z Azure AD bez toho, aby musel spravovat přihlašovací údaje vlastníka zdroje. Azure spravuje přihlašovací údaje. Token získaný zdrojovým systémem je prezentován cílovému systému pro ověřování. 

Cílový systém musí ověřit (identifikovat) a autorizovat zdrojový systém před tím, než povolí přístup. Když cílová služba podporuje ověřování založené na službě Azure AD, přijme přístupový token vydaný službou Azure AD. 

Azure má rovinu ovládacího prvku a rovinu dat. V řídicí rovině vytvoříte prostředky a v rovině dat, ke které k nim přistupujete. Například vytvoříte databázi Cosmos v rovině ovládacího prvku, ale Dotazujte ji v rovině dat.

Jakmile cílový systém přijme token pro ověřování, může podporovat různé mechanismy pro autorizaci své řídicí roviny a roviny dat.

Všechny operace roviny ovládacího prvku Azure se spravují pomocí [Azure Resource Manager](../../azure-resource-manager/management/overview.md) a používají [Access Control na základě rolí Azure](../../role-based-access-control/overview.md). V rovině dat má každý cílový systém vlastní mechanismus ověřování. Azure Storage podporuje službu Azure RBAC na rovině dat. Například aplikace používající Azure App Services můžou číst data z Azure Storage a aplikace, které používají službu Azure Kubernetes, můžou číst tajné kódy uložené v Azure Key Vault.

Další informace o řídicích a datových rovinách najdete v tématech [Řídicí rovina a operace roviny dat – Azure Resource Manager](../../azure-resource-manager/management/control-plane-and-data-plane.md).

Všechny služby Azure budou nakonec podporovat spravované identity. Další informace najdete v tématu [služby, které podporují spravované identity prostředků Azure](../managed-identities-azure-resources/services-support-managed-identities.md).

##  

## <a name="types-of-managed-identities"></a>Typy spravovaných identit

Existují dva typy spravovaných identit – přiřazení systémem a přiřazeno uživateli.

Spravovaná identita přiřazená systémem má následující vlastnosti:

* Mají 1:1 vztah s prostředkem Azure. K jednotlivým VIRTUÁLNÍm počítačům je například přidružená jedinečná spravovaná identita.

* Jsou vázány na životní cyklus prostředků Azure. Po odstranění prostředku se automaticky odstraní spravovaná identita, která je k němu přidružená, což eliminuje riziko spojené s osamocenými účty. 

Uživatelsky přiřazené spravované identity mají následující vlastnosti:

* Životní cyklus těchto identit je nezávislý na prostředku Azure a Vy musíte spravovat životní cyklus. Po odstranění prostředku Azure se přiřazená spravovaná identita přiřazená uživateli automaticky neodstraní.

* Jedna spravovaná identita přiřazená uživateli se dá přiřadit k žádnému nebo více prostředkům Azure.

* Je možné je vytvořit předem a potom přiřadit k prostředku.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Hledání spravovaných instančních objektů identity ve službě Azure AD

Existuje několik způsobů, jak můžete najít spravované identity:

* Použití stránky podnikové aplikace v Azure Portal

* Použití Microsoft Graph

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Active Directory vyberte podnikové aplikace.

2. Vyberte filtr pro "spravované identity". 

   ![Obrázek obrazovky všechny aplikace se zvýrazněním rozevíracího seznamu pro typ aplikace "spravované identity"](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Použití Microsoft Graph

V tenantovi můžete získat seznam všech spravovaných identit s následujícím požadavkem GET na Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Tyto požadavky můžete filtrovat. Další informace najdete v dokumentaci ke službě [Get servicePrincipal](/graph/api/serviceprincipal-get).

## <a name="assess-the-security-of-managed-identities"></a>Posouzení zabezpečení spravovaných identit 

Zabezpečení spravovaných identit můžete vyhodnotit následujícími způsoby:

* Zkontrolujte oprávnění a ujistěte se, že je vybrán nejméně privilegovaný model. Pomocí následující rutiny PowerShellu získáte oprávnění přiřazená vašim spravovaným identitám.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Ujistěte se, že spravovaná identita není součástí žádné privilegované skupiny, jako je například skupina správců.  
To můžete provést vytvořením výčtu členů vysoce privilegovaných skupin pomocí PowerShellu.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Ujistěte se, že víte, k jakým prostředkům má spravovaná identita přístup](../../role-based-access-control/role-assignments-list-powershell.md).

## <a name="move-to-managed-identities"></a>Přesunout na spravované identity

Pokud používáte instanční objekt nebo uživatelský účet služby Azure AD, vyhodnoťte, jestli můžete místo toho použít spravovanou identitu, abyste vyloučili nutnost chránit, otáčet a spravovat přihlašovací údaje. 

## <a name="next-steps"></a>Další kroky

**Informace o vytváření spravovaných identit najdete v těchto tématech:** 

[Vytvořte spravovanou identitu přiřazenou uživatelem](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

[Povolení spravované identity přiřazené systémem během vytváření prostředků](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[Povolit spravovanou identitu přiřazenou systémem u existujícího prostředku](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Další informace o účtech služeb najdete v těchto tématech:**

[Úvod k účtům služby Azure Active Directory](service-accounts-introduction-azure.md)

[Zabezpečení instančních objektů](service-accounts-principal.md)

[Řízení účtů služeb Azure](service-accounts-governing-azure.md)

[Seznámení s místními účty služeb](service-accounts-on-premises.md)

 

 

