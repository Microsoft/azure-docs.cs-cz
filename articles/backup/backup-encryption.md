---
title: Šifrování ve službě Azure Backup
description: Přečtěte si, jak funkce šifrování v Azure Backup pomůžou chránit zálohovaná data a plnit požadavky na zabezpečení vaší firmy.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325670"
---
# <a name="encryption-in-azure-backup"></a>Šifrování ve službě Azure Backup

Všechna vaše Zálohovaná data se automaticky šifrují, když se ukládají v cloudu pomocí Azure Storage šifrování, což vám pomůže splnit závazky zabezpečení a dodržování předpisů. Tato neaktivní neaktivní data jsou šifrovaná pomocí 256 šifrování AES, což je jedno z nejsilnější dostupných šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Kromě šifrování v klidovém režimu se všechna vaše Zálohovaná data přenáší přes protokol HTTPS. Vždycky zůstane v páteřní síti Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Úrovně šifrování v Azure Backup

Azure Backup zahrnuje šifrování na dvou úrovních:

- **Šifrování dat v trezoru Recovery Services**
  - **Použití klíčů spravovaných platformou**: ve výchozím nastavení se všechna vaše data šifrují pomocí klíčů spravovaných platformou. Pro povolení tohoto šifrování není nutné provádět žádnou explicitní akci z vašeho konce. Platí pro všechny úlohy, které se zálohují do vašeho trezoru Recovery Services.
  - **Použití klíčů spravovaných zákazníkem**: při zálohování Virtual Machines Azure si můžete zvolit šifrování vašich dat pomocí šifrovacích klíčů vlastněných a spravovaných vámi. Azure Backup umožňuje používat klíče RSA uložené v Azure Key Vault k šifrování záloh. Šifrovací klíč, který se používá k šifrování záloh, může být jiný než ten, který se používá pro zdroj. Data jsou chráněná pomocí šifrovacího klíče založeného na standardu AES 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Díky tomu máte plnou kontrolu nad daty a klíči. Pokud chcete povolit šifrování, je potřeba, abyste službě Recovery Services trezoru udělíte přístup k šifrovacímu klíči v Azure Key Vault. Kdykoli je to potřeba, můžete klíč zakázat nebo odvolat přístup. Před tím, než se pokusíte ochránit jakékoli položky do trezoru, je však nutné povolit šifrování pomocí vašich klíčů. [Další informace najdete tady](encryption-at-rest-with-cmk.md).
  - **Šifrování na úrovni infrastruktury**: Kromě šifrování vašich dat v Recovery Services trezoru pomocí klíčů spravovaných zákazníkem můžete také zvolit, aby na infrastruktuře úložiště byla nakonfigurovaná další vrstva šifrování. Toto šifrování infrastruktury spravuje platforma. Společně s šifrováním v klidovém formátu pomocí klíčů spravovaných zákazníkem umožňuje použití dvou vrstev šifrování zálohovaných dat. Šifrování infrastruktury se dá nakonfigurovat jenom v případě, že se nejdřív rozhodnete používat vlastní klíče pro šifrování v klidovém umístění. Šifrování infrastruktury používá pro šifrování dat klíče spravované platformou.
- **Šifrování specifické pro zálohovanou úlohu**  
  - **Zálohování virtuálních počítačů Azure**: Azure Backup podporuje zálohování virtuálních počítačů s disky šifrovanými pomocí [klíčů spravovaných platformou](../virtual-machines/disk-encryption.md#platform-managed-keys)a také vlastních [klíčů spravovaných zákazníkem](../virtual-machines/disk-encryption.md#customer-managed-keys) , které vlastníte a spravujete sami. Kromě toho můžete také zálohovat virtuální počítače Azure, které mají své operační systémy nebo datové disky šifrované pomocí [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE používá pro virtuální počítače s Windows nástroj BitLocker a DM-Crypt pro virtuální počítače se systémem Linux k provádění šifrování v hostovi.

>[!NOTE]
>Šifrování infrastruktury je momentálně ve verzi omezené verze Preview a je dostupné v oblastech USA – východ, US West2, USA (střed) – jih, US Gov – Arizona a US GOV) – Virginia. Pokud chcete funkci používat v kterékoli z těchto oblastí, vyplňte [Tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) a pošlete nám e-mail na adresu [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md)
- [Azure Backup Nejčastější dotazy](backup-azure-backup-faq.md#encryption) týkající se šifrování