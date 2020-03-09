---
title: Povolit Azure Disk Encryption pro virtuální počítače se systémem Linux
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače se systémem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 96ac1becfed74141b3b1544646f5d82bd0985045
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396834"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption pro virtuální počítače se systémem Linux 

Azure Disk Encryption pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Používá funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytování šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure a je integrována s [Azure Key Vault](../../key-vault/index.yml) , která vám pomůžou řídit a spravovat klíče šifrování disku a tajné kódy. 

Pokud používáte [Azure Security Center](../../security-center/index.yml), budete upozorněni v případě, že máte virtuální počítače, které nejsou šifrované. Zobrazit výstrahy jako vysokou závažností a doporučuje se pro šifrování tyto virtuální počítače.

![Upozornění šifrování disku Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
> - Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné. Musíte mít aktivní předplatné Azure platnou k vytváření prostředků v Azure v podporovaných oblastech.
> - V současné době generace 2 virtuální počítače nepodporují Azure Disk Encryption. Podrobnosti najdete v tématu [Podpora pro virtuální počítače 2. generace v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) .

Základní informace o Azure Disk Encryption pro Linux najdete během několika minut pomocí [virtuálního počítače se systémem Linux pomocí Azure CLI](disk-encryption-cli-quickstart.md) nebo [Vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure PowerShellu pro rychlý Start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Podporované virtuální počítače a operační systémy

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Virtuální počítače se systémem Linux jsou k dispozici v [různých velikostech](sizes.md). Azure Disk Encryption není k dispozici na virtuálních počítačích [Basic, a-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ani na virtuálních počítačích, které nesplňují tyto minimální požadavky na paměť:

| Virtuální počítač | Minimální požadavek na paměť |
|--|--|
| Virtuální počítače se systémem Linux jenom při šifrování datových svazků| 2 GB |
| Virtuální počítače se systémem Linux při šifrování dat a svazků operačních systémů a v případě použití systému souborů root (/) je 4 GB nebo méně | 8 GB |
| Virtuální počítače se systémem Linux při šifrování dat a svazků operačních systémů a využití systému souborů root (/) je větší než 4 GB | Použití kořenového souborového systému * 2. Například 16 GB použití kořenového systému souborů vyžaduje aspoň 32 GB paměti RAM. |

Po dokončení procesu šifrování disku s operačním systémem u virtuálních počítačů se systémem Linux lze virtuální počítač nakonfigurovat tak, aby běžel s méně paměti. 

Azure Disk Encryption je k dispozici také pro virtuální počítače s Premium Storage. 

### <a name="supported-operating-systems"></a>Podporované operační systémy

Azure Disk Encryption je podporovaná u podmnožiny [distribucí systému Linux schváleného službou Azure](endorsed-distros.md), což je podmnožina všech možných distribucí serveru se systémem Linux.

![Vennův diagram distribucí serveru Linux, které podporují Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Distribuce serverů pro Linux, které nejsou schváleny v Azure, nepodporují Azure Disk Encryption; z těch, které jsou schváleny, podporuje pouze následující distribuce a verze Azure Disk Encryption:

| Linuxové distribuce | Verze | Typ svazku podporovaný pro šifrování|
| --- | --- |--- |
| Ubuntu | 18,04| Disk operačního systému a dat |
| Ubuntu | 16.04| Disk operačního systému a dat |
| Ubuntu | 14.04.5</br>[s vyladěným jádrem Azure se aktualizovala na 4,15 nebo novější.](disk-encryption-troubleshooting.md) | Disk operačního systému a dat |
| RHEL | 7,7 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,6 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.5 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,4 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.3 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.2 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 6.8 | Datový disk (viz poznámka níže) |
| RHEL | 6.7 | Datový disk (viz poznámka níže) |
| CentOS | 7,7 | Disk operačního systému a dat |
| CentOS | 7,6 | Disk operačního systému a dat |
| CentOS | 7.5 | Disk operačního systému a dat |
| CentOS | 7,4 | Disk operačního systému a dat |
| CentOS | 7.3 | Disk operačního systému a dat |
| CentOS | 7.2N | Disk operačního systému a dat |
| CentOS | 6.8 | Datový disk |
| openSUSE | 42,3 | Datový disk |
| SLES | 12-SP4 | Datový disk |
| SLES | 12-SP3 | Datový disk |

> [!NOTE]
> Nová implementace Azure Disk Encryption je podporovaná pro RHEL operační systém a datový disk pro Image RHEL7 s průběžnými platbami.  
>
> ADE se podporuje taky pro zlaté RHELy s využitím vlastního předplatného, ale až **po** registraci předplatného. Další informace najdete v tématu [Red Hat Enterprise Linux na základě vlastních předplatných Gold v Azure](../workloads/redhat/byos.md##encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) .

## <a name="additional-vm-requirements"></a>Další požadavky na virtuální počítače

Azure Disk Encryption vyžaduje, aby byly v systému přítomné moduly dm-crypt a vfat. Odebráním nebo zakázáním VFAT z výchozí image znemožníte systému číst klíč a získat klíč potřebný k odemknutí disků při dalším restartování. Kroky pro posílení zabezpečení systému, které odebírají modul VFAT ze systému, nejsou kompatibilní s Azure Disk Encryption. 

Než povolíte šifrování, datové disky, které mají být zašifrované, musí být správně uvedené v adresáři/etc/fstab. Použijte název zařízení trvalé bloku pro tuto položku jako zařízení, které názvy ve formátu "/ dev/sdX" nelze spoléhat na přidruženy stejném disku mezi restartováními, zejména po šifrování se použije. Další podrobnosti o tomto chování najdete v tématu [řešení potíží se změnami názvů zařízení virtuálních počítačů se systémem Linux](troubleshoot-device-names-problems.md) .

Ujistěte se, že nastavení /etc/fstab jsou správně nakonfigurovány pro připojení. Tato nastavení nakonfigurujete, spusťte připojení – příkaz nebo restartujte virtuální počítač a aktivuje tímto způsobem opětovné připojení. Jakmile, která se dokončí, zkontrolujte výstup příkazu lsblk k ověření, že na jednotce je pořád připojený. 
- Pokud soubor /etc/fstab není správně připojit jednotku před povolením šifrování, Azure Disk Encryption nebude možné ji správně připojit.
- Azure Disk Encryption proces přesune informace připojení mimo /etc/fstab a do jeho vlastní konfigurační soubor jako součást procesu šifrování. Není možné dospod vidět, že položka chybí /etc/fstab po data šifrování jednotky dokončí.
- Před zahájením šifrování nezapomeňte zastavit všechny služby a procesy, které by mohly být zapsány do připojených datových disků, a zakázat je, aby se po restartování nerestartoval automaticky. V těchto oddílech můžou být soubory otevřené a brání tak postupu šifrování je znovu připojit, což způsobí selhání šifrování. 
- Po restartování počítače bude trvat dobu procesu Azure Disk Encryption pro připojení nově šifrovanými disky. Bude okamžitě k dispozici po restartu. Proces potřebuje čas ke spuštění, odemčení a pak připojte šifrované jednotky, teprve pak ji bude k dispozici pro jiné procesy pro přístup k. Tento proces může trvat déle než minutu po restartování počítače v závislosti na charakteristikách systému.

Příklad příkazů, které lze použít k připojení datových disků a vytvoření potřebných položek/etc/fstab, najdete ve skriptu rozhraní příkazového [řádku Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) (řádky 244-248) a [skriptu powershellu pro Azure Disk Encryption požadavky](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Požadavky na síť

Aby bylo možné povolit funkci Azure Disk Encryption, musí virtuální počítače se systémem Linux splňovat následující požadavky konfigurace koncového bodu sítě:
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač se systémem Linux schopný připojit se ke koncovému bodu Azure Active Directory \[login.microsoftonline.com\].
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač se systémem Linux schopný se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač se systémem Linux musí být schopný připojit se ke koncovému bodu Azure Storage, který je hostitelem úložiště rozšíření Azure a účtu úložiště Azure, který je hostitelem souborů VHD.
  -  Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure na Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo pro povolení odchozích připojení k IP adres. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Trezor klíčů a virtuální počítače se musí nacházet ve stejné oblasti a předplatném Azure.

Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologie
Následující tabulka popisuje některé běžné výrazy používané v dokumentaci ke službě Azure Disk Encryption:

| Terminologie | Definice |
| --- | --- |
| Azure Key Vault | Key Vault je služba pro správu klíčů, kryptografické, který je založen na informace o zpracování normy FIPS (Federal) ověřených modulech hardwarového zabezpečení. Tyto normy pomáhají chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) je optimalizováno pro správu a správu prostředků Azure z příkazového řádku.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) je transparentní podsystém šifrování disku založený na systému Linux, který slouží k povolení šifrování disku na virtuálních počítačích se systémem Linux. |
| Klíč šifrování klíče (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete zadat modulu hardwarového zabezpečení (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutiny Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Další kroky

- [Rychlý Start – vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure CLI](disk-encryption-cli-quickstart.md)
- [Rychlý Start – vytvoření a šifrování virtuálního počítače se systémem Linux pomocí prostředí Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Linuxem](disk-encryption-linux.md)
- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](disk-encryption-key-vault.md)


