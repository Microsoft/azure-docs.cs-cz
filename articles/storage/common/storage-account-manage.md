---
title: Správa nastavení účtu úložiště v Azure Portal-Azure Storage | Microsoft Docs
description: Naučte se spravovat nastavení účtu úložiště v Azure Portal, včetně konfigurace nastavení řízení přístupu, opětovného generování přístupových klíčů k účtu, změna úrovně přístupu nebo změny typu replikace, kterou účet používá. Také se dozvíte, jak odstranit účet úložiště na portálu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 60104496006e790887dd9c4b3e4c3196e0ef6444
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671359"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Správa nastavení účtu úložiště v Azure Portal

V [Azure Portal](https://portal.azure.com)je k dispozici celá řada nastavení pro váš účet úložiště. Tento článek popisuje některá z těchto nastavení a jejich použití.

## <a name="access-control"></a>Řízení přístupu

Azure Storage podporuje autorizaci pomocí Azure Active Directory pro úložiště objektů BLOB a úložiště front prostřednictvím řízení přístupu na základě role (RBAC). Další informace o autorizaci se službou Azure AD najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md).

Nastavení **řízení přístupu** v Azure Portal nabízí jednoduchý způsob, jak přiřadit role RBAC uživatelům, skupinám, objektům služby a spravovaným identitám. Další informace o přiřazování rolí RBAC najdete v tématu [Správa přístupových práv k datům BLOB a front pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tags

Azure Storage podporuje značky Azure Resource Manager k uspořádání prostředků Azure pomocí přizpůsobené taxonomie. Můžete použít značky pro účty úložiště, abyste je mohli seskupit v rámci svého předplatného logickým způsobem.

U účtů úložiště je název značky omezený na 128 znaků a hodnota značky je omezena na 256 znaků.

Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Přístupové klíče

Když vytvoříte účet úložiště, Azure vygeneruje 2 512 přístupové klíče účtu úložiště. Tyto klíče se dají použít k autorizaci přístupu k účtu úložiště přes sdílený klíč. Klíče můžete otáčet a znovu vygenerovat, aniž byste museli přerušovat vaše aplikace, a Microsoft doporučuje, abyste to učinili pravidelně.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Zobrazit klíče účtu a připojovací řetězec

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Znovu vygenerovat přístupové klíče

Společnost Microsoft doporučuje, abyste znovu vygenerovali přístupové klíče, aby se zajistila bezpečnost účtu úložiště. Přiřadí se dva přístupové klíče, abyste mohli klíče otáčet. Při otočení klíčů se ujistěte, že vaše aplikace udržuje přístup k Azure Storage v průběhu celého procesu. 

> [!WARNING]
> Opětovné generování přístupových klíčů může mít vliv na jakékoli aplikace nebo služby Azure, které jsou závislé na klíči účtu úložiště. Všichni klienti, kteří používají klíč účtu pro přístup k účtu úložiště, se musí aktualizovat tak, aby používali nový klíč, včetně Media Services, cloudu, desktopových a mobilních aplikací a aplikací grafického uživatelského rozhraní pro Azure Storage, jako je například [Průzkumník služby Azure Storage ](https://azure.microsoft.com/features/storage-explorer/).

Použijte tento postup k otočení klíčů účtu úložiště:

1. Aktualizujte připojovací řetězce v kódu aplikace pro použití sekundárního klíče.
2. Znovu vygenerujte primární přístupový klíč pro účet úložiště. V okně **přístupové klíče** v Azure Portal klikněte na **znovu vygenerovat klíč1**a pak kliknutím na **Ano** potvrďte, že chcete vygenerovat nový klíč.
3. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
4. Stejným způsobem pak opětovně vygenerujte sekundární přístupový klíč.

## <a name="account-configuration"></a>Konfigurace účtu

Po vytvoření účtu úložiště můžete změnit jeho konfiguraci. Můžete například změnit způsob replikace dat nebo změnit úroveň přístupu účtu z horké na studenou. V [Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště a pak vyhledejte a klikněte na **Konfigurace** v části **Nastavení** , abyste si mohli zobrazit nebo změnit konfiguraci účtu.

Změna konfigurace účtu úložiště může způsobit zvýšení nákladů. Další podrobnosti najdete na stránce s [cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Pokud chcete odebrat účet úložiště, který už nepoužíváte, přejděte na účet úložiště na portálu [Azure Portal](https://portal.azure.com) a klikněte na **Odstranit**. Odstraněním účtu úložiště se odstraní celý účet, včetně všech dat v účtu.

> [!WARNING]
> Odstraněný účet úložiště není možné obnovit ani není možné načíst žádný obsah, který byl součástí účtu před jeho odstraněním. Nezapomeňte si před odstraněním účtu zazálohovat všechno, co chcete uložit. To platí také pro všechny prostředky v rámci účtu – po odstranění jsou objekt blob, tabulka, fronta nebo soubor odstraněny trvale.
> 

Pokud se pokusíte odstranit účet úložiště, který je přidružený virtuálnímu počítači Azure, můžete obdržet chybu, že se účet úložiště stále používá. Pomoc při odstraňování potíží s touto chybou najdete v tématu popisujícím [řešení chyb při odstraňování účtů úložiště](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Další kroky

- [Přehled účtu Azure Storage](storage-account-overview.md)
- [Vytvoření účtu úložiště](storage-quickstart-create-account.md)
