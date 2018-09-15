---
title: Azure Disk Encryption pro virtuální počítače IaaS – přehled | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Microsoft Azure Disk Encryption pro virtuální počítače IaaS.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/14/2018
ms.openlocfilehash: 193aa8f87a90eb7bbf1e2c49132ad480881d41fe
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633465"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption pro virtuální počítače IaaS 
Microsoft Azure platí závazek k zajištění ochrany osobních údajů, suverenita dat a umožňuje řízení hostované v Azure celou řadu technologie šifrování, řídit a spravovat šifrovací klíče dat a dat přístup k řízení a auditu. Tento ovládací prvek poskytuje zákazníkům Azure vybrat řešení, které nejlíp vyhovují jejich potřebám firmy. Tento článek vás seznámí s technologických řešeních "Disk Encryption pro Windows a Linux virtuálních počítačů Azure IaaS", k ochraně a chránit vaše data ke splnění požadavků vaší organizace na zabezpečení a závazky dodržování předpisů. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Přehled
Azure Disk Encryption (ADE) je funkce, která umožňuje šifrovat disky virtuálních počítačů Windows a Linuxem v režimu IaaS. ADE využívá standardní oborový [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování disků s operačním systémem a data. Toto řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure storage.

Azure disk encryption pro Windows a virtuálních počítačů IaaS s Linuxem je v **všeobecné dostupnosti** ve všech veřejných oblastech Azure a oblastech AzureGov pro standardní virtuální počítače a virtuální počítače s premium storage. Při použití řešení Azure Disk Encryption-management, můžete splnit následující obchodní potřeby:

* Virtuální počítače IaaS, jsou zabezpečené v klidovém stavu pomocí standardní šifrovací technologie adresu zabezpečení organizace a požadavky na dodržování předpisů.
* Spuštění virtuálních počítačů IaaS v části klíče řídí zákazníka a zásady a auditovat jejich využití v trezoru klíčů.


Pokud používáte Azure Security Center, bude ho můžete výstrahu, pokud máte virtuální počítače, které nejsou šifrovány. Tyto výstrahy se zobrazují jako upozornění s vysokou závažností. Doporučuje se tyto virtuální počítače zašifrovat.
![Upozornění šifrování disku Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné.


## <a name="encryption-scenarios"></a>Šifrovací scénáře
Řešení Azure Disk Encryption podporuje následující scénáře zákazníka:

* Povoluje šifrování na nové virtuální počítače IaaS Windows vytvořené z předem šifrované virtuální pevný disk, šifrovacích klíčů 
* Povoluje šifrování na nové virtuální počítače IaaS vytvořené z imagí podporovaných Galerie Azure
* Povoluje šifrování na existující virtuální počítače IaaS v Azure
* Povoluje šifrování na škálovací sady virtuálních počítačů Windows
* Povolit šifrování na datových jednotkách pro škálovací sady virtuálních počítačů Linux
* Zakažte šifrování u virtuálních počítačů IaaS Windows
* Zakažte šifrování u datových jednotek virtuálních počítačů IaaS s Linuxem
* Zakažte šifrování u škálovací sady virtuálních počítačů Windows
* Zakažte šifrování u datových jednotek pro škálovací sady virtuálních počítačů Linux
* Povolit šifrování spravovaného disku virtuální počítače
* Aktualizace nastavení šifrování existující šifrované premium a neprémiové úložiště virtuálního počítače
* Zálohování a obnovení šifrovaných virtuálních počítačů

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud jsou povolena ve službě Microsoft Azure:

* Integrace se službou Azure Key Vault
* Virtuální počítače úrovně Standard: [A, D, DS, G, GS, F a podobně řady virtuálních počítačů IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * [Virtuální počítače s Linuxem](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) v rámci těchto úrovních, musí splňovat minimální požadovaná paměť 7 GB
* Povolení šifrování na Windows a virtuálních počítačů IaaS s Linuxem, spravovaný disk a škálovací sady virtuálních počítačů z podporované Image Galerie Azure
* Zakázat šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS s Windows, škálování virtuální počítače a spravovaných disků virtuálních počítačů
* Zakázat šifrování na datových jednotkách pro Linuxové virtuální počítače IaaS, škálování virtuální počítače a spravovaných disků virtuálních počítačů
* Povoluje šifrování na virtuální počítače IaaS s klientským operačním systémem Windows
* Povolit šifrování na svazcích s cestami k připojení
* Povoluje šifrování na nakonfigurovanou disků virtuálních počítačů s Linuxem pomocí mdadm prokládání (RAID)
* Povoluje šifrování na virtuální počítače s Linuxem pomocí LVM datových disků
* Povoluje šifrování na operační systém Linux virtuálního počítače a datového disku 
* Povolit šifrování na virtuálních počítačích s Windows nakonfigurovaný s prostory úložiště
* Aktualizace nastavení šifrování existující šifrované premium a neprémiové úložiště virtuálního počítače
* Zálohování a obnovení šifrovaných virtuálních počítačů, ne KEK a KEK scénáře (KEK - šifrovací klíč klíče)
* Všechny veřejné Azure a AzureGov oblasti jsou podporovány.

Toto řešení nepodporuje následující scénáře, funkce a technologie:

* Úroveň Basic virtuálních počítačů IaaS
* Zakázáním šifrování na disku s operačním systémem pro virtuální počítače IaaS s Linuxem
* Zakázáním šifrování na datové jednotky, pokud je šifrované jednotky operačního systému pro virtuální počítače Iaas s Linuxem
* Virtuální počítače IaaS, které jsou vytvořeny pomocí klasické metody vytvoření virtuálního počítače
* Povolení šifrování na vlastních imagích virtuálních počítačů IaaS s Linuxem zákazníka
* Integrace s vaší místní služba správy klíčů
* Služba soubory Azure (sdílený systém souborů)
* Network File System (NFS)
* Dynamické svazky
* Virtuální počítače s Windows, které jsou nakonfigurované pro systémy založené na softwaru diskového pole RAID

## <a name="encryption-features"></a>Funkce šifrování
Když povolíte a nasadíte Azure Disk Encryption pro virtuální počítače Azure IaaS, jsou povoleny následující funkce, v závislosti na konfiguraci k dispozici:

* Šifrování svazku operačního systému k ochraně spouštěcího svazku v klidovém stavu ve službě storage
* Šifrování datové svazky k ochraně objemy dat v klidovém stavu ve službě storage
* Zakázáním šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS s Windows
* Zakázáním šifrování na datových jednotkách virtuálních počítačů IaaS s Linuxem (pouze v případě není šifrované jednotky operačního systému)
* Pomáhá chránit šifrovací klíče a tajné kódy ve vašem předplatném služby key vault
* Vytváření sestav stav šifrování šifrovaných virtuálních počítačů IaaS
* Odebrání nastavení konfigurace šifrování disku z virtuálního počítače IaaS
* Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup

Zahrnuje řešení Azure Disk Encryption pro virtuální počítače IaaS pro řešení Windows a Linux:

* Rozšíření disk encryption pro Windows.
* Rozšíření šifrování disku pro Linux.
* Šifrování disku, rutin prostředí PowerShell.
* Šifrování disku rutiny rozhraní příkazového řádku Azure (CLI).
* Šablony Azure Resource Manageru šifrování disku.

Řešení Azure Disk Encryption je podporována u virtuálních počítačů IaaS s Windows nebo Linux OS. Další informace o podporovaných operačních systémech najdete v článku [požadavky](azure-security-disk-encryption-prerequisites.md) článku.

> [!NOTE]
> Neexistuje žádný další poplatek pro šifrování disků virtuálních počítačů pomocí Azure Disk Encryption. Standardní [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) platí pro trezor klíčů používá k ukládání šifrovacích klíčů. 


## <a name="encryption-workflow"></a>Pracovní postup šifrování

 Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem, proveďte následující kroky:

1. Zvolte scénáři šifrování z předchozí šifrovací scénáře.
2. Vyjádřit výslovný souhlas s povolením disk encryption pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač IaaS.
   * Pro nové virtuální počítače, které jsou vytvořeny z webu Marketplace a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolit šifrování na virtuálním počítači IaaS.

3. Udělte přístup na platformu Azure ke čtení materiálu šifrovací klíč (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači IaaS.

4. Azure aktualizuje model služby virtuálního počítače se šifrováním, konfigurace služby key vault a nastaví šifrovaný virtuální počítač.

 ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Dešifrování pracovního postupu
Pokud chcete zakázat šifrování disků pro virtuální počítače IaaS, proveďte následující postup vysoké úrovně:

1. Zvolte můžete zakázat šifrování (dešifrování) spuštěného virtuálního počítače IaaS v Azure a zadejte požadovanou konfiguraci dešifrování. Můžete vypnout pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure.

 Tento krok zakazuje šifrování operačního systému nebo objem dat nebo na spuštěný virtuální počítač IaaS Windows. Nicméně jak je uvedeno v předchozí části, zakazuje šifrování disku operačního systému Linux nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem jako disk s operačním systémem není šifrovaný.
2. Aktualizuje model služby virtuálních počítačů Azure a virtuálních počítačů IaaS je označen dešifrovaný. Obsah virtuálního počítače už jsou v klidovém stavu zašifrovaná.

> [!NOTE]
> Operace disable šifrování nedojde k odstranění trezoru klíčů a šifrování klíče materiál (šifrovací klíče nástroje BitLocker systému Windows) nebo přístupové heslo pro Linux.
 > Zakazuje šifrování disku operačního systému Linux se nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem.
Zakazuje šifrování disku pro Linux není podporována, pokud je šifrované jednotky operačního systému.


## <a name="encryption-workflow-previous-release"></a>Šifrování pracovního postupu (předchozí verze)

Novou verzi sady Azure disk encryption eliminuje požadavek na poskytnutí parametrem aplikace Azure AD povolit šifrování disku virtuálního počítače. Nové verze se už nevyžadují zadejte přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrován bez parametrů aplikace Azure AD pomocí nové verze. Virtuální počítače, které již byly šifrované pomocí aplikace Azure AD, parametry jsou stále podporovány a má pokračovat udržovat syntaxí AAD. Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem (předchozí verzi), proveďte následující kroky:

1. Zvolte scénáři šifrování z předchozí šifrovací scénáře.
2. Vyjádřit výslovný souhlas s povolením disk encryption pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač IaaS.
   * Pro nové virtuální počítače, které jsou vytvořeny z webu Marketplace a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolit šifrování na virtuálním počítači IaaS.

3. Udělte přístup na platformu Azure ke čtení materiálu šifrovací klíč (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači IaaS.

4. Poskytnout identitu aplikace služby Azure Active Directory (Azure AD) pro zápis materiálu k vašemu trezoru klíčů šifrovací klíč. Tento postup umožňuje šifrování na virtuálním počítači IaaS pro scénáře uvedené v kroku 2.

5. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.


## <a name="terminology"></a>Terminologie
Vysvětlení některých běžných termínů používaných v této technologie, použijte následující tabulku terminologie:

| Terminologie | Definice |
| --- | --- |
| Azure AD | Azure AD je [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Účet Azure AD se používá pro ověřování, ukládání a načítání tajných kódů z trezoru klíčů. |
| Azure Key Vault | Key Vault je služba správy klíčů, kryptografické, který je založen na informace o zpracování normy FIPS (Federal) ověřit modulů hardwarového zabezpečení, které pomáhají chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v tématu [služby Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| BitLocker |[Nástroj BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznán odvětví Windows svazku šifrovací technologie, která se používá k povolení šifrování disku ve virtuálních počítačích IaaS Windows. |
| BEK | Šifrování klíče Bitlockeru se používají k zašifrování spouštěcí svazek s operačním systémem a datové svazky. Klíče Bitlockeru jsou chráněné ve službě key vault jako tajné kódy. |
| Rozhraní příkazového řádku | Zobrazit [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) je podsystém založených na Linuxu nebo transparentního šifrování disků, který se používá k povolení šifrování disků na virtuální počítače IaaS s Linuxem. |
| KEK | Šifrovací klíč klíče je asymetrický klíč (RSA 2048), který vám pomůže chránit nebo zabalovat tajné kódy. Můžete zadat modulu hardwarového zabezpečení (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další informace najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| PS rutiny | Zobrazit [rutin prostředí Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Požadavky na službu Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
