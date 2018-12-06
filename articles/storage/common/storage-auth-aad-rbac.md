---
title: RBAC umožňuje spravovat přístupová práva ke kontejnerům a front (preview) – služby Azure Storage | Dokumentace Microsoftu
description: Použití řízení přístupu na základě role (RBAC) k přiřazování rolí pro přístup k datům objektu blob a fronty na uživatele, skupiny, instančních objektů aplikace nebo identity spravovaných služeb. Azure Storage podporuje předdefinované a vlastní role pro oprávnění ke kontejnerům a fronty.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: ac62800e81cece61e9f51c496ace2868629a49a1
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960239"
---
# <a name="manage-access-rights-to-azure-blob-and-queue-data-with-rbac-preview"></a>Spravovat přístupová práva k Azure Blob a fronty dat pomocí RBAC (Preview)

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k kontejnery nebo fronty. Když roli RBAC se přiřadí identity Azure AD, že identita je udělen přístup k těmto prostředkům podle zadaného oboru. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Můžete přiřadit přístupová práva pro prostředky služby Azure Storage pomocí webu Azure portal, nástrojů příkazového řádku Azure a rozhraní API pro správu Azure. 

Identity Azure AD může být uživatele, skupiny nebo instanční objekt aplikace, nebo může být spravovaná identita pro prostředky Azure. Objekt zabezpečení může být uživatele, skupinu nebo instanční objekt služby aplikace. A [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) je automaticky spravované identity používá k ověření z aplikace běžící v Azure virtuální počítače, aplikace function App, škálovací sady virtuálních počítačů a dalších. Přehled identity ve službě Azure AD najdete v tématu [řešení identit Azure pochopit](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

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

|Rozsah|Vlastník dat objektů BLOB|Přispěvatel dat objektu BLOB|Čtenář dat objektu BLOB|Přispěvatel dat fronty služby|Čtenář dat fronty služby|
|---|---|---|---|---|---|
|Úroveň Subscrition|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v rámci předplatného|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v rámci předplatného| Přístup pro čtení pro všechny kontejnery a objekty BLOB v rámci předplatného|Přístup pro čtení a zápis pro všechny fronty v rámci předplatného|Přístup pro čtení pro všechny fronty v rámci předplatného|
|Úrovni skupiny prostředků|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB ve skupině prostředků|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB ve skupině prostředků|Přístup pro čtení pro všechny kontejnery a objekty BLOB ve skupině prostředků|Přístup pro čtení a zápis pro všechny fronty ve skupině prostředků|Přístup pro čtení pro všechny fronty ve skupině prostředků|
|Úroveň účtu úložiště|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v účtu úložiště|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v účtu úložiště|Přístup pro čtení pro všechny kontejnery a objekty BLOB v účtu úložiště|Přístup pro čtení a zápis do všech front v účtu úložiště|Přístup pro čtení pro všechny fronty v účtu úložiště|
|Úroveň kontejneru či fronty|Přístup pro čtení a zápis do zadaného kontejneru a jeho objektům BLOB|Přístup pro čtení a zápis do zadaného kontejneru a jeho objektům BLOB|Přístup pro čtení zadaného kontejneru a jeho objektům BLOB|Přístup pro čtení a zápis do zadané fronty|Přístup pro čtení do zadané fronty|

> [!NOTE]
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště, kontejner nebo fronty.

Podrobnosti o oprávněních požadovaných k volání operací služby Azure Storage najdete v tématu [oprávnění pro volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Následující části vysvětlují, jak přiřadit role omezená na účet úložiště, nebo jenom pro kontejner.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Přiřazení role omezená na účet úložiště na webu Azure Portal

Přiřazení předdefinované role uděluje přístup pro všechny kontejnery nebo front v účtu úložiště na webu Azure Portal:

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.
1. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu k účtu. Vyberte **přiřazení rolí** kartu pro zobrazení seznamu přiřazení rolí.

    ![Snímek obrazovky zobrazující nastavení řízení přístupu úložiště](media/storage-auth-aad-rbac/portal-access-control.png)

1. Klikněte na tlačítko **přidat přiřazení role** tlačítko Přidat novou roli.
1. V **přidat přiřazení role** okna, vyberte roli, kterou chcete přiřadit k Azure AD identity. Potom prohledávat při vyhledávání identity, kterým chcete přiřadit danou roli. Například na následujícím obrázku **Čtenář dat objektu Blob služby Storage (Preview)** role přiřazené uživateli.

    ![Snímek obrazovky ukazující, jak přiřadit roli RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Klikněte na **Uložit**. Identity, kterému jste přiřadili roli objeví v seznamu pod tuto roli. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění ke čtení pro všechna data objektů blob v účtu úložiště.

    ![Snímek obrazovky zobrazující seznam uživatelů, které jsou přiřazeny k roli](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Přiřazení role omezená na kontejneru nebo fronty na webu Azure Portal

> [!IMPORTANT]
> Nelze provést, pokud se pomocí účtu s hierarchického oboru názvů ještě nepovolila.

Postup pro přiřazení předdefinované role obor do kontejneru, nebo do fronty jsou podobné. Zde zobrazená procedura přiřadí role omezená na kontejner, ale můžete použít stejný postup přiřazení role obor pro frontu: 

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště a zobrazit **přehled** pro účet.
1. V části Služba vyberte **objekty BLOB**. 
1. Najděte kontejner, pro kterou chcete přiřadit roli a zobrazí nastavení kontejneru. 
1. Vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu pro kontejner. Vyberte **přiřazení rolí** kartu pro zobrazení seznamu přiřazení rolí.

    ![Snímek obrazovky zobrazující nastavení řízení přístupu kontejneru](media/storage-auth-aad-rbac/portal-access-control-container.png)
1. Klikněte na tlačítko **přidat přiřazení role** tlačítko Přidat novou roli.
1. V **přidat přiřazení role** okna, vyberte roli, kterou chcete přiřadit k Azure AD identity. Potom lokalizováním identit, ke kterému chcete přiřadit danou roli.
1. Klikněte na **Uložit**. Identity, kterému jste přiřadili roli objeví v seznamu pod tuto roli. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění ke čtení k datům v kontejneru s názvem *ukázkový kontejner*.

    ![Snímek obrazovky zobrazující seznam uživatelů, které jsou přiřazeny k roli](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Další postup

- Další informace o RBAC najdete v tématu [co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).
- Naučte se přiřazovat a spravovat přiřazení rolí pro RBAC pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí Azure Powershellu](../../role-based-access-control/role-assignments-powershell.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../../role-based-access-control/role-assignments-rest.md)
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi Azure Storage](storage-auth-aad-app.md).
- Další informace o integraci služby Azure AD pro kontejnery služby Azure a fronty, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
