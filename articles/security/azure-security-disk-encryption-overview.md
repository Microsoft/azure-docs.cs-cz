---
title: Co je Azure Disk Encryption?
description: Tento článek obsahuje přehled služby Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 58c5c8321e505fe2c1c7d19c58fe0d031b75b3e4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294865"
---
# <a name="azure-disk-encryption-overview"></a>Přehled služby Azure Disk Encryption

Azure Disk Encryption pomáhá zabezpečit a chránit vaše data, aby splňovala závazky vaší organizace zabezpečení a dodržování předpisů z hlediska. Používá [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování pro operační systém a datové disky virtuálních počítačů Azure (VM). Je integrovaná taky s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat klíče pro šifrování disků a tajné klíče a zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu v úložišti Azure. Azure Disk Encryption pro Windows a virtuální počítače s Linuxem jsou obecně dostupné ve všech veřejných oblastech Azure a oblastech Azure Government pro standardní virtuální počítače a virtuální počítače se službou Azure Premium Storage. 

Pokud používáte Azure Security Center, budete upozorněni, pokud máte virtuální počítače, které nejsou šifrovány. Zobrazit výstrahy jako vysokou závažností a doporučuje se pro šifrování tyto virtuální počítače.

![Upozornění šifrování disku Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Některá doporučení může zvýšit dat, sítě, nebo využití výpočetních prostředků a výsledkem další náklady na licence nebo předplatné.


## <a name="encryption-scenarios"></a>Šifrovací scénáře

S Azure Disk Encryption je vyřešit požadavky na dodržování předpisů a zabezpečení organizace díky zabezpečení vašich virtuálních počítačů Azure v klidovém stavu pomocí standardní šifrovací technologie. Můžete také nakonfigurovat virtuální počítače ke spuštění pod správou zákazníka klíče a zásady (BYOK) a auditovat využití klíčů v trezoru klíčů.

Azure Disk Encryption podporuje následující scénáře zákazníka:

* Povolení a zákaz šifrování na nové virtuální počítače vytvořené z imagí podporovaných Galerie Azure.
* Povolení a zákaz šifrování v existujících virtuálních počítačů, které běží v Azure.
* Povolení a zákaz šifrování na nové virtuální počítače Windows vytvořené z předem šifrované virtuální pevný disk a šifrovacích klíčů.
* Povolení a zákaz šifrování u škálování virtuálního počítače Windows nastaví.
* Povolení a zákaz šifrování dat disků pro škálovací sady virtuálních počítačů Linux.
* Povolení a zákaz šifrování spravovaného disku virtuální počítače.
* Aktualizace nastavení šifrování existující šifrované Premium a Premium Storage VM.
* Zálohování a obnovení šifrovaných virtuálních počítačů.
* Přineste vlastní šifrování (BYOE) a scénáře typu přineste si vlastní klíč (BYOK), ve kterých zákazníkům používat jejich vlastní šifrovací klíče a uložit je do služby Azure key vault.

Také podporuje následující scénáře pro virtuální počítače při jsou povolena ve službě Microsoft Azure:

* Integrace se službou Azure Key Vault.
* [Virtuální počítače úrovně standard](https://azure.microsoft.com/pricing/details/virtual-machines/) , které splňují [minimální požadovaná paměť](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Povolení šifrování na Windows a virtuální počítače s Linuxem, spravovaný disk a škálovací sady virtuálních počítačů podporované Image Galerie Azure.
* Zakazuje šifrování u operačního systému a datové disky pro virtuální počítače s Windows, škálování virtuální počítače a spravovaných disků virtuálních počítačů.
* Zakázat šifrování na datových jednotkách pro virtuální počítače s Linuxem, škálování virtuální počítače a spravovaných disků virtuálních počítačů.
* Povolení šifrování na virtuální počítače s klientským operačním systémem Windows.
* Povolení šifrování na svazcích s cestami k připojení.
* Povolení šifrování na virtuální počítače s Linuxem, které jsou nakonfigurované pro disk s použitím mdadm prokládání (RAID).
* Povolení šifrování na virtuální počítače s Linuxem používající LVM pro datové disky.
* Povolení šifrování na operační systém Linux virtuálního počítače a datové disky.

   > [!NOTE]
   > Šifrování jednotky operačního systému u některých Linuxových distribucích se nepodporuje. Další informace najdete v tématu [Azure Disk Encryption podporované operační systémy: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Povolení šifrování na virtuálních počítačích, které mají nakonfigurované prostory úložiště Windows od verze Windows serveru 2016.
* Zálohování a obnovení šifrovaných virtuálních počítačů pro scénáře bez KEK a šifrovací klíč klíče (KEK).

Azure Disk Encryption nefunguje pro následující scénáře, funkce a technologie:

* Šifrování na úrovni basic virtuální počítač nebo virtuální počítače vytvořené prostřednictvím klasické metody vytvoření virtuálního počítače.
* Zakázáním šifrování na disku operačního systému nebo datový disk virtuálního počítače s Linuxem při šifrované jednotky operačního systému.
* Šifrování jednotky operačního systému pro škálovací virtuálních počítačů Linux nastaví.
* Šifrování Windows virtuální počítače nakonfigurované systémy založené na softwaru diskového pole RAID.
* Šifrování vlastních imagí na virtuální počítače s Linuxem.
* Integrace s v místním systémem správy klíčů.
* Soubory Azure (sdílený systém souborů).
* Network File System (NFS).
* Dynamické svazky.

## <a name="encryption-features"></a>Funkce šifrování

Když povolíte a nasadíte Azure Disk Encryption pro virtuální počítače Azure, můžete nakonfigurovat následující funkce, aby byla povolená:

* Šifrování svazku operačního systému k ochraně spouštěcího svazku v klidovém stavu ve službě storage.
* Šifruje datové svazky k ochraně objemy dat v klidovém stavu ve službě storage.
* Zakázáním šifrování na jednotkách operačního systému a dat pro virtuální počítače s Windows.
* Zakázáním šifrování dat disky pro virtuální počítače s Linuxem (jenom v případě není zašifrovaný jednotky operačního systému).
* Pomáhá chránit šifrovací klíče a tajné kódy ve vašem předplatném Azure Key Vault.
* Hlásí stav šifrování šifrovaných virtuálních počítačů.
* Odebírá se nastavení konfigurace šifrování disku z virtuálního počítače.
* Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup.

Zahrnuje řešení Azure Disk Encryption pro virtuální počítače pro Windows a Linux:

* [Rozšíření disk encryption pro Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Rozšíření šifrování disku pro Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Rutiny Powershellu disk encryption](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Rutiny rozhraní příkazového řádku Azure disk encryption](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Šablony Azure Resource Manageru disk encryption](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Není k dispozici bez dalších poplatků k šifrování disků virtuálních počítačů pomocí Azure Disk Encryption. Standardní [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) platí pro trezor klíčů, který se používá k ukládání šifrovacích klíčů. 

## <a name="encryption-workflow"></a>Pracovní postup šifrování

Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem, proveďte následující kroky:

1. Přihlásit se k povolení šifrování disků pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač.
   * Pro nové virtuální počítače, které jsou vytvořené z imagí podporovaných galerie a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolení šifrování na virtuálním počítači.

1. Udělte přístup na platformu Azure ke čtení materiál klíče šifrování (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači.

1. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.

   ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Dešifrování pracovního postupu
Pokud chcete zakázat šifrování disků pro virtuální počítače, proveďte následující postup vysoké úrovně:

1. Vybrat můžete zakázat šifrování (dešifrování) spuštěného virtuálního počítače v Azure a zadejte požadovanou konfiguraci dešifrování. Můžete vypnout pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure.

   Tento krok zakazuje šifrování operačního systému nebo objem dat nebo na spuštěný virtuální počítač Windows. Jak je uvedeno v předchozí části, zakazuje šifrování disku operačního systému Linux se nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem jako disk s operačním systémem není šifrovaný.

1. Aktualizace Azure modelu služby virtuálního počítače a virtuálního počítače je označena jako dešifrovat. Obsah virtuálního počítače už jsou v klidovém stavu zašifrovaná.

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

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač.
   * Pro nové virtuální počítače, které jsou vytvořeny z webu Marketplace a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolit šifrování na virtuálním počítači.

1. Udělte přístup na platformu Azure ke čtení materiál klíče šifrování (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači.

1. Poskytnout identitu aplikace služby Azure AD zapisovat materiálu k vašemu trezoru klíčů šifrovací klíč. Tento krok povoluje šifrování na virtuálním počítači pro scénáře uvedené v kroku 2.

1. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.


## <a name="terminology"></a>Terminologie
Následující tabulka definuje některé z běžných termínů používaných v dokumentaci k Azure disk encryption:

| Terminologie | Definice |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) účet se používá k ověření, ukládání a načítání tajných kódů z trezoru klíčů. |
| Azure Key Vault | Key Vault je služba pro správu klíčů, kryptografické, který je založen na informace o zpracování normy FIPS (Federal) ověřených modulech hardwarového zabezpečení. Tyto normy pomáhají chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| BitLocker |[Nástroj BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznán odvětví Windows svazku šifrovací technologie, která se používá k povolení šifrování disku na virtuálních počítačích s Windows. |
| BEK | Nástroj BitLocker šifrovacích klíčů (klíče BEK) se používají k zašifrování spouštěcí svazek s operačním systémem a datové svazky. BEKs jsou chráněné ve službě key vault jako tajné kódy. |
| Azure CLI | [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) je optimalizovaná pro správu prostředků Azure z příkazového řádku.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) je podsystém založených na Linuxu nebo transparentního šifrování disků, který se používá k povolení šifrování disků na virtuální počítače s Linuxem. |
| Šifrovací klíč klíče (KEK) | Asymetrický klíč (RSA 2048), který vám pomůže chránit nebo zabalovat tajné kódy. Můžete zadat modulu hardwarového zabezpečení (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutin prostředí Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Další postup

Abyste mohli začít, najdete v článku [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

