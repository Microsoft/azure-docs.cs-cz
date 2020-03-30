---
title: Povolení šifrování disku Azure pro virtuální počítače s Windows
description: Tento článek obsahuje pokyny k povolení šifrování disku Microsoft Azure pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266777"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Šifrování disku Azure pro virtuální počítače s Windows 

Azure Disk Encryption přispívá k zabezpečení a ochraně vašich dat, aby byly splněny závazky organizace související se zabezpečením a dodržováním předpisů. Používá funkci [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) systému Windows k zajištění šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure (VM) a je integrován s [Azure Key Vault,](../../key-vault/index.yml) který vám pomůže řídit a spravovat šifrovací klíče a tajné klíče disku. 

Pokud používáte [Azure Security Center](../../security-center/index.yml), budete upozorněni, pokud máte virtuální počítače, které nejsou šifrované. Výstrahy zobrazit jako vysoké závažnosti a doporučení je šifrovat tyto virtuální počítače.

![Výstraha šifrování disku Centra zabezpečení Azure](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Podrobnosti najdete [v tématu Azure Disk Encryption with Azure AD (předchozí verze).](disk-encryption-overview-aad.md) 
> - Některá doporučení mohou zvýšit využití dat, sítě nebo výpočetních prostředků, což vede k dodatečným nákladům na licenci nebo předplatné. Chcete-li vytvářet prostředky v Azure v podporovaných oblastech, musíte mít platné aktivní předplatné Azure.

Základy Azure Disk Encryption pro Windows se můžete naučit během několika minut pomocí [rychlého spuštění Vytvořit a zašifrovat virtuální počítač s Windows pomocí rychlého startu Azure CLI](disk-encryption-cli-quickstart.md) nebo [Vytvořit a zašifrovat virtuální počítač s Windows pomocí rychlého startu Azure Powershellu](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Podporované virtuální počítače a operační systémy

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Virtuální počítače se systémem Windows jsou k dispozici v [řadě velikostí](sizes-general.md). Azure Disk Encryption není k dispozici na [základních virtuálních počítačích řady A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)nebo na virtuálních počítačích s méně než 2 GB paměti.

Azure Disk Encryption je taky k dispozici pro virtuální počítače s úložištěm premium.

### <a name="supported-operating-systems"></a>Podporované operační systémy

- Windows klient: Windows 8 a novější.
- Windows Server: Windows Server 2008 R2 a novější.  
 
> [!NOTE]
> Systém Windows Server 2008 R2 vyžaduje instalaci rozhraní .NET Framework 4.5 pro šifrování. Nainstalujte jej ze služby Windows Update pomocí volitelné aktualizace Microsoft .NET Framework 4.5.2 pro systémy se systémem Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core a Windows Server 2016 Core vyžadují, aby byla komponenta bdehdcfg nainstalována na virtuálním počítači pro šifrování.


## <a name="networking-requirements"></a>Požadavky na vytváření sítí
Chcete-li povolit azure disk encryption, virtuální počítače musí splňovat následující požadavky na konfiguraci koncového bodu sítě:
  - Chcete-li získat token pro připojení k trezoru klíčů, musí být virtuální počítač \[s\]Windows schopen se připojit ke koncovému bodu Služby Azure Active Directory, login.microsoftonline.com .
  - Chcete-li zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač se systémem Windows schopen se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač s Windows musí být moc schopen se připojit ke koncovému bodu úložiště Azure, který je hostitelem úložiště rozšíření Azure a účtu úložiště Azure, který hostuje soubory Virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezují přístup z virtuálních počítačů Azure k Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo, které umožní odchozí připojení k IP adresám. Další informace naleznete v [tématu Azure Key Vault za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Požadavky zásad skupiny

Azure Disk Encryption používá nástroj BitLocker externí ochranu klíčů pro virtuální počítače s Windows. U domén ových virtuálních zařízení nenabízené žádné zásady skupiny, které vynucují chrániče čipu TPM. Informace o zásadách skupiny "Povolit nástroj BitLocker bez kompatibilního čipu TPM" naleznete v [tématu Odkaz na zásady skupiny nástroje BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

Zásady nástroje BitLocker na virtuálních počítačích spojených s doménou s vlastními zásadami skupiny musí obsahovat následující nastavení: [Konfigurace uživatelského úložiště informací o obnovení nástroje BitLocker -> Povolit 256bitový obnovovací klíč](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro nástroj BitLocker nejsou kompatibilní. Na počítačích, které neměly správné nastavení zásad, použijte novou zásadu, vynuťte novou zásadu aktualizovat (gpupdate.exe /force) a pak může být restartování vyžadováno.

Azure Disk Encryption se nezdaří, pokud zásady skupiny na úrovni domény blokuje algoritmus AES-CBC, který používá nástroj BitLocker.

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault pro řízení a správu klíče šifrování disku a tajných kódů. Trezor klíčů a virtuální počítače musí být umístěny ve stejné oblasti Azure a předplatné.

Podrobnosti najdete [v tématu Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologie
Následující tabulka definuje některé běžné termíny používané v dokumentaci k šifrování disku Azure:

| Terminologie | Definice |
| --- | --- |
| Azure Key Vault | Trezor klíčů je kryptografická služba pro správu klíčů, která je založena na modulech zabezpečení hardwaru ověřených federálními standardy zpracování informací (FIPS). Tyto standardy pomáhají chránit kryptografické klíče a citlivé tajné klíče. Další informace naleznete v dokumentaci [k úložišti klíčů Azure](https://azure.microsoft.com/services/key-vault/) a v [dokumentaci k vytvoření a konfiguraci trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Velpřesové ovládání Azure](/cli/azure/install-azure-cli) je optimalizované pro správu a správu prostředků Azure z příkazového řádku.|
| BitLocker |[Nástroj BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je oborově uznávaná technologie šifrování svazků systému Windows, která se používá k povolení šifrování disku na virtuálních počítačích se systémem Windows. |
| Šifrovací klíč (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete poskytnout modul hardwarového zabezpečení (HSM) chráněný klíč nebo softwarem chráněný klíč. Další informace naleznete v dokumentaci [k úložišti klíčů Azure](https://azure.microsoft.com/services/key-vault/) a v [dokumentaci k vytvoření a konfiguraci trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md). |
| Rutiny prostředí PowerShell | Další informace najdete v [tématu Rutiny Prostředí Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Další kroky

- [Úvodní příručka – vytvoření a šifrování virtuálního počítače s Windows pomocí azure cli](disk-encryption-cli-quickstart.md)
- [Úvodní příručka – vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Powershellu](disk-encryption-powershell-quickstart.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Windows](disk-encryption-windows.md)
- [Azure Disk Encryption požadavky CLI skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption požadavky PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](disk-encryption-key-vault.md)


