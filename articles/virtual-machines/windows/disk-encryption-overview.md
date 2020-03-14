---
title: Povolení Azure Disk Encryption pro virtuální počítače s Windows
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266777"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption pro virtuální počítače s Windows 

Azure Disk Encryption pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Pomocí funkce [BitLocker](https://en.wikipedia.org/wiki/BitLocker) systému Windows poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure a je integrována s [Azure Key Vault](../../key-vault/index.yml) , které vám pomůžou řídit a spravovat klíče a tajné kódy disku. 

Pokud používáte [Azure Security Center](../../security-center/index.yml), budete upozorněni v případě, že máte virtuální počítače, které nejsou šifrované. Zobrazit výstrahy jako vysokou závažností a doporučuje se pro šifrování tyto virtuální počítače.

![Upozornění šifrování disku Azure Security Center](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
> - Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné. Musíte mít aktivní předplatné Azure platnou k vytváření prostředků v Azure v podporovaných oblastech.

Základní informace o Azure Disk Encryption pro Windows během několika minut se dozvíte v tématu [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure CLI](disk-encryption-cli-quickstart.md) nebo [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure PowerShellu pro rychlý Start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Podporované virtuální počítače a operační systémy

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Virtuální počítače s Windows jsou dostupné v [různých velikostech](sizes-general.md). Azure Disk Encryption není k dispozici pro virtuální počítače [Basic, a-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/)nebo na virtuálních počítačích s méně než 2 GB paměti.

Azure Disk Encryption je k dispozici také pro virtuální počítače s Premium Storage.

### <a name="supported-operating-systems"></a>Podporované operační systémy

- Klient Windows: Windows 8 a novější.
- Windows Server: Windows Server 2008 R2 a novější.  
 
> [!NOTE]
> Systém Windows Server 2008 R2 vyžaduje instalaci .NET Framework 4,5 pro šifrování; nainstalujte ji z web Windows Update s volitelnou aktualizací Microsoft .NET Framework 4.5.2 pro systémy Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core a Windows Server 2016 Core vyžadují, aby byla na virtuálním počítači nainstalovaná součást BdeHdCfg pro šifrování.


## <a name="networking-requirements"></a>Požadavky na síť
Aby bylo možné povolit Azure Disk Encryption, musí virtuální počítače splňovat následující požadavky konfigurace koncového bodu sítě:
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač s Windows schopný připojit se k Azure Active Directory koncovému bodu \[login.microsoftonline.com\].
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač s Windows schopný se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač s Windows musí být schopný se připojit ke koncovému bodu Azure Storage, který hostuje úložiště rozšíření Azure a účet úložiště Azure hostující soubory VHD.
  -  Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure na Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo pro povolení odchozích připojení k IP adres. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Zásady skupiny požadavky

Azure Disk Encryption používá ochranu pomocí externího klíče BitLockeru pro virtuální počítače s Windows. Pro virtuální počítače připojené k doméně, push nemáte žádné zásady skupiny, které vynucují ochrany pomocí čipu TPM. Informace o zásadách skupiny pro "povolení nástroje BitLocker bez kompatibilního čipu TPM" najdete v tématu [přehled zásady skupiny BitLockeru](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastními zásadami skupiny musí zahrnovat následující nastavení: [Konfigurace úložiště informací pro obnovení BitLockeru v případě, > povolení 256 bitového obnovovacího klíče](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro BitLocker nejsou kompatibilní. Na počítačích, které nebyly k dispozici nastavení správné zásady, použijí nové zásady vynutí nové zásady aktualizace (gpupdate.exe/Force) a následného restartování může být nutné.

Azure Disk Encryption dojde k chybě, pokud zásady skupiny na úrovni domény blokují algoritmus AES-CBC, který je používán nástrojem BitLocker.

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Trezor klíčů a virtuální počítače se musí nacházet ve stejné oblasti a předplatném Azure.

Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologie
Následující tabulka popisuje některé běžné výrazy používané v dokumentaci ke službě Azure Disk Encryption:

| Terminologie | Definice |
| --- | --- |
| Azure Key Vault | Key Vault je služba pro správu klíčů, kryptografické, který je založen na informace o zpracování normy FIPS (Federal) ověřených modulech hardwarového zabezpečení. Tyto normy pomáhají chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) je optimalizováno pro správu a správu prostředků Azure z příkazového řádku.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznaná technologie pro šifrování svazků Windows, která se používá k povolení šifrování disku na virtuálních počítačích s Windows. |
| Klíč šifrování klíče (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete zadat modulu hardwarového zabezpečení (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutiny Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Další kroky

- [Rychlý Start – vytvoření a šifrování virtuálního počítače s Windows pomocí Azure CLI](disk-encryption-cli-quickstart.md)
- [Rychlý Start – vytvoření a šifrování virtuálního počítače s Windows pomocí Azure PowerShellu](disk-encryption-powershell-quickstart.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Windows](disk-encryption-windows.md)
- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](disk-encryption-key-vault.md)


