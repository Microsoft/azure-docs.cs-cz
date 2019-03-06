---
title: Pomocí webu Azure portal ke správě Azure AD přístupová práva ke kontejnerům a fronty pomocí RBAC (preview) – služby Azure Storage | Dokumentace Microsoftu
description: Pomocí řízení přístupu na základě rolí (RBAC) z portálu Azure portal k přiřazení přístupu ke kontejnerům a fronty a objekty zabezpečení. Azure Storage podporuje předdefinované a vlastní role RBAC pro ověřování pomocí Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/01/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8050027bac3d2f13ac457edcdad9275c040305da
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431852"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal-preview"></a>Udělení přístupu k kontejnery služby Azure a fronty pomocí RBAC na webu Azure Portal (preview)

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k kontejnery nebo fronty. 

Když roli RBAC je přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Objekt zabezpečení Azure AD může být uživatel, skupina, instančního objektu aplikace nebo [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak pomocí webu Azure portal přiřadíte role RBAC. Na webu Azure portal poskytuje jednoduché rozhraní pro přiřazení role RBAC a správu přístupu k prostředkům úložiště. Můžete také přiřadit role RBAC pro objekt blob a fronty prostředků pomocí nástroje příkazového řádku Azure nebo rozhraní API pro správu služby Azure Storage. Další informace o rolích RBAC pro prostředky úložiště, najdete v části [ověřit přístup k Azure, objekty BLOB a fronty pomocí Azure Active Directory (Preview)](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určení oboru prostředků 

Předtím, než přiřadíte roli RBAC k objektu zabezpečení, určete obor přístupu, který by měl mít objekt zabezpečení. Osvědčené postupy určují, že je vždy vhodné udělit pouze nejužší možná obor.

Následující seznam popisuje úrovně, ve kterém můžete upřesnit rozsah přístupu k prostředkům Azure blob a fronty, počínaje zpomalit:

- **Kontejner.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem objektům BLOB v kontejneru, jakož i kontejner vlastností a metadat.
- **Jednotlivé fronty.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup k zprávy ve frontě, jakož i fronty vlastností a metadat.
- **Účet úložiště.** V tomto oboru objekt zabezpečení má přístup do všech kontejnerů a jeho objekty BLOB, nebo všechny fronty a jejich zprávy.
- **Skupina prostředků.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem kontejnery nebo fronty ve všech účtů úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem kontejnery nebo fronty ve všech účtů úložiště ve všech skupinách prostředků v předplatném.

Až zjistíte požadovaný obor na přiřazení role, přejděte na příslušný prostředek na webu Azure Portal. Zobrazení **řízení přístupu (IAM)** nastavení prostředků a postupujte podle pokynů v dalších částech a spravovat přiřazení rolí.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Přiřadíte role RBAC pomocí webu Azure portal

Udělení přístupu k prostředkům blob a fronty pomocí přihlašovacích údajů Azure AD na webu Azure Portal zahrnuje následující kroky: 

1. Přiřadíte příslušné role RBAC úložiště Azure pro udělení přístupu k kontejnery nebo fronty. Přístup pro čtení, přiřaďte **čtecí modul dat objektů Blob (Preview)** nebo **Čtenář dat fronty služby (Preview)** role. Pro čtení, zápisu a odstranění přístup, přiřaďte **Přispěvatel dat objektu Blob (Preview)** nebo **Přispěvatel dat fronty služby (Preview)** role. Můžete také přiřadit vlastní roli.

1. Azure Resource Manageru přiřadit [čtečky](../../role-based-access-control/built-in-roles.md#reader) role pro uživatele, kteří potřebují přístup k kontejnery nebo fronty pomocí webu Azure portal pomocí svých přihlašovacích údajů Azure AD. 

Následující části popisují každou z těchto kroků podrobněji.

### <a name="assign-a-built-in-rbac-role"></a>Přiřazení předdefinované role RBAC

Před přiřazením role k objektu zabezpečení, ujistěte se, že jste vezměte v úvahu rozsah oprávnění, která udělujete. Zkontrolujte [určit rozsah prostředků](#determine-resource-scope) části rozhodnout vhodným rozsahem.

Zde zobrazená procedura přiřadí role omezená na kontejner, ale můžete použít stejný postup přiřazení role obor pro frontu: 

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště a zobrazit **přehled** pro účet.
1. V části Služba vyberte **objekty BLOB**. 
1. Najděte kontejner, pro kterou chcete přiřadit roli a zobrazí nastavení kontejneru. 
1. Vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu pro kontejner. Vyberte **přiřazení rolí** kartu pro zobrazení seznamu přiřazení rolí.

    ![Snímek obrazovky zobrazující nastavení řízení přístupu kontejneru](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Klikněte na tlačítko **přidat přiřazení role** tlačítko Přidat novou roli.
1. V **přidat přiřazení role** okna, vyberte roli služby Azure Storage, kterou chcete přiřadit. Potom prohledávat při vyhledávání objektu zabezpečení, ke kterému chcete přiřadit danou roli.

    ![Snímek obrazovky ukazující, jak přiřadit roli RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Klikněte na **Uložit**. Identity, kterému jste přiřadili roli objeví v seznamu pod tuto roli. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění ke čtení k datům v kontejneru s názvem *ukázkový kontejner*.

    ![Snímek obrazovky zobrazující seznam uživatelů, které jsou přiřazeny k roli](media/storage-auth-aad-rbac/container-scoped-role.png)

Můžete použít podobným způsobem přiřazení role omezená na účet úložiště, skupinu prostředků nebo předplatného.

> [!NOTE]
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště, kontejner nebo fronty.
> 
> Nelze přiřadit role omezená na kontejneru nebo fronty, pokud má váš účet úložiště hierarchického oboru názvů povolené.

### <a name="assign-the-reader-role-for-portal-access"></a>Přiřadit role Čtenář pro přístup k portálu

Když přiřadíte předdefinovaných i vlastních rolí pro službu Azure Storage k objektu zabezpečení, udělujete oprávnění pro tento objekt zabezpečení k provedení operací s daty ve vašem účtu úložiště. Předdefinované **čtecí modul dat** v těchto rolích zajišťují oprávnění ke čtení pro data z kontejneru nebo fronty, zatímco integrovaná v **Přispěvatel Data** v těchto rolích zajišťují čtení, zápis a odstranění oprávnění ke kontejneru nebo fronty. Zadaný prostředek mají rozsah oprávnění.  

Například, pokud přiřadíte **Přispěvatel dat objektu Blob služby Storage (Preview)** role uživateli Mary na úrovni kontejner s názvem **ukázkový kontejner**, pak je Mary udělen čtení, zápisu a odstranění přístup ke všem objekty BLOB v tomto kontejneru.

Nicméně, pokud chce Mary zobrazení objektu blob na webu Azure Portal, pak bude **Přispěvatel dat objektu Blob služby Storage (Preview)** role samostatně, nebudou zajišťovat dostatečná oprávnění k procházení portálu na objekt blob, aby bylo možné zobrazit. Další služby Azure AD oprávnění nejsou vyžadována k přejděte na portálu a zobrazit další materiály, které jsou viditelné.

Pokud budou uživatelé potřebovat přístup k objektům blob na webu Azure Portal a pak je přiřadit další role RBAC, moct [čtečky](../../role-based-access-control/built-in-roles.md#reader) role za účelem tito uživatelé na úrovni účtu úložiště nebo vyšší. **Čtečky** rolí je role Azure Resource Manageru, která umožňuje uživatelům zobrazit prostředků účtu úložiště, ale ne je upravovat. Neposkytuje oprávnění ke čtení k datům ve službě Azure Storage, ale jenom pro účet správy zdrojů.

Následující postup použijte k přiřazení **čtečky** rolí tak, aby uživatel může přístup k objektům blob na webu Azure Portal. V tomto příkladu má obor přiřazení k účtu úložiště:

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.
1. Vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu pro účet úložiště. Vyberte **přiřazení rolí** kartu pro zobrazení seznamu přiřazení rolí.
1. V **přidat přiřazení role** okna, vyberte **čtečky** role. 
1. Z **přiřadit přístup k** rozevíracího seznamu, vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**.
1. Prohledávat při vyhledávání objektu zabezpečení, ke kterému chcete přiřadit roli.
1. Uložte přiřazení role.

> [!NOTE]
> Přiřazení role čtenáře je potřebný jenom u uživatelů, kteří potřebují přístup k objektům BLOB nebo fronty pomocí webu Azure portal. 

## <a name="use-azure-ad-credentials-with-the-portal"></a>Přihlašovací údaje služby Azure AD pomocí portálu

Pro přístup k objektům BLOB nebo fronty na webu Azure Portal pomocí svých přihlašovacích údajů Azure AD, použijte odkazy ve verzi preview je znázorněno na následujícím obrázku:

![Přístup k objektům BLOB nebo fronty pomocí přihlašovacích údajů Azure AD na portálu](media/storage-auth-aad-rbac/access-data-azure-ad.png)

Pokud přistupujete k datům objektu blob nebo fronty pomocí produkčního odkazů spíše než odkazy ve verzi preview, na webu Azure portal k autorizaci přístupu, nikoli pomocí služby Azure AD používá klíč účtu.

## <a name="next-steps"></a>Další postup

- Další informace o RBAC najdete v tématu [co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).
- Naučte se přiřazovat a spravovat přiřazení rolí pro RBAC pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí Azure Powershellu](../../role-based-access-control/role-assignments-powershell.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../../role-based-access-control/role-assignments-rest.md)
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi Azure Storage](storage-auth-aad-app.md).
- Další informace o integraci služby Azure AD pro kontejnery služby Azure a fronty, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
