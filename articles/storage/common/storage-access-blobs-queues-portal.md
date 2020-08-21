---
title: Vyberte, jak autorizovat přístup k datům objektů BLOB nebo Queue v Azure Portal
titleSuffix: Azure Storage
description: Při přístupu k datům objektů BLOB nebo front pomocí Azure Portal portál vytvoří žádosti, které se Azure Storage v rámci pokrývání. Tyto požadavky na Azure Storage se dají ověřit a autorizovat pomocí účtu Azure AD nebo přístupového klíče účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: contperfq1
ms.openlocfilehash: 7ab0899f49fc28d787e6b53945558709353c4512
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691441"
---
# <a name="choose-how-to-authorize-access-to-blob-or-queue-data-in-the-azure-portal"></a>Vyberte, jak autorizovat přístup k datům objektů BLOB nebo Queue v Azure Portal

Při přístupu k datům objektů BLOB nebo front pomocí [Azure Portal](https://portal.azure.com)portál vytvoří žádosti, které se Azure Storage v rámci pokrývání. Požadavek na Azure Storage může být autorizovaný buď pomocí účtu Azure AD, nebo přístupového klíče účtu úložiště. Portál indikuje, kterou metodu používáte, a umožňuje přepínat mezi nimi, pokud máte příslušná oprávnění.  

Můžete také určit, jak autorizovat jednotlivé operace nahrávání objektů BLOB v Azure Portal. Ve výchozím nastavení používá portál k autorizaci operace nahrávání objektů BLOB jakoukoli metodu, kterou už používáte. při nahrávání objektu BLOB máte ale možnost toto nastavení změnit.

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Oprávnění potřebná pro přístup k datům objektů BLOB nebo front

V závislosti na tom, jak chcete autorizovat přístup k datům objektů BLOB nebo Queue v Azure Portal, budete potřebovat specifická oprávnění. Ve většině případů jsou tato oprávnění k dispozici prostřednictvím řízení přístupu na základě role (RBAC). Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Použití přístupového klíče účtu

Pokud chcete získat přístup k datům objektů BLOB a front pomocí přístupového klíče účtu, musíte mít přiřazenou roli Azure, která zahrnuje akci RBAC **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Tato role Azure může být integrovaná nebo vlastní role. Předdefinované role, které podporují **Microsoft. Storage/storageAccounts/klíče listkey/Action** , zahrnují:

- Role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Role [přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Role [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Když se pokusíte o přístup k datům objektů BLOB nebo front v Azure Portal, portál nejprve zkontroluje, jestli vám byla přiřazena role s **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Pokud jste k této akci přiřadili roli, portál použije klíč účtu pro přístup k datům BLOB a Queue. Pokud jste k této akci nepřiřadili roli, pokusí se portál získat přístup k datům pomocí účtu Azure AD.

> [!NOTE]
> Správci služby pro klasický odběr role správce a spolusprávce zahrnují ekvivalent role Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) . Role **vlastníka** zahrnuje všechny akce, včetně **Microsoft. Storage/storageAccounts/klíče listkey/Action**, takže uživatel s jednou z těchto rolí pro správu může také přistupovat k datům objektů BLOB a Queue pomocí klíče účtu. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Použití účtu Azure AD

Pokud chcete získat přístup k datům objektů BLOB nebo front z Azure Portal pomocí svého účtu služby Azure AD, musí být pro vás oba následující příkazy pravdivé:

- Je vám přiřazena minimálně role [čtenář](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager, která je vymezená na úroveň účtu úložiště nebo vyšší. Role **Čtenář** uděluje nejvyšší omezení oprávnění, ale také je přijatelná jiná role Azure Resource Manager, která uděluje přístup k prostředkům správy účtu úložiště.
- Přiřadili jste buď integrovanou, nebo vlastní roli, která poskytuje přístup k datům BLOB nebo Queue.

Přiřazení role **Čtenář** nebo jiné Azure Resource Manager přiřazení role je nezbytné, aby uživatel mohl zobrazit a procházet prostředky správy účtu úložiště v Azure Portal. Role Azure, které udělují přístup k datům BLOB nebo Queue, neudělují přístup k prostředkům správy účtu úložiště. Pro přístup k datům objektů BLOB nebo front na portálu potřebuje uživatel oprávnění k procházení prostředků účtu úložiště. Další informace o tomto požadavku najdete v tématu [přiřazení role čtenáře pro přístup k portálu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Mezi předdefinované role, které podporují přístup k datům objektů BLOB nebo Queue, patří:

- [Vlastník dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): pro řízení přístupu POSIX pro Azure Data Lake Storage Gen2.
- [Přispěvatel dat objektu BLOB služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): oprávnění ke čtení, zápisu a odstraňování objektů BLOB.
- [Čtečka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): oprávnění jen pro čtení pro objekty blob.
- [Přispěvatel dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): oprávnění ke čtení, zápisu a odstraňování front.
- [Čtecí modul dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): oprávnění jen pro čtení pro fronty.

Vlastní role mohou podporovat různé kombinace stejných oprávnění poskytovaných integrovanými rolemi. Další informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../../role-based-access-control/custom-roles.md) a [pochopení definic rolí pro prostředky Azure](../../role-based-access-control/role-definitions.md).

Výpis front s rolí správce předplatného s klasickou sadou není podporován. K vypsání front se musí uživatel přiřadit k rolím **čtenář** Azure Resource Manager, roli **čtečky dat fronty úložiště** nebo k roli **Přispěvatel dat fronty úložiště** .

> [!IMPORTANT]
> Verze Preview Průzkumník služby Storage v Azure Portal nepodporuje použití přihlašovacích údajů Azure AD k zobrazení a úpravě dat objektů BLOB nebo Queue. Průzkumník služby Storage v Azure Portal vždy používá klíče účtu pro přístup k datům. Pokud chcete použít Průzkumník služby Storage v Azure Portal, musíte mít přiřazenou roli, která zahrnuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Přechod na objekty blob nebo fronty na portálu

Pokud chcete zobrazit data objektu BLOB nebo fronty na portálu, přejděte k **přehledu** svého účtu úložiště a klikněte na odkazy pro **objekty blob** nebo **fronty**. Případně můžete přejít na části **BLOB Service** a **Služba front** v nabídce. 

![Přechod na data objektů BLOB nebo Queue v Azure Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Určení aktuální metody ověřování

Když přejdete do kontejneru nebo do fronty, Azure Portal určuje, jestli aktuálně používáte přístupový klíč účtu nebo účet Azure AD k ověřování.

Příklady v této části ukazují přístup ke kontejneru a jeho objektům blob, ale portál při přístupu ke frontě a jejím zprávám zobrazuje stejnou zprávu, nebo výpis front.

### <a name="authenticate-with-the-account-access-key"></a>Ověřování pomocí přístupového klíče účtu

Pokud ověřujete pomocí přístupového klíče účtu, zobrazí se na portálu **přístupová klávesa** , která se zadala jako metoda ověřování:

![Aktuálně probíhá přístup k datům kontejneru s klíčem účtu.](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Pokud chcete přepnout na používání účtu Azure AD, klikněte na odkaz zvýrazněný v imagi. Pokud máte příslušná oprávnění prostřednictvím rolí Azure, které jsou vám přiřazeny, budete moct pokračovat. Pokud ale nemáte správná oprávnění, zobrazí se chybová zpráva podobná následující:

![Pokud účet Azure AD nepodporuje přístup, zobrazí se chyba.](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Všimněte si, že v seznamu se nezobrazí žádné objekty blob, pokud váš účet Azure AD nemá oprávnění k jejich zobrazení. Klikněte na odkaz **Přepnout na přístup k klávesám** a znovu použijte přístupovou klávesu pro ověřování.

### <a name="authenticate-with-your-azure-ad-account"></a>Ověřování pomocí účtu Azure AD

Pokud ověřujete pomocí účtu Azure AD, zobrazí se na portálu **uživatelský účet Azure AD** , který jste zadali jako metodu ověřování:

![Aktuálně probíhá přístup k datům kontejneru s účtem služby Azure AD.](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Chcete-li přepnout na používání přístupového klíče účtu, klikněte na odkaz zvýrazněný v obrázku. Pokud máte přístup k klíči účtu, budete moct pokračovat. Pokud ale nemáte přístup k klíči účtu, zobrazí se chybová zpráva podobná následující:

![Pokud nemáte přístup k klíči účtu, zobrazí se chyba.](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Všimněte si, že v seznamu se nezobrazí žádné objekty blob, pokud nemáte přístup k klíčům účtu. Klikněte na odkaz **Přepnout na uživatelský účet Azure AD** , abyste mohli znovu použít účet Azure AD pro ověřování.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Určete, jak autorizovat operaci nahrávání objektu BLOB.

Při nahrávání objektu BLOB z Azure Portal můžete určit, jestli se má tato operace ověřovat a autorizovat pomocí přístupového klíče účtu nebo pomocí přihlašovacích údajů Azure AD. Ve výchozím nastavení používá portál aktuální metodu ověřování, jak je znázorněno v [části určení aktuální metody ověřování](#determine-the-current-authentication-method).

Chcete-li určit, jak autorizovat operaci nahrání objektu blob, postupujte podle těchto kroků:

1. V Azure Portal přejděte do kontejneru, do kterého chcete nahrát objekt BLOB.
1. Vyberte tlačítko **Nahrát**.
1. Rozbalením části **Upřesnit** zobrazíte upřesňující vlastnosti objektu BLOB.
1. V poli **typ ověřování** určete, jestli chcete autorizovat operaci nahrávání pomocí účtu Azure AD, nebo pomocí přístupového klíče účtu, jak je znázorněno na následujícím obrázku:

    :::image type="content" source="media/storage-access-blobs-queues-portal/auth-blob-upload.png" alt-text="Snímek obrazovky ukazující, jak změnit metodu autorizace při nahrávání objektu BLOB":::

## <a name="next-steps"></a>Další kroky

- [Ověřování přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md)
- [V Azure Portal udělte přístup k kontejnerům a frontám Azure pomocí RBAC.](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí PowerShellu](storage-auth-aad-rbac-powershell.md)
