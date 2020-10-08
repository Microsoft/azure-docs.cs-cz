---
title: Použití poskytovatele prostředků Azure Storage k přístupu k prostředkům správy
description: Poskytovatel prostředků Azure Storage je služba, která poskytuje přístup k prostředkům správy pro Azure Storage. Poskytovatele prostředků Azure Storage můžete použít k vytváření, aktualizaci, správě a odstraňování prostředků, jako jsou účty úložiště, privátní koncové body a klíče pro přístup k účtu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e71e56b9da06bfd8f3be24481efd619b788a8839
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822273"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Použití poskytovatele prostředků Azure Storage k přístupu k prostředkům správy

Azure Resource Manager je služba nasazování a správy pro Azure. Poskytovatel prostředků Azure Storage je služba založená na Azure Resource Manager a, která poskytuje přístup k prostředkům správy Azure Storage. Poskytovatele prostředků Azure Storage můžete použít k vytváření, aktualizaci, správě a odstraňování prostředků, jako jsou účty úložiště, privátní koncové body a klíče pro přístup k účtu. Další informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](/azure/azure-resource-manager/resource-group-overview).

Poskytovatele prostředků Azure Storage můžete použít k provádění akcí, jako je vytvoření nebo odstranění účtu úložiště nebo získání seznamu účtů úložiště v rámci předplatného. Pokud chcete autorizovat žádosti u poskytovatele prostředků Azure Storage, použijte Azure Active Directory (Azure AD). Tento článek popisuje, jak přiřadit oprávnění k prostředkům správy a odkazuje na příklady, které ukazují, jak provádět požadavky na poskytovatele prostředků Azure Storage.

## <a name="management-resources-versus-data-resources"></a>Prostředky pro správu versus datové prostředky

Microsoft poskytuje dvě rozhraní REST API pro práci s Azure Storagemi prostředky. Tato rozhraní API tvoří základ všech akcí, které můžete provádět proti Azure Storage. REST API Azure Storage vám umožní pracovat s daty v účtu úložiště, včetně objektů blob, front, souborů a dat tabulek. Azure Storage poskytovatel prostředků REST API vám umožní pracovat s účtem úložiště a souvisejícími prostředky.

Požadavek, který čte nebo zapisuje data objektů blob, vyžaduje různá oprávnění, než je požadavek, který provádí operaci správy. Azure RBAC poskytuje jemně odstupňovanou kontrolu nad oprávněními pro oba typy prostředků. Když k objektu zabezpečení přiřadíte roli Azure, ujistěte se, že rozumíte tomu, jaká oprávnění budou udělena. Podrobné informace, které popisují, které akce jsou spojené s jednotlivými integrovanými rolemi Azure, najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

Azure Storage podporuje použití Azure AD k autorizaci požadavků na úložiště objektů BLOB a front. Informace o rolích Azure pro operace s daty objektů BLOB a front najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí služby Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Přiřazení oprávnění pro správu k řízení přístupu na základě role v Azure (Azure RBAC)

Každé předplatné Azure má přidruženou Azure Active Directory, která spravuje uživatele, skupiny a aplikace. Uživatel, skupina nebo aplikace se také označuje jako instanční objekt zabezpečení v kontextu [platformy Microsoft Identity](/azure/active-directory/develop/). Přístup k prostředkům v předplatném můžete udělit objektu zabezpečení, který je definovaný ve službě Active Directory pomocí řízení přístupu založeného na rolích Azure (Azure RBAC).

Když přiřadíte roli Azure k objektu zabezpečení, také určíte rozsah, ve kterém budou oprávnění udělená rolí platná. Pro operace správy můžete přiřadit roli na úrovni předplatného, skupiny prostředků nebo účtu úložiště. Roli Azure můžete přiřadit k objektu zabezpečení pomocí [Azure Portal](https://portal.azure.com/), [nástrojů Azure CLI](../../cli-install-nodejs.md), [powershellu](/powershell/azure/)nebo [poskytovatele prostředků Azure Storage REST API](/rest/api/storagerp).

Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md) a [klasické role správců předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Předdefinované role pro operace správy

Azure poskytuje předdefinované role, které udělují oprávnění k volání operací správy. Azure Storage také nabízí předdefinované role určené pro použití s poskytovatelem prostředků Azure Storage.

Předdefinované role, které udělují oprávnění k volání operací správy úložiště, zahrnují role popsané v následující tabulce:

|    Role Azure    |    Popis    |    Zahrnuje přístup k klíčům účtu?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Vlastník** | Může spravovat všechny prostředky úložiště a přistupovat k prostředkům.  | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |
| **Přispěvatel**  | Může spravovat všechny prostředky úložiště, ale nemůže spravovat přístup k prostředkům. | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |
| **Čtenář** | Může zobrazit informace o účtu úložiště, ale nemůže zobrazit klíče účtu. | No. |
| **Přispěvatel účtů úložiště** | Může spravovat účet úložiště, získávat informace o skupinách prostředků a prostředcích předplatného a vytvářet a spravovat nasazení skupin prostředků předplatného. | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |
| **Správce uživatelského přístupu** | Může spravovat přístup k účtu úložiště.   | Ano, umožňuje objektu zabezpečení přiřazovat jakákoli oprávnění sami a ostatním. |
| **Přispěvatel virtuálních počítačů** | Může spravovat virtuální počítače, ale ne účet úložiště, ke kterému jsou připojené.   | Ano, poskytuje oprávnění k zobrazení a obnovení klíčů účtu úložiště. |

Třetí sloupec v tabulce uvádí, zda integrovaná role podporuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Tato akce udělí oprávnění ke čtení a obnovení klíčů účtu úložiště. Oprávnění pro přístup k prostředkům správy Azure Storage nezahrnují také oprávnění k přístupu k datům. Pokud ale uživatel má přístup k klíčům účtu, pak může použít klíče účtu pro přístup k Azure Storage dat prostřednictvím autorizace pomocí sdíleného klíče.

### <a name="custom-roles-for-management-operations"></a>Vlastní role pro operace správy

Azure také podporuje definování vlastních rolí Azure pro přístup k prostředkům správy. Další informace o vlastních rolích najdete v tématu [vlastní role Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Ukázky kódů

Příklady kódu, které ukazují, jak autorizovat a volat operace správy z knihoven správy Azure Storage, najdete v následujících ukázkách:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager oproti klasickým nasazením

Modely Resource Manager a Classic představují dva různé způsoby nasazení a správy vašich řešení Azure. Microsoft doporučuje při vytváření nového účtu úložiště použít model nasazení Azure Resource Manager. Pokud je to možné, Microsoft také doporučuje znovu vytvořit existující účty klasického úložiště s modelem Správce prostředků. I když můžete vytvořit účet úložiště pomocí modelu nasazení Classic, klasický model je méně flexibilní a nakonec bude zastaralý.

Další informace o modelech nasazení Azure najdete v tématu [Správce prostředků a klasické nasazení](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
- [Co je řízení přístupu na základě role v Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Cíle škálovatelnosti pro poskytovatele prostředků Azure Storage](scalability-targets-resource-provider.md)
