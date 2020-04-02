---
title: Správa přístupových klíčů pro účet
titleSuffix: Azure Storage
description: Přečtěte si, jak zobrazit, spravovat a otáčet přístupové klíče účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: b4e91aa59168deb18375bf86ae77f655ca3dab47
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521277"
---
# <a name="manage-storage-account-access-keys"></a>Správa přístupových klíčů účtu úložiště

Když vytvoříte účet úložiště, Azure vygeneruje dva 512bitové klíče pro přístup k účtu úložiště. Tyto klíče lze použít k autorizaci přístupu k datům v účtu úložiště prostřednictvím autorizace sdíleného klíče.

Společnost Microsoft doporučuje používat Azure Key Vault ke správě přístupových klíčů a pravidelně otáčet a regenerovat klíče. Použití služby Azure Key Vault usnadňuje otáčení klíčů bez přerušení do aplikací. Klíče můžete také otáčet ručně.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Zobrazení přístupových klíčů a připojovacího řetězce

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Správa přístupových klíčů pomocí služby Azure Key Vault

Společnost Microsoft doporučuje ke správě a otočení přístupových klíčů používat azure key vault. Vaše aplikace může bezpečně přistupovat k vašim klíčům v trezoru klíčů, takže se můžete vyhnout jejich ukládání s kódem aplikace. Další informace o použití trezoru klíčů pro správu klíčů naleznete v následujících článcích:

- [Správa klíčů účtů úložiště pomocí Azure Key Vault a PowerShellu](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Správa klíčů účtů úložiště pomocí azure key vaultu a azure cli](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ruční otočení přístupových kláves

Společnost Microsoft doporučuje pravidelně střídat přístupové klíče, aby byl váš účet úložiště zabezpečen. Pokud je to možné, použijte Azure Key Vault ke správě přístupových klíčů. Pokud trezor klíčů nepoužíváte, budete muset klíče otočit ručně.

Jsou přiřazeny dva přístupové klávesy, abyste je mohli otáčet. S dvěma klíči zajišťuje, že vaše aplikace udržuje přístup k Azure Storage v průběhu celého procesu.

> [!WARNING]
> Obnovení přístupových klíčů může ovlivnit všechny aplikace nebo služby Azure, které jsou závislé na klíči účtu úložiště. Všichni klienti, kteří používají klíč účtu pro přístup k účtu úložiště, musí být aktualizováni, aby používali nový klíč, včetně mediálních služeb, cloudových, desktopových a mobilních aplikací a grafických aplikací uživatelského rozhraní pro Azure Storage, jako je [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Chcete-li otočit klíče účtu úložiště, postupujte podle tohoto postupu:

1. Aktualizujte připojovací řetězce v kódu aplikace a použijte sekundární klíč.
2. Znovu vygenerujte primární přístupový klíč pro účet úložiště. V okně **Přístupové klíče** na webu Azure portal klikněte na **Regenerate Key1**a potom kliknutím na **Ano** potvrďte, že chcete vygenerovat nový klíč.
3. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
4. Stejným způsobem pak opětovně vygenerujte sekundární přístupový klíč.

> [!NOTE]
> Společnost Microsoft doporučuje používat pouze jeden z klíčů ve všech aplikacích současně. Pokud používáte klíč 1 v některých místech a klíč 2 v jiných, nebudete moci otáčet klíče bez ztráty přístupu některé aplikace.

Chcete-li otočit přístupové klíče účtu, musí být uživatel správcem služeb nebo musí být přiřazena role RBAC, která zahrnuje **microsoft.storage/storageaccounts/regeneratekey/action**. Some built-in RBAC roles that include this action are the **Owner**, **Contributor**, and **Storage Account Key Operator Service Role** roles. Další informace o roli správce služby najdete [v tématu Klasické role správce předplatného, role Azure RBAC a role Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Podrobné informace o předdefinovaných rolích RBAC pro Azure Storage najdete v části **Úložiště** [ve integrovaných rolích Azure pro Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Další kroky

- [Přehled účtu úložiště Azure](storage-account-overview.md)
- [Vytvoření účtu úložiště](storage-account-create.md)
