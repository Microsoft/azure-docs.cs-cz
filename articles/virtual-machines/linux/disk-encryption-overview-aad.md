---
title: Azure Disk Encryption se předpoklady pro aplikace Azure AD (předchozí verze)
description: Tento článek popisuje doplňky Azure Disk Encryption pro virtuální počítače se systémem Linux s dalšími požadavky a požadavky pro Azure Disk Encryption s Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558334"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption s Azure Active Directory (AD) (předchozí verze)

Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure Active Directory (Azure AD) pro povolení šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD pomocí nové vydané verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-overview.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.

Tento článek popisuje doplňky [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-overview.md) s dalšími požadavky a požadavky pro Azure Disk Encryption s Azure AD (předchozí verze).

Informace v těchto oddílech zůstávají stejné:

- [Podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Další požadavky na virtuální počítače](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Sítě a Zásady skupiny

Pokud chcete funkci Azure Disk Encryption povolit pomocí starší syntaxe parametru AAD, virtuální počítače infrastruktury jako služby (IaaS) musí splňovat následující požadavky konfigurace koncového bodu sítě: 
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu Azure AD, \[ Login.microsoftonline.com \] .
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit ke koncovému bodu Azure Storage, který hostuje úložiště rozšíření Azure a účet úložiště Azure, který hostuje soubory VHD.
  -  Pokud vaše zásada zabezpečení omezuje přístup z virtuálních počítačů Azure na Internet, můžete přeložit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo tak, aby umožňovalo odchozí připojení k IP adresám. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../../key-vault/general/access-behind-firewall.md).
  - Pokud je v systému Windows explicitně zakázán protokol TLS 1,0 a verze rozhraní .NET není aktualizována na 4,6 nebo novější, umožňuje následující změna registru Azure Disk Encryption vybrat novější verzi protokolu TLS:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Zásady skupiny
 - Řešení Azure Disk Encryption využívá ochranu pomocí nástroje BitLocker pro virtuální počítače s Windows IaaS. Pro virtuální počítače připojené k doméně neinstalujte žádné zásady skupiny, které vynutily ochranu čipem TPM. Informace o Zásady skupiny možnosti **Povolení BitLockeru bez kompatibilního čipu TPM** najdete v tématu [Zásady skupiny BitLockeru](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastním Zásady skupiny musí zahrnovat následující nastavení: [Konfigurace úložiště informací pro obnovení BitLockeru – > povolení 256ho obnovovacího klíče](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdařila, pokud je nastavení vlastního Zásady skupiny BitLockeru nekompatibilní. V počítačích, které nemají správné nastavení zásad, použijte novou zásadu, vynutí aktualizaci nové zásady (gpupdate.exe/Force) a pak se v případě potřeby restartuje. 

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče 

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Trezor klíčů a virtuální počítače se musí nacházet ve stejné oblasti a předplatném Azure.

Další informace najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Další kroky

- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
- [Povolení Azure Disk Encryption s Azure AD na virtuálních počítačích se systémem Linux (předchozí verze)](disk-encryption-linux-aad.md)
- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
