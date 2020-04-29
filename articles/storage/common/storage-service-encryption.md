---
title: Azure Storage šifrování dat v klidovém umístění
description: Azure Storage chrání vaše data tím, že je před trvalým nasazením do cloudu automaticky šifruje. Pro šifrování dat ve vašem účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457939"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage šifrování dat v klidovém umístění

Azure Storage automaticky šifruje vaše data při jejich trvalém uložení do cloudu. Azure Storage šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

## <a name="about-azure-storage-encryption"></a>O šifrování Azure Storage

Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Azure Storage šifrování je povolené pro všechny účty úložiště, včetně účtů úložiště Správce prostředků a Classic. Šifrování Azure Storage nelze zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování.

Data v účtu úložiště jsou šifrovaná bez ohledu na úroveň výkonu (Standard nebo Premium), úroveň přístupu (horkou nebo studenou) nebo model nasazení (Azure Resource Manager nebo klasický). Všechny objekty BLOB v archivní úrovni jsou také šifrované. Všechny možnosti redundance Azure Storage podporují šifrování a všechna data v primární i sekundární oblasti jsou zašifrovaná, pokud je geografická replikace povolená. Všechny prostředky Azure Storage jsou zašifrované, včetně objektů blob, disků, souborů, front a tabulek. Všechna metadata objektů jsou také šifrována. Azure Storage šifrování se neúčtují žádné další náklady.

Všechny objekty blob bloku, doplňovací objekty blob nebo objekty blob stránky, které byly zapsány do Azure Storage po 20. října 2017, jsou zašifrovány. Objekty blob vytvořené před tímto datem budou i nadále zašifrovány procesem na pozadí. Pokud chcete vynutit šifrování objektu blob, který se vytvořil před 20. října 2017, můžete objekt BLOB přepsat. Informace o tom, jak zjistit stav šifrování objektu blob, najdete v tématu [ověření stavu šifrování objektu BLOB](../blobs/storage-blob-encryption-status.md).

Další informace o kryptografických modulech podkladových Azure Storage šifrování najdete v tématu [kryptografické rozhraní API: další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Data v novém účtu úložiště se šifrují pomocí klíčů spravovaných Microsoftem. Pro šifrování vašich dat můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, máte dvě možnosti:

- Můžete zadat *klíč spravovaný zákazníkem* Azure Key Vault, který se má použít pro šifrování a dešifrování dat v úložišti objektů BLOB a ve službě soubory Azure. <sup>1.2</sup> . Další informace o klíčích spravovaných zákazníkem najdete v tématu [použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování Azure Storage](encryption-customer-managed-keys.md).
- Můžete zadat *klíč poskytnutý zákazníkem* pro operace BLOB Storage. Klient, který vytváří požadavek na čtení nebo zápis proti úložišti objektů blob, může do žádosti přidat šifrovací klíč a získat tak podrobné řízení způsobu, jakým se data objektů BLOB šifrují a dešifrují. Další informace o klíčích poskytovaných zákazníkem najdete v tématu [zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB (Preview)](encryption-customer-provided-keys.md).

Následující tabulka porovnává možnosti správy klíčů pro Azure Storage šifrování.

|                                        |    Klíče spravované společností Microsoft                             |    Klíče spravované zákazníkem                                                                                                                        |    Klíče poskytované zákazníky                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operace šifrování a dešifrování    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Podporované služby Azure Storage Services    |    Všechny                                                |    BLOB Storage, soubory Azure<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Úložiště klíčů                         |    Úložiště klíčů Microsoftu    |    Azure Key Vault                                                                                                                              |    Vlastní úložiště klíčů zákazníka                                                                 |
|    Zodpovědnost za střídání klíčů         |    Microsoft                                          |    Zákazník                                                                                                                                     |    Zákazník                                                                      |
|    Řízení klíčů                          |    Microsoft                                     |    Zákazník                                                                                                                    |    Zákazník                                                                 |

<sup>1</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s úložištěm Queue, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s tabulkovým úložištěm, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v Azure CLI](storage-encryption-keys-cli.md)
