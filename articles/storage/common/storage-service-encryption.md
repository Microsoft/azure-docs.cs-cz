---
title: "Šifrování služby úložiště Azure pro Data v klidovém stavu | Microsoft Docs"
description: "Pomocí funkce šifrování služby úložiště Azure k šifrování úložiště objektů Blob v Azure na straně služby při ukládání dat a při načítání dat ho dešifrovat."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: lakasa
ms.openlocfilehash: 6b56cbb4220ce1c8767724938dd531b8ae5c3920
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data

Azure šifrování služby úložiště pro Data v klidovém stavu pomáhá chránit data na splňují vaše organizace zabezpečení a dodržování předpisů závazky. Pomocí této funkce Azure Storage automaticky šifruje vaše data před uložením do služby Azure Storage a dešifruje data před načtení. Zpracování šifrování, šifrování v rest, dešifrování a správy klíčů v nástroji šifrování služby úložiště je pro uživatele transparentní. Všechna data zapsat do služby Azure Storage je zašifrovaná pomocí 256 bitů [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednu z nejsilnějších bloku šifer k dispozici.

Šifrování služby úložiště je povolená pro všechny účty nové a existující úložiště a nejde zakázat. Protože vaše data je ve výchozím nastavení zabezpečená, nemusíte upravovat kód nebo aplikace, které chcete využít výhod šifrování služby úložiště.

Tato funkce automaticky šifruje data:

- Obě úrovně výkonu (Standard a Premium).
- Obou modelů nasazení (Azure Resource Manager a klasický).
- Všechna úložiště Azure services (úložiště objektů Blob, úložiště Queue, Table storage a Azure Files). 

Šifrování služby úložiště nemá vliv na výkon úložiště Azure.

Spravované společností Microsoft šifrovací klíče můžete použít pomocí šifrování služby úložiště, nebo můžete použít vlastní šifrovací klíče. Další informace o používání vlastního klíče najdete v tématu [šifrování služby úložiště v Azure Key Vault klíče spravovaného zákazníkem](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Nastavení šifrování zobrazení na portálu Azure

Chcete-li zobrazit nastavení pro šifrování služby úložiště, přihlaste se k [portál Azure](https://portal.azure.com) a vyberte účet úložiště. V **nastavení** podokně, vyberte **šifrování** nastavení.

![Portál snímek obrazovky nastavení šifrování](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Nejčastější dotazy týkající se šifrování služby úložiště

**Otázka: je nutné účet úložiště classic. Můžete povolit šifrování služby úložiště na něm?**

Ve výchozím nastavení pro všechny účty úložiště je povolené šifrování služby úložiště A: (classic a Resource Manager).

**Otázka: jak můžete šifrovat data ve svůj účet úložiště classic?**

Odpověď: šifrováním ve výchozím nastavení povolené Azure Storage šifruje automaticky nová data. 

**Otázka: je nutné účet správce prostředků úložiště. Můžete povolit šifrování služby úložiště na něm?**

A: na všechny existující účty úložiště Resource Manager ve výchozím nastavení je povolené šifrování služby úložiště. Tato možnost je podporována pro úložiště objektů Blob, úložiště Table, Queue storage a Azure Files. 

**Otázka: jak šifrují data v účtu úložiště, Resource Manager?**

Šifrování služby úložiště A: je povoleno ve výchozím nastavení pro všechny účty úložiště – classic a Resource Manager. Stávající data však nejsou šifrována. K šifrování stávající data, můžete ho zkopírujte do jiný název nebo jiný kontejner a potom odeberte nezašifrované verze. 

**Otázka: je možné vytvořit účty úložiště pomocí šifrování služby úložiště je povolit pomocí Azure PowerShell a rozhraní příkazového řádku Azure?**

Odpověď: šifrování služby úložiště ve výchozím nastavení zapnutá v době vytváření libovolný účet úložiště (classic nebo Resource Manager). Vlastnosti účtu můžete ověřit pomocí Azure PowerShell a rozhraní příkazového řádku Azure.

**Otázka: jak mnohem víc Azure Storage náklady Pokud je povolené šifrování služby úložiště?**

Odpověď: není bez dalších nákladů.

**Otázka: kdo spravuje šifrovacích klíčů?**

Odpověď: Microsoft spravuje klíče.

**Otázka: je možné použít vlastní šifrovací klíče?**

Odpověď: není v tuto chvíli.

**Otázka: je možné odvolat přístup k šifrovacím klíčům?**

Odpověď: není v tuto chvíli. Microsoft plně spravuje klíče.

**Otázka: je ve výchozím nastavení povolené šifrování služby úložiště po vytvoření účtu úložiště?**

Odpověď: Ano, je ve výchozím nastavení pro všechny účty úložiště – Azure Resource Manager a klasický povolené šifrování služby úložiště (pomocí klíče spravovaný společností Microsoft). Co povolena pro všechny služby také – úložiště objektů Blob, úložiště Table, Queue storage a Azure Files.

**Otázka: jak se to liší od Azure Disk Encryption?**

Odpověď: azure Disk Encryption se používá k šifrování operačního systému a datové disky ve virtuálních počítačů IaaS. Další informace najdete v tématu [Průvodce zabezpečením úložiště](../storage-security-guide.md).

**Otázka: Co když povolit Azure Disk Encryption na Moje datové disky?**

Odpověď: to bude fungovat bez problémů. Obě metody zašifruje data.

**Otázka: svůj účet úložiště je nastavený na geo redundantně replikovat. Pomocí šifrování služby úložiště budou mé redundantní kopie také zašifrovaná?**

Odpověď: Ano, jsou šifrovány veškeré kopie tohoto účtu úložiště. Všechny redundance nejsou podporovány možnosti--místně redundantní úložiště, zónově redundantní úložiště, geograficky redundantní úložiště a geograficky redundantní úložiště s přístupem pro čtení.

**Otázka: je možné zakázat šifrování na svůj účet úložiště?**

Odpověď: šifrování je ve výchozím nastavení povolené a není možné můžete zakázat šifrování pro váš účet úložiště. 

**Otázka: je šifrování služby úložiště povoleny pouze v určitých oblastí?**

A: šifrování služby úložiště je k dispozici ve všech oblastech pro všechny služby. 

**Otázka: jak I contact někdo po máte potíže nebo chcete poskytnout zpětnou vazbu?**

Odpověď: kontaktní [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) pro nějaké problémy nebo zpětnou vazbu týkající se šifrování služby úložiště.

## <a name="next-steps"></a>Další postup
Úložiště Azure poskytuje že komplexní soubor funkce zabezpečení této společně umožňují vývojářům sestavení zabezpečených aplikací. Další informace najdete v tématu [Průvodce zabezpečením úložiště](../storage-security-guide.md).
