---
title: Přehled – Azure Disk Encryption pro virtuální počítače IaaS | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Microsoft Azure Disk Encryption pro virtuální počítače IaaS.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 1674a54ea78e7fea2cc39cb26fefc52b8764bc4c
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200849"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption pro virtuální počítače IaaS

Microsoft Azure se zaměřuje na zajištění ochrany osobních údajů a suverenity dat. Azure umožňuje řídit data hostovaných v Azure prostřednictvím celou řadu technologie šifrování, řídit a spravovat šifrovací klíče a řízení a auditování přístupu k datům. Tento ovládací prvek poskytuje zákazníkům Azure s volnost ve výběru řešení, které nejlíp vyhovují jejich potřebám firmy. Tento článek vás seznámí s technologické řešení: "Azure Disk Encryption pro Windows a Linuxem v režimu IaaS virtual machines (VM)." Tato technologie pomáhá zabezpečit a chránit vaše data pro splnění vaší organizace na zabezpečení a závazky dodržování předpisů. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Přehled

Azure Disk Encryption je funkce, která umožňuje šifrovat disky Windows a virtuálních počítačů IaaS s Linuxem. Šifrování disku využívá standardní oborový [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování disků s operačním systémem a data. Toto řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure storage.

Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem jsou obecně dostupné ve všech veřejných oblastech Azure a oblastech Azure Government pro standardní virtuální počítače a virtuální počítače se službou Azure Premium Storage. Při použití řešení pro správu šifrování disku, můžete splnit následující obchodní potřeby:

* Virtuální počítače IaaS, jsou zabezpečené v klidovém stavu pomocí standardní šifrovací technologie k vyřešení organizační požadavky na zabezpečení a dodržování předpisů.
* Spuštění virtuálních počítačů IaaS pod správou zákazníka klíčů a zásad. Můžete auditovat jejich využití v trezoru klíčů.

Pokud používáte Azure Security Center, budete upozorněni, pokud máte virtuální počítače, které nejsou šifrovány. Zobrazit výstrahy jako vysokou závažností a doporučuje se pro šifrování tyto virtuální počítače.

![Upozornění šifrování disku Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Některá doporučení může zvýšit dat, sítě, nebo využití výpočetních prostředků a výsledkem další náklady na licence nebo předplatné.


## <a name="encryption-scenarios"></a>Šifrovací scénáře

Řešení šifrování disku podporuje následující scénáře zákazníka:

* Povoluje šifrování na nové virtuální počítače IaaS Windows vytvořené z předem šifrované virtuální pevný disk, šifrovacích klíčů.
* Povoluje šifrování na nové virtuální počítače IaaS vytvořené z imagí podporovaných Galerie Azure.
* Povoluje šifrování na existující virtuální počítače IaaS, které běží v Azure.
* Povoluje šifrování na škálovací sady virtuálních počítačů Windows.
* Povolte šifrování na datových jednotkách pro škálovací sady virtuálních počítačů Linux.
* Zakážete šifrování na virtuálních počítačích IaaS s Windows.
* Zakažte šifrování u datových jednotek virtuálních počítačů IaaS s Linuxem.
* Zakažte šifrování u škálovací sady virtuálních počítačů Windows.
* Zakažte šifrování u datových jednotek pro škálovací sady virtuálních počítačů Linux.
* Povolte šifrování spravovaného disku virtuální počítače.
* Aktualizace nastavení šifrování existující šifrované Premium a Premium Storage VM.
* Zálohování a obnovení šifrovaných virtuálních počítačů.

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud jsou povolena ve službě Microsoft Azure:

* Integrace se službou Azure Key Vault.
* Úroveň Standard virtuálních počítačů: [A, D, DS, G, GS, F a tak dále, řadu virtuálních počítačů IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/). [Virtuální počítače s Linuxem](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) v rámci těchto úrovních, musí splňovat minimální požadovaná paměť 7 GB.
* Povolení šifrování na Windows a virtuálních počítačů IaaS s Linuxem, spravovaný disk a škálovací sady virtuálních počítačů podporované Image Galerie Azure.
* Zakázat šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS s Windows, škálování virtuální počítače a spravovaných disků virtuálních počítačů.
* Zakázat šifrování na datových jednotkách pro Linuxové virtuální počítače IaaS, škálování virtuální počítače a spravovaných disků virtuálních počítačů.
* Povoluje šifrování na virtuální počítače IaaS, která spustí klientským operačním systémem Windows.
* Povolte šifrování na svazcích s cestami k připojení.
* Povoluje šifrování na virtuální počítače s Linuxem, které jsou nakonfigurované pro disk s použitím mdadm prokládání (RAID).
* Povoluje šifrování na virtuální počítače s Linuxem používající LVM pro datové disky.
* Povoluje šifrování na operační systém Linux virtuálního počítače a datové disky.

   > [!NOTE]
   > Šifrování jednotky operačního systému u některých Linuxových distribucích se nepodporuje. Další informace najdete v tématu [nejčastější dotazy týkající se Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) článku.
   
* Povolte šifrování na virtuálních počítačích s Windows, které jsou nakonfigurované pro prostory úložiště ve Windows.
* Aktualizace nastavení šifrování pro existující šifrované Premium a Premium Storage VM.
* Zálohování a obnovení šifrovaných virtuálních počítačů pro scénáře bez KEK a šifrovací klíč klíče (KEK).
* Všechny veřejné Azure a Azure Government oblasti jsou podporovány.

Toto řešení nepodporuje následující scénáře, funkce a technologie:

* Úroveň Basic virtuální počítače IaaS.
* Zakážete šifrování na disku s operačním systémem pro virtuální počítače IaaS s Linuxem.
* Zakážete šifrování na datový disk, když je šifrované jednotky operačního systému pro virtuální počítače IaaS s Linuxem.
* Virtuální počítače IaaS, které jsou vytvořeny pomocí klasické metody vytvoření virtuálního počítače.
* Povolte šifrování zákazníka vlastních imagí na virtuální počítače IaaS s Linuxem.
* Integrace s vaší místní systémem správy klíčů.
* Soubory Azure (sdílený systém souborů).
* Network File System (NFS).
* Dynamické svazky.
* Windows VMs, které jsou nakonfigurované pro systémy založené na softwaru diskového pole RAID.

## <a name="encryption-features"></a>Funkce šifrování

Když povolíte a nasadíte Disk Encryption pro virtuální počítače Azure IaaS, tyto funkce jsou povolené v závislosti na poskytnutá konfigurace:

* Šifrování svazku operačního systému k ochraně spouštěcího svazku v klidovém stavu ve službě storage.
* Šifrování datové svazky k ochraně objemy dat v klidovém stavu ve službě storage.
* Zakážete šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS s Windows.
* Zakažte šifrování u datových jednotek virtuálních počítačů IaaS s Linuxem (jenom v případě není zašifrovaný jednotky operačního systému).
* Chrání šifrovací klíče a tajné kódy ve vašem předplatném Azure Key Vault.
* Zaznamenat stav šifrování šifrovaných virtuálních počítačů IaaS.
* Odeberte nastavení konfigurace šifrování disku z virtuálního počítače IaaS.
* Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup.

Zahrnuje řešení Azure Disk Encryption pro virtuální počítače IaaS pro řešení Windows a Linux:

* Rozšíření disk encryption pro Windows.
* Rozšíření šifrování disku pro Linux.
* Rutiny Powershellu šifrování disku.
* Rutiny Azure CLI šifrování disku.
* Šablony Azure Resource Manageru šifrování disku.

Řešení Azure Disk Encryption je podporována ve virtuálních počítačích IaaS, která spustí Windows nebo Linux OS. Další informace o podporovaných operačních systémech najdete v článku [požadavky](azure-security-disk-encryption-prerequisites.md) článku.

> [!NOTE]
> Není k dispozici bez dalších poplatků k šifrování disků virtuálních počítačů pomocí Azure Disk Encryption. Standardní [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) platí pro trezor klíčů, který se používá k ukládání šifrovacích klíčů. 


## <a name="encryption-workflow"></a>Pracovní postup šifrování

Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem, proveďte následující kroky:

1. Vybírat scénáře uvedené ve scénáři šifrování [šifrovací scénáře](#encryption-scenarios) oddílu.

1. Přihlásit se k povolení šifrování disků pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač IaaS.
   * Pro nové virtuální počítače, které jsou vytvořeny z webu Marketplace a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolit šifrování na virtuálním počítači IaaS.

1. Udělte přístup na platformu Azure ke čtení materiál klíče šifrování (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači IaaS.

1. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.

   ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Dešifrování pracovního postupu
Pokud chcete zakázat šifrování disků pro virtuální počítače IaaS, proveďte následující postup vysoké úrovně:

1. Zvolte můžete zakázat šifrování (dešifrování) spuštěného virtuálního počítače IaaS v Azure a zadejte požadovanou konfiguraci dešifrování. Můžete vypnout pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure.

   Tento krok zakazuje šifrování operačního systému nebo objem dat nebo na spuštěný virtuální počítač IaaS Windows. Jak je uvedeno v předchozí části, zakazuje šifrování disku operačního systému Linux se nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem jako disk s operačním systémem není šifrovaný.

1. Aktualizace Azure modelu služby virtuálních počítačů a virtuálních počítačů IaaS je označen jako dešifrovat. Obsah virtuálního počítače už jsou v klidovém stavu zašifrovaná.

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

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač IaaS.
   * Pro nové virtuální počítače, které jsou vytvořeny z webu Marketplace a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolit šifrování na virtuálním počítači IaaS.

1. Udělte přístup na platformu Azure ke čtení materiál klíče šifrování (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači IaaS.

1. Poskytnout identitu aplikace služby Azure AD zapisovat materiálu k vašemu trezoru klíčů šifrovací klíč. Tento krok povoluje šifrování na virtuálním počítači IaaS pro scénáře uvedené v kroku 2.

1. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.


## <a name="terminology"></a>Terminologie
Následující tabulka definuje některých běžných termínů, které se používají v této technologie:

| Terminologie | Definice |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) účet se používá k ověření, ukládání a načítání tajných kódů z trezoru klíčů. |
| Azure Key Vault | Key Vault je služba pro správu klíčů, kryptografické, který je založen na informace o zpracování normy FIPS (Federal) ověřených modulech hardwarového zabezpečení. Tyto normy pomáhají chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| BitLocker |[Nástroj BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznán odvětví Windows svazku šifrovací technologie, která se používá k povolení šifrování disku ve virtuálních počítačích IaaS Windows. |
| BEK | Nástroj BitLocker šifrovacích klíčů (klíče BEK) se používají k zašifrování spouštěcí svazek s operačním systémem a datové svazky. BEKs jsou chráněné ve službě key vault jako tajné kódy. |
| Azure CLI | [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) je optimalizovaná pro správu prostředků Azure z příkazového řádku.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) je podsystém založených na Linuxu nebo transparentního šifrování disků, který se používá k povolení šifrování disků na virtuální počítače IaaS s Linuxem. |
| KEK | Šifrovací klíč klíče (KEK) je asymetrický klíč (RSA 2048), který vám pomůže chránit nebo zabalovat tajné kódy. Můžete zadat modulu hardwarového zabezpečení (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutin prostředí Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
