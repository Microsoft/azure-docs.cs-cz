---
title: Azure Disk Encryption pro Windows a virtuální počítače s Linuxem v režimu IaaS | Dokumentace Microsoftu
description: Tento článek obsahuje přehled Microsoft Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: devtiw
ms.openlocfilehash: f350716d0ca906376f3eadce9e117694ff14515c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "35756392"
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption pro Windows a virtuální počítače s Linuxem v režimu IaaS
Microsoft Azure je důrazně zaměřuje na zajištění ochrany osobních údajů, suverenita dat a umožňuje řídit, které jsou hostované v Azure data prostřednictvím řady pokročilé technologie šifrování, řídit a spravovat šifrovací klíče, řízení a auditování přístupu k datům. Zákazníci Azure získáte tak flexibilitu zvolit řešení, které nejlíp vyhovují jejich potřebám firmy. V tomto dokumentu jsme vás seznámí s novou technologii řešení "Řešení Azure Disk Encryption pro Windows a Linuxem v režimu IaaS VM na" k ochraně a chránit vaše data ke splnění požadavků vaší organizace na zabezpečení a závazky dodržování předpisů. Dokument poskytuje prostředí pro podrobné informace o tom, jak používat funkce šifrování disku Azure, včetně Podporované scénáře.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Přehled
Azure Disk Encryption je nová funkce, která umožňuje šifrovat disky virtuálních počítačů Windows a Linuxem v režimu IaaS. Azure Disk Encryption využívá standard odvětví v oblasti [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování pro operační systém a datové disky. Toto řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby key vault. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure storage.

Azure disk encryption pro Windows a virtuálních počítačů IaaS s Linuxem je teď v **všeobecné dostupnosti** ve všech veřejných oblastech Azure a oblastech AzureGov pro standardní virtuální počítače a virtuální počítače s premium storage.

> [!NOTE]
> Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné.


### <a name="encryption-scenarios"></a>Šifrovací scénáře
Řešení Azure Disk Encryption podporuje následující scénáře zákazníka:

* Povoluje šifrování na nové virtuální počítače IaaS vytvořené z předem šifrované virtuální pevný disk a šifrovací klíče
* Povoluje šifrování na nové virtuální počítače IaaS vytvořené z imagí podporovaných Galerie Azure
* Povoluje šifrování na existující virtuální počítače IaaS v Azure
* Zakažte šifrování u virtuálních počítačů IaaS Windows
* Zakažte šifrování u datových jednotek virtuálních počítačů IaaS s Linuxem
* Povolit šifrování spravovaného disku virtuální počítače
* Aktualizace nastavení šifrování existující šifrované premium a neprémiové úložiště virtuálního počítače
* Zálohování a obnovení šifrovaných virtuálních počítačů

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud je povolen v Microsoft Azure:

* Integrace se službou Azure Key Vault
* Virtuální počítače úrovně Standard: [A, D, DS, G, GS, F a podobně řady virtuálních počítačů IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Povoluje šifrování na Windows a Linuxové virtuální počítače IaaS a spravovaných disků virtuálních počítačů z podporované Image Galerie Azure
* Zakázat šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS s Windows a virtuální počítače spravovaného disku
* Zakázat šifrování na datových jednotkách pro Linuxové virtuální počítače IaaS a spravovaných disků virtuálních počítačů
* Povoluje šifrování na virtuální počítače IaaS s klientským operačním systémem Windows
* Povolit šifrování na svazcích s cestami k připojení
* Povoluje šifrování na nakonfigurovanou disků virtuálních počítačů s Linuxem pomocí mdadm prokládání (RAID)
* Povoluje šifrování na virtuální počítače s Linuxem pomocí LVM datových disků
* Povolit šifrování disků s operačním systémem a daty na Linuxu LVM 7.3 
* Povolit šifrování na virtuálních počítačích s Windows nakonfigurovaný s prostory úložiště
* Aktualizace nastavení šifrování existující šifrované premium a neprémiové úložiště virtuálního počítače
* Zálohování a obnovení šifrovaných virtuálních počítačů, ne KEK a KEK scénáře (KEK - šifrovací klíč klíče)
* Všechny veřejné Azure a AzureGov oblasti jsou podporovány.

Toto řešení nepodporuje následující scénáře, funkce a technologie:

* Úroveň Basic virtuálních počítačů IaaS
* Zakázáním šifrování na disku s operačním systémem pro virtuální počítače IaaS s Linuxem
* Zakázáním šifrování na datové jednotky, pokud je šifrované jednotky operačního systému pro virtuální počítače Iaas s Linuxem
* Virtuální počítače IaaS, které jsou vytvořeny pomocí klasické metody vytvoření virtuálního počítače
* Povoluje šifrování na Windows a virtuální počítače s Linuxem v režimu IaaS zákazníka vlastních imagí není podporován.
* Integrace s vaší místní služba správy klíčů
* Služba soubory Azure (sdílený systém souborů), Network File System (NFS), dynamických svazků a virtuálních počítačů s Windows, které jsou nakonfigurované pro systémy založené na softwaru diskového pole RAID

### <a name="encryption-features"></a>Funkce šifrování
Když povolíte a nasadíte Azure Disk Encryption pro virtuální počítače Azure IaaS, jsou povoleny následující funkce, v závislosti na konfiguraci k dispozici:

* Šifrování svazku operačního systému k ochraně spouštěcího svazku v klidovém stavu ve službě storage
* Šifrování datové svazky k ochraně objemy dat v klidovém stavu ve službě storage
* Zakázáním šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS s Windows
* Zakázáním šifrování dat (pouze v případě jednotky s operačním systémem není zašifrovaný) disky pro virtuální počítače IaaS s Linuxem
* Pomáhá chránit šifrovací klíče a tajné kódy ve vašem předplatném služby key vault
* Vytváření sestav stav šifrování šifrovaných virtuálních počítačů IaaS
* Odebrání nastavení konfigurace šifrování disku z virtuálního počítače IaaS
* Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup

Zahrnuje řešení Azure Disk Encryption pro virtuální počítače IaaS pro řešení Windows a Linux:

* Šifrování disku rozšíření pro Windows.
* Šifrování disku rozšíření pro Linux.
* Rutiny Powershellu šifrování disku.
* Disk šifrování rutiny rozhraní příkazového řádku Azure (CLI).
* Šifrování disku šablony Azure Resource Manageru.

Řešení Azure Disk Encryption je podporována u virtuálních počítačů IaaS s Windows nebo Linux OS. Další informace o podporovaných operačních systémů najdete v tématu "Požadavky" v tématu.

> [!NOTE]
> Neplatí žádné další poplatky pro šifrování disků virtuálních počítačů pomocí Azure Disk Encryption.

### <a name="value-proposition"></a>Návrh hodnoty
Při použití řešení Azure Disk Encryption-management, můžete splnit následující obchodní potřeby:

* Virtuální počítače IaaS jsou zabezpečené v klidovém stavu, protože adresa organizační požadavky na zabezpečení a dodržování předpisů pomocí standardní šifrovací technologie.
* Spuštění virtuálních počítačů IaaS v části klíče řídí zákazníka a zásady a auditovat jejich využití v trezoru klíčů.

### <a name="encryption-workflow"></a>Pracovní postup šifrování
Pokud chcete povolit disk encryption pro Windows a virtuální počítače s Linuxem, postupujte takto:

1. Zvolte scénáři šifrování z předchozí šifrovací scénáře.
2. Vyjádřit výslovný souhlas s povolením disk encryption pomocí šablony Azure Disk Encryption Resource Manageru, rutin prostředí PowerShell nebo rozhraní příkazového řádku a zadejte požadovanou konfiguraci šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrání šifrovaného virtuálního pevného disku do účtu úložiště a šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování pro povoluje šifrování na nový virtuální počítač IaaS.
   * Pro nové virtuální počítače, které jsou vytvořeny z webu Marketplace a stávající virtuální počítače, na kterých už běží v Azure zadejte konfiguraci šifrování pro povolit šifrování na virtuálním počítači IaaS.

3. Udělte přístup na platformu Azure ke čtení materiálu šifrovací klíč (šifrovací klíče nástroje BitLocker systému Windows) a heslo pro Linux z trezoru klíčů na povolit šifrování na virtuálním počítači IaaS.

4. Poskytnout identitu aplikace služby Azure Active Directory (Azure AD) pro zápis materiálu k vašemu trezoru klíčů šifrovací klíč. Tento postup umožňuje šifrování na virtuálním počítači IaaS pro scénáře uvedené v kroku 2.

5. Azure aktualizuje model služby virtuálního počítače s šifrováním a konfigurace služby key vault a nastaví šifrovaný virtuální počítač.

 ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Dešifrování pracovního postupu
Pokud chcete zakázat šifrování disků pro virtuální počítače IaaS, proveďte následující postup vysoké úrovně:

1. Vybrat můžete zakázat šifrování (dešifrování) spuštěného virtuálního počítače IaaS v Azure pomocí šablony Azure Disk Encryption Resource Manageru nebo rutiny prostředí PowerShell a zadejte požadovanou konfiguraci dešifrování.

 Tento krok zakazuje šifrování operačního systému nebo objem dat nebo na spuštěný virtuální počítač IaaS Windows. Nicméně jak je uvedeno v předchozí části, zakazuje šifrování disku operačního systému Linux nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem jako disk s operačním systémem není šifrovaný.
2. Aktualizuje model služby virtuálních počítačů Azure a virtuálních počítačů IaaS je označen dešifrovaný. Obsah virtuálního počítače už jsou v klidovém stavu zašifrovaná.

> [!NOTE]
> Operace disable šifrování nedojde k odstranění trezoru klíčů a šifrování klíče materiál (šifrovací klíče nástroje BitLocker systému Windows) nebo přístupové heslo pro Linux.
 > Zakazuje šifrování disku operačního systému Linux se nepodporuje. Dešifrování kroku je povoleno pouze pro datové jednotky na virtuální počítače s Linuxem.
Zakazuje šifrování disku pro Linux není podporována, pokud je šifrované jednotky operačního systému.

## <a name="prerequisites"></a>Požadavky
Dříve než povolíte Azure Disk Encryption ve virtuálních počítačích Azure IaaS pro podporované scénáře, které nebyly popsány v části "Přehled", viz následující požadavky:

* Musíte mít aktivní předplatné Azure platnou k vytváření prostředků v Azure v podporovaných oblastech.
* Azure Disk Encryption je podporováno v následujících verzích Windows serveru: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows serveru 2016.
* Azure Disk Encryption je podporováno v následujících verzích klienta Windows: klienta systému Windows 8 a Windows 10 klienta.

> [!NOTE]
> Pro Windows Server 2008 R2 musíte mít rozhraní .NET Framework 4.5 nainstalované před povolením šifrování v Azure. Můžete jej nainstalovat z webu Windows Update nainstalováním volitelnou aktualizaci Microsoft .NET Framework 4.5.2 x64 systémů Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Azure Disk Encryption je pouze podporované na konkrétní Galerie Azure Linux server distribucích a verzích.  Seznam aktuálně podporovaných verzí najdete [nejčastější dotazy týkající se Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq).

* Azure Disk Encryption vyžaduje, aby váš trezor klíčů a virtuální počítače jsou umístěny ve stejné oblasti Azure a předplatné.

> [!NOTE]
> Konfigurace prostředků v oblastech způsobí selhání při povolování funkce Azure Disk Encryption.

* Nastavení a konfigurace trezoru klíčů pro Azure Disk Encryption, najdete v části **nastavení a konfiguraci trezoru klíčů pro Azure Disk Encryption** v *požadavky* části tohoto článku.
* Nastavení a konfigurace aplikace Azure AD ve službě Azure Active directory pro Azure Disk Encryption, najdete v části **nastavení aplikace Azure AD ve službě Azure Active Directory** v *požadavky* část Tento článek.
* Nastavení a konfigurace zásady přístupu trezoru klíčů pro aplikaci Azure AD, najdete v části **nastavit zásady přístupu trezoru klíčů pro aplikaci Azure AD** v *požadavky* části tohoto článku.
* Příprava virtuálního pevného disku předem šifrované Windows, najdete v části **připravit předem šifrované virtuální pevný disk Windows** v *příloha*.
* Pokud chcete připravit předem šifrované linuxového virtuálního pevného disku, najdete v části **připravit předem šifrované linuxového virtuálního pevného disku** v *příloha*.
* Platforma Azure potřebuje přístup k šifrovacím klíčům a tajným kódům v trezoru klíčů, aby byly k dispozici k virtuálnímu počítači při spuštění a dešifruje svazek s operačním systémem virtuálního počítače. Chcete-li udělit oprávnění na platformě Azure, nastavte **EnabledForDiskEncryption** vlastnosti ve službě key vault. Další informace najdete v tématu **nastavení a konfiguraci trezoru klíčů pro Azure Disk Encryption** v dodatku.
* Tajný kód trezoru klíčů a adres URL KEK musí být označené verzí. Azure vynucuje toto omezení správy verzí. Platný tajný kód a adresy URL KEK viz následující příklady:

  * Příklad platná adresa URL tajného kódu:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příkladem platné adresy URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů služby key vault a KEK adresy URL. Příkladem adresy URL není podporováno a podporované služby key vault naleznete v následujících tématech:

  * Adresa URL nemůže být přijata trezoru klíčů  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adresa URL přijatelné služby key vault:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Povolit Azure Disk Encryption funkce, virtuální počítače IaaS musí splňovat následující požadavky na konfiguraci koncového bodu sítě:
  * Získá token pro připojení k vašemu trezoru klíčů, musí být schopný se připojit k Azure Active Directory koncový bod, virtuálních počítačů IaaS \[login.microsoftonline.com\].
  * Zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS může připojit ke koncovému bodu trezoru klíčů.
  * Virtuální počítač IaaS musí být schopný se připojit k koncový bod služby Azure storage, který je hostitelem úložiště rozšíření Azure a účet úložiště Azure, který je hostitelem souborů virtuálního pevného disku.

  > [!NOTE]
  > Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure na Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo pro povolení odchozích připojení k IP adres.
  >
  >Ke konfiguraci a přístup ke službě Azure Key Vault za bránou firewall (https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Použijte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [verzi Azure Powershellu](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Azure Disk Encryption nepodporuje [Azure PowerShell SDK verze 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Pokud se vám zobrazuje chybu související s využitím Azure Powershellu 1.1.0 naleznete v tématu [Azure Disk šifrování chyba související s Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Pokud chcete spouštět jakékoli příkazy rozhraní příkazového řádku Azure a přidružte ji k vašemu předplatnému Azure, je třeba nejprve nainstalovat rozhraní příkazového řádku Azure:
  * Instalace rozhraní příkazového řádku Azure a přidružte jej k vašemu předplatnému Azure najdete v tématu [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
  * Použití Azure CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru, najdete v článku [příkazy rozhraní příkazového řádku Azure v režimu Resource Manageru](../virtual-machines/azure-cli-arm-commands.md).

* Při šifrování spravovaného disku, je povinná požadovaných součástí se snímek spravovaného disku nebo zálohování disku mimo Azure Disk Encryption před povolením šifrování.  Bez předchozího provedení zálohy na místě všechny Neočekávaná chyba při šifrování může mít za následek disk a virtuální počítač nedostupný bez možnosti obnovení.  Set-AzureRmVMDiskEncryptionExtension není aktuálně zálohování spravovaných disků a skončí chybou, je-li použít proti spravovaný disk, pokud byl zadán parametr - skipVmBackup.  Tento parametr je bezpečné používat, pokud již byla provedena zálohy mimo Azure Disk Encryption.   Pokud je zadán parametr - skipVmBackup, rutina nebude vytvořit zálohu spravovaného disku před šifrování.  Z tohoto důvodu bude považován za povinný předpokladem Ujistěte se, že záložní kopie spravovaného disku virtuálního počítače je na místě před povolením Azure Disk Encryption v případě, že je později potřeba obnovení.  
> [!NOTE]
 > Parametr - skipVmBackup byste nikdy neměli používat Pokud snímku nebo zálohování již bylo mimo Azure Disk Encryption. 

* Řešení Azure Disk Encryption používá ochrana externí klíče nástroje BitLocker pro virtuální počítače IaaS s Windows. Virtuální počítače připojené k doméně, neměňte push pro doménu žádné zásady skupiny, které vynucují ochrany pomocí čipu TPM. Informace o zásadách skupiny na "Povolit BitLocker bez kompatibilního čipu TPM" najdete v tématu [odkaz zásad skupiny Bitlockeru](https://technet.microsoft.com/library/ee706521).
* Zásady nástroje BitLocker na virtuálních počítačích připojených k doméně pomocí zásad vlastní skupiny musí zahrnovat následující nastavení: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption selže, když jsou nekompatibilní nastavení zásad vlastní skupiny pro Bitlocker. Na počítačích, které nemá správné zásady nastavení, použít nové zásady, vynucení novou zásadu pro aktualizaci (gpupdate.exe/Force) a následného restartování může vyžadovat.  
* K vytvoření aplikace Azure AD, vytvoření trezoru klíčů, nebo nastavení existujícího trezoru klíčů a povolit šifrování, najdete v článku [požadovaných součástí skript prostředí PowerShell Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Konfigurovat požadavky související s šifrování disku pomocí rozhraní příkazového řádku Azure, najdete v článku [tohoto skriptu Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Se používá služba Azure Backup k zálohování a obnovení šifrovaných virtuálních počítačů, když je povoleno šifrování s Azure Disk Encryption, šifrování virtuálních počítačů pomocí Azure Disk Encryption key konfigurace. Služba Backup podporuje virtuální počítače, které jsou šifrované pomocí no-KEK nebo KEK konfigurace. Zobrazit [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup šifrování](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* Při šifrování svazku operačního systému Linux, mějte na paměti, že se aktuálně vyžaduje na konci procesu restartování virtuálního počítače. To můžete udělat prostřednictvím portálu, powershellu nebo rozhraní příkazového řádku.   Pokud chcete sledovat průběh šifrování, pravidelně dotazovala na stavové zprávy vrácené Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Po dokončení šifrování, označí Tato stavová zpráva vrácená tímto příkazem. Například "zpráva o průběhu: úspěšně šifrování disku s operačním systémem, restartujte prosím virtuální počítač" v tuto chvíli je možné virtuální počítač restartovat a použít.  

* Azure Disk Encryption pro Linux vyžaduje datové disky pro připojený soubor v systému Linux před šifrování

* Rekurzivně připojené datové disky nejsou podporovány službou Azure Disk Encryption pro Linux. Například, pokud cílový systém má připojený disk na/foo/panelu a potom jiného /foo/bar/baz šifrování /foo/bar/baz proběhne úspěšně, ale šifrování/foo/panel se nezdaří. 

* Azure Disk Encryption je podporován pouze v galerii Azure, které jsou podporovány bitové kopie, které splňují výše uvedené požadavky. Zákazník vlastních imagí nepodporují z důvodu vlastní oddíl schémata a proces chování, které mohou existovat v těchto imagí. Image z Galerie i dál, na základě virtuální počítače, které původně splnit požadavky, ale byly upraveny po vytvoření možná není kompatibilní.  K tomu je důvod, navrhované postup pro šifrování virtuálního počítače s Linuxem ke spuštění z čisté Galerie obrázků, zašifrovat virtuální počítač a pak přidejte vlastní software nebo data k virtuálnímu počítači podle potřeby.  

* Azure Disk Encryption a místní data svazek – svazek klíče Bek pro Windows a/mnt/azure_bek_disk pro virtuální počítače IaaS s Linuxem pro bezpečné uložení šifrovacího klíče. Odstranit nebo upravit obsah tohoto disku. Protože přítomnost klíče šifrování je potřeba pro všechny operace šifrování na virtuálním počítači IaaS není odpojit disk. Soubor README součástí svazek obsahuje další podrobnosti.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Nastavení aplikace Azure AD ve službě Azure Active Directory
Pokud je třeba šifrování, aby byla povolená na spuštěný virtuální počítač v Azure, Azure Disk Encryption generuje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů se vyžaduje ověřování Azure AD.

Pro tento účel vytvořte aplikaci Azure AD. Můžete najít podrobné pokyny pro registraci aplikace v části "Získat Identity pro aplikace" v blogovém příspěvku [Azure Key Vault – krok za krokem](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Tento příspěvek obsahuje také řadu příkladů užitečné pro nastavení a konfigurace trezoru klíčů. Pro účely ověřování můžete použít ověřování založené na tajný kód klienta nebo ověřování klienta na základě certifikátů Azure AD.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Ověřování na základě tajný kód klienta pro službu Azure AD
Následující části můžete nakonfigurovat ověřování na základě tajný kód klienta pro službu Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Vytvoření aplikace Azure AD pomocí Azure Powershellu
Použijte následující rutinu prostředí PowerShell k vytvoření aplikace Azure AD:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId je ID klienta Azure AD a $aadClientSecret je tajný kód klienta, který jste měli použít později, aby Azure Disk Encryption. Tajný klíč klienta Azure AD chrání odpovídajícím způsobem.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Nastavení ID klienta Azure AD a tajný klíč z portálu Azure portal
ID klienta Azure AD a tajný kód můžete nastavit také pomocí webu Azure Portal. Pokud chcete provést tuto úlohu, postupujte takto:

1. Vyberte **všechny služby > Azure Active Directory**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-service.png)

2. Vyberte **registrace aplikací > Registrace nové aplikace**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-app-registration.png)

3. Zadejte požadované informace a vytvořte aplikaci:

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-app.png)

4. Vyberte nově vytvořenou aplikaci, a zobrazte její vlastnosti, včetně ID aplikace.  Chcete-li vytvořit klíč pro aplikaci, vyberte **Nastavení > klíče**, přidejte popis a vypršení platnosti pro klíče a klikněte na tlačítko **uložit**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-pw.png)

5. Zkopírujte vygenerovaný tajná hodnota a odpovídajícím způsobem ho chrání.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>Použít existující aplikace
Spusťte následující příkazy, získat a používat [modulu Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Je třeba spustit následující příkazy z nové okno Powershellu. Nepoužívejte ke spuštění příkazů Azure Powershellu nebo v okně Azure Resource Manageru. Doporučujeme tento přístup, protože jsou tyto rutiny v modulu MSOnline nebo Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Ověřování pomocí certifikátu pro službu Azure AD
> [!NOTE]
> Ověřování na základě certifikátů Azure AD není aktuálně podporována u virtuálních počítačů s Linuxem.

Následující části ukazují, jak nakonfigurovat ověřování pomocí certifikátu pro službu Azure AD.

##### <a name="create-an-azure-ad-application"></a>Vytvoření aplikace Azure AD
Chcete-li vytvořit aplikaci Azure AD, spusťte následující rutiny prostředí PowerShell:

> [!NOTE]
> Nahraďte následující `yourpassword` řetězec s zabezpečené heslem a chránit heslem.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Po dokončení tohoto kroku, nahrajte soubor PFX do trezoru klíčů a povolte zásady přístupu, které jsou potřebné k nasazení tohoto certifikátu do virtuálního počítače.

##### <a name="use-an-existing-azure-ad-application"></a>Použijte existující aplikaci Azure AD
Při konfiguraci ověřování pomocí certifikátů pro existující aplikace, použijte rutiny prostředí PowerShell je vidět tady. Ujistěte se, že spuštění z nové okno Powershellu.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Po dokončení tohoto kroku, nahrajte soubor PFX do trezoru klíčů a povolte zásady přístupu, který je nezbytný pro nasazení certifikátu do virtuálního počítače.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Nahrajte soubor PFX do trezoru klíčů
Podrobné vysvětlení tohoto procesu najdete v tématu [The oficiální Key Vault Blog týmu Azure](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Následující rutiny Powershellu jsou však vše, co potřebujete pro úlohu. Nezapomeňte spustit z konzoly Azure Powershellu.

> [!NOTE]
> Nahraďte následující `yourpassword` řetězec s zabezpečené heslem a chránit heslem.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Nasazení certifikátu v trezoru klíčů do existujícího virtuálního počítače
Po dokončení nahrávání PFX, nasaďte certifikát ve službě key vault do existujícího virtuálního počítače s následujícími možnostmi:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD
Vaše aplikace Azure AD potřebuje oprávnění pro přístup k klíčů nebo tajných klíčů v trezoru. Použití [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) rutiny k udělení oprávnění k aplikaci pomocí ID klienta (který se vygeneroval při aplikace byl zaregistrován) jako _– ServicePrincipalName_ hodnotu parametru. Další informace najdete v blogovém příspěvku [Azure Key Vault – krok za krokem](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Tady je příklad toho, jak provést tuto úlohu prostřednictvím Powershellu:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption je potřeba nakonfigurovat následující zásady přístupu pro klientské aplikace Azure AD: _WrapKey_ a _nastavit_ oprávnění.

## <a name="terminology"></a>Terminologie
Vysvětlení některých běžných termínů používaných v této technologie, použijte následující tabulku terminologie:

| Terminologie | Definice |
| --- | --- |
| Azure AD | Azure AD je [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Účet Azure AD je předpokladem pro ověřování, ukládání a načítání tajných kódů z trezoru klíčů. |
| Azure Key Vault | Key Vault je služba pro správu klíčů, kryptografické založenou na modulech zabezpečení hardwaru Federal Information Processing Standards FIPS ověřit, které pomáhá chránit kryptografické klíče a tajné kódy citlivé. Další informace najdete v tématu [služby Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| ARM | Azure Resource Manager |
| BitLocker |[Nástroj BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznán odvětví Windows svazku šifrovací technologie, která se používá k povolení šifrování disku ve virtuálních počítačích IaaS Windows. |
| BEK | Šifrování klíče Bitlockeru se používají k zašifrování spouštěcí svazek s operačním systémem a datové svazky. Klíče Bitlockeru jsou chráněné ve službě key vault jako tajné kódy. |
| Rozhraní příkazového řádku | Zobrazit [rozhraní příkazového řádku Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) je podsystém založených na Linuxu nebo transparentního šifrování disků, který se používá k povolení šifrování disků na virtuální počítače IaaS s Linuxem. |
| KEK | Šifrovací klíč klíče je asymetrický klíč (RSA 2048), který vám pomůže chránit nebo zabalovat tajné kódy. Můžete zadat hardwarové zabezpečení modulech (HSM)-chráněný klíč, nebo klíč chráněný softwarem. Další podrobnosti najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| PS rutiny | Zobrazit [rutin prostředí Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Nastavení a konfigurace trezoru klíčů pro Azure Disk Encryption
Azure Disk Encryption pomáhá chránit šifrování disku klíče a tajné klíče v trezoru klíčů. Nastavení trezoru klíčů pro Azure Disk Encryption, proveďte kroky v každé z následujících částí.

#### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
Chcete-li vytvořit trezor klíčů, použijte jednu z následujících možností:

* ["101-Key-trezor – vytvořit" šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Rutiny Azure key vault prostředí PowerShell](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Jak [zabezpečení trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Pokud jste již nastavili trezor klíčů pro vaše předplatné, přejděte k další části.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Nastavit šifrovací klíč klíče (volitelné)
Pokud chcete použít pro další úroveň zabezpečení pro šifrovací klíče nástroje BitLocker KEK, přidejte k trezoru klíčů KEK. Použití [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) rutina pro vytvoření šifrovací klíč klíče v trezoru klíčů. Můžete také importovat KEK z vaší místní správy k klíče HSM. Další podrobnosti najdete v tématu [dokumentace ke službě Key Vault](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Klíče KEK přidáte tak, že přejdete do Azure Resource Manageru nebo pomocí rozhraní služby key vault.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Nastavit oprávnění pro trezor klíčů
Platforma Azure potřebuje přístup k šifrování klíčů nebo tajných klíčů v trezoru klíčů, aby byly k dispozici pro virtuální počítač pro spuštění a dešifrování svazky. Chcete-li udělit oprávnění na platformu Azure, nastavte **EnabledForDiskEncryption** vlastnost klíče trezoru pomocí rutiny prostředí PowerShell služby key vault:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Můžete také nastavit **EnabledForDiskEncryption** vlastnost přechodem [Azure Resource Exploreru](https://resources.azure.com).

Jak bylo zmíněno výše, je nutné nastavit **EnabledForDiskEncryption** vlastnosti trezoru klíčů. V opačném případě se nasazení nezdaří.

Můžete nastavit pro vaši aplikaci Azure AD z rozhraní služby key vault zásady přístupu, jak je znázorněno zde:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Na **pokročilé zásady přístupu** kartu, ujistěte se, že váš trezor klíčů je povolena pro Azure Disk Encryption:

![Služby Azure key vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Šifrování disku nasazení scénářů a uživatelského prostředí
Můžete povolit řadu scénářů šifrování disku a kroků může lišit v závislosti scénáři. Následující části se věnují scénáře podrobněji.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Povoluje šifrování na nové virtuální počítače IaaS, které jsou vytvořeny z Marketplace
Můžete povolit šifrování disku na nový virtuální počítač IaaS Windows z Tržiště v Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Na šablony Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci šifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a potom klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

> [!NOTE]
> Tato šablona vytvoří nový virtuální počítač šifrovaný Windows s, která používá image z galerie systému Windows Server 2012.

Můžete povolit šifrování disku na nový IaaS Red Hat Linux 7.2 virtuální počítač s polem RAID-0 200 GB prostřednictvím tohoto [šablony Resource Manageru](https://aka.ms/fde-rhel). Po nasazení šablony, ověřte stav šifrování virtuálního počítače pomocí `Get-AzureRmVmDiskEncryptionStatus` rutiny, jak je popsáno v [jednotky s operačním systémem šifrování na spuštěný virtuální počítač s Linuxem](#encrypting-os-drive-on-a-running-linux-vm). Když je počítač vrátí stav _VMRestartPending_, restartujte virtuální počítač.

Následující tabulka uvádí parametry šablony Resource Manageru pro nové virtuální počítače z Tržiště scénáře pomocí ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| adminUserName | Uživatelské jméno správce pro virtuální počítač. |
| adminPassword | Heslo správce pro virtuální počítač. |
| newStorageAccountName | Název účtu úložiště pro uložení operačního systému a dat virtuálních pevných disků. |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze standardní A, D a G series. |
| virtualNetworkName | Název virtuální sítě, síťové karty virtuálního počítače by měly patřit do. |
| subnetName | Název podsítě ve virtuální síti, která síťové karty virtuálního počítače by měly patřit do. |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultURL | Adresa URL, která klíč Bitlockeru, musí být nahrán do trezoru klíčů. Můžete ho získat pomocí rutiny `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | Adresa URL šifrovací klíč klíče, který se používá k šifrování vygenerovaný klíč nástroje BitLocker (volitelné). |
| keyVaultResourceGroup | Skupina prostředků trezoru klíčů. |
| vmName | Název virtuálního počítače, který má být proveden na operace šifrování. |

> [!NOTE]
> _KeyEncryptionKeyURL_ je volitelný parametr. Můžete přinést vlastní KEK další ochranná datového šifrovacího klíče (tajný klíč přístupového hesla) v trezoru klíčů.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Povoluje šifrování na nové virtuální počítače IaaS, které jsou vytvořeny z šifrované zákazníka virtuální pevný disk a šifrovací klíče
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části popisují podrobněji šablonu Resource Manageru a příkazech rozhraní příkazového řádku.

Postupujte podle pokynů od jednoho z těchto oddílů pro přípravu předem šifrované imagí, které lze použít v Azure. Po vytvoření image můžete použít kroky v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava virtuálního pevného disku předem šifrované Windows](#preparing-a-pre-encrypted-windows-vhd)
* [Připravit předem šifrované linuxového virtuálního pevného disku](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Pomocí šablony Resource Manageru
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Na šablony Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci šifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a potom klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

Následující tabulka uvádí parametry šablony Resource Manageru pro šifrované virtuální pevný disk:

| Parametr | Popis |
| --- | --- |
| newStorageAccountName | Název účtu úložiště pro uložení šifrovaného virtuálního pevného disku operačního systému. Tento účet úložiště by měl již byly vytvořeny ve stejné skupině prostředků a stejném umístění jako virtuální počítač. |
| osVhdUri | Identifikátor URI virtuálního pevného disku operačního systému z účtu úložiště. |
| osType | Typ produktu operačního systému (Windows/Linux). |
| virtualNetworkName | Název virtuální sítě, síťové karty virtuálního počítače by měly patřit do. Název by měl již byly vytvořeny ve stejné skupině prostředků a stejném umístění jako virtuální počítač. |
| subnetName | Název podsítě ve virtuální síti, která síťové karty virtuálního počítače by měly patřit do. |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze standardní A, D a G series. |
| keyVaultResourceID | ID prostředku, který identifikuje prostředek trezoru klíčů v Azure Resource Manageru. Můžete ho získat pomocí rutiny prostředí PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | Adresa URL klíče šifrování disku, který je nastavený v trezoru klíčů. |
| keyVaultKekUrl | Adresa URL šifrovací klíč klíče pro šifrování klíč vygenerovaný šifrování disku. |
| vmName | Název virtuálního počítače IaaS. |

#### <a name="using-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný pomocí rutiny prostředí PowerShell [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Pomocí příkazů rozhraní příkazového řádku
Pokud chcete povolit šifrování disku v tomto scénáři pomocí příkazů rozhraní příkazového řádku, postupujte takto:

1. Nastavit zásady přístupu trezoru klíčů:

   * Nastavte **EnabledForDiskEncryption** příznak:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Nastavit oprávnění k aplikaci Azure AD k zápisu tajných klíčů do trezoru klíčů:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Pokud chcete povolit šifrování na existující nebo spuštěný virtuální počítač, zadejte:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Načíst stav šifrování:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Pokud chcete povolit šifrování na nový virtuální počítač z šifrované virtuální pevný disk, použijte následující parametry s `azure vm create` příkaz:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Povoluje šifrování na existující nebo spuštění virtuálního počítače IaaS s Windows v Azure
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části podrobněji popisují jak se dá povolit pomocí šablony Resource Manageru a příkazech rozhraní příkazového řádku.

#### <a name="using-the-resource-manager-template"></a>Pomocí šablony Resource Manageru
Můžete povolit šifrování disku v existující nebo běžící virtuální počítače IaaS s Windows v Azure s použitím [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Na šablony Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci šifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a potom klikněte na tlačítko **vytvořit** chcete povolit šifrování na existující nebo spuštěného virtuálního počítače IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spouštění virtuálních počítačů, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do služby key vault. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do služby key vault. |
| keyVaultName | Název, který klíč Bitlockeru, musí být nahrán do trezoru klíčů. Můžete ho získat pomocí rutiny `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adresa URL šifrovací klíč klíče, který se používá k šifrování vygenerovaný klíč Bitlockeru. Tento parametr je nepovinný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, je nutné zadat _keyEncryptionKeyURL_ hodnotu. |
| volumeType | Typ svazku, který provádí operace šifrování na. Platné hodnoty jsou _OS_, _Data_, a _všechny_. |
| sequenceVersion | Pořadí verze Bitlockeru operace. Toto číslo verze postupně zvyšuje vždy, když je provedena operace šifrování disku ve stejném virtuálním počítači. |
| vmName | Název virtuálního počítače, který má být proveden na operace šifrování. |

> [!NOTE]
> _KeyEncryptionKeyURL_ je volitelný parametr. Vlastní KEK možné přenést do dalšího chránit šifrovací klíč dat (šifrování nástrojem BitLocker tajný klíč) ve službě key vault.

#### <a name="using-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Informace o povolení šifrování v Azure Disk Encryption pomocí rutin prostředí PowerShell najdete v tématu v příspěvcích na blogu [prozkoumání Azure Disk Encryption pomocí Azure Powershellu – část 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) a [prozkoumání Azure Disk Encryption pomocí Azure Powershellu – část 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Pomocí příkazů rozhraní příkazového řádku
Pokud chcete povolit šifrování na existující nebo spuštění virtuálního počítače IaaS s Windows v Azure pomocí příkazů rozhraní příkazového řádku, postupujte takto:

1. Nastavení zásad přístupu ve službě key vault:
   * Nastavte **EnabledForDiskEncryption** příznak:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Nastavit oprávnění k aplikaci Azure AD k zápisu tajných klíčů do trezoru klíčů:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Pokud chcete povolit šifrování na existující nebo spuštěný virtuální počítač:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Pokud chcete získat stav šifrování:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Pokud chcete povolit šifrování na nový virtuální počítač z šifrované virtuální pevný disk, použijte následující parametry s `azure vm create` příkaz:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Povoluje šifrování na existující nebo spuštěné IaaS virtuálního počítače s Linuxem v Azure
Můžete povolit šifrování disku v existující nebo spuštěné IaaS virtuálního počítače s Linuxem v Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klikněte na tlačítko **nasadit do Azure** na šablony Azure rychlý start, zadejte na konfiguraci šifrování **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a potom klikněte na tlačítko **vytvořit** chcete povolit šifrování na existující nebo spuštěného virtuálního počítače IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spouštění virtuálních počítačů, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do služby key vault. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultName | Název, který klíč Bitlockeru, musí být nahrán do trezoru klíčů. Můžete ho získat pomocí rutiny `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adresa URL šifrovací klíč klíče, který se používá k šifrování vygenerovaný klíč Bitlockeru. Tento parametr je nepovinný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, je nutné zadat _keyEncryptionKeyURL_ hodnotu. |
| volumeType | Typ svazku, který provádí operace šifrování na. Platné podporované hodnoty jsou _OS_ nebo _všechny_ (viz podporované distribuce Linuxu a jejich verze pro operační systém a datové disky v části prerequisiteis dříve). |
| sequenceVersion | Pořadí verze Bitlockeru operace. Toto číslo verze postupně zvyšuje vždy, když je provedena operace šifrování disku ve stejném virtuálním počítači. |
| vmName | Název virtuálního počítače, který má být proveden na operace šifrování. |
| přístupové heslo | Jako datový šifrovací klíč, zadejte silné heslo. |

> [!NOTE]
> _KeyEncryptionKeyURL_ je volitelný parametr. Můžete přinést vlastní KEK další ochranná datového šifrovacího klíče (tajný klíč přístupového hesla) v trezoru klíčů.

#### <a name="cli-commands"></a>Příkazy rozhraní příkazového řádku
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný po instalaci a použití [příkazu rozhraní příkazového řádku](../cli-install-nodejs.md). Pokud chcete povolit šifrování na existující nebo běžící virtuální počítače IaaS s Linuxem v Azure pomocí příkazů rozhraní příkazového řádku, postupujte takto:

1. Nastavit zásady přístupu trezoru klíčů:

 * Nastavte **EnabledForDiskEncryption** příznak:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Nastavit oprávnění k aplikaci Azure AD k zápisu tajných klíčů do trezoru klíčů:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Pokud chcete povolit šifrování na existující nebo spuštěný virtuální počítač:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Načíst stav šifrování:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Pokud chcete povolit šifrování na nový virtuální počítač z šifrované virtuální pevný disk, použijte následující parametry s `azure vm create` příkaz:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Získat stav šifrování šifrovaných virtuálních počítačů IaaS
Pomocí Azure Resource Manageru, můžete získat stav šifrování [rutin prostředí PowerShell](/powershell/azure/overview), nebo pomocí příkazů rozhraní příkazového řádku. Následující části popisují, jak pomocí webu Azure Portal a příkazy rozhraní příkazového řádku zobrazíte stav šifrování.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Načíst stav šifrování šifrovaných virtuálního počítače Windows pomocí Azure Resource Manageru
Stav šifrování objektu IaaS VM můžete získat z Azure Resource Manageru následujícím způsobem:

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com/)a potom klikněte na tlačítko **virtuálních počítačů** v levém podokně zobrazíte souhrnné zobrazení virtuálních počítačů ve vašem předplatném. Můžete filtrovat tak, že vyberete název předplatného v zobrazení virtuálních počítačů **předplatné** rozevíracího seznamu.

2. V horní části **virtuálních počítačů** klikněte na **sloupce**.

3. Na **zvolit sloupec** okně vyberte **šifrování disku**a potom klikněte na tlačítko **aktualizace**. Měli byste vidět šifrování disku sloupec zobrazuje stav šifrování _povoleno_ nebo _nejsou povolené_ pro každý virtuální počítač, jak je znázorněno na následujícím obrázku:

 ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Získat stav šifrování šifrovaných virtuálních počítačů IaaS (Windows/Linux) pomocí rutiny Powershellu šifrování disku
Stav šifrování objektu IaaS VM můžete získat z rutiny prostředí PowerShell šifrování disku `Get-AzureRmVMDiskEncryptionStatus`. Chcete-li získat nastavení šifrování pro virtuální počítač, zadejte následující příkaz:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Si můžete prohlédnout výstup _Get-AzureRmVMDiskEncryptionStatus_ pro šifrování klíče adresy URL.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Nastavení hodnoty OSVolumeEncrypted a DataVolumesEncrypted jsou nastaveny na _šifrované_, který ukazuje, že oba svazky jsou šifrované službou Azure Disk Encryption. Informace o povolení šifrování v Azure Disk Encryption pomocí rutin prostředí PowerShell najdete v tématu v příspěvcích na blogu [prozkoumání Azure Disk Encryption pomocí Azure Powershellu – část 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) a [prozkoumání Azure Disk Encryption pomocí Azure Powershellu – část 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Na virtuální počítače s Linuxem, bude trvat tři až čtyři minut `Get-AzureRmVMDiskEncryptionStatus` rutiny zaznamenat stav šifrování.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Získat stav šifrování objektu IaaS VM z příkazu rozhraní příkazového řádku šifrování disku
Stav šifrování objektu IaaS VM můžete získat pomocí příkazu rozhraní příkazového řádku šifrování disku `azure vm show-disk-encryption-status`. Chcete-li získat nastavení šifrování pro virtuální počítač, zadejte relaci rozhraní příkazového řádku Azure:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Zakažte šifrování u spuštěných virtuálních počítačů IaaS Windows
Můžete zakázat šifrování na spuštěných virtuálních počítačů IaaS s Linuxem nebo Windows pomocí šablony Azure Disk Encryption Resource Manageru nebo rutiny prostředí PowerShell a zadejte požadovanou konfiguraci dešifrování.

##### <a name="windows-vm"></a>Virtuální počítač s Windows
Zakázat šifrování krok zakazuje šifrování operačního systému, objem dat nebo na spuštěný virtuální počítač IaaS Windows. Nelze zakázat svazek s operačním systémem a nechte objem dat šifrována. Při provádění kroku zakázat šifrování modelu nasazení Azure classic aktualizace modelu služby virtuálních počítačů a virtuálních počítačů IaaS Windows je označen dešifrovaný. Obsah virtuálního počítače už jsou v klidovém stavu zašifrovaná. Dešifrování nedojde k odstranění trezoru klíčů a materiálu šifrovacího klíče (šifrovací klíče nástroje BitLocker pro Windows a heslo pro Linux).

##### <a name="linux-vm"></a>Virtuální počítači s Linuxem
Zakázat šifrování krok zakazuje šifrování objem dat na spuštěný virtuální počítač Linux IaaS. Tento krok funguje jenom v případě disk s operačním systémem není šifrovaný.

> [!NOTE]
> Zakázáním šifrování na disku s operačním systémem není povoleno na virtuální počítače s Linuxem.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Zakázat šifrování na existující nebo spuštěný virtuální počítač IaaS
Můžete zakázat šifrování disku u spuštěných virtuálních počítačů IaaS Windows s použitím [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. V šabloně Azure rychlý start, klikněte na **nasadit do Azure**, zadejte v konfiguraci dešifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a potom klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

Pro virtuální počítače s Linuxem, můžete zakázat šifrování pomocí [zakažte šifrování u spuštěného virtuálního počítače s Linuxem](https://aka.ms/decrypt-linuxvm) šablony.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro zákaz šifrování u spuštěného virtuálního počítače IaaS:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače, který má být proveden na operace šifrování.
| volumeType | Typ operace dešifrování se provádí na svazku. Platné hodnoty jsou _OS_, _Data_, a _všechny_. Už se nedá vypnout šifrování u spuštěných virtuálních počítačů IaaS Windows operačního systému a spouštěcí svazek bez šifrování zakážete na _Data_ svazku. Všimněte si také, že zakazuje šifrování disku s operačním systémem není povolený u virtuálních počítačů s Linuxem. |
| sequenceVersion | Pořadí verze Bitlockeru operace. Toto číslo verze zvýší pokaždé, když diskem operace dešifrování se provádí na jednom virtuálním počítači. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Zakázat šifrování na existující nebo spuštěný virtuální počítač IaaS
Pokud chcete zakázat šifrování na existující nebo spuštěný virtuální počítač IaaS pomocí rutiny prostředí PowerShell, najdete v článku [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Tato rutina podporuje virtuální počítače s Linuxem i Windows. Pokud chcete zakázat šifrování, nainstaluje rozšíření na virtuálním počítači. Pokud _název_ parametr není zadán, rozšíření s výchozím názvem _AzureDiskEncryption virtuálních počítačů pro Windows_ se vytvoří.

Na virtuální počítače s Linuxem je použít rozšíření AzureDiskEncryptionForLinux.

> [!NOTE]
> Tato rutina restartování virtuálního počítače.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Povoluje šifrování na předem šifrovaných virtuálních počítačů IaaS s Azure Managed Disks
Pomocí šablony ARM spravovaného disku Azure můžete vytvořit šifrovaný virtuální počítač z předem šifrované pomocí šablony ARM, umístěný ve VHD   
[Vytvořit nové šifrované spravovaného disku z předem šifrované virtuální pevný disk nebo úložiště objektů blob] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Povoluje šifrování na nový virtuální počítač IaaS Linuxu s Azure Managed Disks
Pomocí šablony ARM spravovaného disku Azure můžete vytvořit nové šifrované IaaS virtuálního počítače s Linuxem pomocí šablony ARM v   
[Nasazení RHEL 7.2 s šifrování celého disku] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Povoluje šifrování na nový virtuální počítač IaaS Windows s Azure Managed Disks
 Pomocí šablony ARM spravovaného disku Azure můžete vytvořit nové šifrované IaaS virtuálního počítače s Linuxem pomocí šablony ARM v   
 [Vytvořit nové šifrované Windows IaaS spravovaného disku virtuálního počítače z image z Galerie] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption.  Snímek spravovaného disku může být přijata z portálu nebo Azure Backup se dá použít.  Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování.  Po provedení zálohy rutinu Set-AzureRmVMDiskEncryptionExtension slouží k šifrování zadáním parametru - skipVmBackup spravované disky.  Tento příkaz se nezdaří proti spravovaného disku Virtuálního počítače, dokud byly provedeny zálohy a byl zadán tento parametr.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Aktualizace nastavení šifrování z existujícího virtuálního počítače šifrovaná jiné úrovně než premium
  Použít existující rozhraní podpora šifrování disku Azure pro spuštění virtuálního počítače [rutiny PS, šablony ARM nebo rozhraní příkazového řádku] aktualizace nastavení šifrování jako AAD ID a tajný kód klienta, šifrovací klíč klíče [KEK], šifrovací klíč Bitlockeru pro virtuální počítač Windows nebo přístupové heslo pro virtuální počítač s Linuxem atd. Aktualizace nastavení šifrování se podporuje pro úrovně premium i úrovně než premium úložiště virtuálních počítačů.

## <a name="appendix"></a>Příloha
### <a name="connect-to-your-subscription"></a>Připojení k vašemu předplatnému
Než budete pokračovat, zkontrolujte *požadavky* části v tomto článku. Po zajištění, že jsou splněné všechny požadavky na připojení k vašemu předplatnému následujícím způsobem:

1. Spusťte relaci Azure Powershellu a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:

    `Connect-AzureRmAccount`

2. Pokud máte více předplatných a chcete určit nich se má použít, zadejte následující příkaz k zobrazení předplatných pro váš účet:

    `Get-AzureRmSubscription`

3. Pokud chcete zadat předplatné, které chcete použít, zadejte:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Pokud chcete ověřit správnost předplatné nakonfigurované, zadejte:

    `Get-AzureRmSubscription`

5. Pokud chcete potvrdit, že jsou nainstalované rutiny Azure Disk Encryption, zadejte:

    `Get-command *diskencryption*`

6. Následující výstup potvrzuje instalace Azure Disk Encryption Powershellu:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Příprava virtuálního pevného disku předem šifrované Windows
Následující části jsou nezbytné pro přípravu předem šifrované virtuální pevný disk Windows pro nasazení jako šifrovaný virtuální pevný disk v Azure IaaS. Použijte informace k přípravě a spuštění virtuálního počítače čerstvé Windows (VHD) na Azure Site Recovery a Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizace zásad skupiny umožňující bez TPM pro ochranu operačního systému
Konfigurace nastavení zásad skupiny bitlockeru určují **nástroj BitLocker Drive Encryption**, které najdete v části **zásady místního počítače** > **konfigurace počítače**  >  **Šablony pro správu** > **součásti Windows**. Změna tohoto nastavení můžete **jednotky operačního systému** > **vyžadovat další ověření při spuštění** > **povolit nástroj BitLocker bez kompatibilního čipu TPM**, jak je znázorněno na následujícím obrázku:

![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Instalace součásti funkcí nástroje BitLocker
Pro Windows Server 2012 a novější použijte následující příkaz:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 použijte následující příkaz:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Příprava svazek s operačním systémem pomocí Bitlockeru `bdehdcfg`
Komprimovat oddílu operačního systému a příprava nástroje BitLocker na počítači, spusťte následující příkaz:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrana svazku operačního systému pomocí nástroje BitLocker
Použití [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) příkaz, který povoluje šifrování na spouštěcím svazku pomocí externí ochranné zařízení klíče. Externí klíče (soubor .bek) můžete také umístíte na externím disku nebo svazku. Po dalším restartování je povolené šifrování v systému a spouštěcí svazek.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Příprava virtuálního počítače pomocí samostatných/prostředku datového virtuálního pevného disku pro získání externího klíče pomocí nástroje BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Šifrování disku s operačním systémem na spuštěný virtuální počítač s Linuxem

##### <a name="prerequisites-for-os-disk-encryption"></a>Požadavky na šifrování disku operačního systému

* Virtuální počítač musí používat kompatibilní se šifrováním disku operačního systému distribučního jak je uvedeno v [Azure Disk Encryption – nejčastější dotazy](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support) 
* Virtuální počítač musí být vytvořené z Marketplace image v Azure Resource Manageru.
* Virtuální počítač Azure s minimálně 4 GB paměti RAM (doporučená velikost je 7 GB).
* (Pro RHEL a CentOS) Zakážete SELinux. Pokud chcete zakázat SELinux, naleznete v tématu "4.4.2. Zakázání SELinux"v [Průvodce SELinux uživatele a správce](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na virtuálním počítači.
* Když zakážete SELinux, restartujte virtuální počítač alespoň jednou.

##### <a name="steps"></a>Kroky
1. Vytvoření virtuálního počítače pomocí jedné z distribuce zadali dřív.

 Pro 7.2 CentOS je podporováno šifrování disku operačního systému přes speciální image. Pokud chcete použít tuto bitovou kopii, zadejte "7.2n" jako SKU při vytváření virtuálního počítače:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurace virtuálního počítače podle vašich potřeb. Pokud chcete k šifrování všech (operační systém + data) jednotkami, musí být zadané a možnost připojit z /etc/fstab datové jednotky.

 > [!NOTE]
 > Použijte UUID =... k určení datové jednotky v/etc/fstab místo zadávání názvu zařízení blok (například/dev/sdb1). Během šifrování se změní pořadí jednotky na virtuálním počítači. Pokud váš virtuální počítač závisí na konkrétní pořadí blokovat zařízení, dojde k selhání připojení po šifrování.

3. Odhlaste se z relace SSH.

4. K šifrování operačního systému, určete volumeType jako **všechny** nebo **OS** při vám [povolit šifrování](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Všechny procesy uživatelskou, které nejsou spuštěné jako `systemd` služby by měl ukončen s `SIGKILL`. Restartujte virtuální počítač. Když povolíte šifrování disku operačního systému spuštěného virtuálního počítače, naplánujte výpadky virtuálního počítače.

5. Pravidelně sledovat průběh šifrování pomocí pokynů [další části](#monitoring-os-encryption-progress).

6. Po Get-AzureRmVmDiskEncryptionStatus ukazuje "VMRestartPending", restartujte virtuální počítač po přihlášení k němu nebo pomocí portálu, Powershellu nebo rozhraní příkazového řádku.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Předtím, než je restartovat, doporučujeme uložit [Diagnostika spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) virtuálního počítače.

#### <a name="monitoring-os-encryption-progress"></a>Sledování průběhu šifrování operačního systému
Můžete sledovat průběh šifrování operačního systému třemi způsoby:

* Použití `Get-AzureRmVmDiskEncryptionStatus` rutiny a zkontrolujte pole zpráva o průběhu:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Po virtuálním počítači dosáhne "Zahájeno šifrování disku operačního systému", trvá přibližně 40 až 50 minut na Premium storage zálohy virtuálního počítače.

 Z důvodu [vydat #388](https://github.com/Azure/WALinuxAgent/issues/388) v WALinuxAgent, `OsVolumeEncrypted` a `DataVolumesEncrypted` jako `Unknown` v některých distribucích. S WALinuxAgent verze 2.1.5 a později je tento problém automaticky opraven. Pokud se zobrazí `Unknown` ve výstupu, můžete ověřit stav šifrování disku pomocí Průzkumníka prostředků Azure.

 Přejděte na [Azure Resource Exploreru](https://resources.azure.com/)a potom rozbalte tuto hierarchii na levém panelu výběr:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 V InstanceView přejděte dolů a zobrazit stav šifrování jednotky.

 ![Zobrazení Instance virtuálního počítače](./media/azure-security-disk-encryption/vm-instanceview.png)

* Podívejte se na [Diagnostika spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Zprávy z rozšíření ADE musí předcházet `[AzureDiskEncryption]`.

* Přihlaste se k virtuálnímu počítači pomocí SSH a získejte rozšíření protokolu:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Doporučujeme vám, že není přihlásíte k virtuálnímu počítači Probíhá šifrování operačního systému. Kopírovat protokoly jenom v případě, že tyto dvě metody se nezdařilo.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Připravit předem šifrované linuxového virtuálního pevného disku
##### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurace šifrování během instalace distribučního následujícím způsobem:

1. Vyberte **konfigurace šifrovaných svazcích** při rozdělit disky.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Vytvoření samostatné spouštěcí jednotka, která nesmí být zašifrovaná. Šifrování kořenové jednotce.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Zadejte heslo. Toto je heslo, které nahrajete do služby key vault.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Dokončení vytváření oddílů.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Při spuštění virtuálního počítače a vyzváni k zadání přístupového hesla použijte heslo, které jste zadali v kroku 3.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Příprava virtuálního počítače pro jeho odeslání do Azure s využitím [tyto pokyny](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nespouštějte na poslední krok (zrušení zřízení virtuálního počítače) ještě.

Konfigurace šifrování pro práci s Azure následujícím způsobem:

1. Vytvoření souboru v rámci /usr/local/sbin/azure_crypt_key.sh, s obsahem v následujícím skriptu. Věnujte pozornost KeyFileName, protože je název souboru přístupové heslo používané Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Změna konfigurace kryptografie v */etc/crypttab*. Mělo by to vypadat takto:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Pokud upravujete *azure_crypt_key.sh* ve Windows a zkopírován do Linuxu spusťte `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Přidáte spustitelný soubor oprávnění ke skriptu:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Upravit */etc/initramfs-tools/modules* přidáním řádků:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Spustit `update-initramfs -u -k all` aktualizovat initramfs provést `keyscript` projeví.

7. Nyní můžete zrušit zřízení virtuálního počítače.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Pokračovat k dalšímu kroku a [nahrát virtuální pevný disk](#upload-encrypted-vhd-to-an-azure-storage-account) do Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Pokud chcete nakonfigurovat šifrování během instalace distribučního, postupujte takto:
1. Když je rozdělit disky, vyberte **šifrování svazku skupiny**a pak zadejte heslo. Jedná se o heslo, který odešlete do trezoru klíčů.

 ![Instalace v openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Spuštění virtuálního počítače pomocí hesla.

 ![Instalace v openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Příprava virtuálního počítače pro jeho odeslání do Azure podle pokynů v [Příprava virtuálního počítače se SLES nebo openSUSE pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nespouštějte na poslední krok (zrušení zřízení virtuálního počítače) ještě.

Ke konfiguraci šifrování pro práci s Azure, postupujte takto:
1. Upravit /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Okomentujte řádky na konci souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Připojte následující řádek na začátek souboru /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```
    DRACUT_SYSTEMD=0
 ```
A změňte všechny výskyty položky:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
na:
```
    if [ 1 ]; then
```
4. Upravit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a přidejte je do "# Otevřít LUKS zařízení":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Spustit `/usr/sbin/dracut -f -v` aktualizovat initrd.

6. Nyní můžete zrušit zřízení virtuálního počítače a [nahrát virtuální pevný disk](#upload-encrypted-vhd-to-an-azure-storage-account) do Azure.

##### <a name="centos-7"></a>CentOS 7
Pokud chcete nakonfigurovat šifrování během instalace distribučního, postupujte takto:
1. Vyberte **šifrovat data** při rozdělit disky.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Ujistěte se, že **šifrovat** je vybrán pro kořenový oddíl.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Zadejte heslo. Toto je heslo, které odešlete do trezoru klíčů.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Při spuštění virtuálního počítače a vyzváni k zadání přístupového hesla použijte heslo, které jste zadali v kroku 3.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Příprava virtuálního počítače pro jeho odeslání do Azure s využitím pokynů "CentOS 7.0 +" [Příprava virtuálního počítače založeného na CentOS pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nespouštějte na poslední krok (zrušení zřízení virtuálního počítače) ještě.

6. Nyní můžete zrušit zřízení virtuálního počítače a [nahrát virtuální pevný disk](#upload-encrypted-vhd-to-an-azure-storage-account) do Azure.

Ke konfiguraci šifrování pro práci s Azure, postupujte takto:

1. Upravit /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Okomentujte řádky na konci souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Připojte následující řádek na začátek souboru /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```
    DRACUT_SYSTEMD=0
```
A změňte všechny výskyty položky:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
na
```
    if [ 1 ]; then
```
4. Upravit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a nový kód přidejte za "otevřené LUKS zařízení #":
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Spustit "/ usr/sbin/dracut - f - v" aktualizovat initrd.

![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Nahrání šifrovaného virtuálního pevného disku do účtu služby Azure storage
Po povolení šifrování nástrojem BitLocker nebo šifrování DM-Crypt místní šifrované virtuální pevný disk je potřeba nahrát do účtu úložiště.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Nahrát tajný kód disk encryption pro předem šifrovaných virtuálních počítačů do trezoru klíčů
Tajný kód disk encryption, který jste získali dříve musí být odeslán jako tajný klíč v trezoru klíčů. Key vault musí mít šifrování disku a povolenými oprávněními pro vašeho klienta Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Tajný kód disk encryption není šifrován KEK
Chcete-li nastavit tajný klíč v trezoru klíčů, použijte [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Pokud máte virtuální počítače s Windows, je zakódován jako řetězec ve formátu base64 a pak nahrají do vašeho trezoru klíčů pomocí souboru klíče bek `Set-AzureKeyVaultSecret` rutiny. Heslo pro Linux, jsou zakódovány jako řetězec ve formátu base64 a pak nahrají do služby key vault. Kromě toho Ujistěte se, že následující značky jsou nastaveny při vytvoření tajného klíče v trezoru klíčů.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Použití `$secretUrl` v dalším kroku pro [připojení disku s operačním systémem bez použití KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Tajný kód disk encryption šifrován KEK
Před odesláním do služby key vault tajný klíč, které můžete volitelně šifrovat pomocí šifrovací klíč klíče. Použít obtékání [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) nejprve šifrování tajného klíče pomocí klíče šifrovacího klíče. Výstupem této operace zalamování řádků je řetězec kódování URL ve formátu base64, který pak můžete nahrát jako tajný kód pomocí [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) rutiny.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Použití `$KeyEncryptionKey` a `$secretUrl` v dalším kroku pro [připojení disku s operačním systémem pomocí KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Po připojení disku s operačním systémem zadejte adresa URL tajného kódu
#### <a name="without-using-a-kek"></a>Bez použití KEK
Když jsou připojení disku s operačním systémem, je nutné předat `$secretUrl`. Adresa URL vytvořená v části "šifrování disku tajný klíč není šifrován KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Použití KEK
Po připojení disku s operačním systémem, předejte `$KeyEncryptionKey` a `$secretUrl`. Adresa URL vytvořená v části "šifrování disku tajný klíč není šifrován KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="for-more-information"></a>Další informace
[Prozkoumání Azure Disk Encryption pomocí Azure Powershellu – část 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Prozkoumání Azure Disk Encryption pomocí Azure Powershellu – část 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
