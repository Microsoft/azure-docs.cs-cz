---
title: Obory šifrování pro úložiště objektů BLOB
description: Obory šifrování poskytují možnost správy šifrování na úrovni kontejneru nebo jednotlivého objektu BLOB. Obory šifrování můžete použít k vytvoření zabezpečených hranic mezi daty, která se nacházejí ve stejném účtu úložiště, ale patří různým zákazníkům.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640453"
---
# <a name="encryption-scopes-for-blob-storage"></a>Obory šifrování pro úložiště objektů BLOB

Rozsahy šifrování umožňují spravovat šifrování pomocí klíče, který je vymezen na kontejner nebo na jednotlivé objekty blob. Obory šifrování můžete použít k vytvoření zabezpečených hranic mezi daty, která se nacházejí ve stejném účtu úložiště, ale patří různým zákazníkům.

Další informace o práci s obory šifrování najdete v tématu [Vytvoření a Správa oborů šifrování](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Jak fungují rozsahy šifrování

Ve výchozím nastavení je účet úložiště zašifrovaný pomocí klíče, který je vymezený na celý účet úložiště. Při definování oboru šifrování zadáte klíč, který může být vymezen na kontejner nebo na jednotlivý objekt BLOB. Při použití oboru šifrování u objektu BLOB je objekt BLOB zašifrovaný pomocí tohoto klíče. Pokud je rozsah šifrování použit pro kontejner, slouží jako výchozí obor pro objekty BLOB v tomto kontejneru, takže všechny objekty blob, které jsou odeslány do tohoto kontejneru, mohou být zašifrovány stejným klíčem. Kontejner může být nakonfigurován tak, aby vynutil výchozí rozsah šifrování pro všechny objekty BLOB v kontejneru, nebo aby bylo možné do kontejneru odeslat samostatný objekt BLOB s jiným oborem šifrování než s výchozím nastavením.

Operace čtení u objektu blob, který byl vytvořen s rozsahem šifrování, jsou transparentní, pokud rozsah šifrování není zakázán.

### <a name="key-management"></a>Správa klíčů

Při definování oboru šifrování můžete určit, jestli je obor chráněný pomocí klíče spravovaného společností Microsoft, nebo pomocí klíče spravovaného zákazníkem, který je uložený v Azure Key Vault. Různé obory šifrování ve stejném účtu úložiště můžou používat klíče spravované Microsoftem nebo zákazníky. Můžete také přepnout typ klíče, který se používá k ochraně oboru šifrování z klíče spravovaného zákazníkem na klíč spravovaný společností Microsoft, nebo naopak. Další informace o klíčích spravovaných zákazníkem najdete v tématu [klíče spravované zákazníkem pro Azure Storage šifrování](../common/customer-managed-keys-overview.md). Další informace o klíčích spravovaných Microsoftem najdete v tématu [o správě šifrovacích klíčů](../common/storage-service-encryption.md#about-encryption-key-management).

Pokud definujete obor šifrování s klíčem spravovaným zákazníkem, můžete si zvolit, jestli chcete aktualizovat verzi klíče buď automaticky, nebo ručně. Pokud se rozhodnete tuto verzi klíče automaticky aktualizovat, Azure Storage zkontroluje Trezor klíčů nebo spravovaný modul HSM denně pro novou verzi klíče spravovaného zákazníkem a automaticky aktualizuje klíč na nejnovější verzi. Další informace o aktualizaci verze klíče pro klíč spravovaný zákazníkem najdete v tématu [aktualizace verze klíče](../common/customer-managed-keys-overview.md#update-the-key-version).

Účet úložiště může mít až 10 000 rozsahů šifrování chráněných pomocí klíčů spravovaných zákazníkem, pro které se automaticky aktualizuje klíčová verze. Pokud váš účet úložiště už má 10 000 obory šifrování, které jsou chráněné pomocí automatických aktualizací klíčů spravovaných zákazníkem, je nutné, aby se verze klíče aktualizovala ručně pro všechny další šifrovací obory, které jsou chráněné pomocí klíčů spravovaných zákazníkem.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Obory šifrování pro kontejnery a objekty blob

Při vytváření kontejneru můžete zadat výchozí rozsah šifrování pro objekty blob, které se následně nahrály do tohoto kontejneru. Když pro kontejner zadáte výchozí rozsah šifrování, můžete určit, jak se vynutil výchozí rozsah šifrování:

- Můžete vyžadovat, aby všechny objekty blob nahrané do kontejneru používaly výchozí obor šifrování. V takovém případě je každý objekt BLOB v kontejneru zašifrovaný se stejným klíčem.
- Můžete povolit, aby klient pro kontejner přepíše výchozí rozsah šifrování, aby mohl být nahrán objekt BLOB s jiným oborem šifrování než s výchozím rozsahem. V takovém případě mohou být objekty BLOB v kontejneru šifrovány pomocí různých klíčů.

Následující tabulka shrnuje chování operace nahrávání objektů BLOB v závislosti na tom, jak je výchozí obor šifrování nakonfigurovaný pro kontejner:

| Rozsah šifrování definovaný na kontejneru je... | Nahrává se objekt BLOB s výchozím rozsahem šifrování... | Nahrává se objekt BLOB s jiným oborem šifrování než s výchozím oborem... |
|--|--|--|
| Výchozí rozsah šifrování s povolenými přepsáními | Úspěšné | Úspěšné |
| Výchozí rozsah šifrování s potlačenými přepsáními je zakázaný. | Úspěšné | Spuštění |

Pro kontejner v okamžiku vytvoření kontejneru musí být zadán výchozí rozsah šifrování.

Pokud pro kontejner není zadaný žádný výchozí obor šifrování, můžete nahrát objekt BLOB pomocí jakéhokoli oboru šifrování, který jste definovali pro účet úložiště. V době, kdy se objekt BLOB nahraje, se musí zadat rozsah šifrování.

## <a name="disabling-an-encryption-scope"></a>Zakázání oboru šifrování

Když zakážete rozsah šifrování, všechny následné operace čtení nebo zápisu provedené s oborem šifrování selžou s kódem chyby HTTP 403 (zakázáno). Pokud znovu povolíte rozsah šifrování, operace čtení a zápisu budou normálně pokračovat.

Pokud je zakázaný obor šifrování, už se vám neúčtují. Zakažte všechny obory šifrování, které nepotřebujete, aby nedocházelo k zbytečným poplatkům.

Pokud je váš obor šifrování chráněn pomocí klíče spravovaného zákazníkem a odstraníte klíč v trezoru klíčů, data nebudou přístupná. Nezapomeňte taky zakázat rozsah šifrování, abyste se vyhnuli tomu, že se vám bude účtovat.

Mějte na paměti, že klíče spravované zákazníkem jsou chráněny pomocí ochrany proti odstranění a vyprázdnění v trezoru klíčů a odstraněný klíč podléhá chování definovanému pro tyto vlastnosti. Další informace najdete v následujících tématech v dokumentaci k Azure Key Vault:

- [Použití obnovitelného odstranění s využitím PowerShellu](../../key-vault/general/key-vault-recovery.md)
- [Jak používat obnovitelné odstranění pomocí rozhraní příkazového řádku](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Rozsah šifrování není možné odstranit.

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](../common/storage-service-encryption.md)
- [Vytváření a Správa oborů šifrování](encryption-scope-manage.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](../common/customer-managed-keys-overview.md)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)