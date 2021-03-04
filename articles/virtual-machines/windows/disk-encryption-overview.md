---
title: Povolení Azure Disk Encryption pro virtuální počítače s Windows
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: e9436a9ec7db660fa2d7012df98188b96ea8ee16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694304"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption pro virtuální počítače s Windows

Azure Disk Encryption přispívá k zabezpečení a ochraně vašich dat, aby byly splněny závazky organizace související se zabezpečením a dodržováním předpisů. Pomocí funkce [BitLocker](https://en.wikipedia.org/wiki/BitLocker) systému Windows poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure a je integrována s [Azure Key Vault](../../key-vault/index.yml) , které vám pomůžou řídit a spravovat klíče a tajné kódy disku.

Azure Disk Encryption je odolný proti zóně stejným způsobem jako Virtual Machines. Podrobnosti najdete v tématu [služby Azure, které podporují zóny dostupnosti](../../availability-zones/az-region.md).

Pokud používáte [Azure Security Center](../../security-center/index.yml), budete upozorněni v případě, že máte virtuální počítače, které nejsou šifrované. Výstrahy se zobrazují jako Vysoká závažnost a doporučení slouží k šifrování těchto virtuálních počítačů.

![Výstraha Azure Security Center šifrování disku](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
> - Některá doporučení můžou zvýšit využití dat, sítě nebo výpočetních prostředků, což má za následek další licence nebo náklady na předplatné. Abyste mohli vytvářet prostředky v Azure v podporovaných oblastech, musíte mít platné aktivní předplatné Azure.

Základní informace o Azure Disk Encryption pro Windows během několika minut se dozvíte v tématu [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure CLI](disk-encryption-cli-quickstart.md) nebo [Vytvoření a šifrování virtuálního počítače s Windows pomocí Průvodce rychlým startem Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Podporované virtuální počítače a operační systémy

### <a name="supported-vms"></a>Podporované virtuální počítače

Virtuální počítače s Windows jsou dostupné v [různých velikostech](../sizes-general.md). Azure Disk Encryption se podporuje u virtuálních počítačů 1. generace a 2. generace. Azure Disk Encryption je k dispozici také pro virtuální počítače s Premium Storage.

Azure Disk Encryption není k dispozici pro virtuální počítače [Basic, a-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/)nebo na virtuálních počítačích s méně než 2 GB paměti.  Azure Disk Encryption není k dispozici také pro image virtuálních počítačů bez dočasných disků (dv4, Dsv4, Ev4 a Esv4).  Podívejte [se na velikost virtuálních počítačů Azure bez místního dočasného disku](../azure-vms-no-temp-disk.md).  Další výjimky naleznete v tématu [Azure Disk Encryption: nepodporované scénáře](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Podporované operační systémy

- Klient Windows: Windows 8 a novější.
- Windows Server: Windows Server 2008 R2 a novější.  
 
> [!NOTE]
> Systém Windows Server 2008 R2 vyžaduje instalaci .NET Framework 4,5 pro šifrování; nainstalujte ji z web Windows Update s volitelnou aktualizací Microsoft .NET Framework 4.5.2 pro systémy Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core a Windows Server 2016 Core vyžadují, aby byla na virtuálním počítači nainstalovaná součást BdeHdCfg pro šifrování.


## <a name="networking-requirements"></a>Požadavky na síť
Aby bylo možné povolit Azure Disk Encryption, musí virtuální počítače splňovat následující požadavky konfigurace koncového bodu sítě:
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač s Windows schopný se připojit k Azure Active Directory koncovému bodu, \[ Login.microsoftonline.com \] .
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač s Windows schopný se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač s Windows musí být schopný se připojit ke koncovému bodu Azure Storage, který hostuje úložiště rozšíření Azure a účet úložiště Azure hostující soubory VHD.
  -  Pokud vaše zásada zabezpečení omezuje přístup z virtuálních počítačů Azure na Internet, můžete přeložit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo tak, aby umožňovalo odchozí připojení k IP adresám. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Zásady skupiny požadavky

Azure Disk Encryption používá ochranu pomocí externího klíče BitLockeru pro virtuální počítače s Windows. Pro virtuální počítače připojené k doméně neinstalujte žádné zásady skupiny, které vynutily ochranu čipem TPM. Informace o zásadách skupiny pro "povolení nástroje BitLocker bez kompatibilního čipu TPM" najdete v tématu [přehled zásady skupiny BitLockeru](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastními zásadami skupiny musí zahrnovat následující nastavení: [Konfigurace úložiště informací pro obnovení BitLockeru v případě, > povolení 256 bitového obnovovacího klíče](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro BitLocker nejsou kompatibilní. V počítačích, které nemají správné nastavení zásad, použijte novou zásadu, vynutí aktualizaci nové zásady (gpupdate.exe/Force) a pak se může vyžadovat restartování.

Azure Disk Encryption dojde k chybě, pokud zásady skupiny na úrovni domény blokují algoritmus AES-CBC, který je používán nástrojem BitLocker.

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Trezor klíčů a virtuální počítače se musí nacházet ve stejné oblasti a předplatném Azure.

Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologie
Následující tabulka popisuje některé běžné výrazy používané v dokumentaci ke službě Azure Disk Encryption:

| Terminologie | Definice |
| --- | --- |
| Azure Key Vault | Key Vault je kryptografická služba pro správu klíčů založená na modulech zabezpečení hardwaru FIPS (Federal Information Processing Standards) ověřené. Tyto standardy usnadňují ochranu kryptografických klíčů a citlivých tajných klíčů. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) je optimalizováno pro správu a správu prostředků Azure z příkazového řádku.|
| BitLocker |[BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) je rozpoznaná technologie pro šifrování svazků Windows, která se používá k povolení šifrování disku na virtuálních počítačích s Windows. |
| Klíč šifrování klíče (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete poskytnout klíč chráněný modulem hardwarového zabezpečení (HSM) nebo klíč chráněný softwarem. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutiny Azure PowerShell](/powershell/azure/). |

## <a name="next-steps"></a>Další kroky

- [Rychlý Start – vytvoření a šifrování virtuálního počítače s Windows pomocí Azure CLI ](disk-encryption-cli-quickstart.md)
- [Rychlý Start – vytvoření a šifrování virtuálního počítače s Windows pomocí Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Windows](disk-encryption-windows.md)
- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) 
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](disk-encryption-key-vault.md)