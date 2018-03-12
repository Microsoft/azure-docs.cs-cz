---
title: "Šifrování služby úložiště Azure pro Data v klidovém stavu | Microsoft Docs"
description: "Použít funkci šifrování služby úložiště Azure pro šifrování Azure Blob Storage na straně služby při ukládání dat a při získávání data ho dešifrovat."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: lakasa
ms.openlocfilehash: 29f6a38f7a7f0f107dab8c99e750a8dec803f6f0
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data

Azure Storage Service šifrování (SSE) pro Data v klidovém stavu pomáhá chránit a ochranu dat, aby splňovaly vaše organizace zabezpečení a dodržování předpisů závazky. Pomocí této funkce Azure Storage automaticky šifruje vaše data před uložením do služby Azure Storage a dešifruje ji před načtení. Zpracování šifrování, šifrování v rest, dešifrování a správu klíčů poskytované SSE jsou pro uživatele transparentní. Všechna data zapsaná do služby Azure Storage se šifruje pomocí 256 bitů [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednu z nejsilnějších bloku šifer k dispozici.

SSE je povolená pro všechny účty nové a existující úložiště a nejde zakázat. Protože ve výchozím nastavení je zabezpečená data, není potřeba upravovat kód nebo aplikace, které chcete využít výhod SSE.

 SSE automaticky šifruje data všechny úrovně výkonu (Standard a Premium), všechny modely nasazení (Azure Resource Manager a klasický) a všechny služby Azure Storage (objekt Blob, fronty, tabulky a soubor). 

Spravované společností Microsoft šifrovací klíče můžete použít s SSE nebo můžete použít vlastní šifrovací klíče. Další informace o používání vlastního klíče najdete v tématu [zákazníka pomocí šifrování služby úložiště spravované klíče v Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Nastavení šifrování zobrazení na portálu Azure

Chcete-li zobrazit nastavení pro šifrování služby úložiště, přihlaste se [portál Azure](https://portal.azure.com) a vyberte účet úložiště. Na **nastavení** okně klikněte na nastavení šifrování.

![Možnost šifrování portálu snímek obrazovky zobrazující](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Nejčastější dotazy týkající se šifrování služby úložiště

**Otázka: je nutné stávající účet úložiště classic. Můžete povolit SSE na něm?**

Odpověď: SSE je povoleno ve výchozím nastavení pro všechny účty úložiště (Classic a Resource Manager účty úložiště).

**Otázka: jak můžete šifrovat data ve svůj účet úložiště classic?**

A: s šifrováním ve výchozím nastavení povolené nová data se šifrují automaticky pomocí služby úložiště. 

**Otázka: je nutné stávající účet úložiště Resource Manager. Můžete povolit SSE na něm?**

Odpověď: SSE je standardně na všechny existující účty úložiště Resource Manager. Tato možnost je podporována pro objekty BLOB, tabulek, souborů a fronty úložiště. 

**Otázka: nastavit jako k šifrování aktuální data v existující účet úložiště Resource Manager?**

Ve výchozím nastavení pro všechny účty úložiště - Classic a Resource Manager A: s SSE povolené účty úložiště. Data, které byly již existuje však nebudou šifrována. Existující data šifrovat, můžete je zkopírovat na jiný název nebo jiný kontejner a pak odeberte nezašifrované verze. 

**Otázka: je možné vytvořit nové účty úložiště s SSE povolit pomocí Azure PowerShell a rozhraní příkazového řádku Azure?**

Odpověď: SSE je povoleno ve výchozím nastavení v době vytváření libovolný účet úložiště (Classic a Resource Manager účty). Můžete ověřit pomocí prostředí Azure PowerShell a rozhraní příkazového řádku Azure vlastnosti účtu.

**Otázka: jak mnohem víc Azure Storage náklady Pokud je povoleno SSE?**

Odpověď: není bez dalších nákladů.

**Otázka: kdo spravuje šifrovacích klíčů?**

Odpověď: klíče jsou spravované microsoftem.

**Otázka: je možné použít vlastní šifrovací klíče?**

Odpověď: pracujeme na poskytnutí možnosti pro zákazníky, aby vlastní šifrovací klíče.

**Otázka: je možné odvolat přístup k šifrovacím klíčům?**

Odpověď: není v tuto chvíli; klíče jsou plně spravované microsoftem.

**Otázka: je SSE ve výchozím nastavení povolené, po vytvoření nového účtu úložiště?**

Odpověď: Ano, je ve výchozím nastavení pro všechny účty úložiště – Azure Resource Manageru a účty úložiště Classic povolené SSE pomocí Microsoft Managed klíče. Je povolena pro všechny služby také – objekt Blob, tabulky, fronty a úložiště souborů.

**Otázka: jak se to liší od Azure Disk Encryption?**

Odpověď: azure Disk Encryption se používá k šifrování operačního systému a datové disky ve virtuálních počítačů IaaS. Další podrobnosti, navštivte naše [Průvodce zabezpečením úložiště](../storage-security-guide.md).

**Otázka: Co když povolit Azure Disk Encryption na Moje datové disky?**

Odpověď: to bude fungovat bez problémů. Vaše data se budou šifrovat pomocí obou metod.

**Otázka: svůj účet úložiště je nastavený na geo redundantně replikovat. S SSE budou mé redundantní kopie také zašifrovaná?**

Odpověď: Ano, všechny kopie účtu úložiště se šifrují, a podporují všechny možnosti redundance – místně redundantní úložiště (LRS), zóny-Redundant Storage (ZRS), Geo-Redundant Storage (GRS) a přístup pro čtení Geo-Redundant Storage (RA-GRS) –.

**Otázka: nelze zakázat šifrování na svůj účet úložiště.**

Odpověď: šifrování je ve výchozím nastavení povolené a není možné můžete zakázat šifrování pro váš účet úložiště. 

**Otázka: je SSE povolen pouze v určitých oblastí?**

Odpověď: na SSE je k dispozici ve všech oblastech pro všechny služby. 

**Otázka: jak I contact někdo po jakýchkoli problémů nebo chcete poskytnout zpětnou vazbu?**

Odpověď: kontaktní [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) pro veškeré problémy související s šifrování služby úložiště.

## <a name="next-steps"></a>Další kroky
Úložiště Azure poskytuje komplexní sadu funkcí zabezpečení, které společně umožňují vývojářům vytvářet aplikace zabezpečené. Další podrobnosti naleznete [Průvodce zabezpečením úložiště](../storage-security-guide.md).
