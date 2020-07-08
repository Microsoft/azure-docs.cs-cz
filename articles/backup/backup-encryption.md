---
title: Šifrování v Azure Backup
description: Přečtěte si, jak funkce šifrování v Azure Backup pomůžou chránit zálohovaná data a plnit požadavky na zabezpečení vaší firmy.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 0a3f4db4d248d2534cfebd617be0f3ccc9647f15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807734"
---
# <a name="encryption-in-azure-backup"></a>Šifrování v Azure Backup

Všechna vaše Zálohovaná data se automaticky šifrují, když se ukládají v cloudu pomocí Azure Storage šifrování, což vám pomůže splnit závazky zabezpečení a dodržování předpisů. Tato neaktivní neaktivní data jsou šifrovaná pomocí 256 šifrování AES, což je jedno z nejsilnější dostupných šifrovacích šifr a je kompatibilní se standardem FIPS 140-2.

Kromě šifrování v klidovém režimu se všechna vaše Zálohovaná data přenáší přes protokol HTTPS. Vždycky zůstane v páteřní síti Azure.

Další informace najdete v tématu [Azure Storage šifrování pro](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)neaktivní neaktivní data. V tématu [Azure Backup – Nejčastější dotazy](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) , které vám pomohou zodpovědět případné dotazy týkající se šifrování.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Šifrování zálohovaných dat pomocí klíčů spravovaných platformou

Ve výchozím nastavení se všechna vaše data šifrují pomocí klíčů spravovaných platformou. K povolení tohoto šifrování nemusíte provádět žádnou explicitní akci z vaší služby a vztahuje se na všechny úlohy, které se zálohují do vašeho trezoru Recovery Services.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem

Při zálohování Virtual Machines Azure teď můžete data šifrovat pomocí klíčů vlastněných a spravovaných vámi. Azure Backup umožňuje používat klíče RSA uložené v Azure Key Vault k šifrování záloh. Šifrovací klíč, který se používá k šifrování záloh, může být jiný než ten, který se používá pro zdroj. Data jsou chráněná pomocí šifrovacího klíče založeného na standardu AES 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Díky tomu máte plnou kontrolu nad daty a klíči. Chcete-li šifrování zakázat, je nutné, aby měl Recovery Services trezoru udělen přístup k šifrovacímu klíči v Azure Key Vault. Kdykoli je to potřeba, můžete klíč zakázat nebo odvolat přístup. Před tím, než se pokusíte ochránit jakékoli položky do trezoru, je však nutné povolit šifrování pomocí vašich klíčů.

>[!NOTE]
>Tato funkce je aktuálně v omezeném rozsahu dostupnosti. Pokud chcete data záloh šifrovat pomocí zákaznických klíčů, vyplňte prosím [Tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) a pošlete nám e-mail AskAzureBackupTeam@microsoft.com . Všimněte si, že možnost použití této funkce podléhá schválení Azure Backup služby.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Zálohování virtuálních počítačů se spravovanými disky šifrovaných pomocí klíčů spravovaných zákazníkem

Azure Backup taky umožňuje zálohovat virtuální počítače Azure, které používají váš klíč pro [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Klíč, který se používá k šifrování disků, je uložený v Azure Key Vault a spravuje vám. Šifrování služby Storage (SSE) pomocí klíčů spravovaných zákazníkem se liší od Azure Disk Encryption, protože ADE využívá BitLocker (pro Windows) a DM-crypt (pro Linux) k provádění šifrování v rámci hosta, SSE šifruje data ve službě úložiště a umožňuje pro vaše virtuální počítače používat libovolný operační systém nebo Image. Další podrobnosti najdete v tématu [šifrování spravovaných disků pomocí zákaznických spravovaných klíčů](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Zálohování virtuálních počítačů šifrovaných pomocí ADE

Pomocí Azure Backup můžete také zálohovat virtuální počítače Azure, které mají své operační systémy nebo datové disky šifrované pomocí Azure Disk Encryption. ADE pomocí nástroje BitLocker pro virtuální počítače s Windows a DM-crypt pro virtuální počítače se systémem Linux provádí šifrování v hostůch. Podrobnosti najdete v tématu [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Další kroky

- [Zálohování a obnovení šifrovaného virtuálního počítače Azure](backup-azure-vms-encryption.md)
