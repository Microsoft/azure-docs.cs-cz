---
title: Agent Azure Backup – nejčastější dotazy
description: Odpovědi na běžné dotazy týkající se fungování agenta Azure Backup a omezení zálohování a uchovávání.
services: backup
author: trinadhk
manager: shreeshd
keywords: zálohování a zotavení po havárii; služba zálohování
ms.service: backup
ms.topic: conceptual
ms.date: 7/18/2017
ms.author: trinadhk
ms.openlocfilehash: 4234b76c192ba3bbeaca9593250cc855c073e380
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605527"
---
# <a name="questions-about-the-azure-backup-agent"></a>Dotazy týkající se agenta Azure Backup
Tento článek obsahuje odpovědi na běžné dotazy, které vám pomůžou rychle porozumět komponentám agenta Azure Backup. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Otázky týkající se služby Azure Backup můžete také publikovat na [diskusním fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="configure-backup"></a>Konfigurace zálohování
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Kde mohu stáhnout nejnovější verzi agenta Azure Backup? <br/>
Nejnovější verzi agenta pro zálohování Windows Serveru, aplikace System Center DPM nebo klienta Windows si můžete stáhnout [zde](http://aka.ms/azurebackup_agent). Chcete-li zálohovat virtuální počítač, použijte agenta virtuálního počítače (který automaticky nainstaluje správné rozšíření). Agent virtuálního počítače je již nainstalován na virtuálních počítačích vytvořených z galerie Azure.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Při konfiguraci agenta Azure Backup se zobrazí výzva k zadání přihlašovacích údajů trezoru. Mohou přihlašovací údaje trezoru vypršet?
Ano, přihlašovací údaje trezoru vyprší po 48 hodinách. Pokud soubor vyprší, přihlaste se k webu Azure Portal a ze svého trezoru si stáhněte soubory s přihlašovacími údaji trezoru.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>Z jakých typů jednotek můžu zálohovat soubory a složky? <br/>
Nemůžete zálohovat následující jednotky a svazky:

* Vyměnitelné médium: Všechny zdroje položek k zálohování se musí hlásit jako pevné.
* Svazky jen pro čtení: Svazek musí být zapisovatelný, aby fungovala služba Stínová kopie svazku (VSS).
* Offline svazky: Svazek musí být online, aby fungovala služba VSS.
* Sdílené síťové složky: Svazek musí být místní pro server, aby mohl být zálohovaný pomocí online zálohování.
* Svazky chráněné nástrojem Bitlocker: Předtím, než může dojít k zálohování, musí být svazek odemčený.
* Identifikace systému souborů: Jediným podporovaným systémem souborů je NTFS.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>Jaké typy souborů a složek mohu zálohovat ze svého serveru?<br/>
Jsou podporovány následující typy:

* Šifrované
* Komprimované
* Řídké
* Komprimované a řídké
* Pevné odkazy: Není podporováno, vynecháno
* Bod rozboru: Není podporováno, vynecháno
* Šifrované a řídké: Není podporováno, vynecháno
* Komprimovaný datový proud: Není podporováno, vynecháno
* Řídký datový proud: Není podporováno, vynecháno

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Mohu nainstalovat agenta Azure Backup na virtuální počítač Azure, který už je zálohovaný službou Azure Backup pomocí rozšíření virtuálního počítače? <br/>
Jistě. Azure Backup poskytuje zálohování na úrovni virtuálních počítačů pro virtuální počítače Azure, které používají rozšíření virtuálního počítače. Pokud chcete chránit soubory a složky na hostovaném operačním systému Windows, nainstalujte na něj agenta Azure Backup.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Mohu nainstalovat agenta Azure Backup na virtuální počítač Azure a použít ho k zálohování souborů a složek umístěných na dočasném úložišti poskytnutém virtuálním počítačem Azure? <br/>
Ano. Nainstalujte agenta Azure Backup na hostovaný operační systém Windows a zálohujte soubory a složky do dočasného úložiště. Jakmile dojde k vymazání dat na dočasném úložišti, úlohy zálohování selžou. Navíc pokud dojde k vymazání dat na dočasném úložišti, budete moci provést obnovení pouze na stálé úložiště.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Jaký je požadavek na minimální velikost složky mezipaměti? <br/>
Velikost složky mezipaměti určuje množství dat, která zálohujete. Složka mezipaměti by měla zabírat 5 % požadovaného místa pro úložiště dat.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Jak mohu zaregistrovat svůj server k jinému datovému centru?<br/>
Zálohovaná data se odesílají do datového centra trezoru, ke kterému je agent registrován. Nejjednodušší způsob, jak změnit datové centrum, je odinstalování agenta a jeho přeinstalování a zaregistrování k novému trezoru, který patří k požadovanému datovému centru.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Funguje agent Azure Backup na serveru, který používá odstranění duplicit Windows Serveru 2012? <br/>
Ano. Služba agenta během přípravy operace zálohování převádí odstraněná duplicitní data na normální data. Poté optimalizuje data pro zálohování, zašifruje je, a zašifrovaná data odešle do online služby zálohování.

## <a name="backup"></a>Backup
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Jak změním umístění mezipaměti, zadané pro agenta Azure Backup?<br/>
Umístění mezipaměti můžete změnit pomocí následujícího seznamu.

1. Zastavte modul zálohování spuštěním následujícího příkazu v příkazovém řádku se zvýšenými oprávněními:

    ```PS C:\> Net stop obengine``` 
  
2. Soubory nepřesouvejte. Místo toho zkopírujte složku s místem v mezipaměti na jinou jednotku s dostatkem volného místa. Po potvrzení, že zálohování s novým místem v mezipaměti funguje správně, můžete původní místo v mezipaměti odebrat.
3. U následujících položek registru aktualizujte cestu k nové složce s místem v mezipaměti.<br/>

    | Cesta k registru | Klíč registru | Hodnota |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

4. Restartujte modul Backup spuštěním následujícího příkazu v příkazovém řádku se zvýšenými oprávněními:

    ```PS C:\> Net start obengine```

Po úspěšném dokončení vytvoření zálohy v novém umístění mezipaměti můžete původní složku mezipaměti odebrat.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Kam můžu dát složku mezipaměti, aby agent Azure Backup fungoval podle očekávání?<br/>
Pro složku mezipaměti nedoporučujeme používat následující umístění:

* Sdílenou síťovou složku nebo vyměnitelné médium: Složka mezipaměti musí být místní pro server, který potřebuje zálohování pomocí online zálohování. Síťová umístění a vyměnitelná média jako jednotky USB nejsou podporovaná.
* Offline svazky: Složka mezipaměti musí být online pro očekávané zálohování pomocí agenta Azure Backup.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Jsou nějaké atributy složky mezipaměti, které nejsou podporované?<br/>
Složka mezipaměti nepodporuje následující atributy nebo jejich kombinace:

* Šifrované
* S odstraněním duplicit
* Komprimované
* Řídké
* Bod rozboru

Složka mezipaměti ani virtuální pevný disk s metadaty nemají atributy vyžadované pro agenta Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Existuje způsob, jak nastavit šířku pásma používaného službou Backup?<br/>
  Ano, k úpravě šířky pásma použijte možnost **Změnit vlastnosti** v agentu Backup. Můžete upravit šířku pásma a dobu, kdy tuto šířku pásma používáte. Podrobné pokyny najdete v tématu **[Povolení omezení využití sítě](backup-configure-vault.md#enable-network-throttling)**.

## <a name="manage-backups"></a>Správa záloh
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Co se stane, když přejmenuji server Windows, který zálohuje data do Azure?<br/>
Když server přejmenujete, všechna stávající nastavená zálohování se zastaví.
Zaregistrujte nový název serveru k trezoru služby Backup. Když zaregistrujete nový název trezoru, první zálohování bude provedeno jako *úplné*. Pokud potřebujete obnovit data zálohovaná do trezoru se starým názvem serveru, použijte možnost [**Jiný server**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) v průvodci **Obnova dat**.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Jaká je maximální délka cesty k souboru, kterou lze zadat v zásadě služby Backup pomocí agenta Azure Backup? <br/>
Agent Azure Backup se spoléhá na systém souborů NTFS. [Specifikace délky cesty k souboru je omezená rozhraním API systému Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Pokud mají soubory, které chcete chránit, délku cesty k souboru větší než povoluje rozhraní API systému Windows, zálohujte nadřazenou složku nebo diskovou jednotku.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Jaké znaky jsou povolené v cestě k souboru zásady Azure Backup pomocí agenta Azure Backup? <br>
 Agent Azure Backup se spoléhá na systém souborů NTFS. Pro specifikaci souboru povoluje [znaky podporované systémem souborů NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions). 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Zobrazuje se upozornění „Služba Azure Backup nebyla pro tento server nakonfigurovaná“ i přesto, že jsem nakonfiguroval zásadu zálohování. <br/>
Toto upozornění se zobrazí v případě, že se nastavení plánu zálohování uložené na místním serveru neshodují s nastaveními uloženými v trezoru záloh. Pokud došlo k obnovení serveru nebo nastavení do známého stavu, může dojít ke ztrátě synchronizace plánů zálohování. Pokud se zobrazí toto upozornění, [překonfigurujte zásadu zálohování](backup-azure-manage-windows-server.md) a poté **spusťte Zálohovat nyní**, aby došlo k opětovné synchronizaci místního serveru s Azure. 
