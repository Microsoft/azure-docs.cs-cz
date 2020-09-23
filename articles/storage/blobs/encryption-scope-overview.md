---
title: Obory šifrování pro úložiště objektů BLOB (Preview)
description: Obory šifrování poskytují možnost správy šifrování na úrovni kontejneru nebo jednotlivého objektu BLOB. Obory šifrování můžete použít k vytvoření zabezpečených hranic mezi daty, která se nacházejí ve stejném účtu úložiště, ale patří různým zákazníkům.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 041b0bf57f57fd8ddd74c8330888d75e31aacc83
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995135"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Obory šifrování pro úložiště objektů BLOB (Preview)

Obory šifrování poskytují možnost správy šifrování na úrovni kontejneru nebo jednotlivého objektu BLOB. Obory šifrování můžete použít k vytvoření zabezpečených hranic mezi daty, která se nacházejí ve stejném účtu úložiště, ale patří různým zákazníkům.

Ve výchozím nastavení je účet úložiště zašifrovaný pomocí klíče, který je vymezený na celý účet úložiště. S rozsahem šifrování můžete určit, že jeden nebo více kontejnerů je šifrovaných klíčem, který je vymezen pouze pro tyto kontejnery.

Pro ochranu a řízení přístupu ke klíči, který šifruje vaše data, se můžete rozhodnout pro použití klíčů spravovaných společností Microsoft nebo klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault nebo Key Vault spravovaného modelu hardwarového zabezpečení (HSM) (Preview). Různé obory šifrování ve stejném účtu úložiště můžou používat klíče spravované Microsoftem nebo zákazníky.

Po vytvoření oboru šifrování můžete zadat obor šifrování pro požadavek na vytvoření kontejneru nebo objektu BLOB. Další informace o tom, jak vytvořit rozsah šifrování, najdete v tématu [Vytvoření a Správa oborů šifrování (Preview)](encryption-scope-manage.md).

> [!NOTE]
> U geograficky redundantního úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantního úložiště s přístupem pro čtení (RA-GZRS) ve verzi Preview se obory šifrování nepodporují.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> Obory šifrování ve verzi Preview jsou určené jenom pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.
>
> Chcete-li se vyhnout neočekávaným nákladům, je nutné zakázat všechny obory šifrování, které aktuálně nepotřebujete.

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Vytvoření kontejneru nebo objektu BLOB s rozsahem šifrování

Objekty blob, které jsou vytvořeny v oboru šifrování, jsou šifrovány klíčem zadaným pro daný obor. Při vytváření objektu blob můžete určit rozsah šifrování pro jednotlivý objekt blob, nebo můžete při vytváření kontejneru určit výchozí rozsah šifrování. Když je na úrovni kontejneru zadaný výchozí rozsah šifrování, všechny objekty BLOB v tomto kontejneru se šifrují pomocí klíče přidruženého k výchozímu oboru.

Když vytvoříte objekt BLOB v kontejneru, který má výchozí obor šifrování, můžete zadat rozsah šifrování, který přepíše výchozí rozsah šifrování, pokud je kontejner nakonfigurovaný tak, aby povoloval přepsání výchozího oboru šifrování. Pokud chcete zabránit přepsání výchozího oboru šifrování, nakonfigurujte kontejner tak, aby odepřel přepsání pro jednotlivý objekt BLOB.

Operace čtení u objektu blob, který patří do oboru šifrování, jsou transparentní, pokud rozsah šifrování není zakázán.

## <a name="disable-an-encryption-scope"></a>Zakázání oboru šifrování

Když zakážete rozsah šifrování, všechny následné operace čtení nebo zápisu provedené s oborem šifrování selžou s kódem chyby HTTP 403 (zakázáno). Pokud znovu povolíte rozsah šifrování, operace čtení a zápisu budou normálně pokračovat.

Pokud je zakázaný obor šifrování, už se vám neúčtují. Zakažte všechny obory šifrování, které nepotřebujete, aby nedocházelo k zbytečným poplatkům.

Pokud je váš obor šifrování chráněný pomocí klíčů spravovaných zákazníkem, můžete taky odstranit přidružený klíč v trezoru klíčů nebo spravovaném modulu HSM, aby se tento obor šifrování zakázal. Mějte na paměti, že klíče spravované zákazníkem jsou chráněné pomocí ochrany proti odstranění a vyprázdnění, a to v trezoru klíčů nebo spravovaném HSM. odstraněný klíč podléhá chování definovanému těmito vlastnostmi. Další informace najdete v následujících tématech v dokumentaci k Azure Key Vault:

- [Použití obnovitelného odstranění s využitím PowerShellu](../../key-vault/general/soft-delete-powershell.md)
- [Jak používat obnovitelné odstranění pomocí rozhraní příkazového řádku](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Rozsah šifrování není možné odstranit.

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](../common/storage-service-encryption.md)
- [Vytváření a Správa oborů šifrování (Preview)](encryption-scope-manage.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](../common/customer-managed-keys-overview.md)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
