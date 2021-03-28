---
title: Šifrování služby Azure Storage pro neaktivní uložená data
description: Azure Storage chrání vaše data tím, že je před trvalým nasazením do cloudu automaticky šifruje. Pro šifrování dat ve vašem účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641116"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data

Azure Storage používá šifrování na straně serveru (SSE) k automatickému šifrování vašich dat při trvalém ukládání do cloudu. Azure Storage šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

## <a name="about-azure-storage-encryption"></a>O šifrování Azure Storage

Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Azure Storage šifrování je povolené pro všechny účty úložiště, včetně účtů úložiště Správce prostředků a Classic. Šifrování Azure Storage nelze zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování.

Data v účtu úložiště jsou šifrovaná bez ohledu na úroveň výkonu (Standard nebo Premium), úroveň přístupu (horkou nebo studenou) nebo model nasazení (Azure Resource Manager nebo klasický). Všechny objekty BLOB v archivní úrovni jsou také šifrované. Všechny možnosti redundance Azure Storage podporují šifrování a všechna data v primární i sekundární oblasti jsou zašifrovaná, pokud je geografická replikace povolená. Všechny prostředky Azure Storage jsou zašifrované, včetně objektů blob, disků, souborů, front a tabulek. Všechna metadata objektů jsou také šifrována. Azure Storage šifrování se neúčtují žádné další náklady.

Všechny objekty blob bloku, doplňovací objekty blob nebo objekty blob stránky, které byly zapsány do Azure Storage po 20. října 2017, jsou zašifrovány. Objekty blob vytvořené před tímto datem budou i nadále zašifrovány procesem na pozadí. Pokud chcete vynutit šifrování objektu blob, který se vytvořil před 20. října 2017, můžete objekt BLOB přepsat. Informace o tom, jak zjistit stav šifrování objektu blob, najdete v tématu [ověření stavu šifrování objektu BLOB](../blobs/storage-blob-encryption-status.md).

Další informace o kryptografických modulech podkladových Azure Storage šifrování najdete v tématu [kryptografické rozhraní API: další generace](/windows/desktop/seccng/cng-portal).

Informace o šifrování a správě klíčů pro Azure Managed disks najdete v tématu [šifrování na straně serveru služby Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Data v novém účtu úložiště se ve výchozím nastavení šifrují pomocí klíčů spravovaných Microsoftem. Můžete i nadále spoléhat na klíče spravované Microsoftem pro šifrování vašich dat, nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, máte dvě možnosti. Můžete použít buď typ správy klíčů, nebo obojí:

- Můžete zadat *klíč spravovaný zákazníkem* , který se použije pro šifrování a dešifrování dat v úložišti objektů BLOB a ve službě soubory Azure. <sup>1, 2</sup> spravované klíče pro zákazníky musí být uložené ve Azure Key Vault nebo Azure Key Vault spravovaném modelu hardwarového zabezpečení (HSM) (Preview). Další informace o klíčích spravovaných zákazníkem najdete v tématu [použití klíčů spravovaných zákazníkem pro Azure Storage šifrování](./customer-managed-keys-overview.md).
- Můžete zadat *klíč poskytnutý zákazníkem* pro operace BLOB Storage. Klient, který vytváří požadavek na čtení nebo zápis proti úložišti objektů blob, může do žádosti přidat šifrovací klíč a získat tak podrobné řízení způsobu, jakým se data objektů BLOB šifrují a dešifrují. Další informace o klíčích poskytovaných zákazníkem najdete v tématu [poskytnutí šifrovacího klíče pro požadavek na úložiště objektů BLOB](../blobs/encryption-customer-provided-keys.md).

Následující tabulka porovnává možnosti správy klíčů pro Azure Storage šifrování.

| Parametr správy klíčů | Klíče spravované Microsoftem | Klíče spravované zákazníkem | Klíče poskytované zákazníky |
|--|--|--|--|
| Operace šifrování a dešifrování | Azure | Azure | Azure |
| Podporované služby Azure Storage Services | Vše | BLOB Storage, soubory Azure<sup>1, 2</sup> | Blob Storage |
| Úložiště klíčů | Úložiště klíčů Microsoftu | Azure Key Vault nebo Key Vault HSM | Vlastní úložiště klíčů zákazníka |
| Zodpovědnost za střídání klíčů | Microsoft | Zákazník | Zákazník |
| Řízení klíčů | Microsoft | Zákazník | Zákazník |

<sup>1</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s úložištěm Queue, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s tabulkovým úložištěm, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Klíče spravované Microsoftem se podle požadavků na dodržování předpisů otočí odpovídajícím způsobem. Pokud máte specifické požadavky na střídání klíčů, společnost Microsoft doporučuje přejít na klíče spravované zákazníkem, abyste mohli snadno spravovat a auditovat své otáčení.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Dvojité šifrování dat pomocí šifrování infrastruktury

Zákazníci, kteří vyžadují vysokou úroveň záruky, že jejich data jsou zabezpečená, můžou na úrovni infrastruktury Azure Storage povolit taky 256 šifrování AES. Když je povolené šifrování infrastruktury, data v účtu úložiště se &mdash; na úrovni služby šifrují dvakrát a jednou na úrovni infrastruktury &mdash; se dvěma různými šifrovacími algoritmy a dvěma různými klíči. Dvojité šifrování Azure Storage dat chrání před scénářem, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V tomto scénáři bude další vrstva šifrování nadále chránit vaše data.

Šifrování na úrovni služby podporuje použití klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem s Azure Key Vault. Šifrování na úrovni infrastruktury spoléhá na klíče spravované Microsoftem a vždycky používá samostatný klíč.

Další informace o tom, jak vytvořit účet úložiště, který umožňuje šifrování infrastruktury, najdete v tématu [Vytvoření účtu úložiště s povoleným šifrováním infrastruktury pro dvojité šifrování dat](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](customer-managed-keys-overview.md)
- [Obory šifrování pro úložiště objektů BLOB](../blobs/encryption-scope-overview.md)
- [Zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB](../blobs/encryption-customer-provided-keys.md)
