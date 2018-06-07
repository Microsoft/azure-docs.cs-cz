---
title: Používat funkci RBAC ke správě oprávnění ke kontejnerům Azure Storage a front (Preview) | Microsoft Docs
description: Pomocí řízení přístupu na základě role (RBA) přiřadit role pro přístup k datům Azure Storage a uživatelé, skupiny, objekty služby aplikace nebo identita spravované služby. Úložiště Azure podporuje předdefinované a vlastní role pro přístupová práva ke kontejnerům a fronty.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: cb77bd4418e105c877202f0f1725350380ea2308
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660744"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Správa přístupových práv k Azure Storage data pomocí RBAC (Preview)

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Úložiště Azure definuje sadu předdefinovaných RBAC role, které zahrnuje společné sady oprávnění použila k přístupu kontejnery nebo fronty. Když RBAC role je přiřazená Azure AD identity, že identita je udělen přístup k tyto prostředky podle zadaného oboru. Přístup můžete omezená na úroveň předplatné, skupinu prostředků, účet úložiště, nebo jednotlivé kontejneru nebo fronty. Můžete přiřadit přístupová práva pro prostředky úložiště Azure pomocí portálu Azure, nástroje příkazového řádku Azure a rozhraní API pro správu Azure. 

Azure AD identity může být uživatele, skupinu nebo objekt aplikace služby, nebo to může být *identita spravované služby*. Objekt zabezpečení může být uživatele, skupinu nebo objekt aplikace služby. A [identita spravované služby](../../active-directory/managed-service-identity/overview.md) je automaticky spravované identity použít k ověřování z aplikací, které běží ve virtuálních počítačích Azure, funkce aplikací, sady škálování virtuálního počítače a dalších. Přehled identity ve službě Azure AD najdete v tématu [řešeních identity v Azure pochopit](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>Role RBAC pro Azure Storage

Úložiště Azure podporuje role RBAC předdefinované a vlastní. Úložiště Azure nabízí tyto předdefinované role RBAC pro použití s Azure AD:

- [Úložiště objektů Blob dat přispěvatele (Preview)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Čtecí modul dat objektů Blob Storage (Preview)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Úložiště fronty dat přispěvatele (Preview)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Čtecí modul dat fronty úložiště (Preview)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Další informace o tom, vestavěné role, které jsou definovány pro Azure Storage najdete v tématu [pochopit definice role](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Můžete také definovat vlastní role pro použití s kontejnery a fronty. Další informace najdete v tématu [vytvářet vlastní role pro řízení přístupu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md). 

> [!IMPORTANT]
> Tato verze preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nebudou dostupné, dokud integrace Azure AD pro Azure Storage je deklarovaná všeobecně dostupná. Pokud pro váš scénář není dosud podporován integrace Azure AD, nadále používat sdílený klíč autorizace nebo tokeny SAS ve svých aplikacích. Další informace o verzi preview najdete v tématu [ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview)](storage-auth-aad.md).
>
> Ve verzi Preview přiřazení rolí pro RBAC může trvat až pět minut, aby se rozšířily změny.

## <a name="assign-a-role-to-a-security-principal"></a>Přiřazení role objekt zabezpečení

Role RBAC přiřadíte identitou Azure udělit oprávnění pro kontejnery nebo fronty ve vašem účtu úložiště. Přiřazení role k účtu úložiště nebo na konkrétní kontejner nebo fronty, můžete určit obor. Následující tabulka shrnuje přístupová práva udělují předdefinované role, v závislosti na rozsahu: 

|                                 |     Přispěvatel Data objektů BLOB                                                 |     Čtecí modul dat objektů BLOB                                                |     Přispěvatel Data fronty                                  |     Čtecí modul dat fronty                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Vymezeno na předplatné       |    Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB ve vašem předplatném       |    Přístup pro čtení pro všechny kontejnery a objekty BLOB ve vašem předplatném       |    Přístup pro čtení a zápis pro všechny fronty v rámci předplatného       |    Přístup pro čtení pro všechny fronty v rámci předplatného         |
|    Vymezeno na skupinu prostředků     |    Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB ve skupině prostředků     |    Přístup pro čtení pro všechny kontejnery a objekty BLOB ve skupině prostředků     |    Přístup pro čtení a zápis pro všechny fronty ve skupině prostředků     |    Přístup pro čtení pro všechny fronty ve skupině prostředků     |
|    Obor k účtu úložiště    |    Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v účtu úložiště    |    Přístup pro čtení pro všechny kontejnery a objekty BLOB v účtu úložiště    |    Přístup pro čtení a zápis pro všechny fronty v účtu úložiště    |    Přístup pro čtení pro všechny fronty v účtu úložiště    |
|    Obor do kontejneru nebo fronty    |    Přístup pro čtení a zápis pro zadaný kontejner a jeho objekty BLOB              |    Přístup pro čtení zadaný kontejner a jeho objekty BLOB              |    Přístup pro čtení a zápis k zadané frontě                  |    Přístup pro čtení k zadané frontě                    |

> [!NOTE]
> Jako vlastníka účtu Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami role RBAC pro Azure Storage. Můžete ji přiřadit na úrovni vaše předplatné, skupinu prostředků, účet úložiště, nebo z kontejneru nebo fronty.

Informace o oprávněních k volání operací Azure Storage najdete v tématu [oprávnění k volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Následující části vysvětlují, jak přiřadit role obor k účtu úložiště, nebo jenom pro jednotlivé kontejneru.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Přiřazení role obor k účtu úložiště na portálu Azure

Přiřazení předdefinovaná role, udělení přístupu k všechny kontejnery nebo fronty v účtu storage na portálu Azure:

1. V [portál Azure](https://azure.portal.com/), přejděte na svůj účet úložiště.
2. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu pro účet. Klikněte **přidat** tlačítko Přidat novou roli.

    ![Snímek obrazovky znázorňující zprávu úložiště nastavení řízení přístupu](media/storage-auth-aad-rbac/portal-access-control.png)

3. V **přidat oprávnění** okně vyberte role, kterou chcete přiřadit k Azure AD identity. Poté vyhledejte ke zjištění identity, kterému chcete přiřadit tuto roli. Například na následujícím obrázku **čtecí modul dat objektů Blob Storage (Preview)** role přiřazen k uživateli.

    ![Snímek obrazovky znázorňující způsob přiřazení role RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Klikněte na **Uložit**. Identita, kterému jste přiřadili roli objeví v seznamu pod dané roli. Například následující obrázek ukazuje, že uživatelé přidat nyní mají oprávnění ke čtení všechna data objektů blob v účtu úložiště.

    ![Snímek obrazovky zobrazující seznam uživatelů přidružených k roli](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Přiřazení role rozsah z kontejneru nebo fronty na portálu Azure

Kroky pro přiřazení předdefinovaná role obor na kontejner nebo do fronty jsou podobné. Postup tady uvedené přiřadí roli obor do kontejneru, ale můžete postupovat podle stejných kroků přiřazení role obor do fronty: 

1. V [portál Azure](https://azure.portal.com/), přejděte na svůj účet úložiště a zobrazit **přehled** pro účet.
2. V části služby objektů Blob, vyberte **procházet objekty BLOB**. 
3. Vyhledejte kontejneru, pro kterou chcete přiřadit role a zobrazení nastavení kontejneru. 
4. Vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu pro příslušný kontejner.
5. V **přidat oprávnění** okno, vyberte roli, kterou chcete přiřadit k Azure AD identity. Potom lokalizováním identit, ke kterému chcete přiřadit tuto roli.
6. Klikněte na **Uložit**. Identita, kterému jste přiřadili roli objeví v seznamu pod dané roli. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění ke čtení dat v kontejner s názvem *ukázkový kontejner*.

    ![Snímek obrazovky zobrazující seznam uživatelů přidružených k roli](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Další kroky

- Další informace o RBAC najdete v tématu [Začínáme s řízením přístupu na základě rolí](../../role-based-access-control/overview.md).
- Informace o tom, které chcete přiřadit a spravovat přiřazení rolí pro RBAC v prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí prostředí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../../role-based-access-control/role-assignments-rest.md)
- Informace o tom, k autorizaci přístupu ke kontejnerům a fronty ze v rámci aplikace úložiště, najdete v části [používání Azure AD s aplikacemi Azure Storage](storage-auth-aad-app.md).
- Další informace o integraci služby Azure AD pro Azure kontejnery a fronty najdete v tématu blog týmu Azure Storage, post, [uvedení ověřování Preview služby Azure AD pro Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 