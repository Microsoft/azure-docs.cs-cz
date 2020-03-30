---
title: Použití zprostředkovatele prostředků úložiště Azure pro přístup k prostředkům pro správu
description: Poskytovatel prostředků Úložiště Azure je služba, která poskytuje přístup k prostředkům pro správu pro Azure Storage. Pomocí zprostředkovatele prostředků Úložiště Azure můžete vytvářet, aktualizovat, spravovat a odstraňovat prostředky, jako jsou účty úložiště, soukromé koncové body a klíče přístupu k účtu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972346"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Použití zprostředkovatele prostředků úložiště Azure pro přístup k prostředkům pro správu

Azure Resource Manager je služba nasazování a správy pro Azure. Poskytovatel prostředků úložiště Azure je služba, která je založená na Azure Resource Managera a která poskytuje přístup k prostředkům pro správu pro Azure Storage. Pomocí zprostředkovatele prostředků Úložiště Azure můžete vytvářet, aktualizovat, spravovat a odstraňovat prostředky, jako jsou účty úložiště, soukromé koncové body a klíče přístupu k účtu. Další informace o Azure Resource Manager, najdete [v tématu Přehled Správce prostředků Azure](/azure/azure-resource-manager/resource-group-overview).

Pomocí poskytovatele prostředků Úložiště Azure můžete provádět akce, jako je vytvoření nebo odstranění účtu úložiště nebo získání seznamu účtů úložiště v předplatném. Pokud chcete autorizovat požadavky proti poskytovateli prostředků Azure Storage, použijte Azure Active Directory (Azure AD). Tento článek popisuje, jak přiřadit oprávnění k prostředkům pro správu a odkazuje na příklady, které ukazují, jak provádět požadavky proti poskytovateli prostředků Úložiště Azure.

## <a name="management-resources-versus-data-resources"></a>Prostředky pro správu versus datové prostředky

Microsoft poskytuje dvě rozhraní REST API pro práci s prostředky Azure Storage. Tato api tvoří základ všech akcí, které můžete provádět proti Azure Storage. Rozhraní AZURE Storage REST API umožňuje pracovat s daty ve vašem účtu úložiště, včetně dat objektů blob, fronty, souborů a tabulek. Rozhraní REST ROZHRANÍ REST zprostředkovatele úložiště Azure umožňuje pracovat s účtem úložiště a souvisejícími prostředky.

Požadavek, který čte nebo zapisuje data objektů blob, vyžaduje jiná oprávnění než požadavek, který provádí operaci správy. RBAC poskytuje jemně odstupňovanou kontrolu nad oprávněními pro oba typy prostředků. Při přiřazení role RBAC k objektu zabezpečení, ujistěte se, že chápete, jaká oprávnění bude tento objekt zabezpečení udělena. Podrobný odkaz, který popisuje, které akce jsou přidruženy ke každé předdefinované roli RBAC, najdete [v tématu předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

Azure Storage podporuje použití Azure AD k autorizaci požadavků na úložiště objektů Blob a Fronty. Informace o rolích RBAC pro operace s daty objektů blob a fronty naleznete v [tématu Autorizace přístupu k objektům BLOB a frontám pomocí služby Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Přiřazení oprávnění pro správu pomocí řízení přístupu založeného na rolích (RBAC)

Každé předplatné Azure má přidružený Adresář Azure Active Directory, který spravuje uživatele, skupiny a aplikace. Uživatel, skupina nebo aplikace se také označuje jako zaregistrovaný objekt zabezpečení v kontextu [platformy identit společnosti Microsoft](/azure/active-directory/develop/). Můžete udělit přístup k prostředkům v předplatném zaregistrovaný objekt zabezpečení, který je definován ve službě Active Directory pomocí řízení přístupu na základě rolí (RBAC).

Při přiřazení role RBAC k objektu zabezpečení také označíte obor, ve kterém jsou platná oprávnění udělená rolí. Pro operace správy můžete přiřadit roli na úrovni předplatného, skupiny prostředků nebo účtu úložiště. Roli RBAC můžete přiřadit k objektu zabezpečení pomocí [portálu Azure](https://portal.azure.com/), [nástrojů Azure CLI](../../cli-install-nodejs.md), [PowerShellu](/powershell/azureps-cmdlets-docs)nebo [rozhraní REST API zprostředkovatele úložiště Azure](/rest/api/storagerp).

Další informace o RBAC najdete [v tématu Co je řízení přístupu na základě rolí (RBAC) pro prostředky Azure?](../../role-based-access-control/overview.md) a [klasické role správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Integrované role pro operace správy

Azure poskytuje předdefinované role, které udělují oprávnění k operacím správy volání. Azure Storage také poskytuje integrované role speciálně pro použití s poskytovatelem prostředků Úložiště Azure.

Předdefinované role, které udělují oprávnění k volání operací správy úložiště, zahrnují role popsané v následující tabulce:

|    Role RBAC    |    Popis    |    Zahrnuje přístup ke klíčům účtu?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Vlastník** | Můžete spravovat všechny prostředky úložiště a přístup k prostředkům.  | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |
| **Přispěvatel**  | Může spravovat všechny prostředky úložiště, ale nemůže spravovat přiřazení k prostředkům. | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |
| **Čtenář** | Můžete zobrazit informace o účtu úložiště, ale nelze zobrazit klíče účtu. | Ne. |
| **Přispěvatel účtů úložiště** | Můžete spravovat účet úložiště, získat informace o skupinách prostředků a prostředcích předplatného a vytvářet a spravovat nasazení skupin prostředků předplatného. | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |
| **Správce uživatelského přístupu** | Můžete spravovat přístup k účtu úložiště.   | Ano, umožňuje zaregistrovaný objekt zabezpečení přiřadit všechna oprávnění pro sebe a ostatní. |
| **Přispěvatel virtuálních počítačů** | Můžete spravovat virtuální počítače, ale ne účet úložiště, ke kterému jsou připojeny.   | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |

Třetí sloupec v tabulce označuje, zda předdefinovaná role podporuje **microsoft.storage/storageaccounts/listkeys/action**. Tato akce uděluje oprávnění ke čtení a opětovnému vygenerování klíčů účtu úložiště. Oprávnění k přístupu k prostředkům pro správu úložiště Azure také nezahrnují oprávnění k přístupu k datům. Pokud má však uživatel přístup ke klíčům účtu, může pomocí klíčů účtu přistupovat k datům služby Azure Storage prostřednictvím autorizace sdíleného klíče.

### <a name="custom-roles-for-management-operations"></a>Vlastní role pro operace správy

Azure také podporuje definování vlastních rolí RBAC pro přístup k prostředkům pro správu. Další informace o vlastních rolích najdete [v tématu Vlastní role pro prostředky Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Ukázky kódů

Příklady kódu, které ukazují, jak autorizovat a volat operace správy z knihoven správy úložiště Azure, najdete v následujících ukázkách:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager versus klasická nasazení

Modely Resource Manager a Classic představují dva různé způsoby nasazení a správy vašich řešení Azure. Společnost Microsoft doporučuje používat model nasazení Azure Resource Manageru při vytváření nového účtu úložiště. Pokud je to možné, společnost Microsoft také doporučuje znovu vytvořit existující klasické účty úložiště pomocí modelu Resource Manager. I když můžete vytvořit účet úložiště pomocí klasického modelu nasazení, klasický model je méně flexibilní a nakonec se zastaralá.

Další informace o modelech nasazení Azure najdete v tématu [Správce prostředků a klasické nasazení](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Další kroky

- [Přehled Správce prostředků Azure](/azure/azure-resource-manager/resource-group-overview)
- [Co je řízení přístupu na základě role (RBAC) u prostředků Azure?](../../role-based-access-control/overview.md)
- [Cíle škálovatelnosti pro poskytovatele prostředků úložiště Azure](scalability-targets-resource-provider.md)
