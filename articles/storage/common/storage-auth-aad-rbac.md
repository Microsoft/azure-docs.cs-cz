---
title: RBAC umožňuje spravovat přístupová práva ke kontejnerům služby Azure Storage a fronty (Preview) | Dokumentace Microsoftu
description: Použití řízení přístupu na základě rolí (RBA) k přiřazování rolí pro přístup k datům služby Azure Storage na uživatele, skupiny, instančních objektů aplikace nebo identity spravovaných služeb. Azure Storage podporuje předdefinované a vlastní role pro oprávnění ke kontejnerům a fronty.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 7dde80d8134a6f30f4f32086a2dd030712051256
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093138"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Správa přístupových práv k datům služby Azure Storage pomocí RBAC (Preview)

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k kontejnery nebo fronty. Když roli RBAC se přiřadí identity Azure AD, že identita je udělen přístup k těmto prostředkům podle zadaného oboru. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Můžete přiřadit přístupová práva pro prostředky služby Azure Storage pomocí webu Azure portal, nástrojů příkazového řádku Azure a rozhraní API pro správu Azure. 

Identity Azure AD může být uživatele, skupinu nebo instanční objekt služby aplikace nebo může být *se identita spravované služby*. Objekt zabezpečení může být uživatele, skupinu nebo instanční objekt služby aplikace. A [se identita spravované služby](../../active-directory/managed-identities-azure-resources/overview.md) je automaticky spravované identity používá k ověření z aplikace běžící v Azure virtuální počítače, aplikace function App, škálovací sady virtuálních počítačů a dalších. Přehled identity ve službě Azure AD najdete v tématu [řešení identit Azure pochopit](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>Role RBAC pro Azure Storage

Azure Storage podporuje předdefinované i vlastní role RBAC. Azure Storage nabízí tyto předdefinované role RBAC pro použití se službou Azure AD:

- [Přispěvatel dat objektu Blob služby Storage (Preview)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Čtenář dat objektu Blob služby Storage (Preview)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Přispěvatel dat fronty služby Storage (Preview)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Čtenář dat fronty služby Storage (Preview)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Další informace o tom, předdefinované role jsou definované pro službu Azure Storage, najdete v článku [pochopení definic rolí](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Můžete také definovat vlastní role pro použití s kontejnery a fronty. Další informace najdete v tématu [vytváření vlastních rolí pro řízení přístupu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>Přiřadit roli instančnímu objektu zabezpečení

Přiřazení RBAC role pro Azure identity k udělení oprávnění pro kontejnery nebo fronty ve vašem účtu úložiště. Můžete omezit rozsah přiřazení rolí k účtu úložiště nebo konkrétní kontejner nebo fronty. Následující tabulka shrnuje přístupová práva udělují předdefinované role, v závislosti na rozsahu: 

|                                 |     Přispěvatel dat objektu BLOB                                                 |     Čtenář dat objektu BLOB                                                |     Přispěvatel dat fronty služby                                  |     Čtenář dat fronty služby                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Omezená na předplatné       |    Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v rámci předplatného       |    Přístup pro čtení pro všechny kontejnery a objekty BLOB v rámci předplatného       |    Přístup pro čtení a zápis pro všechny fronty v rámci předplatného       |    Přístup pro čtení pro všechny fronty v rámci předplatného         |
|    Obor do skupiny prostředků     |    Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB ve skupině prostředků     |    Přístup pro čtení pro všechny kontejnery a objekty BLOB ve skupině prostředků     |    Přístup pro čtení a zápis pro všechny fronty ve skupině prostředků     |    Přístup pro čtení pro všechny fronty ve skupině prostředků     |
|    Obor do účtu úložiště    |    Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v účtu úložiště    |    Přístup pro čtení pro všechny kontejnery a objekty BLOB v účtu úložiště    |    Přístup pro čtení a zápis do všech front v účtu úložiště    |    Přístup pro čtení pro všechny fronty v účtu úložiště    |
|    Rozsah kontejneru či fronty    |    Přístup pro čtení a zápis do zadaného kontejneru a jeho objektům BLOB              |    Přístup pro čtení zadaného kontejneru a jeho objektům BLOB              |    Přístup pro čtení a zápis do zadané fronty                  |    Přístup pro čtení do zadané fronty                    |

> [!NOTE]
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště, kontejner nebo fronty.

Podrobnosti o oprávněních požadovaných k volání operací služby Azure Storage najdete v tématu [oprávnění pro volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Následující části vysvětlují, jak přiřadit role omezená na účet úložiště, nebo jenom pro kontejner.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Přiřazení role omezená na účet úložiště na webu Azure Portal

Přiřazení předdefinované role uděluje přístup pro všechny kontejnery nebo front v účtu úložiště na webu Azure Portal:

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.
2. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu k účtu. Klikněte na tlačítko **přidat** tlačítko Přidat novou roli.

    ![Snímek obrazovky zobrazující nastavení řízení přístupu úložiště](media/storage-auth-aad-rbac/portal-access-control.png)

3. V **přidat oprávnění** okna, vyberte roli, kterou chcete přiřadit k Azure AD identity. Potom prohledávat při vyhledávání identity, kterým chcete přiřadit danou roli. Například na následujícím obrázku **Čtenář dat objektu Blob služby Storage (Preview)** role přiřazené uživateli.

    ![Snímek obrazovky ukazující, jak přiřadit roli RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Klikněte na **Uložit**. Identity, kterému jste přiřadili roli objeví v seznamu pod tuto roli. Například následující obrázek ukazuje, že uživatelé přidaní Teď máte oprávnění ke čtení pro všechna data objektů blob v účtu úložiště.

    ![Snímek obrazovky zobrazující seznam uživatelů, které jsou přiřazeny k roli](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Přiřazení role omezená na kontejneru nebo fronty na webu Azure Portal

Postup pro přiřazení předdefinované role obor do kontejneru, nebo do fronty jsou podobné. Zde zobrazená procedura přiřadí role omezená na kontejner, ale můžete použít stejný postup přiřazení role obor pro frontu: 

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště a zobrazit **přehled** pro účet.
2. V části služby Blob Service, vyberte **procházet objekty BLOB**. 
3. Najděte kontejner, pro kterou chcete přiřadit roli a zobrazí nastavení kontejneru. 
4. Vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu pro kontejner.
5. V **přidat oprávnění** okna, vyberte roli, kterou chcete přiřadit k Azure AD identity. Potom lokalizováním identit, ke kterému chcete přiřadit danou roli.
6. Klikněte na **Uložit**. Identity, kterému jste přiřadili roli objeví v seznamu pod tuto roli. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění ke čtení k datům v kontejneru s názvem *ukázkový kontejner*.

    ![Snímek obrazovky zobrazující seznam uživatelů, které jsou přiřazeny k roli](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Další kroky

- Další informace o RBAC najdete v tématu [Začínáme s řízením přístupu na základě rolí](../../role-based-access-control/overview.md).
- Naučte se přiřazovat a spravovat přiřazení rolí pro RBAC pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí Azure Powershellu](../../role-based-access-control/role-assignments-powershell.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../../role-based-access-control/role-assignments-rest.md)
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi Azure Storage](storage-auth-aad-app.md).
- Další informace o integraci služby Azure AD pro kontejnery služby Azure a fronty, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 
