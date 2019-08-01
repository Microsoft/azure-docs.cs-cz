---
title: Co je Azure Disk Encryption?
description: Tento článek poskytuje přehled Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/29/2019
ms.custom: seodec18
ms.openlocfilehash: c5e568dd073376295e4865994fba8ae5b5ac59a0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640994"
---
# <a name="azure-disk-encryption-overview"></a>Přehled Azure Disk Encryption

Azure Disk Encryption pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Používá funkci [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytování šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure (VM). Integruje se také s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , která vám usnadní kontrolu a správu klíčů a tajných kódů disku a zajišťuje, aby všechna data na discích virtuálních počítačů byla v klidovém stavu zašifrovaná v úložišti Azure Storage. Azure Disk Encryption pro virtuální počítače s Windows a Linux je všeobecně dostupná ve všech veřejných oblastech Azure a oblasti Azure Government pro standardní virtuální počítače a virtuální počítače s Azure Premium Storage. 

Pokud používáte Azure Security Center, budete upozorněni, pokud máte virtuální počítače, které nejsou šifrovány. Zobrazit výstrahy jako vysokou závažností a doporučuje se pro šifrování tyto virtuální počítače.

![Upozornění šifrování disku Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Některá doporučení může zvýšit dat, sítě, nebo využití výpočetních prostředků a výsledkem další náklady na licence nebo předplatné.


## <a name="encryption-scenarios"></a>Šifrovací scénáře

Pomocí Azure Disk Encryption můžete řešit požadavky organizace na zabezpečení a dodržování předpisů díky zabezpečení virtuálních počítačů Azure v klidovém prostředí pomocí standardní šifrovací technologie v oboru. Virtuální počítače můžete také nakonfigurovat tak, aby se spouštěly v rámci klíčů a zásad řízených zákazníkem (BYOK), a auditovat používání těchto klíčů v trezoru klíčů.

Azure Disk Encryption podporuje následující scénáře zákazníků:

* Povolování a zakazování šifrování u nových virtuálních počítačů vytvořených z podporovaných imagí Galerie Azure.
* Povolování a zakazování šifrování u stávajících virtuálních počítačů, které běží v Azure.
* Povolování a zakazování šifrování pro nové virtuální počítače s Windows vytvořené z předem zašifrovaného virtuálního pevného disku a šifrovacích klíčů.
* Povolení a zakázání šifrování ve Windows Virtual Machine Scale Sets.
* Povolování a zakazování šifrování u datových jednotek pro systémy Linux Virtual Machine Scale Sets.
* Povolení a zakázání šifrování virtuálních počítačů se spravovanými disky.
* Aktualizuje se nastavení šifrování stávajícího šifrovaného a nePremium Storageho virtuálního počítače na úrovni Premium.
* Zálohování a obnovení šifrovaných virtuálních počítačů.
* Využijte vlastní šifrování (BYOE) a přineste si vlastní klíče (BYOK), ve kterých zákazníci používají vlastní šifrovací klíče a ukládají je do trezoru klíčů Azure.

Podporuje také následující scénáře pro virtuální počítače, pokud jsou povolené v Microsoft Azure:

* Integrace se službou Azure Key Vault.
* [Virtuální počítače úrovně Standard](https://azure.microsoft.com/pricing/details/virtual-machines/) , které splňují [minimální požadavky na paměť](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Povolení šifrování na virtuálních počítačích s Windows a Linux, spravovaných discích a virtuálních počítačích sady škálování z podporovaných imagí Galerie Azure
* Zakázání šifrování v operačních systémech a datových jednotkách pro virtuální počítače s Windows, virtuální počítače s nastavenou velikostí a virtuální počítače se spravovanými disky.
* Zakážete šifrování na datových jednotkách pro virtuální počítače se systémem Linux, virtuální počítače s možností škálování a virtuální počítače se spravovanými disky.
* Povoluje se šifrování na virtuálních počítačích, na kterých běží klientský operační systém Windows.
* Povoluje se šifrování u svazků s cestami připojení.
* Povolení šifrování pro virtuální počítače se systémem Linux, které jsou konfigurovány pomocí diskového disku RAID, pomocí mdadm.
* Povolení šifrování u virtuálních počítačů se systémem Linux, které pro datové disky používají LVM
* Povolení šifrování na discích s operačním systémem Linux a datových disků.

   > [!NOTE]
   > Šifrování jednotky operačního systému u některých Linuxových distribucích se nepodporuje. Další informace najdete v [tématu Podporované operační systémy Azure Disk Encryption: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Povolení šifrování u virtuálních počítačů, které jsou nakonfigurované s prostory úložiště Windows počínaje Windows serverem 2016. Prostory úložiště s přímým přístupem (S2D) ještě není podporovaný.
* Zálohování a obnovení šifrovaných virtuálních počítačů pro KEK (Key Encryption Key) i pro jiné scénáře než KEK.

Azure Disk Encryption nefunguje v následujících scénářích, funkcích a technologiích:

* Šifrování virtuálních počítačů nebo virtuálních počítačů na úrovni Basic vytvořených prostřednictvím metody vytváření virtuálních počítačů
* Zakázání šifrování na jednotce operačního systému nebo datové jednotce virtuálního počítače se systémem Linux, pokud je jednotka operačního systému zašifrovaná.
* Šifrování jednotky operačního systému pro systém Linux Virtual Machine Scale Sets.
* Šifrování virtuálních počítačů s Windows nakonfigurovaných s využitím softwarových systémů RAID.
* Šifrování vlastních imagí na virtuálních počítačích se systémem Linux.
* Integrace s místním systémem správy klíčů.
* Soubory Azure (sdílený systém souborů).
* Network File System (NFS).
* Dynamické svazky.
* Dočasné disky s operačním systémem.

## <a name="encryption-features"></a>Funkce šifrování

Když povolíte a nasadíte Azure Disk Encryption pro virtuální počítače Azure, můžete nakonfigurovat následující funkce, které se mají povolit:

* Šifrování svazku operačního systému pro ochranu spouštěcího svazku v klidovém úložišti.
* Šifrování datových svazků pro ochranu datových svazků v klidovém úložišti.
* Zákaz šifrování v operačním systému a datových jednotkách pro virtuální počítače s Windows
* Zakázání šifrování na datových jednotkách pro virtuální počítače se systémem Linux (pouze v případě, že jednotka operačního systému není šifrovaná).
* Ochrana šifrovacích klíčů a tajných kódů v předplatném Azure Key Vault.
* Oznamuje se stav šifrování šifrovaného virtuálního počítače.
* Odebírá se nastavení konfigurace šifrování disku z virtuálního počítače.
* Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup.

Azure Disk Encryption pro virtuální počítače pro systémy Windows a Linux zahrnují:

* [Rozšíření šifrování disku pro systém Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Rozšíření šifrování disku pro Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Rutiny pro šifrování disků prostředí PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Rutiny pro šifrování disků v Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Šablony Azure Resource Manager Disk Encryption](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Není k dispozici bez dalších poplatků k šifrování disků virtuálních počítačů pomocí Azure Disk Encryption. Standardní [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) platí pro trezor klíčů, který se používá k ukládání šifrovacích klíčů. 

## <a name="encryption-workflow"></a>Pracovní postup šifrování

Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem, proveďte následující kroky:

1. Přihlásit se k povolení šifrování disků pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Pak zadejte konfiguraci šifrování pro povolení šifrování u nového virtuálního počítače.
   * Pro nové virtuální počítače, které jsou vytvořené z podporovaných imagí galerie, a stávající virtuální počítače, které už běží v Azure, poskytněte konfiguraci šifrování, aby se povolilo šifrování na virtuálním počítači.

1. Udělte platformě Azure přístup ke čtení materiálu šifrovacího klíče (šifrovací klíče BitLockeru pro systémy Windows a přístupové heslo pro Linux) z trezoru klíčů, aby se povolilo šifrování na VIRTUÁLNÍm počítači.

1. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.

   ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Dešifrování pracovního postupu
Pokud chcete zakázat šifrování disku pro virtuální počítače, proveďte následující kroky vysoké úrovně:

1. Zvolte Zakázání šifrování (dešifrování) na běžícím virtuálním počítači v Azure a určete konfiguraci dešifrování. Můžete vypnout pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure.

   Tento krok zakazuje šifrování operačního systému nebo datového svazku nebo obojího na spuštěném virtuálním počítači s Windows. Jak je uvedeno v předchozí části, zakazuje šifrování disku operačního systému Linux se nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem jako disk s operačním systémem není šifrovaný.

1. Azure aktualizuje model služby virtuálních počítačů a virtuální počítač je označený jako dešifrovaný. Obsah virtuálního počítače už jsou v klidovém stavu zašifrovaná.

   > [!NOTE]
   > Operace šifrování zakázání nedojde k odstranění trezoru klíčů a šifrování klíče materiál (šifrovací klíče nástroje BitLocker systému Windows) nebo přístupové heslo pro Linux.
   >
   > Zakazuje šifrování disku operačního systému Linux se nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem.
   >
   > Zakazuje šifrování disku pro Linux není podporována, pokud je šifrované jednotky operačního systému.


## <a name="encryption-workflow-previous-release"></a>Šifrování pracovního postupu (předchozí verze)

Novou verzi sady Azure Disk Encryption eliminuje nutnost poskytnout parametr aplikace Azure Active Directory (Azure AD), povolit šifrování disku virtuálního počítače. Nové verze se už nevyžadují zadejte přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrován bez parametrů aplikace Azure AD, když použijete novou verzi. Virtuální počítače, které již byly šifrované pomocí aplikace Azure AD, parametry jsou stále podporovány a má pokračovat udržovat syntaxí Azure AD. Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem (předchozí verzi), proveďte následující kroky:

1. Vybírat scénáře uvedené ve scénáři šifrování [šifrovací scénáře](#encryption-scenarios) oddílu.

1. Přihlásit se k povolení šifrování disků pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Pak zadejte konfiguraci šifrování pro povolení šifrování u nového virtuálního počítače.
   * Pro nové virtuální počítače, které jsou vytvořené z Marketplace a stávající virtuální počítače, které už běží v Azure, zadejte konfiguraci šifrování, která povolí šifrování na virtuálním počítači.

1. Udělte platformě Azure přístup ke čtení materiálu šifrovacího klíče (šifrovací klíče BitLockeru pro systémy Windows a přístupové heslo pro Linux) z trezoru klíčů, aby se povolilo šifrování na VIRTUÁLNÍm počítači.

1. Poskytnout identitu aplikace služby Azure AD zapisovat materiálu k vašemu trezoru klíčů šifrovací klíč. Tento krok umožňuje šifrování na virtuálním počítači pro scénáře, které jsou uvedené v kroku 2.

1. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.


## <a name="terminology"></a>Terminologie
Následující tabulka popisuje některé běžné výrazy používané v dokumentaci ke službě Azure Disk Encryption:

| Terminologie | Definice |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) účet se používá k ověření, ukládání a načítání tajných kódů z trezoru klíčů. |
| Azure Key Vault | Key Vault je služba pro správu klíčů, kryptografické, který je založen na informace o zpracování normy FIPS (Federal) ověřených modulech hardwarového zabezpečení. Tyto normy pomáhají chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznaná technologie pro šifrování svazků Windows, která se používá k povolení šifrování disku na virtuálních počítačích s Windows. |
| BEK | Nástroj BitLocker šifrovacích klíčů (klíče BEK) se používají k zašifrování spouštěcí svazek s operačním systémem a datové svazky. BEKs jsou chráněné ve službě key vault jako tajné kódy. |
| Azure CLI | [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) je optimalizovaná pro správu prostředků Azure z příkazového řádku.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) je transparentní podsystém šifrování disku založený na systému Linux, který slouží k povolení šifrování disku na virtuálních počítačích se systémem Linux. |
| Klíč šifrování klíče (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete zadat modulu hardwarového zabezpečení (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutin prostředí Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Další postup

Informace o tom, jak začít, najdete v tématu [Azure Disk Encryption požadavky](azure-security-disk-encryption-prerequisites.md).

