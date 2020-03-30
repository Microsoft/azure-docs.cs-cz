---
title: Povolení služby Azure Disk Encryption pro virtuální počítače s Linuxem
description: Tento článek obsahuje pokyny k povolení microsoft azure diskšifrování pro virtuální počítače s Linuxem.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff1b37c3053ffa91dcb432cd97a7dd6fd71dad1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250423"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Šifrování disku Azure pro virtuální počítače s Linuxem 

Azure Disk Encryption přispívá k zabezpečení a ochraně vašich dat, aby byly splněny závazky organizace související se zabezpečením a dodržováním předpisů. Používá funkci [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) linuxu k zajištění šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure (VM) a je integrován s [Azure Key Vault,](../../key-vault/index.yml) který vám pomůže řídit a spravovat šifrovací klíče a tajné klíče disku. 

Pokud používáte [Azure Security Center](../../security-center/index.yml), budete upozorněni, pokud máte virtuální počítače, které nejsou šifrované. Výstrahy zobrazit jako vysoké závažnosti a doporučení je šifrovat tyto virtuální počítače.

![Výstraha šifrování disku Centra zabezpečení Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Podrobnosti najdete [v tématu Azure Disk Encryption with Azure AD (předchozí verze).](disk-encryption-overview-aad.md) 
> - Některá doporučení mohou zvýšit využití dat, sítě nebo výpočetních prostředků, což vede k dodatečným nákladům na licenci nebo předplatné. Chcete-li vytvářet prostředky v Azure v podporovaných oblastech, musíte mít platné aktivní předplatné Azure.
> - V současné době generace 2 virtuálnípočítače nepodporují Azure Disk Encryption. Podrobnosti najdete [v tématu Podpora virtuálních počítačích generace 2 v Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)

Základy Azure Disk Encryption for Linux se dozvíte během několika minut pomocí rychlého [spuštění Vytvořit a zašifrovat virtuální počítač SI Linux pomocí rychlého startu Azure CLI](disk-encryption-cli-quickstart.md) nebo [Vytvořit a šifrovat virtuální počítač SIP pomocí rychlého startu Azure Powershellu](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Podporované virtuální počítače a operační systémy

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Virtuální počítače s Linuxem jsou dostupné v [řadě velikostí](sizes.md). Azure Disk Encryption není k dispozici na [základních virtuálních počítačích řady A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)nebo na virtuálních počítačích, které nesplňují tyto minimální požadavky na paměť:

| Virtuální počítač | Minimální požadavek na paměť |
|--|--|
| Virtuální počítače s Linuxem při šifrování datových svazků| 2 GB |
| Virtuální počítače S IP Linuxu při šifrování dat i svazků operačního systému a v tom, kde je použití kořenového (/) souborového systému 4 GB nebo méně | 8 GB |
| Virtuální počítače S IP Linuxu při šifrování dat i svazků operačního systému a kde je použití kořenového (/) souborového systému větší než 4 GB | Použití kořenového souborového systému * 2. Například 16 GB využití kořenového souborového systému vyžaduje alespoň 32 GB paměti RAM |

Po dokončení procesu šifrování disku operačního systému na virtuálních počítačích s Linuxem lze virtuální počítač nakonfigurovat tak, aby běžel s menší pamětí. 

Azure Disk Encryption je taky k dispozici pro virtuální počítače s úložištěm premium. 

### <a name="supported-operating-systems"></a>Podporované operační systémy

Azure Disk Encryption je podporovaná na podmnožině [linuxových distribucí podporovaných Azure](endorsed-distros.md), což je samo o sobě podmnožinou všech možných distribucí linuxového serveru.

![Venndiagram linuxových serverových distribucí, které podporují Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Distribuce linuxových serverů, které Azure nepodporuje, nepodporují Azure Disk Encryption. Z těch, které jsou potvrzeny, podporují azure disk encryption pouze následující distribuce a verze:

| Linuxová distribuce | Version | Typ svazku podporovaný pro šifrování|
| --- | --- |--- |
| Ubuntu | 18.04| Operační systém a datový disk |
| Ubuntu | 16.04| Operační systém a datový disk |
| Ubuntu | 14.04.5</br>[s jádrem Azure aktualizovaným na 4.15 nebo novějším](disk-encryption-troubleshooting.md) | Operační systém a datový disk |
| RHEL | 7.7 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.6 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,5 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.4 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.3 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.2 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 6.8 | Datový disk (viz poznámka níže) |
| RHEL | 6.7 | Datový disk (viz poznámka níže) |
| CentOS | 7.7 | Operační systém a datový disk |
| CentOS | 7.6 | Operační systém a datový disk |
| CentOS | 7,5 | Operační systém a datový disk |
| CentOS | 7.4 | Operační systém a datový disk |
| CentOS | 7.3 | Operační systém a datový disk |
| CentOS | 7.2n | Operační systém a datový disk |
| CentOS | 6.8 | Datový disk |
| openSUSE | 42.3 | Datový disk |
| SLES | 12-SP4 | Datový disk |
| SLES | 12-SP3 | Datový disk |

> [!NOTE]
> Nová implementace Azure Disk Encryption je podporovaná pro operační systém RHEL a datový disk pro bitové kopie rhel7 s průběžným platbou.  
>
> ADE je také podporována pro zlaté obrázky GOLD s portedou bring-your-own-subscription, ale až **po** registraci předplatného . Další informace najdete v tématu [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images v Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Další požadavky na virtuální mísu

Azure Disk Encryption vyžaduje dm-crypt a vfat moduly, které mají být k dispozici v systému. Odebrání nebo zakázání vfat z výchozího obrazu zabrání systému ve čtení hlasitosti klíče a získání klíče potřebného k odemknutí disků při následných restartováních. Kroky posílení zabezpečení systému, které odeberou modul vfat ze systému, nejsou kompatibilní s azure disk encryption. 

Před povolením šifrování musí být datové disky, které mají být šifrovány, správně uvedeny v /etc/fstab. Pro tuto položku použijte trvalý název blokového zařízení, protože názvy zařízení ve formátu "dev/sdX" nelze spoléhat na to, že budou při restartování přidruženy ke stejnému disku, zejména po použití šifrování. Další podrobnosti o tomto chování najdete v [tématu: Poradce při potížích se změnami názvu zařízení virtuálních zařízení linux](troubleshoot-device-names-problems.md)

Zkontrolujte, zda je nastavení /etc/fstab správně nakonfigurováno pro montáž. Chcete-li nakonfigurovat tato nastavení, spusťte příkaz připojení - příkaz nebo restartujte virtuální počítač a spusťte opětovné připojení tímto způsobem. Po dokončení zkontrolujte výstup příkazu lsblk a ověřte, zda je jednotka stále připojena. 
- Pokud soubor /etc/fstab nepřipojí jednotku správně před povolením šifrování, Azure Disk Encryption nebude moct připojit správně.
- Proces šifrování disku Azure přesune informace o připojení z /etc/fstab a do vlastního konfiguračního souboru jako součást procesu šifrování. Nebuďte znepokojeni, když se po dokončení šifrování datové jednotky položka z /etc/fstab zobrazí v položce .".
- Před zahájením šifrování, ujistěte se, že zastavit všechny služby a procesy, které by mohly být zápis na připojené datové disky a zakázat je, aby se automaticky nerestartovat po restartu. Ty by mohly zachovat soubory otevřené na těchto oddílech, což brání šifrování postup k jejich opětovnému připojení, což způsobuje selhání šifrování. 
- Po restartování bude chvíli trvat, než proces šifrování disku Azure připojí nově zašifrované disky. Nebudou okamžitě k dispozici po restartu. Proces potřebuje čas na spuštění, odemknutí a připojení šifrovaných jednotek, než bude k dispozici pro přístup k jiným procesům. Tento proces může trvat déle než minutu po restartu v závislosti na vlastnostech systému.

Příklad příkazů, které lze použít k připojení datových disků a vytvoření nezbytných položek /etc/fstab, najdete ve [skriptu příkazu CLI](https://github.com/ejarvi/ade-cli-getting-started) azure diskové šifrování (řádky 244-248) a [ve skriptu PowerShellu azure disk encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Požadavky na vytváření sítí

Chcete-li povolit funkci Šifrování disku Azure, musí virtuální počítače SEm Linux splňovat následující požadavky na konfiguraci koncového bodu sítě:
  - Chcete-li získat token pro připojení k trezoru klíčů, musí být virtuální počítač \[s\]Linuxem schopen se připojit ke koncovému bodu Služby Azure Active Directory, login.microsoftonline.com .
  - Chcete-li zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač s Linuxem schopen se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač s Linuxem se musí moct připojit ke koncovému bodu úložiště Azure, který je hostitelem úložiště rozšíření Azure a účtu úložiště Azure, který hostuje soubory Virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezují přístup z virtuálních počítačů Azure k Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo, které umožní odchozí připojení k IP adresám. Další informace naleznete v [tématu Azure Key Vault za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault pro řízení a správu klíče šifrování disku a tajných kódů. Trezor klíčů a virtuální počítače musí být umístěny ve stejné oblasti Azure a předplatné.

Podrobnosti najdete [v tématu Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologie
Následující tabulka definuje některé běžné termíny používané v dokumentaci k šifrování disku Azure:

| Terminologie | Definice |
| --- | --- |
| Azure Key Vault | Trezor klíčů je kryptografická služba pro správu klíčů, která je založena na modulech zabezpečení hardwaru ověřených federálními standardy zpracování informací (FIPS). Tyto standardy pomáhají chránit kryptografické klíče a citlivé tajné klíče. Další informace naleznete v dokumentaci [k úložišti klíčů Azure](https://azure.microsoft.com/services/key-vault/) a v [dokumentaci k vytvoření a konfiguraci trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Velpřesové ovládání Azure](/cli/azure/install-azure-cli) je optimalizované pro správu a správu prostředků Azure z příkazového řádku.|
| DM-Krypta |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) je linuxový transparentní diskový šifrovací subsystém, který se používá k povolení šifrování disku na virtuálních počítačích SIP. |
| Šifrovací klíč (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete poskytnout modul hardwarového zabezpečení (HSM) chráněný klíč nebo softwarem chráněný klíč. Další informace naleznete v dokumentaci [k úložišti klíčů Azure](https://azure.microsoft.com/services/key-vault/) a v [dokumentaci k vytvoření a konfiguraci trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md). |
| Rutiny prostředí PowerShell | Další informace najdete v [tématu Rutiny Prostředí Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Další kroky

- [Úvodní příručka – vytvoření a šifrování virtuálního počítače s Linuxem pomocí azure CLI](disk-encryption-cli-quickstart.md)
- [Úvodní příručka – vytvoření a šifrování virtuálního počítače s Linuxem pomocí Azure Powershellu](disk-encryption-powershell-quickstart.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Linuxem](disk-encryption-linux.md)
- [Azure Disk Encryption požadavky CLI skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption požadavky PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](disk-encryption-key-vault.md)


