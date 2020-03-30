---
title: Šifrování Azure Storage pro data v klidovém stavu
description: Azure Storage chrání vaše data tím, že je automaticky šifruje, než je uchovává do cloudu. Můžete se spolehnout na klíče spravované společností Microsoft pro šifrování dat ve vašem účtu úložiště nebo můžete spravovat šifrování pomocí vlastních klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409826"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Šifrování Azure Storage pro data v klidovém stavu

Azure Storage automaticky šifruje vaše data, když je trvalé do cloudu. Šifrování Azure Storage chrání vaše data a pomáhá vám splnit závazky organizace v oblasti zabezpečení a dodržování předpisů.

## <a name="about-azure-storage-encryption"></a>Šifrování azure storage

Data ve Službě Azure Storage se šifrují a dešifrují transparentně pomocí 256bitového [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejsilnějších blokových šifer, která je k dispozici, a je kompatibilní s FIPS 140-2. Šifrování azure storage je podobné šifrování nástroje BitLocker v systému Windows.

Šifrování azure storage je povolené pro všechny účty úložiště, včetně Správce prostředků i klasické účty úložiště. Šifrování azure storage nelze zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód nebo aplikace, abyste využili šifrování Azure Storage.

Data v účtu úložiště se šifrují bez ohledu na úroveň výkonu (standardní nebo prémiovou), úroveň přístupu (horká nebo studená) nebo model nasazení (Azure Resource Manager nebo klasický). Všechny objekty BLOB v archivní vrstvě jsou také šifrované. Všechny možnosti redundance úložiště Azure podporují šifrování a všechna data v primární i sekundární oblasti jsou šifrována, když je povolena geografická replikace. Všechny prostředky Azure Storage jsou šifrované, včetně objektů BLOB, disků, souborů, front a tabulek. Všechna metadata objektu jsou také šifrována. Šifrování azure storage se neplatí.

Každý objekt blob bloku, objekt blob připojení nebo objekt blob stránky, který byl zapsán do služby Azure Storage po 20 října 2017 je šifrovaný. Objekty BLOB vytvořené před tímto datem jsou nadále šifrovány procesem na pozadí. Chcete-li vynutit šifrování objektu blob, který byl vytvořen před 20 října 2017, můžete přepsání objektu blob. Informace o kontrole stavu šifrování objektu blob najdete v [tématu Kontrola stavu šifrování objektu blob](../blobs/storage-blob-encryption-status.md).

Další informace o kryptografických modulech, které jsou základem šifrování Azure Storage, najdete v [tématu Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení jsou data v účtu úložiště šifrována pomocí klíčů spravovaných společností Microsoft. Můžete se spolehnout na klíče spravované společností Microsoft pro šifrování vašich dat, nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, máte dvě možnosti:

- Pomocí služby Azure Key Vault můžete určit *klíč spravovaný zákazníkem,* který se použije k šifrování a dešifrování dat v úložišti objektů Blob a v souborech Azure. <sup>1,2</sup> Další informace o klíčích spravovaných zákazníky najdete [v tématu Použití klíčů spravovaných zákazníkem s Azure Key Vault ke správě šifrování Azure Storage](encryption-customer-managed-keys.md).
- Můžete zadat *klíč zadaný zákazníkem* pro operace úložiště objektů Blob. Klient, který provádí požadavek na čtení nebo zápis proti úložišti objektů Blob, může v žádosti o podrobnou kontrolu nad šifrováním a dešifrováním dat objektů blob obsahovat šifrovaný a dešifrovaný. Další informace o klíčích poskytovaných zákazníkem najdete [v tématu Poskytnutí šifrovacího klíče v žádosti o úložiště objektů Blob (preview).](encryption-customer-provided-keys.md)

Následující tabulka porovnává možnosti správy klíčů pro šifrování Azure Storage.

|                                        |    Klíče spravované společností Microsoft                             |    Klíče spravované zákazníkem                                                                                                                        |    Klíče poskytnuté zákazníkem                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operace šifrování/dešifrování    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Podporované služby Azure Storage    |    Všechny                                                |    Úložiště objektů blob, soubory Azure<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Úložiště klíčů                         |    Úložiště klíčů Microsoftu    |    Azure Key Vault                                                                                                                              |    Azure Key Vault nebo jiné úložiště klíčů                                                                 |
|    Odpovědnost za střídání klíčů         |    Microsoft                                          |    Zákazník                                                                                                                                     |    Zákazník                                                                      |
|    Použití klíče                           |    Microsoft                                          |    Portál Azure, rozhraní REST API zprostředkovatele úložišť, knihovny pro správu úložiště Azure, PowerShell, rozhraní příkazového uživatelského rozhraní        |    Azure Storage REST API (úložiště objektů blob), klientské knihovny Azure Storage    |
|    Přístup ke klíči                          |    Pouze microsoft                                     |    Microsoft, zákazník                                                                                                                    |    Pouze zákazník                                                                 |

<sup>1</sup> Informace o vytvoření účtu, který podporuje používání klíčů spravovaných zákazníkem s úložištěm fronty, naleznete [v tématu Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Informace o vytvoření účtu, který podporuje používání klíčů spravovaných zákazníkem s úložištěm tabulek, naleznete [v tématu Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v Azure CLI](storage-encryption-keys-cli.md)
