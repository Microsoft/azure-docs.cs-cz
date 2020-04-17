---
title: Šifrování disku Azure pomocí Azure AD (předchozí verze)
description: Tento článek obsahuje předpoklady pro použití microsoft azure diskšifrování pro virtuální počítače IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd668fcac3751715fbe91c9aeff98583c9d03d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458909"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Šifrování disku Azure pomocí Azure AD (předchozí verze)

**Nová verze Azure Disk Encryption eliminuje požadavek na poskytování parametru aplikace Azure AD pro povolení šifrování disku virtuálního počítače. S novou verzí už nemusíte poskytovat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD pomocí nové verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v [tématu Azure Disk Encryption for Windows VM](disk-encryption-overview.md). Virtuální počítače, které už byly zašifrované pomocí parametrů aplikace Azure AD, jsou stále podporované a měly by být nadále udržovány pomocí syntaxe AAD.**

Tento článek doplňuje [Azure Disk Encryption pro virtuální počítače s Windows](disk-encryption-overview.md) s dalšími požadavky a požadavky pro šifrování disku Azure s Azure AD (předchozí verze). [Část Podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems) zůstává stejná.

## <a name="networking-and-group-policy"></a>Zásady vytváření sítí a skupin

**Chcete-li povolit funkci Šifrování disku Azure pomocí starší syntaxe parametru AAD, musí virtuální počítače IaaS splňovat následující požadavky na konfiguraci koncového bodu sítě:** 
  - Chcete-li získat token pro připojení k trezoru klíčů, musí být virtuální počítač IaaS schopen se připojit ke koncovému bodu Služby Azure Active Directory, \[login.microsoftonline.com\].
  - Chcete-li zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS schopen se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopen se připojit ke koncovému bodu úložiště Azure, který je hostitelem úložiště rozšíření Azure a účtu úložiště Azure, který hostuje soubory Virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezují přístup z virtuálních počítačů Azure k Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo, které umožní odchozí připojení k IP adresám. Další informace naleznete v [tématu Azure Key Vault za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - Zašifrovaný virtuální virtuální počítače musí být nakonfigurován tak, aby jako výchozí protokol používal protokol TLS 1.2. Pokud byl tls 1.0 explicitně zakázán a verze rozhraní .NET nebyla aktualizována na 4.6 nebo vyšší, následující změna registru umožní ADE vybrat novější verzi TLS:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Zásady skupiny:**
 - Řešení Azure Disk Encryption používá nástroj BitLocker externí ochranu klíčů pro virtuální počítače Windows IaaS. U domén ových virtuálních zařízení nenabízené žádné zásady skupiny, které vynucují chrániče čipu TPM. Informace o zásadách skupiny "Povolit nástroj BitLocker bez kompatibilního čipu TPM" naleznete v [tématu Odkaz na zásady skupiny nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zásady nástroje BitLocker na virtuálních počítačích spojených s doménou s vlastními zásadami skupiny musí obsahovat následující nastavení: [Konfigurace uživatelského úložiště informací o obnovení nástroje BitLocker -> Povolit 256bitový obnovovací klíč](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro nástroj BitLocker nejsou kompatibilní. Na počítačích, které neměly správné nastavení zásad, použijte novou zásadu, vynuťte novou zásadu aktualizovat (gpupdate.exe /force) a pak může být restartování vyžadováno.  

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault pro řízení a správu klíče šifrování disku a tajných kódů. Trezor klíčů a virtuální počítače musí být umístěny ve stejné oblasti Azure a předplatné.

Podrobnosti najdete [v tématu vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure s Azure AD (předchozí verze).](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>Další kroky

- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
- [Povolení šifrování disku Azure pomocí Azure AD na virtuálních počítačích s Windows (předchozí verze)](disk-encryption-windows-aad.md)
- [Azure Disk Encryption požadavky CLI skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption požadavky PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
