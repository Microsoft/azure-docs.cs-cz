---
title: Spravovat nastavení účtu úložiště na webu Azure Portal – Azure Storage | Dokumentace Microsoftu
description: Další informace o správě nastavení účtu úložiště na webu Azure Portal, včetně konfigurace nastavení řízení přístupu, znovu se generuje přístupové klíče účtu, změna úrovně přístupu nebo upravit typ replikace používá tento účet. Také informace o odstranění účtu úložiště na portálu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 66bdc4bd1e17347419a6eccd7c9532db17b33001
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303482"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Spravovat nastavení účtu úložiště na webu Azure Portal

Jsou k dispozici v různých nastavení pro váš účet úložiště [webu Azure portal](https://portal.azure.com). Tento článek popisuje některé z těchto nastavení a způsob jejich použití.

## <a name="access-control"></a>Řízení přístupu

Azure Storage podporuje ověřování pomocí Azure Active Directory pro úložiště objektů Blob a Queue storage prostřednictvím řízení přístupu na základě role (RBAC). Další informace o autorizaci pomocí Azure AD najdete v tématu [autorizovat přístup k Azure, objekty BLOB a fronty pomocí Azure Active Directory](storage-auth-aad.md).

**Řízení přístupu** nastavení na portálu Azure portal nabízí jednoduchý způsob, jak přiřadit role RBAC uživatelům, skupinám, instančních objektů a spravovaných identit. Další informace o přiřazení role RBAC najdete v tématu [Správa přístupových práv k datům objektu blob a fronty pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tags

Azure Storage podporuje značky Azure Resource Manageru pro uspořádání vašich prostředků Azure s vlastní taxonomii. Značky můžete použít k účtům úložiště tak, že je můžete seskupit v rámci vašeho předplatného logickým způsobem.

Pro účty úložiště název značky je omezen na 128 znaků a hodnota značky je omezena na 256 znaků.

Další informace najdete v tématu [používání značek k uspořádání prostředků Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Přístupové klíče

Při vytváření účtu úložiště vygeneruje Azure dva přístupové klíče účtu úložiště 512 bitů. Tyto klíče můžete použít k autorizaci přístupu k účtu úložiště pomocí sdíleného klíče. Můžete otočit a znovu vygenerovat klíče bez přerušení pro vaše aplikace a společnost Microsoft doporučuje, abyste pravidelně.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Zobrazit připojovací řetězec a klíče účtu

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Opětovné vygenerování přístupových klíčů

Společnost Microsoft doporučuje znovu vygenerovat přístupové klíče ke svému pravidelně umožňující bezpečnost vašeho účtu úložiště. Dva přístupové klíče se přiřazují tak, aby otočíte klíče. Při otočení klíčů, zajistíte tím, že vaše aplikace udržuje přístup ke službě Azure Storage v celém procesu. 

> [!WARNING]
> Opětovné generování přístupových klíčů může ovlivnit jakékoli aplikace nebo služby Azure, které jsou závislé na klíč účtu úložiště. Žádné klienty, kteří používají klíč účtu pro přístup k účtu úložiště se musí aktualizovat na používání nového klíče, včetně služby media services, cloud, desktop a mobilní aplikace a grafické uživatelské rozhraní aplikace pro službu Azure Storage, jako například [Azure Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/).

Díky tomuhle procesu obměna klíčů účtu úložiště:

1. Aktualizujte připojovací řetězce v kódu aplikace používat sekundární klíč.
2. Znovu vygenerujte primární přístupový klíč pro účet úložiště. Na **přístupové klíče** okna na webu Azure Portal, klikněte na tlačítko **znovu vygenerovat klíč1**a potom klikněte na tlačítko **Ano** potvrďte, že chcete vygenerovat nový klíč.
3. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
4. Stejným způsobem pak opětovně vygenerujte sekundární přístupový klíč.

## <a name="account-configuration"></a>Konfigurace účtu

Po vytvoření účtu úložiště, můžete upravit jeho konfiguraci. Například můžete změnit způsob, jakým se replikuje vaše data nebo změna úrovně přístupu u účtu z vrstvy Hot na Cool. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště, pak vyhledejte a klikněte na tlačítko **konfigurace** pod **nastavení** můžete zobrazit nebo změnit konfiguraci účtu.

Změna konfigurace účtu úložiště může mít za následek náklady. Další podrobnosti najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) stránky.

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Pokud chcete odebrat účet úložiště, který už nepoužíváte, přejděte na účet úložiště na portálu [Azure Portal](https://portal.azure.com) a klikněte na **Odstranit**. Odstraněním účtu úložiště se odstraní celý účet, včetně všech dat v účtu.

> [!WARNING]
> Odstraněný účet úložiště není možné obnovit ani není možné načíst žádný obsah, který byl součástí účtu před jeho odstraněním. Nezapomeňte si před odstraněním účtu zazálohovat všechno, co chcete uložit. To platí také pro všechny prostředky v rámci účtu – po odstranění jsou objekt blob, tabulka, fronta nebo soubor odstraněny trvale.
> 

Pokud se pokusíte odstranit účet úložiště, který je přidružený virtuálnímu počítači Azure, můžete obdržet chybu, že se účet úložiště stále používá. Pomoc při odstraňování potíží s touto chybou najdete v tématu popisujícím [řešení chyb při odstraňování účtů úložiště](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Další postup

- [Přehled účtu Azure storage](storage-account-overview.md)
- [Vytvoření účtu úložiště](storage-quickstart-create-account.md)
