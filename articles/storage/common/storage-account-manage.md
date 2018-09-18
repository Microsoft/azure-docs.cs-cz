---
title: Spravovat nastavení účtu úložiště na webu Azure Portal – Azure Storage | Dokumentace Microsoftu
description: Další informace o správě nastavení účtu úložiště na webu Azure Portal, včetně konfigurace nastavení řízení přístupu, znovu se generuje přístupové klíče účtu, změna úrovně přístupu nebo upravit typ replikace používá tento účet. Také informace o odstranění účtu úložiště na portálu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 5237be9fd75edc8abf5c5fa043574e8c2deb79e9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740709"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Spravovat nastavení účtu úložiště na webu Azure Portal

Jsou k dispozici v různých nastavení pro váš účet úložiště [webu Azure portal](https://portal.azure.com). Tento článek popisuje některé z těchto nastavení a způsob jejich použití.

## <a name="access-control"></a>Řízení přístupu

Azure Storage podporuje ověřování pomocí Azure Active Directory pro úložiště objektů Blob a Queue storage (preview) prostřednictvím řízení přístupu na základě role (RBAC). Další informace o ověřování pomocí Azure AD najdete v tématu [ověřit přístup k Azure, objekty BLOB a fronty pomocí Azure Active Directory (Preview)](storage-auth-aad.md).

**Řízení přístupu** nastavení na portálu Azure portal nabízí jednoduchý způsob, jak přiřadit role RBAC uživatelům, skupinám, instančních objektů a spravovaných identit. Další informace o přiřazení role RBAC najdete v tématu [Správa přístupových práv k datům objektu blob a fronty pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

> [!NOTE]
> Ověřování uživatelů a aplikací s použitím přihlašovacích údajů Azure AD poskytuje nejvyšší zabezpečení a snadné použití přes jiným způsobem autorizace. Když můžete nadále používat povolení sdíleného klíče s vašimi aplikacemi, používání služby Azure AD obchází potřebou ukládání přístupový klíč k účtu s vaším kódem. Můžete taky dál používat sdílené přístupové podpisy (SAS) k udělení velice přesně kontrolovat přístup k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo si dělat starosti o odvolání SAS ohrožení zabezpečení. 

## <a name="access-keys"></a>Přístupové klíče

Při vytváření účtu úložiště vygeneruje Azure dva přístupové klíče účtu úložiště 512 bitů. Tyto klíče můžete použít k autorizaci přístupu k účtu úložiště pomocí sdíleného klíče. Můžete otočit a znovu vygenerovat klíče bez přerušení pro vaše aplikace a společnost Microsoft doporučuje, abyste pravidelně.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>Zobrazení a zkopírování přístupových klíčů

Zobrazení přihlašovacích údajů účtu úložiště:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Klíč** a kliknutím na tlačítko **Kopírovat** zkopírujte klíč účtu.
5. Alternativně můžete zkopírovat celý připojovací řetězec. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec.

    ![Snímek obrazovky ukazující, jak zobrazit přístupové klíče na webu Azure Portal](media/storage-manage-account/portal-connection-string.png)

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
- [vytvořit účet úložiště](storage-quickstart-create-account.md)
