---
title: Šifrování služby Azure Storage pro neaktivní uložená Data | Dokumentace Microsoftu
description: Šifrování na straně služby Azure Blob storage k ukládání dat pomocí funkce Azure Storage Service Encryption a při získávání data dešifrovat.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: lakasa
ms.openlocfilehash: d469dfb5092f1269a6600ee8ee2f81778fd83b96
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449792"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data

Šifrování služby Azure Storage pro neaktivní uložená Data vám pomůže chránit vaše data pro splnění vaší organizace na zabezpečení a závazky dodržování předpisů. Pomocí této funkce služby Azure Storage automaticky šifruje vaše data před uložením do služby Azure Storage a dešifruje před načítání. Správa šifrování, šifrování neaktivních uložených dat, dešifrování a správu klíčů v šifrování služby Storage je pro uživatele transparentní. Všechna data zapsaná do služby Azure Storage je zašifrovaná pomocí 256bitových [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednu z nejsilnějších bloku šifer k dispozici.

Šifrování služby Storage je povolená pro všechny účty nová a existující úložiště a nejde zakázat. Vzhledem k tomu, že ve výchozím nastavení jsou vaše data zabezpečená, není nutné upravovat kód vaší aplikace a chcete využít výhod šifrování služby Storage.

Tato funkce automaticky šifruje data na:

- Obě úrovně výkonu (Standard a Premium).
- Oba modely nasazení (Azure Resource Manageru a Klasický model).
- Všechny služby Azure Storage services (úložiště objektů Blob, Queue storage, Table storage a Azure Files). 

Šifrování služby Storage nemá vliv na výkon služby Azure Storage.

Spravovaná Microsoftem šifrovací klíče můžete použít pomocí šifrování služby Storage, nebo můžete použít vlastní šifrovací klíče. Další informace o použití vlastních klíčů najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Nastavení šifrování zobrazení na webu Azure Portal

Chcete-li zobrazit nastavení pro šifrování služby Storage, přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte účet úložiště. V **nastavení** podokně, vyberte **šifrování** nastavení.

![Portálu snímek obrazovky zobrazující nastavení šifrování](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Nejčastější dotazy k šifrování služby Storage

**Otázka: Mám klasický účet úložiště. Můžete povolit šifrování služby Storage na něm?**

Odpověď: šifrování služby Storage je povolená pro všechny účty úložiště (classic a Resource Manager).

**Otázka: jak můžete šifrovat data ve svém účtu klasického úložiště?**

Odpověď: šifrováním ve výchozím nastavení povolené Azure Storage automaticky šifruje vaše nová data. 

**Otázka: Mám účtu úložiště Resource Manageru. Můžete povolit šifrování služby Storage na něm?**

Odpověď: storage Service Encryption je povolené ve výchozím nastavení všechny existující účty úložiště Resource Manageru. To platí pro úložiště objektů Blob, Table storage, Queue storage a Azure Files. 

**Otázka: jak šifrují data v účtu úložiště Resource Manageru?**

Odpověď: šifrování služby Storage je povolena pro všechny účty úložiště – classic a Resource Manageru, veškeré stávající soubory v účtu úložiště, vytvořili před povolením šifrování se zpětně získat zašifrovaná pomocí šifrování proces na pozadí.

**Dotaz: lze vytvořit účty úložiště pomocí šifrování služby Storage povolit pomocí Azure Powershellu a rozhraní příkazového řádku Azure?**

Odpověď: šifrování služby Storage je standardně povolená v době vytváření všechny účty úložiště (classic nebo Resource Manager). Vlastnosti účtu můžete ověřit pomocí Azure Powershellu a rozhraní příkazového řádku Azure.

**Otázka: jak služby Azure Storage stát Pokud je povolené šifrování služby Storage?**

Odpověď: neexistuje žádné další poplatky.

**Otázka: Mohu použít vlastní šifrovací klíče?**

Odpověď: Ano, můžete použít vlastní šifrovací klíče. Další informace najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Dotaz: lze odvolat přístup k šifrovacím klíčům?**

Odpověď: Ano, pokud jste [pomocí vlastních šifrovacích klíčů](storage-service-encryption-customer-managed-keys.md) ve službě Azure Key Vault.

**Otázka: je povolená ve výchozím nastavení služba šifrování úložiště při vytváření účtu úložiště?**

Odpověď: Ano, je povolené šifrování služby úložiště pro všechny účty úložiště a pro všechny služby Azure Storage.

**Otázka: jak se to liší od Azure Disk Encryption?**

Odpověď: azure Disk Encryption se používá k šifrování operačního systému a datové disky ve virtuálních počítačích IaaS. Další informace najdete v tématu [Příručka zabezpečení úložiště](../storage-security-guide.md).

**Otázka: Co když mám povolit Azure Disk Encryption pro Moje datové disky?**

Odpověď: to bude fungovat bez problémů. Obě metody se šifrují vaše data.

**Otázka: Můj účet úložiště je nastavený na geo redundantně replikaci. Pomocí šifrování služby Storage budou Moje redundantní kopie také zašifrovaná?**

Odpověď: Ano, jsou šifrovány veškeré kopie tohoto účtu úložiště. Všechny redundance možnosti jsou podporovány – místně redundantní úložiště, zónově redundantní úložiště, geograficky redundantní úložiště a geograficky redundantní úložiště jen pro čtení.

**Dotaz: lze zakázat šifrování na svůj účet úložiště?**

Odpověď: šifrování je povoleno ve výchozím nastavení a není možné zakázat šifrování pro účet úložiště. 

**Otázka: šifrování služby Storage smí obsahovat pouze v určitých oblastech?**

O: šifrování služby storage je k dispozici ve všech oblastech pro všechny služby.

**Otázka: je šifrování služby Storage podle standardu FIPS 140-2 kompatibilní?**

Odpověď: Ano, je šifrování služby Storage podle standardu FIPS 140-2 kompatibilní.

**Dotaz: jak se mám obrátit uživatel můžu jakékoli problémy nebo chcete poskytnout zpětnou vazbu?**

Odpověď: kontaktní [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) pro nějaké problémy nebo zpětnou vazbu související s šifrování služby Storage.

## <a name="next-steps"></a>Další postup
Azure Storage nabízí že komplexní sadu funkcí zabezpečení tohoto vám společně pomohou vývojářům vytvářet zabezpečené aplikace. Další informace najdete v tématu [Příručka zabezpečení úložiště](../storage-security-guide.md).
