---
title: Správa přístupových klíčů pro účet
titleSuffix: Azure Storage
description: Naučte se zobrazovat, spravovat a střídat přístupové klíče k účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 68d0ef14a7b5967d6a4535da85d32128c3a12a19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486726"
---
# <a name="manage-storage-account-access-keys"></a>Správa přístupových klíčů účtu úložiště

Když vytvoříte účet úložiště, Azure vygeneruje 2 512 přístupové klíče účtu úložiště. Tyto klíče se dají použít k autorizaci přístupu k datům v účtu úložiště prostřednictvím autorizace pomocí sdíleného klíče.

Microsoft doporučuje použít Azure Key Vault ke správě přístupových klíčů a k pravidelnému střídání a opětovnému vygenerování klíčů. Použití Azure Key Vault usnadňuje střídání klíčů bez přerušení v aplikacích. Klíče můžete také ručně otáčet.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Zobrazit přístupové klíče a připojovací řetězec

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Správa přístupových klíčů pomocí Azure Key Vault

Microsoft doporučuje, abyste ke správě a střídání přístupových klíčů používali Azure Key Vault. Vaše aplikace může bezpečně přistupovat k vašim klíčům v Key Vault, abyste se mohli vyhnout jejich ukládání do kódu aplikace. Další informace o použití Key Vault ke správě klíčů najdete v následujících článcích:

- [Správa klíčů účtu úložiště pomocí Azure Key Vault a PowerShellu](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ruční otočení přístupových klíčů

Společnost Microsoft doporučuje, abyste své přístupové klíče pravidelně přeměnili, aby se zajistilo zabezpečení účtu úložiště. Pokud je to možné, použijte Azure Key Vault ke správě přístupových klíčů. Pokud Key Vault nepoužíváte, budete muset klíče otočit ručně.

Přiřadí se dva přístupové klíče, abyste mohli klíče otáčet. Máte-li dvě klíče, zajistíte tím, že vaše aplikace bude udržovat v průběhu procesu přístup k Azure Storage.

> [!WARNING]
> Opětovné generování přístupových klíčů může mít vliv na jakékoli aplikace nebo služby Azure, které jsou závislé na klíči účtu úložiště. Všichni klienti, kteří používají klíč účtu pro přístup k účtu úložiště, se musí aktualizovat tak, aby používali nový klíč, včetně Media Services, cloudu, desktopových a mobilních aplikací a aplikací grafického uživatelského rozhraní pro Azure Storage, jako je například [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

Použijte tento postup k otočení klíčů účtu úložiště:

1. Aktualizujte připojovací řetězce v kódu aplikace pro použití sekundárního klíče.
2. Znovu vygenerujte primární přístupový klíč pro účet úložiště. V okně **přístupové klíče** v Azure Portal klikněte na **znovu vygenerovat klíč1**a pak kliknutím na **Ano** potvrďte, že chcete vygenerovat nový klíč.
3. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
4. Stejným způsobem pak opětovně vygenerujte sekundární přístupový klíč.

> [!NOTE]
> Microsoft doporučuje používat ve všech aplikacích současně jenom jeden z klíčů. Pokud na některých místech a v dalších klíčích 2 použijete klíč 1, nebudete moct tyto klíče otočit, aniž by aplikace ztratila přístup.

## <a name="next-steps"></a>Další kroky

- [Přehled účtu Azure Storage](storage-account-overview.md)
- [vytvořit účet úložiště](storage-quickstart-create-account.md)
