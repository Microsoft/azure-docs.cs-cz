---
title: Azure Disk Encryption pro systém Windows a virtuálních počítačů Linux IaaS | Microsoft Docs
description: Tento článek obsahuje přehled Microsoft Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux.
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
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: 5219ebc22e56ad4b5cdfc125f7fa5882c61adb9f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption pro systém Windows a virtuálních počítačů Linux IaaS
Microsoft Azure se důrazně zaměřuje na zajištění ochrany osobních údajů, suverenity data a umožňuje vám řízení vaší Azure hostované data prostřednictvím řadu pokročilých technologiích k šifrování, řídit a spravovat šifrovací klíče, řízení a audit přístupu k datům. To poskytuje Azure zákazníkům flexibilitu zvolit si řešení, které nejlépe vyhovuje potřebám své firmy. V tomto dokumentu jsme vás seznámí s nové řešení technologie "Azure Disk Encryption pro systém Windows a Linux IaaS virtuálního počítače je" k ochraně a ochranu dat, aby splňovaly vaše organizace zabezpečení a dodržování předpisů závazky. Dokumentu poskytuje podrobné pokyny k použití funkcí Azure disk encryption, včetně Podporované scénáře a uživatel dojde.

> [!NOTE]
> Některá doporučení může zvýšit data, síťové nebo využití výpočetních prostředků, což vede k další náklady na licence nebo předplatné.

## <a name="overview"></a>Přehled
Azure Disk Encryption je novou funkci, která umožňuje šifrovat disky virtuálního počítače s Windows a Linux IaaS. Azure Disk Encryption využívá oborový standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce systému Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce systému Linux zajistit šifrování svazku operačního systému a datové disky. Řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) můžete řídit a spravovat šifrování disku klíče a tajné klíče v rámci vašeho předplatného trezoru klíčů. Řešení také zajistí, že všechna data na disky virtuálního počítače jsou zašifrovaná přinejmenším ve službě Azure storage.

Azure disk encryption pro systém Windows a virtuálních počítačů IaaS Linux je nyní v **obecné dostupnosti** do všech veřejných oblastí Azure a oblasti AzureGov standardní virtuální počítače a virtuální počítače s storage úrovně premium.

### <a name="encryption-scenarios"></a>Šifrování scénáře
Řešení Azure Disk Encryption podporuje následující scénáře zákazníka:

* Povolit šifrování na nové virtuální počítače IaaS vytvořené z předem šifrované virtuálního pevného disku a šifrovacích klíčů
* Povolit šifrování na nové virtuální počítače IaaS vytvořené z podporovaných Azure Galerie obrázků
* Povolit šifrování na existující virtuální počítače IaaS běžící v Azure
* Vypnout šifrování u virtuálních počítačů IaaS Windows
* Zakázat šifrování na datových jednotkách pro virtuální počítače IaaS Linux
* Povolit šifrování spravovaných disků na virtuální počítače
* Aktualizovat nastavení šifrování existující šifrované premium a neprémiové úložiště virtuálního počítače
* Zálohování a obnovení šifrovaných virtuálních počítačů.

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud jsou povolené v Microsoft Azure:

* Integrace s Azure Key Vault
* Úroveň Standard virtuálních počítačů: [A, D, DS, G, GS, F a atd. řady virtuální počítače IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Povolit šifrování na Windows a virtuálních počítačů IaaS Linux spravovaných disků virtuálních počítačů z podporovaných Azure Galerie obrázků
* Zakázat šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS Windows a spravovaných disků na virtuální počítače
* Zakázat šifrování na datových jednotkách pro virtuální počítače IaaS Linux a spravovaných disků na virtuální počítače
* Povolit šifrování na virtuální počítače IaaS se systémem OS klienta Windows
* Povolit šifrování na svazcích s cestami připojení
* Povolit šifrování na virtuální počítače s Linuxem nakonfigurovaný s diskem proložení (RAID) pomocí mdadm
* Povolit šifrování na virtuální počítače s Linuxem pomocí LVM pro datové disky
* Povolit šifrování na Linux LVM 7.3 pro disky operačního systému a dat 
* Povolit šifrování na virtuálních počítačích Windows nakonfigurovaný s prostory úložiště
* Aktualizovat nastavení šifrování existující šifrované premium a neprémiové úložiště virtuálního počítače
* Zálohování a obnovení šifrovaných virtuálních počítačů, pro žádné KEK a scénáře KEK (KEK - šifrovací klíče)
* Jsou podporovány všechny veřejné Azure a AzureGov oblastí

Řešení nepodporuje následující scénáře, funkce a technologie:

* Úroveň Basic virtuálních počítačů IaaS
* Zakázáním šifrování na jednotce operačního systému pro virtuální počítače IaaS Linux
* Zakázáním šifrování na datová jednotka, pokud je pro virtuální počítače Iaas Linux šifrované jednotky operačního systému
* Virtuální počítače IaaS, která jsou vytvořená pomocí klasického metody vytvoření virtuálního počítače
* Povolte šifrování na systém Windows a virtuálních počítačů Linux IaaS zákazníka vlastních bitových kopií není podporován.
* Integrace s vaší místní služby správy klíčů
* Soubory Azure (systém souborů sdíleného), Network File System (NFS), dynamické svazky a virtuální počítače Windows, které jsou nakonfigurované s systémy na bázi softwaru diskového pole RAID

### <a name="encryption-features"></a>Funkce šifrování
Když povolíte a nasadíte Azure Disk Encryption pro virtuální počítače Azure IaaS, jsou povolené následující možnosti, v závislosti na konfiguraci poskytuje:

* Šifrování svazku operačního systému k ochraně spouštěcího svazku v klidovém stavu uložených v úložišti
* Šifrování datové svazky k ochraně objemy dat v klidovém stavu uložených v úložišti
* Zakázáním šifrování na jednotkách operačního systému a dat pro virtuální počítače IaaS Windows
* Zakázáním šifrování dat (pouze v případě OS jednotka je není zašifrovaná) disky pro virtuální počítače IaaS Linux
* Zabezpečení šifrovacích klíčů a tajných klíčů ve vašem předplatném trezoru klíčů
* Vytváření sestav stav šifrování šifrovaný IaaS virtuálního počítače
* Odebrání nastavení konfigurace šifrování disku z virtuálního počítače IaaS
* Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby zálohování Azure

Azure Disk Encryption pro virtuální počítače IaaS pro Windows a Linux řešení zahrnuje:

* Šifrování disku rozšíření pro Windows.
* Šifrování disku rozšíření pro Linux.
* Rutiny prostředí PowerShell šifrování disku.
* Disk šifrování rutiny rozhraní příkazového řádku Azure (CLI).
* Šablony Azure Resource Manager šifrování disku.

Podporuje řešení Azure Disk Encryption na virtuální počítače IaaS se systémem Windows nebo operační systém Linux. Další informace o podporovaných operačních systémů najdete v části "Požadavky".

> [!NOTE]
> Není k dispozici bez dalších poplatků pro šifrování disky virtuálních počítačů s Azure Disk Encryption.

### <a name="value-proposition"></a>Návrh hodnoty
Při použití řešení Azure Disk Encryption správy se může stát odpovědí následující obchodních potřeb:

* Virtuální počítače IaaS jsou zabezpečeny v klidu, protože šifrování standardní technologii můžete použít k vyřešení organizační požadavky na zabezpečení a dodržování předpisů.
* Spouštění virtuálních počítačů IaaS v části klíče řídí zákazníka a zásady a auditovat jejich využití v trezoru klíčů.

### <a name="encryption-workflow"></a>Pracovní postup šifrování
Pokud chcete povolit šifrování disku pro systém Windows a virtuální počítače s Linuxem, postupujte takto:

1. Zvolte scénářem šifrování některé z těchto scénářů předchozí šifrování.
2. Vyjádřit výslovný souhlas pro povolení šifrování pomocí šablony Azure Disk Encryption Resource Manageru, rutiny prostředí PowerShell nebo rozhraní příkazového řádku příkaz na disku a zadejte nastavení šifrování.

   * Pro virtuální pevný disk scénář šifrované zákazníka nahrajte šifrované virtuální pevný disk do účtu úložiště a materiál šifrovací klíče do trezoru klíčů. Potom zadejte konfiguraci šifrování chcete povolit šifrování na nový virtuální počítač IaaS.
   * Pro nové virtuální počítače, které jsou vytvořené z Marketplace a stávající virtuální počítače, které jsou již spuštěny v Azure zadejte konfiguraci šifrování chcete povolit šifrování na virtuální počítač IaaS.

3. Udělit přístup na platformu Azure ke čtení materiálu šifrovací klíč (šifrovací klíče nástroje BitLocker pro systémy Windows) a heslo pro Linux z trezoru klíčů na virtuální počítač IaaS povolit šifrování.

4. Zadejte identitu aplikace Azure Active Directory (Azure AD) k zápisu do trezoru klíčů podstatným šifrovací klíč. Díky tomu šifrování u virtuálních počítačů IaaS pro scénáře uveden v kroku 2.

5. Azure aktualizace modelu služby virtuálních počítačů pomocí šifrování a konfigurace trezoru klíčů a nastaví šifrované virtuálního počítače.

 ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Dešifrování pracovního postupu
Pokud chcete zakázat šifrování disku pro virtuální počítače IaaS, proveďte následující postup vysoké úrovně:

1. Zvolte můžete zakázat šifrování (dešifrování) na spuštění virtuálního počítače IaaS v Azure pomocí šablony Azure Disk Encryption Resource Manageru nebo rutiny prostředí PowerShell a zadejte konfiguraci dešifrování.

 Tento krok zakazuje šifrování operačního systému nebo datový svazek nebo obojí do spuštěného virtuálního počítače Windows IaaS. Nicméně jak je uvedeno v předchozí části, zakázáním šifrování disku operačního systému Linux nepodporuje. Krok dešifrování je povoleno pouze pro datové jednotky v virtuální počítače s Linuxem, dokud není šifrován disk operačního systému.
2. Aktualizace modelu služby virtuálních počítačů Azure a virtuální počítač IaaS je označený dešifrovaný. Obsah virtuálního počítače jsou již v zašifrované podobě.

> [!NOTE]
> Operace disable šifrování nedojde k odstranění trezoru klíčů a materiál klíče šifrování (šifrovací klíče nástroje BitLocker pro systémy Windows) nebo přístupové heslo pro Linux.
 > Zakázáním šifrování disku operačního systému Linux není podporována. Krok dešifrování je povoleno pouze pro datové jednotky v virtuální počítače s Linuxem.
Zakázáním šifrování disku data pro Linux není podporována, pokud je šifrované jednotky operačního systému.

## <a name="prerequisites"></a>Požadavky
Než povolíte Azure Disk Encryption na virtuálních počítačích Azure IaaS pro podporované scénáře, které bylo popsané v části "Přehled", viz následující požadavky:

* Musí mít platné aktivní předplatné Azure k vytváření prostředků v Azure v podporovaných oblastí.
* Azure Disk Encryption je podporována v těchto verzích Windows serveru: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016.
* Azure Disk Encryption je podporována v následujících klientských verzí Windows: klienta systému Windows 8 a Windows 10 klienta.

> [!NOTE]
> Pro Windows Server 2008 R2 musíte mít rozhraní .NET Framework 4.5 nainstalované dříve než povolíte šifrování v Azure. Můžete ji nainstalovat z webu Windows Update nainstalováním volitelnou aktualizaci rozhraní Microsoft .NET Framework 4.5.2 na x64 systémů Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Azure Disk Encryption využívá jenom podporované na konkrétní Azure Gallery Linuxových distribucích serveru a verze.  Seznam aktuálně podporovaných verzí, naleznete [Azure Disk Encryption – nejčastější dotazy](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-faq).

* Azure Disk Encryption vyžaduje, aby váš trezor klíčů a virtuální počítače jsou umístěny ve stejné oblasti Azure a předplatné.

> [!NOTE]
> Konfigurace prostředků v oblastech způsobí selhání při povolování funkce Azure Disk Encryption.

* Nastavení a konfigurace trezoru klíčů pro Azure Disk Encryption, najdete v tématu **nastavit registrace a konfigurace trezoru klíčů pro Azure Disk Encryption** v *požadavky* tohoto článku.
* Nastavení a konfigurace aplikace Azure AD ve službě Azure Active directory pro Azure Disk Encryption, najdete v tématu **nastavit aplikaci Azure AD ve službě Azure Active Directory** v *požadavky* část v tomto článku.
* Nastavit a konfigurovat zásady přístupu k trezoru klíčů pro aplikace Azure AD, najdete v tématu **nastavte zásady přístupu trezoru klíčů pro aplikace Azure AD** v *požadavky* tohoto článku.
* Příprava předem šifrované virtuálního pevného disku Windows, najdete v tématu **připravit předem šifrované virtuálního pevného disku Windows** v *příloha*.
* Příprava předem šifrované Linux virtuální pevný disk, najdete v tématu **připravit předem šifrované VHD Linux** v *příloha*.
* Platformy Azure potřebuje přístup k šifrovacích klíčů nebo tajných klíčů v trezoru klíčů, aby byly k dispozici pro virtuální počítač při spuštění a dešifruje svazek operačního systému virtuálního počítače. Chcete-li udělit oprávnění pro platformu Azure, nastavte **EnabledForDiskEncryption** vlastnost v trezoru klíčů. Další informace najdete v tématu **nastavit registrace a konfigurace trezoru klíčů pro Azure Disk Encryption** v příloze.
* Tajný klíč trezoru klíčů a adresy URL KEK musí být verzí. Azure vynucuje toto omezení Správa verzí. Platný tajný klíč a KEK adresy URL najdete v následujících příkladech:

  * Příklad tajný platnou adresu URL:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příklad platnou adresu URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje zadání čísla portu jako součást tajné klíče trezoru klíčů a adresy URL KEK. Příkladem adresy URL není podporován a podporované trezoru klíčů naleznete v následujících tématech:

  * Adresa URL nemůže být přijata trezoru klíčů  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adresa URL přijatelné trezoru klíčů:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Povolit Azure Disk Encryption funkce, virtuální počítače IaaS musí splňovat následující požadavky konfigurace koncového bodu sítě:
  * Získá token pro připojení k trezoru klíčů, musí být možné se připojit k Azure Active Directory koncový bod, virtuálních počítačů IaaS \[login.microsoftonline.com\].
  * Zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS možné se připojit ke koncovému bodu trezoru klíčů.
  * Virtuální počítač IaaS musí být schopný se připojit k úložišti Azure koncový bod, který je hostitelem úložiště rozšíření Azure a účet úložiště Azure, který je hostitelem souborů virtuálního pevného disku.

  > [!NOTE]
  > Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure k Internetu, můžete předchozí URI vyřešit a nakonfigurovat konkrétní pravidlo povolení odchozí připojení k IP adresy.
  >
  >Ke konfiguraci a za bránu firewall (přístup k Azure Key Vaulthttps://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Použijte nejnovější verzi Azure PowerShell SDK verze konfigurovat Azure Disk Encryption. Stáhněte si nejnovější verzi [verzi prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Azure Disk Encryption není podporována na [Azure PowerShell SDK verze 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Pokud jste obdrželi chybu související s použitím prostředí Azure PowerShell 1.1.0, najdete v části [Azure Disk Encryption chyby související s Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Pokud chcete spustit libovolný příkaz příkazového řádku Azure CLI a přidružit ho ke svému předplatnému Azure, musíte nejprve nainstalovat rozhraní příkazového řádku Azure:
  * Chcete-li nainstalovat rozhraní příkazového řádku Azure a přidružit ho ke svému předplatnému Azure, najdete v části [postup instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
  * Pomocí příkazového řádku Azure CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru naleznete v části [rozhraní příkazového řádku Azure v režimu Resource Manager](../virtual-machines/azure-cli-arm-commands.md).

* Při šifrování se spravovaným diskem, je povinné požadavek na převedení snímek spravovaného disku nebo zálohování na disk mimo Azure Disk Encryption před povolením šifrování.  Bez předchozího provedení zálohy na místě jakákoli Neočekávaná chyba při šifrování může vykreslit disk a virtuální počítač nedostupný bez možnosti obnovení.  Set-AzureRmVMDiskEncryptionExtension není aktuálně zálohovat spravovaných disků a bude chyba, je-li použít u se spravovaným diskem, pokud je zadán parametr - skipVmBackup.  Tento parametr nebezpečné pro používání Pokud zálohu již byl změněn mimo Azure Disk Encryption.   Pokud je zadán parametr - skipVmBackup, rutina nebude vytvořit záložní kopii spravovaných disků před šifrování.  Z tohoto důvodu bude považován za povinný předpokladem pro zajistěte, aby zálohování spravovaného disku virtuálního počítače je na místě před povolením Azure Disk Encryption, v případě, že je později potřeba obnovení.  
> [!NOTE]
 > Parametr - skipVmBackup by nikdy nepoužívali, pokud snímek nebo zálohování již byl změněn mimo Azure Disk Encryption. 

* Řešení Azure Disk Encryption používá ochrana externí klíče nástroje BitLocker pro virtuální počítače IaaS Windows. Pro doménu připojené k virtuální počítače, nesmí push žádné zásady skupiny, které vynutit ochrany pomocí čipu TPM. Informace o zásadách skupiny pro "Povolit nástroj BitLocker bez kompatibilním čipem TPM" najdete v tématu [referenční příručka zásad skupiny Bitlockeru](https://technet.microsoft.com/library/ee706521).
* Zásady nástroje BitLocker na virtuální počítače připojené k doméně pomocí zásad skupiny vlastní musí zahrnovat následující nastavení: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption se nezdaří, pokud nejsou kompatibilní, nastavení zásad vlastní skupiny pro Bitlocker. Na počítačích, které nemá správné zásady může být nastavení, použít nové zásady, vynucení nové zásady aktualizace (gpupdate.exe/Force) a pak restartování požadováno.  
* Pokud chcete vytvořit aplikaci Azure AD, vytvoření trezoru klíčů, nebo nastavit existující trezor klíčů a povolit šifrování, najdete v článku [požadovaných skript prostředí PowerShell Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Konfigurace šifrování disku požadavků pomocí Azure CLI, najdete v části [tento skript Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Pomocí služby Azure Backup k zálohování a obnovení virtuálních počítačů služby šifrovaná, pokud je povolené šifrování s Azure Disk Encryption, zašifrujte virtuální počítače pomocí klíče konfigurace Azure Disk Encryption. Služba zálohování podporuje virtuální počítače, které jsou šifrované pomocí ne KEK nebo KEK konfigurace. V tématu [šifrované zálohování a obnovení virtuálních počítačů s šifrováním Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* Při šifrování svazku operačního systému Linux, je momentálně nevyžaduje na konci procesu Všimněte si, že virtuální počítač restartovat. To lze provést přes portál, prostředí powershell nebo rozhraní příkazového řádku.   Pokud chcete sledovat průběh šifrování, pravidelně dotazovat stavovou zprávu vrácený Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Po dokončení šifrování bude tuto oznámí stavovou zprávu vrácený tento příkaz. Například "ProgressMessage: disk operačního systému úspěšně šifrování, restartujte virtuální počítač" v tomto okamžiku může být virtuální počítač restartovat a použita.  

* Azure Disk Encryption pro Linux vyžaduje datové disky tak, aby měl systém připojeného souboru v systému Linux před šifrování

* Rekurzivní připojených data, která nepodporuje disky Azure Disk Encryption pro Linux. Například pokud cílovém systému má v řádku/foo/připojit disk a potom jiné na /foo/bar/baz, šifrování /foo/bar/baz bude úspěšné, ale šifrování s/foo nebo se nezdaří. 

* Azure Disk Encryption je podporován pouze v galerii Azure, které jsou podporované Image, které splňují zmíněnými požadavky. Zákazník vlastními obrázky nejsou podporovány z důvodu vlastní oddíl schémata a proces chování, které mohou existovat na těchto bitových kopií. Navíc založená i galerie image Virtuálního počítače, který původně splněny požadavky ale byly upraveny po vytvoření může být nekompatibilní.  Pro tento návrhy postupu pro šifrování virtuálního počítače s Linuxem z toho důvodu je spustit z Galerie vyčištění bitové kopie, šifrování virtuálního počítače a poté přidejte vlastní softwaru nebo data na virtuální počítač podle potřeby.  

* Azure Disk Encryption a místní datový svazek - Bek svazek pro systém Windows a/mnt/azure_bek_disk pro virtuální počítače IaaS Linux pro bezpečné uložení šifrovacího klíče. Odstranit nebo upravit všechny obsah v tomto disku. Vzhledem k tomu, že šifrovací klíče přítomnosti je potřeba pro všechny operace šifrování pro virtuální počítač IaaS není odpojit disk. Soubor README součástí svazek obsahuje další podrobnosti.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Nastavit aplikaci Azure AD ve službě Azure Active Directory
Pokud je třeba šifrování povolení na spuštění virtuálního počítače v Azure, Azure Disk Encryption generuje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů se vyžaduje ověřování Azure AD.

Pro tento účel vytvořte aplikaci Azure AD. Najdete podrobné pokyny pro registraci aplikace v části "Získat Identity pro aplikace" v příspěvku blogu [Azure Key Vault - krok za krokem](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Tento příspěvek také obsahuje řadu užitečné příklady pro nastavení a konfiguraci trezoru klíčů. Pro účely ověření můžete použít buď ověřování na základě tajný klíč klienta nebo ověřování klientů na základě certifikátů Azure AD.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Ověřování na základě tajný klíč klienta pro Azure AD
Následující části můžete nakonfigurovat ověřování na základě tajný klíč klienta pro Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Vytvořit aplikaci Azure AD pomocí Azure PowerShell
Pomocí následující rutiny prostředí PowerShell vytvořit aplikaci Azure AD:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId Azure AD ClientID a $aadClientSecret je sdílený tajný klíč klienta, který by měl později použít k povolení Azure Disk Encryption. Správně zabezpečit tajný klíč klienta Azure AD.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Nastavení ID klienta Azure AD a tajný klíč z portálu Azure
Můžete také nastavit tak vaše ID klienta Azure AD a tajný klíč pomocí portálu Azure. K provedení této úlohy, postupujte takto:

1. Vyberte **všechny služby > Azure Active Directory**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-service.png)

2. Vyberte **registrace aplikace > novou registraci aplikace**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-app-registration.png)

3. Zadejte požadované informace a vytvoření aplikace:

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-app.png)

4. Vyberte nově vytvořenou aplikaci a zobrazte její vlastnosti, včetně ID aplikace.  Chcete-li vytvořit klíč pro aplikaci, vyberte **Nastavení > klíče**, přidat popis a vypršení platnosti pro klíč a klikněte na tlačítko **uložit**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-pw.png)

5. Zkopírujte vygenerovaný tajná hodnota a chrání ji odpovídajícím způsobem.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>Použít stávající aplikaci
Pokud chcete spustit následující příkazy, získat a použít [modulu Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Je třeba spustit následující příkazy z nové okno prostředí PowerShell. Nepoužívejte provést příkazy prostředí Azure PowerShell nebo v okně Azure Resource Manager. Doporučujeme vám tento přístup, protože jsou tyto rutiny v modulu MSOnline nebo Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Ověřování pomocí certifikátů pro Azure AD
> [!NOTE]
> Na virtuální počítače s Linuxem se aktuálně nepodporuje ověřování pomocí certifikátů Azure AD.

V následujících ukazují, jak nakonfigurovat ověřování pomocí certifikátů pro Azure AD.

##### <a name="create-an-azure-ad-application"></a>Vytvořit aplikaci Azure AD
Pokud chcete vytvořit aplikaci Azure AD, spusťte následující rutiny prostředí PowerShell:

> [!NOTE]
> Nahraďte následující `yourpassword` řetězec pomocí zabezpečeného hesla a zabezpečit heslo.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Po dokončení tohoto kroku můžete nahrát soubor PFX do trezoru klíčů a povolte zásady přístupu, které jsou potřeba k nasazení tohoto certifikátu pro virtuální počítač.

##### <a name="use-an-existing-azure-ad-application"></a>Použít existující aplikaci Azure AD
Pokud nakonfigurujete ověřování pomocí certifikátů pro existující aplikace, použijte rutiny prostředí PowerShell, které jsou tady uvedené. Ujistěte se, že je provést z nové okno prostředí PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Po dokončení tohoto kroku můžete nahrát soubor PFX do trezoru klíčů a povolte zásady přístupu, které je potřeba k nasazení certifikátu k virtuálnímu počítači.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Nahrání souboru PFX do trezoru klíčů
Podrobné vysvětlení tohoto procesu najdete v tématu [oficiální Azure Key Vault Blog týmu](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Následující rutiny prostředí PowerShell jsou však všechny, které jsou potřeba pro úlohu. Ujistěte se, že je provést z konzoly Azure PowerShell.

> [!NOTE]
> Nahraďte následující `yourpassword` řetězec pomocí zabezpečeného hesla a zabezpečit heslo.

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

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Nasaďte certifikát v trezoru klíčů na existující virtuální počítač
Po dokončení nahrávání soubor PFX, nasaďte certifikát v trezoru klíčů na existující virtuální počítač s následujícími službami:
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

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Nastavte zásady přístupu trezoru klíčů pro aplikace Azure AD
Aplikace Azure AD musí práva pro přístup k klíčů nebo tajných klíčů v trezoru. Použití [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) rutiny udělit oprávnění k aplikaci, pomocí ID klienta (který byl vygenerován při aplikaci byl zaregistrován) jako _– ServicePrincipalName_ hodnota parametru. Další informace naleznete v příspěvku blogu [Azure Key Vault - krok za krokem](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Tady je příklad toho, jak to provést pomocí prostředí PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption vyžaduje, abyste nakonfigurovali následující zásady přístupu k vaší aplikaci klienta Azure AD: _WrapKey_ a _nastavit_ oprávnění.

## <a name="terminology"></a>Terminologie
Abyste pochopili, některé z běžných podmínek používá tuto technologii, použijte následující tabulku terminologie:

| Terminologie | Definice |
| --- | --- |
| Azure AD | Azure AD je [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Účet Azure AD je předpokladem pro ověřování, ukládání a načítání tajné klíče z trezoru klíčů. |
| Azure Key Vault | Key Vault je služba kryptografické klíče správy, která je založena na moduly ověřit Federal Information Processing Standards FIPS hardwarového zabezpečení, které v zájmu ochrany kryptografické klíče a tajné klíče citlivé. Další informace najdete v tématu [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| ARM | Azure Resource Manager |
| BitLocker |[Nástroj BitLocker](https://technet.microsoft.com/library/hh831713.aspx) je rozpoznána odvětví Windows svazku šifrovací technologie, která slouží k povolení šifrování disku na virtuální počítače IaaS Windows. |
| BEK | Nástroj BitLocker šifrovací klíče se používají k šifrování spouštěcí svazek operačního systému a datové svazky. Klíče Bitlockeru chráněna jako tajných klíčů v trezoru klíčů. |
| Rozhraní příkazového řádku | V tématu [rozhraní příkazového řádku Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) je podsystém systémem Linux, transparentní-šifrování disku, který slouží k povolení šifrování disku na virtuální počítače IaaS Linux. |
| KEK | Klíče šifrovací klíč je asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajný klíč. Můžete zadat hardwaru zabezpečení (HSM) moduly-chráněný klíč, nebo softwarově chráněný klíč. Další podrobnosti najdete v tématu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentaci. |
| PS rutiny | V tématu [rutin prostředí Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Nastavení a konfigurace trezoru klíčů pro Azure Disk Encryption
Azure Disk Encryption pomáhá chránit šifrování disku klíče a tajné klíče v trezoru klíčů. Pro nastavení trezoru klíčů pro Azure Disk Encryption, proveďte kroky v každé z těchto částí.

#### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
Vytvoření trezoru klíčů, použijte jednu z následujících možností:

* ["101-Key-trezoru-vytvořit" šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Rutiny Azure PowerShell trezoru klíčů](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Postup [zabezpečit váš trezor klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Pokud jste již nastavili trezoru klíčů pro vaše předplatné, přejděte k další části.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Nastavení klíče šifrovací klíč (volitelné)
Pokud chcete použít pro další úroveň zabezpečení pro šifrovací klíče nástroje BitLocker KEK, přidejte k trezoru klíčů KEK. Použití [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) vytvořte klíč šifrovacího klíče v trezoru klíčů. Můžete také importovat KEK z vaší místní správy k klíče HSM. Další podrobnosti najdete v tématu [klíč trezoru dokumentaci](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Klíče KEK můžete přidat tak, že přejdete do Azure Resource Manageru nebo přes rozhraní váš trezor klíčů.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Nastavte oprávnění trezoru klíčů
Platformy Azure potřebuje přístup k šifrovacích klíčů nebo tajných klíčů v trezoru klíčů, aby je bylo možné virtuální počítač pro spouštění a dešifrování svazky. Chcete-li udělit oprávnění pro platformu Azure, nastavte **EnabledForDiskEncryption** vlastnost klíč trezoru pomocí rutiny prostředí PowerShell trezoru klíčů:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Můžete také nastavit **EnabledForDiskEncryption** vlastnost navštivte stránky [Průzkumníka prostředků Azure](https://resources.azure.com).

Jak už bylo zmíněno dříve, je nutné nastavit **EnabledForDiskEncryption** vlastnost v trezoru klíčů. Jinak se nasazení nezdaří.

Můžete nastavit pro vaši aplikaci Azure AD z rozhraní trezoru klíčů, zásady přístupu, jak je vidět tady:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Na **zásady rozšířené přístupu** kartě, ujistěte se, že váš trezor klíčů je povolena pro Azure Disk Encryption:

![Azure trezoru klíčů](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scénáře nasazení šifrování disku a koncových uživatelů
Můžete povolit mnoho scénářů šifrování disku, a kroky mohou lišit v závislosti scénáři. Následující části se věnují scénáře podrobněji.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Povolit šifrování na nové virtuální počítače IaaS, vytvořený z Marketplace
Můžete povolit šifrování disku na nový virtuální počítač IaaS Windows v Marketplace v Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. V šabloně Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci šifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a pak klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

> [!NOTE]
> Tato šablona vytvoří nový virtuální počítač šifrované Windows, která využívá image systému Windows Server 2012 galerie.

Můžete povolit šifrování disku na nový IaaS RedHat Linux 7.2 virtuální počítač s polem RAID-0 200 GB prostřednictvím tohoto [šablony Resource Manageru](https://aka.ms/fde-rhel). Po nasazení šablony, ověřte stav šifrování virtuálních počítačů pomocí `Get-AzureRmVmDiskEncryptionStatus` rutiny, jak je popsáno v [OS šifrování jednotky na spuštěný virtuální počítač s Linuxem](#encrypting-os-drive-on-a-running-linux-vm). Když je počítač vrátí stav _VMRestartPending_, restartujte virtuální počítač.

Následující tabulka uvádí parametrů šablony Resource Manageru pro nové virtuální počítače z Marketplace scénáře pomocí ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| adminUserName | Uživatelské jméno správce pro virtuální počítač. |
| adminPassword | Uživatelské heslo správce pro virtuální počítač. |
| newStorageAccountName | Název účtu úložiště pro ukládání operačního systému a dat virtuální pevné disky. |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze standardní A, D a G řady. |
| virtualNetworkName | Název sítě VNet, která na síťový adaptér virtuálního počítače by měly patřit do. |
| subnetName | Název v síti VNet, která na síťový adaptér virtuálního počítače by měly patřit do podsítě. |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| AADClientSecret | Tajný klíč klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultURL | Adresa URL trezoru klíčů, který klíč Bitlockeru, musí být nahrán do. Můžete ho získat pomocí rutiny `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | Adresa URL klíče šifrovací klíč, který se používá k šifrování generovaný klíč nástroje BitLocker (volitelné). |
| keyVaultResourceGroup | Skupina prostředků služby key vault. |
| vmName | Název virtuálního počítače, který operace šifrování je třeba provést na. |

> [!NOTE]
> _KeyEncryptionKeyURL_ je volitelný parametr. Můžete zahrnout vlastní KEK na další chránit datový šifrovací klíč (heslo tajný klíč) v trezoru klíčů.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Povolit šifrování na nové virtuální počítače IaaS, vytvořený z šifrované zákazníka virtuálního pevného disku a šifrovacích klíčů
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutiny prostředí PowerShell nebo rozhraní příkazového řádku. Následující části popisují podrobněji šablony Resource Manageru a rozhraní příkazového řádku.

Postupujte podle pokynů z jednoho z těchto částí pro přípravu předem šifrované bitové kopie, které lze použít v Azure. Po vytvoření image slouží k vytvoření šifrované virtuální počítač Azure podle kroků v další části.

* [Příprava předem šifrované virtuálního pevného disku Windows](#preparing-a-pre-encrypted-windows-vhd)
* [Příprava předem šifrované Linux virtuální pevný disk](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Pomocí šablony Resource Manageru
Můžete povolit šifrování disku na svůj šifrované disk VHD pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. V šabloně Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci šifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a pak klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

Následující tabulka uvádí parametrů šablony Resource Manageru pro vaše virtuální pevný disk šifrovaný:

| Parametr | Popis |
| --- | --- |
| newStorageAccountName | Název účtu úložiště pro ukládání šifrované virtuální pevný disk operačního systému. Tento účet úložiště by byl již vytvořen ve stejné skupině prostředků a stejné umístění jako virtuální počítač. |
| osVhdUri | Identifikátor URI virtuálního pevného disku operačního systému z účtu úložiště. |
| osType | Typ produktu operačního systému (Windows nebo Linuxem). |
| virtualNetworkName | Název sítě VNet, která na síťový adaptér virtuálního počítače by měly patřit do. Název by měl byl již vytvořen ve stejné skupině prostředků a stejné umístění jako virtuální počítač. |
| subnetName | Název v síti VNet, která na síťový adaptér virtuálního počítače by měly patřit do podsítě. |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze standardní A, D a G řady. |
| keyVaultResourceID | ID prostředku, který identifikuje prostředek trezoru klíčů ve službě Správce prostředků Azure. Můžete ho získat pomocí rutiny prostředí PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | Adresa URL disku šifrovací klíč, který je nastavený v trezoru klíčů. |
| keyVaultKekUrl | Adresa URL klíče šifrovacího klíče pro šifrování klíče generované šifrování disku. |
| vmName | Název virtuálního počítače IaaS. |

#### <a name="using-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Můžete povolit šifrování disku na svůj disk VHD šifrované pomocí rutiny prostředí PowerShell [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Pomocí rozhraní příkazového řádku
Pokud chcete povolit šifrování disku pro tento scénář pomocí rozhraní příkazového řádku, postupujte takto:

1. Nastavení zásad přístupu v trezoru klíčů:

   * Nastavte **EnabledForDiskEncryption** příznak:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Nastavte oprávnění k zápisu tajných klíčů do trezoru klíčů aplikace Azure AD:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Pokud chcete povolit šifrování na stávající nebo spuštěný virtuální počítač, zadejte:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Načíst stav šifrování:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Chcete-li povolit šifrování na nový virtuální počítač z vaší šifrované virtuálního pevného disku, použijte následující parametry s `azure vm create` příkaz:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Povolit šifrování na existující nebo běžící virtuální počítače IaaS Windows v Azure
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutiny prostředí PowerShell nebo rozhraní příkazového řádku. Následující části popisují podrobněji povolení pomocí šablony Resource Manageru a rozhraní příkazového řádku.

#### <a name="using-the-resource-manager-template"></a>Pomocí šablony Resource Manageru
Můžete povolit šifrování disku na existující nebo spuštěných virtuálních počítačů IaaS Windows Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. V šabloně Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci šifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a pak klikněte na tlačítko **vytvořit** chcete povolit šifrování na stávající nebo spuštěných virtuálních počítačů IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spuštěných virtuálních počítačů, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| AADClientSecret | Tajný klíč klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, který klíč Bitlockeru, musí být nahrán do. Můžete ho získat pomocí rutiny `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adresa URL klíče šifrovací klíč, který se používá k šifrování generovaný klíč nástroje BitLocker. Tento parametr je nepovinný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, je nutné zadat _keyEncryptionKeyURL_ hodnotu. |
| volumeType | Typ svazku, který operace šifrování. Platné hodnoty jsou _OS_, _Data_, a _všechny_. |
| sequenceVersion | Pořadí verze operace nástroje BitLocker. Toto číslo verze zvýší pokaždé, když se operace šifrování disku provádí na stejného virtuálního počítače. |
| vmName | Název virtuálního počítače, který operace šifrování je třeba provést na. |

> [!NOTE]
> _KeyEncryptionKeyURL_ je volitelný parametr. Vlastní KEK můžete zahrnout do další chránit datový šifrovací klíč (šifrování nástroje BitLocker tajný klíč) v trezoru klíčů.

#### <a name="using-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Informace o povolení šifrování s Azure Disk Encryption pomocí rutin prostředí PowerShell, najdete v příspěvcích na blogu [prozkoumat Azure Disk Encryption s prostředím Azure PowerShell – část 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) a [prozkoumat Azure Disk Encryption v prostředí Azure PowerShell - část 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Pomocí rozhraní příkazového řádku
Pokud chcete povolit šifrování na existující nebo spuštěné virtuální počítače IaaS Windows v Azure pomocí rozhraní příkazového řádku, postupujte takto:

1. Nastavení zásad přístupu v trezoru klíčů:
   * Nastavte **EnabledForDiskEncryption** příznak:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Nastavte oprávnění k zápisu tajných klíčů do trezoru klíčů aplikace Azure AD:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Chcete-li povolit šifrování na stávající nebo spuštěný virtuální počítač:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Pokud chcete získat stav šifrování:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Chcete-li povolit šifrování na nový virtuální počítač z vaší šifrované virtuálního pevného disku, použijte následující parametry s `azure vm create` příkaz:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Povolit šifrování na existující nebo spuštěné IaaS Linux virtuální počítač v Azure
Můžete povolit šifrování disku na existující nebo spuštěné IaaS Linux virtuální počítač v Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klikněte na tlačítko **nasadit do Azure** v šabloně Azure rychlý start, zadejte konfiguraci šifrování **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a pak klikněte na tlačítko **vytvořit** chcete povolit šifrování na stávající nebo spuštěných virtuálních počítačů IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spuštěných virtuálních počítačů, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| AADClientSecret | Tajný klíč klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, který klíč Bitlockeru, musí být nahrán do. Můžete ho získat pomocí rutiny `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adresa URL klíče šifrovací klíč, který se používá k šifrování generovaný klíč nástroje BitLocker. Tento parametr je nepovinný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, je nutné zadat _keyEncryptionKeyURL_ hodnotu. |
| volumeType | Typ svazku, který operace šifrování. Platné podporované hodnoty jsou _OS_ nebo _všechny_ (distribucích systému Linux najdete v tématu podporované a jejich verze pro operační systém a datové disky v dřívější části prerequisiteis). |
| sequenceVersion | Pořadí verze operace nástroje BitLocker. Toto číslo verze zvýší pokaždé, když se operace šifrování disku provádí na stejného virtuálního počítače. |
| vmName | Název virtuálního počítače, který operace šifrování je třeba provést na. |
| passPhrase | Zadejte silné heslo jako datový šifrovací klíč. |

> [!NOTE]
> _KeyEncryptionKeyURL_ je volitelný parametr. Můžete zahrnout vlastní KEK na další chránit datový šifrovací klíč (heslo tajný klíč) v trezoru klíčů.

#### <a name="cli-commands"></a>Rozhraní příkazového řádku
Můžete povolit šifrování disku na svůj disk VHD šifrované instalaci a použití [rozhraní příkazového řádku příkaz](../cli-install-nodejs.md). Pokud chcete povolit šifrování na existující nebo běžící virtuální počítače IaaS Linux v Azure pomocí rozhraní příkazového řádku, postupujte takto:

1. Nastavit zásady přístupu v trezoru klíčů:

 * Nastavte **EnabledForDiskEncryption** příznak:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Nastavte oprávnění k zápisu tajných klíčů do trezoru klíčů aplikace Azure AD:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Chcete-li povolit šifrování na stávající nebo spuštěný virtuální počítač:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Načíst stav šifrování:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Chcete-li povolit šifrování na nový virtuální počítač z vaší šifrované virtuálního pevného disku, použijte následující parametry s `azure vm create` příkaz:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Načíst stav šifrování virtuálního počítače šifrovaný IaaS
Pomocí Azure Resource Manager, můžete získat stav šifrování [rutiny prostředí PowerShell](/powershell/azure/overview), nebo rozhraní příkazového řádku. Následující části popisují, jak načíst stav šifrování pomocí webu Azure Portal a rozhraní příkazového řádku.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Načíst stav šifrování šifrovaných virtuální počítač Windows pomocí Azure Resource Manager
Stav šifrování virtuálních počítačů IaaS můžete získat z Azure Resource Manager následujícím postupem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/)a potom klikněte na **virtuální počítače** v levém podokně zobrazíte souhrnné zobrazení virtuálních počítačů v rámci vašeho předplatného. Můžete filtrovat zobrazení virtuálních počítačů tak, že vyberete název odběru v **předplatné** rozevíracího seznamu.

2. V horní části **virtuální počítače** klikněte na tlačítko **sloupce**.

3. Na **vyberte sloupec** vyberte **šifrování disku**a potom klikněte na **aktualizace**. Měli byste vidět šifrování disku sloupec zobrazuje stav šifrování _povoleno_ nebo _Nepovoleno_ pro každý virtuální počítač, jak je znázorněno na následujícím obrázku:

 ![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Načíst stav šifrování virtuálního počítače šifrovaný IaaS (Windows nebo Linuxem) pomocí rutiny prostředí PowerShell šifrování disku
Stav šifrování virtuálních počítačů IaaS můžete získat z rutiny prostředí PowerShell šifrování disku `Get-AzureRmVMDiskEncryptionStatus`. Chcete-li získat nastavení šifrování pro virtuální počítač, zadejte následující příkaz:

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

Nastavení hodnoty OSVolumeEncrypted a DataVolumesEncrypted jsou nastaveny na _šifrovaný_, který ukazuje, že jsou oba svazky šifrované pomocí Azure Disk Encryption. Informace o povolení šifrování s Azure Disk Encryption pomocí rutin prostředí PowerShell, najdete v příspěvcích na blogu [prozkoumat Azure Disk Encryption s prostředím Azure PowerShell – část 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) a [prozkoumat Azure Disk Encryption v prostředí Azure PowerShell - část 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Na virtuální počítače s Linuxem, trvá tři až čtyři minut `Get-AzureRmVMDiskEncryptionStatus` rutiny nahlásit stav šifrování.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Načíst stav šifrování virtuálních počítačů IaaS z rozhraní příkazového řádku příkaz šifrování disku
Stav šifrování virtuálních počítačů IaaS můžete získat pomocí rozhraní příkazového řádku příkaz šifrování disku `azure vm show-disk-encryption-status`. Chcete-li získat nastavení šifrování pro virtuální počítač, zadejte relaci příkazového řádku Azure CLI:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Zakázat šifrování u spuštěných virtuálních počítačů IaaS Windows
Můžete zakázat šifrování na spuštěný Windows nebo virtuálních počítačů IaaS Linux pomocí šablony Azure Disk Encryption Resource Manageru nebo rutiny prostředí PowerShell a zadejte konfiguraci dešifrování.

##### <a name="windows-vm"></a>Virtuální počítač s Windows
Zakázat šifrování krok zakazuje šifrování operačního systému, datový svazek nebo obojí do spuštěného virtuálního počítače Windows IaaS. Nelze zakázat svazku operačního systému a nechte datový svazek zašifrovaná. Při provádění krok zakázat šifrování modelu nasazení Azure classic aktualizace modelu služby virtuálních počítačů a virtuálních počítačů IaaS Windows je označena dešifrovaný. Obsah virtuálního počítače jsou již v zašifrované podobě. Dešifrování nedojde k odstranění trezoru klíčů a materiál klíče pro šifrování (šifrovací klíče nástroje BitLocker pro systém Windows a heslo pro Linux).

##### <a name="linux-vm"></a>Virtuální počítač s Linuxem
Zakázat šifrování krok zakazuje šifrování datový svazek na spuštěného virtuálního počítače s Linuxem IaaS. Tento krok je funkční pouze v případě disk operačního systému není zašifrován.

> [!NOTE]
> Zakázáním šifrování na disk operačního systému není povolená na virtuální počítače s Linuxem.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Zakázat šifrování na stávající nebo spuštěný virtuální počítač IaaS
Můžete zakázat šifrování disku na spuštěných virtuálních počítačů IaaS Windows pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. V šabloně Azure rychlý start, klikněte na tlačítko **nasadit do Azure**, zadejte v konfiguraci dešifrování na **parametry** okna a pak klikněte na tlačítko **OK**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, právní podmínky a smlouvy a pak klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

Pro virtuální počítače s Linuxem, můžete zakázat šifrování pomocí [vypnout šifrování u spuštěného virtuálního počítače s Linuxem](https://aka.ms/decrypt-linuxvm) šablony.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro zakázáním šifrování na spuštěný virtuální počítač IaaS:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače, který operace šifrování je třeba provést na.
| volumeType | Typ svazku, který na se provést operaci dešifrování. Platné hodnoty jsou _OS_, _Data_, a _všechny_. Nelze zakázat šifrování u spuštěných virtuálních počítačů IaaS Windows operačního systému nebo spouštěcí svazek bez zakázáním šifrování na _Data_ svazku. Všimněte si také, že zakázáním šifrování na disk operačního systému není povolená na virtuální počítače s Linuxem. |
| sequenceVersion | Pořadí verze operace nástroje BitLocker. Toto číslo verze zvýší pokaždé, když provádí operaci dešifrování disku na stejného virtuálního počítače. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Zakázat šifrování na stávající nebo spuštěný virtuální počítač IaaS
Můžete zakázat šifrování na stávající nebo spuštěný virtuální počítač IaaS pomocí rutiny prostředí PowerShell, najdete v části [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Tato rutina podporuje systém Windows a virtuální počítače s Linuxem. Můžete zakázat šifrování, nainstaluje rozšíření ve virtuálním počítači. Pokud _název_ není zadán parametr, rozšíření s výchozím názvem _AzureDiskEncryption virtuálních počítačů pro Windows_ je vytvořena.

Na virtuální počítače s Linuxem je použít AzureDiskEncryptionForLinux rozšíření.

> [!NOTE]
> Tato rutina restartuje virtuální počítač.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Povolit šifrování na předem šifrované virtuálních počítačů IaaS s diskem spravované Azure
Pomocí šablony Azure ARM disku spravované vytvoření šifrované virtuálního počítače z disku VHD předem šifrované pomocí šablony ARM nacházející se v   
[Vytvořit nové šifrované spravovaným diskem z předem šifrované objektu blob virtuálního pevného disku nebo úložiště] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Povolit šifrování na nový virtuální počítač IaaS Linux s diskem spravované Azure
Pomocí šablony Azure ARM disku spravované k vytvoření nové šifrovaný IaaS virtuálního počítače s Linuxem pomocí šablony ARM nacházející se v   
[Nasazení RHEL 7.2 s šifrování celého disku] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Povolit šifrování na nový virtuální počítač IaaS Windows s Azure spravované disku
 Pomocí šablony Azure ARM disku spravované k vytvoření nové šifrovaný IaaS virtuálního počítače s Linuxem pomocí šablony ARM nacházející se v   
 [Vytvořit nové šifrovaný IaaS spravované disku virtuální počítač s Windows z Galerie obrázek] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Je nutné snímku nebo zálohování spravovaných disků na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption.  Snímek spravovaného disku můžete provést z portálu, nebo lze použít Azure Backup.  Zálohování zajistěte, aby byla možnost obnovení možné v případě jakékoli neočekávané selhání při šifrování.  Po provedení zálohy rutinu Set-AzureRmVMDiskEncryptionExtension slouží k šifrování spravované disky zadáním parametru - skipVmBackup.  Tento příkaz se nezdaří proti spravovaných disků na základě Virtuálního počítače, dokud zálohu byl změněn a tento parametr byla zadána.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Aktualizovat nastavení šifrování pro existující virtuálního počítače šifrovaná-úrovně premium
  Použít existující šifrování podporované rozhraní Azure disk pro spuštění virtuálního počítače [PS rutiny, rozhraní příkazového řádku nebo ARM šablon] aktualizovat nastavení šifrování typu klient AAD ID nebo tajný klíč šifrovacího klíče [KEK] šifrovací klíč nástroje BitLocker pro virtuální počítač s Windows nebo přístupové heslo pro virtuální počítač s Linuxem atd. Nastavení šifrování aktualizace je podporována pro premium a neprémiové úložiště virtuálních počítačů.

## <a name="appendix"></a>Příloha
### <a name="connect-to-your-subscription"></a>Připojení k vašemu předplatnému
Než budete pokračovat, zkontrolujte *požadavky* v tomto článku. Po zajištění, že byly splněny všechny požadavky, připojení k vašemu předplatnému následujícím způsobem:

1. Spusťte relaci prostředí Azure PowerShell a přihlaste se k účtu Azure pomocí následujícího příkazu:

    `Login-AzureRmAccount`

2. Pokud máte více předplatných a chcete zadat jeden použít, zadejte následující příkaz pro zobrazení předplatných pro váš účet:

    `Get-AzureRmSubscription`

3. Chcete-li zadat odběr, který chcete použít, zadejte:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Chcete-li ověřit správnost předplatné nakonfigurované, zadejte:

    `Get-AzureRmSubscription`

5. Pokud chcete potvrdit, jestli že jsou nainstalované rutiny Azure Disk Encryption, zadejte:

    `Get-command *diskencryption*`

6. Tento výstup potvrdí instalace Azure Disk Encryption PowerShell:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Příprava předem šifrované virtuálního pevného disku Windows
Následující části jsou nezbytné pro přípravu předem šifrované virtuálního pevného disku Windows pro nasazení jako šifrované virtuální pevný disk v Azure IaaS. Použijte informace k přípravě a spustit novou Windows virtuálních počítačů (VHD) na Azure Site Recovery nebo Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizace zásad skupiny umožňující bez čipu TPM k ochraně operačního systému
Konfigurace nastavení zásad skupiny pro BitLocker **nástroj BitLocker Drive Encryption**, které najdete v části **zásady místního počítače** > **konfigurace počítače**  >  **Šablony pro správu** > **součásti systému Windows**. Změňte toto nastavení chcete **jednotky operačního systému** > **vyžadovat další ověření při spuštění** > **povolit nástroj BitLocker bez kompatibilním čipem TPM**, jak je znázorněno na následujícím obrázku:

![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Nainstalujte komponenty funkce nástroje BitLocker
Pro Windows Server 2012 a novější použijte následující příkaz:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Pro Windows Server 2008 R2 použijte následující příkaz:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Příprava svazku operačního systému pomocí nástroje BitLocker `bdehdcfg`
Pokud chcete komprimovat oddílu operačního systému a příprava počítače pro nástroj BitLocker, spusťte následující příkaz:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrana svazku operačního systému pomocí nástroje BitLocker
Použití [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) příkaz Povolit šifrování na spouštěcím svazku pomocí externí ochranné zařízení klíče. Také umístíte externí klíč (soubor .bek) na externím disku nebo svazku. Po dalším restartování je povolené šifrování v systému nebo spouštěcí svazek.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Příprava virtuálního počítače s samostatné dat nebo prostředků virtuálního pevného disku pro získání externí klíče pomocí nástroje BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Šifrování jednotce operačního systému na spuštěný virtuální počítač s Linuxem

##### <a name="prerequisites-for-os-disk-encryption"></a>Požadavky pro šifrování disku operačního systému

* Virtuální počítač musí používat kompatibilní s šifrování disku operačního systému distribučního popsaných [Azure Disk Encryption – nejčastější dotazy](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support) 
* Virtuální počítač musí být vytvořený z Marketplace image ve službě Správce prostředků Azure.
* Virtuální počítač Azure s minimálně 4 GB paměti RAM (doporučená velikost je 7 GB).
* (Pro RHEL a CentOS) Zakážete SELinux. Pokud chcete zakázat SELinux, najdete v části "4.4.2. Zakázání SELinux"v [Průvodce SELinux uživatele a správce](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) ve virtuálním počítači.
* Po zakázání SELinux restartujte virtuální počítač alespoň jednou.

##### <a name="steps"></a>Kroky
1. Vytvoření virtuálního počítače pomocí jednoho z rozdělení zadali dřív.

 7.2 CentOS je podporováno šifrování disku operačního systému přes speciální bitové kopie. Chcete-li použít tuto bitovou kopii, zadejte "7.2n" jako verze SKU při vytváření virtuálního počítače:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurace virtuálního počítače podle svých potřeb. Pokud budete k šifrování všech (operační systém + data) disky, musí být zadané a připojit z /etc/fstab datové jednotky.

 > [!NOTE]
 > Použít UUID =... k určení datových jednotkách v fstab/etc/místo zadání názvu zařízení blok (například/dev/sdb1). Během šifrování se změní pořadí jednotky ve virtuálním počítači. Pokud virtuálního počítače závisí na konkrétní pořadí blokovat zařízení, se nezdaří připojit je po dokončení šifrování.

3. Odhlásit z relace SSH.

4. K šifrování operačního systému, zadejte volumeType jako **všechny** nebo **OS** když jste [povolit šifrování](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Všechny procesy uživatelského prostoru, které nejsou spuštěny jako `systemd` služby by měl být ukončen s `SIGKILL`. Restartování virtuálního počítače. Pokud povolíte šifrování disku operačního systému na spuštěný virtuální počítač, naplánujte na výpadek virtuálních počítačů.

5. Pravidelně sledovat průběh šifrování pomocí pokynů v [další části](#monitoring-os-encryption-progress).

6. Po Get-AzureRmVmDiskEncryptionStatus zobrazuje "VMRestartPending", restartujte virtuální počítač po přihlášení k němu nebo pomocí portálu, prostředí PowerShell nebo rozhraní příkazového řádku.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Než restartujete, doporučujeme, abyste uložili [spouštění diagnostiky](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) virtuálního počítače.

#### <a name="monitoring-os-encryption-progress"></a>Sledování průběhu šifrování operačního systému
Můžete sledovat průběh šifrování OS třemi způsoby:

* Použití `Get-AzureRmVmDiskEncryptionStatus` rutiny a zkontrolovat ProgressMessage pole:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Po virtuálního počítače dosáhne "Spuštění šifrování disku operačního systému", jak dlouho trvá asi 40 až 50 minut na storage úrovně Premium zálohovaný virtuálních počítačů.

 Z důvodu [vydání #388](https://github.com/Azure/WALinuxAgent/issues/388) v WALinuxAgent, `OsVolumeEncrypted` a `DataVolumesEncrypted` zobrazují jako `Unknown` v některých distribucích. S WALinuxAgent verze 2.1.5 a novější, se tento problém vyřešen automaticky. Pokud se zobrazí `Unknown` ve výstupu, můžete ověřit stav šifrování disku pomocí Průzkumníka prostředků Azure.

 Přejděte na [Průzkumníka prostředků Azure](https://resources.azure.com/)a potom rozbalte tuto hierarchii panelu výběr na levé straně:

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

 V InstanceView posuňte se dolů a zjistit stav šifrování jednotky.

 ![Zobrazení Instance virtuálního počítače](./media/azure-security-disk-encryption/vm-instanceview.png)

* Podívejte se na [spouštění diagnostiky](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Zprávy z rozšíření ADE by měla obsahovat předponu s `[AzureDiskEncryption]`.

* Přihlaste se k virtuálnímu počítači pomocí protokolu SSH a získat rozšíření protokolu z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Doporučujeme vám, že není přihlášení k virtuálnímu počítači v průběhu šifrování operačního systému. Zkopírujte protokoly jenom v případě, že tyto dvě metody se nezdařilo.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Příprava předem šifrované Linux virtuální pevný disk
##### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurace šifrování během instalace distribučního následujícím způsobem:

1. Vyberte **konfigurovat šifrované svazky** při vytvoření diskových oddílů.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Vytvořte samostatný spouštěcí jednotka, která nesmí být zašifrovaná. Šifrování kořenové jednotce.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Zadejte přístupové heslo. Toto je heslo, které nahrajte do trezoru klíčů.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Dokončete vytváření oddílů.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Když spustíte virtuální počítač a vyzváni k přístupové heslo, použijte přístupové heslo, které jste zadali v kroku 3.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Příprava virtuálního počítače pro odesílání do Azure pomocí [tyto pokyny](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače) ještě.

Konfigurace šifrování pro práci s Azure následujícím způsobem:

1. Vytvořte soubor pod /usr/local/sbin/azure_crypt_key.sh, s obsahem v následující skript. Věnujte pozornost KeyFileName, protože je název souboru přístupové heslo používané Azure.

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

2. Změna konfigurace crypt v */etc/crypttab*. Mělo by to vypadat takto:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Pokud upravujete *azure_crypt_key.sh* v systému Windows a můžete zkopírovat do systému Linux, spusťte `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Přidejte oprávnění spustitelný soubor skriptu:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Upravit */etc/initramfs-tools/modules* připojením řádky:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Spustit `update-initramfs -u -k all` aktualizace initramfs, aby `keyscript` vstoupila v platnost.

7. Nyní můžete zrušení zřízení virtuálního počítače.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Pokračovat k dalšímu kroku a [nahrát svůj disk VHD](#upload-encrypted-vhd-to-an-azure-storage-account) do Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Pokud chcete konfigurovat šifrování během instalace distribučního, postupujte takto:
1. Při vytvoření diskových oddílů, vyberte **šifrování svazku skupiny**a pak zadejte heslo. Toto je heslo, které odešlete do trezoru klíčů.

 ![Instalační program openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Spuštění virtuálního počítače pomocí hesla.

 ![Instalační program openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Příprava virtuálního počítače chcete nahrát do Azure podle pokynů v [Příprava virtuálního počítače, SLES nebo openSUSE pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače) ještě.

Konfigurace šifrování pro práci s Azure, postupujte takto:
1. Upravit /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Komentář tyto řádky na konci souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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
A změňte všechny výskyty:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
na:
```
    if [ 1 ]; then
```
4. Upravit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a připojit k "# otevřete LUKS zařízení":

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

6. Teď můžete zrušení zřízení virtuálního počítače a [nahrát svůj disk VHD](#upload-encrypted-vhd-to-an-azure-storage-account) do Azure.

##### <a name="centos-7"></a>CentOS 7
Pokud chcete konfigurovat šifrování během instalace distribučního, postupujte takto:
1. Vyberte **šifrovat data** při oddílu disky.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Zajistěte, aby **šifrovat** je vybraná pro kořenový oddíl.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Zadejte přístupové heslo. Toto je heslo, které odešlete do trezoru klíčů.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Když spustíte virtuální počítač a vyzváni k přístupové heslo, použijte přístupové heslo, které jste zadali v kroku 3.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Příprava virtuálního počítače pro odesílání do Azure podle pokynů "CentOS 7.0 +" v [Příprava virtuálního počítače, na základě CentOS pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače) ještě.

6. Teď můžete zrušení zřízení virtuálního počítače a [nahrát svůj disk VHD](#upload-encrypted-vhd-to-an-azure-storage-account) do Azure.

Konfigurace šifrování pro práci s Azure, postupujte takto:

1. Upravit /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Komentář tyto řádky na konci souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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
A změňte všechny výskyty:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
na
```
    if [ 1 ]; then
```
4. Upravit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a připojit toto po "# otevřete LUKS zařízení":
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

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Nahrát šifrované virtuálního pevného disku k účtu úložiště Azure
Povolíte šifrování nástrojem BitLocker nebo DM-Crypt šifrování, místní šifrované virtuálního pevného disku musí být nahrán do účtu úložiště.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Nahrát tajný klíč šifrování disku předem šifrované virtuálního počítače k trezoru klíčů
Šifrování disku tajný klíč, který jste získali dříve musí být odeslán jako tajný klíč v trezoru klíčů. Trezor klíčů musí mít oprávnění povoleno pro vašeho klienta Azure AD a šifrování disku.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Disk encryption tajný klíč není šifrován KEK
Chcete-li nastavit tajný klíč v trezoru klíčů, použijte [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Pokud máte virtuální počítače s Windows, je soubor bek kódovaný jako base64 řetězec a pak odeslat k trezoru klíčů pomocí `Set-AzureKeyVaultSecret` rutiny. Pro Linux je heslo kódovaný jako base64 řetězec a pak odeslat do trezoru klíčů. Kromě toho se ujistěte, že jsou při vytváření tajný klíč v trezoru klíčů nastavte následující značky.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Použití `$secretUrl` v dalším kroku pro [připojení disk operačního systému bez použití KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Tajný klíč šifrování disku šifrován KEK
Před nahráním tajný klíč do trezoru klíčů, můžete volitelně šifrováním pomocí klíče šifrovací klíč. Použít obtékání [rozhraní API](https://msdn.microsoft.com/library/azure/dn878066.aspx) k šifrování nejprve tajného klíče pomocí klíče šifrovacího klíče. Výstup této operace zalamování je řetězec ve formátu base64 kódovaná adresou URL, které potom můžete nahrát jako tajného klíče pomocí [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) rutiny.

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

Použití `$KeyEncryptionKey` a `$secretUrl` v dalším kroku pro [připojení disku operačního systému pomocí KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Zadejte tajný adresu URL, když připojíte disku operačního systému
#### <a name="without-using-a-kek"></a>Bez použití KEK
Když připojujete disk operačního systému, je třeba předat `$secretUrl`. Adresa URL byla vygenerována v části "šifrování disku tajný klíč není šifrován KEK".

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
Pokud se připojíte disk operačního systému, předat `$KeyEncryptionKey` a `$secretUrl`. Adresa URL byla vygenerována v části "šifrování disku tajný klíč není šifrován KEK".

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
[Prozkoumejte Azure Disk Encryption pomocí Azure PowerShell – část 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Prozkoumejte Azure Disk Encryption pomocí prostředí Azure PowerShell - část 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
