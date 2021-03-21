---
title: Azure Disk Encryption s Azure AD (předchozí verze)
description: Tento článek popisuje předpoklady pro použití Microsoft Azureho šifrování disku pro virtuální počítače s IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8db9aad279c151073f8c674854d139276a84b064
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555376"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption s Azure AD (předchozí verze)

**Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure AD, aby bylo možné povolit šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD, které používají novou verzi. Pokyny k povolení šifrování disků virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption pro virtuální počítače s Windows](disk-encryption-overview.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.**

Tento článek doplňuje [Azure Disk Encryption pro virtuální počítače s Windows](disk-encryption-overview.md) a další požadavky a požadavky pro Azure Disk Encryption s Azure AD (předchozí verze). Oddíly [podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems) zůstanou stejné.

## <a name="networking-and-group-policy"></a>Sítě a Zásady skupiny

**Pokud chcete funkci Azure Disk Encryption povolit pomocí starší syntaxe parametru AAD, virtuální počítače s IaaS musí splňovat následující požadavky konfigurace koncového bodu sítě:** 
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač IaaS schopný připojit se Azure Active Directory ke koncovému bodu, \[ Login.microsoftonline.com \] .
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit ke koncovému bodu Azure Storage, který hostuje úložiště rozšíření Azure a účet úložiště Azure, který hostuje soubory VHD.
  -  Pokud vaše zásada zabezpečení omezuje přístup z virtuálních počítačů Azure na Internet, můžete přeložit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo tak, aby umožňovalo odchozí připojení k IP adresám. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../../key-vault/general/access-behind-firewall.md).
  - Virtuální počítač, který se má zašifrovat, musí být nakonfigurovaný tak, aby používal TLS 1,2 jako výchozí protokol. Pokud byl protokol TLS 1,0 explicitně zakázán a verze .NET nebyla aktualizována na 4,6 nebo vyšší, umožní tato změna registru vybrat novější verzi protokolu TLS:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Zásady skupiny:**
 - Řešení Azure Disk Encryption využívá ochranu pomocí nástroje BitLocker pro virtuální počítače s Windows IaaS. Pro virtuální počítače připojené k doméně neinstalujte žádné zásady skupiny, které vynutily ochranu čipem TPM. Informace o zásadách skupiny pro "povolení nástroje BitLocker bez kompatibilního čipu TPM" najdete v tématu [přehled zásady skupiny BitLockeru](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastními zásadami skupiny musí zahrnovat následující nastavení: [Konfigurace úložiště informací pro obnovení BitLockeru v případě, > povolení 256 bitového obnovovacího klíče](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro BitLocker nejsou kompatibilní. V počítačích, které nemají správné nastavení zásad, použijte novou zásadu, vynutí aktualizaci nové zásady (gpupdate.exe/Force) a pak se může vyžadovat restartování.  

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Trezor klíčů a virtuální počítače se musí nacházet ve stejné oblasti a předplatném Azure.

Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Další kroky

- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
- [Povolení Azure Disk Encryption s Azure AD na virtuálních počítačích s Windows (předchozí verze)](disk-encryption-windows-aad.md)
- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
