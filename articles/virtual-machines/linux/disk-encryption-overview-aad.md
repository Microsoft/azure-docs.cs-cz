---
title: Šifrování disku Azure s požadavky aplikací Azure AD (předchozí verze)
description: Tento článek obsahuje předpoklady pro použití microsoft azure diskšifrování pro virtuální počítače IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970583"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Šifrování disku Azure pomocí Azure AD (předchozí verze)

Nová verze Azure Disk Encryption eliminuje požadavek na poskytování parametru aplikace Azure Active Directory (Azure AD) k povolení šifrování disku virtuálního počítače. S novou verzí už nemusíte poskytovat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být šifrované bez parametrů aplikace Azure AD pomocí nové verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption for Linux VM](disk-encryption-overview.md). Virtuální počítače, které už byly zašifrované pomocí parametrů aplikace Azure AD, jsou stále podporované a měly by být nadále udržovány pomocí syntaxe AAD.

Tento článek obsahuje doplňky pro [Azure Disk Encryption pro virtuální počítače s Linuxem](disk-encryption-overview.md) s dalšími požadavky a požadavky pro šifrování disku Azure s Azure AD (předchozí verze).

Informace v těchto oddílech zůstávají stejné:

- [Podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Další požadavky na virtuální mísu](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Zásady vytváření sítí a skupin

Chcete-li povolit funkci Šifrování disku Azure pomocí starší syntaxe parametru AAD, musí virtuální počítače infrastruktury jako služby (IaaS) splňovat následující požadavky na konfiguraci koncového bodu sítě: 
  - Chcete-li získat token pro připojení k trezoru klíčů, musí být virtuální počítač IaaS schopen se připojit ke koncovému bodu Azure AD, \[login.microsoftonline.com\].
  - Chcete-li zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS schopen se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopen se připojit ke koncovému bodu úložiště Azure, který je hostitelem úložiště rozšíření Azure a účtu úložiště Azure, který hostuje soubory Virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezují přístup z virtuálních počítačů Azure k internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo, které umožní odchozí připojení k IP adresám. Další informace naleznete v [tématu Azure Key Vault za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - Pokud je ve Windows tls 1.0 explicitně zakázán a verze .NET není aktualizována na 4.6 nebo vyšší, následující změna registru povolí šifrování disku Azure vybrat novější verzi TLS:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Zásady skupiny
 - Řešení Azure Disk Encryption používá nástroj BitLocker externí ochranu klíčů pro virtuální počítače Windows IaaS. U virtuálních zařízení spojených s doménou netlačte na žádné zásady skupiny, které vynucují chrániče čipu TPM. Informace o zásadách skupiny pro možnost **Povolit nástroj BitLocker bez kompatibilního čipu TPM**naleznete [v tématu BitLocker Group Policy reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Zásady nástroje BitLocker na virtuálních počítačích spojených s doménou s vlastními zásadami skupiny musí obsahovat následující nastavení: [Konfigurace uživatelského úložiště informací o obnovení nástroje BitLocker -> Povolit 256bitový obnovovací klíč](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud jsou vlastní nastavení zásad skupiny pro nástroj BitLocker nekompatibilní. Na počítačích, které nemají správné nastavení zásad, použijte novou zásadu, vynuťte novou zásadu aktualizovat (gpupdate.exe /force) a restartujte, pokud je to požadováno. 

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče 

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných kódů disku. Trezor klíčů a virtuální počítače musí být umístěny ve stejné oblasti Azure a předplatné.

Další informace najdete [v tématu vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure s Azure AD (předchozí verze).](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>Další kroky

- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
- [Povolení šifrování disku Azure pomocí Azure AD na virtuálních počítačích s Linuxem (předchozí verze)](disk-encryption-linux-aad.md)
- [Azure Disk Encryption požadavky CLI skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption požadavky PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)