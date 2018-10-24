---
title: Prepeare serveru aplikace DPM k zálohování úloh do Azure
description: Úvod k zálohování dat DPM do trezoru služby Azure Recovery Services.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, aplikace data protection manager, zálohy aplikace dpm
ms.service: backup
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: adigan
ms.openlocfilehash: 3efe2f02666c69ff648eaab39fbc1dfe9dc5e3e7
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945429"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Příprava zálohování úloh do Azure pomocí nástroje System Center DPM

Tento článek vysvětluje postup přípravy pro System Center Data Protection Manager (DPM) zálohování do Azure pomocí služby Azure Backup. 

Tento článek poskytuje:

- Přehled o nasazení aplikace DPM pomocí služby Azure Backup.
- Předpoklady a omezení pomocí služby Azure Backup s DPM.
- Kroky pro přípravu Azure, včetně nastavení trezoru služby Recovery Services Backup a volitelně změnu typu pro trezor služby Azure storage.
- Kroků při přípravě serveru DPM, včetně stažení trezorů přihlašovací údaje, instalace agenta Azure Backup a registrace serveru DPM v trezoru.
- Tipy pro řešení potíží pro běžné chyby. 


## <a name="why-back-up-dpm-to-azure"></a>Proč zálohování virtuálních počítačů do Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) zálohuje data souborů a aplikací. Aplikace DPM komunikuje s Azure Backup následujícím způsobem:

* **Aplikace DPM běžící na fyzický server nebo místní virtuální počítač** – můžete zálohovat data do trezoru služby Backup v Azure, kromě na disk a pásky zálohy.
* **Aplikace DPM běžící na Virtuálním počítači Azure** – ze System Center 2012 R2 s aktualizací Update 3 nebo novější, můžete nasadit aplikace DPM na Virtuálním počítači Azure. Můžete zálohovat data na disky Azure připojené k virtuálnímu počítači nebo pomocí služby Azure Backup k zálohování dat do trezoru služby Backup.

Zálohování serverů aplikace DPM do Azure obchodní výhody patří:

* Pro místní aplikace DPM Azure Backup poskytuje alternativu k nasazení dlouhodobé na pásku.
* Aplikace DPM běžící na Virtuálním počítači Azure Azure Backup vám umožní přenést úložiště z disku Azure. Uložením starších data v trezoru služby Backup vault umožňuje byznysu tak škálování uložením nových dat na disk.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

**Nastavení** | **Požadavek** 
--- | ---
Aplikace DPM na Virtuálním počítači Azure | System Center 2012 R2 s kumulativní aktualizací aplikace DPM 2012 R2 3 nebo novější.
Aplikace DPM na fyzickém serveru | System Center 2012 SP1 nebo novější; System Center 2012 R2. 
Aplikace DPM ve virtuálním počítači Hyper-V | System Center 2012 SP1 nebo novější; System Center 2012 R2. 
Aplikace DPM ve virtuálním počítači VMware | System Center 2012 R2 s kumulativní aktualizací 5 nebo novější.
Komponenty | Server aplikace DPM musí mít Windows PowerShell a rozhraní .net Framework 4.5 nainstalované.
Podporované aplikace | [Přečtěte si](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) co aplikace DPM může zálohovat.
Podporované typy souborů | Tyto typy souborů lze zálohovat pomocí služby Azure Backup: šifrované (pouze úplné zálohy); Komprimované (je podporováno přírůstkové zálohování); Zhuštěné (je podporováno přírůstkové zálohování); Komprimované a zhuštěné (zpracovány jako zhuštěné).
Nepodporované typy souborů | Servery v systémech souborů s rozlišením velkých; pevné odkazy (vynecháno); spojovací body (vynecháno); zašifrované a komprimované (vynecháno); šifrované a zhuštěné (vynecháno); Komprimovaný datový proud; analýzy datového proudu.
Místní úložiště | Každý počítač, který chcete zálohovat, musí mít místní volné úložiště, který je minimálně 5 % velikost dat, která je právě zálohován.  Například zálohování 100 GB dat vyžaduje minimálně 5 GB volného místa v pomocné umístění.
Trezoru úložiště | Neexistuje žádné omezení množství dat, která vám může zálohovat do Azure Backup vault, ale velikost zdroje dat (třeba virtuální počítač nebo databázi) nepřekročí 54400 GB.
Agent Azure Backup | Pokud DPM běží na System Center 2012 SP1, nainstalujte kumulativní 2 nebo novější pro DPM SP1. Toto je požadována pro instalaci agenta.<br/><br/> Tento článek popisuje, jak nasadit nejnovější verzi agenta Azure Backup, také označovaný jako agent Microsoft Azure Recovery Service (MARS). Pokud máte starší verzi nasadit, aktualizujte na nejnovější verzi zajistěte, že zálohování funguje podle očekávání. 


Než začnete, potřebujete účet Azure s povolenou funkcí Azure Backup. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Přečtěte si informace o [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).


[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]


## <a name="modify-storage-settings"></a>Úprava nastavení úložiště

Můžete zvolit mezi geograficky redundantním úložištěm a místně redundantní úložiště.

- Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště.
- Pokud se úložiště vaší primární zálohou, ponechte tato možnost nastavená na geograficky redundantní úložiště. Pokud chcete levnější možnost, která není úplně jako trvalý, použijte následující postup ke konfiguraci místně redundantní úložiště.
- Další informace o [služby Azure storage](../storage/common/storage-redundancy.md)a [geograficky redundantní](../storage/common/storage-redundancy-grs.md) a [místně redundantní](../storage/common/storage-redundancy-lrs.md) možnosti úložiště.
- Úprava nastavení úložiště před prvotní zálohy. Pokud jste již zálohovali položku, zastavte zálohování v trezoru před úpravou nastavení úložiště.

Chcete-li upravit nastavení replikace úložiště:

1. Otevření řídicího panelu trezoru.

2. V **spravovat**, klikněte na tlačítko **infrastruktura zálohování**.

3. V **konfigurace zálohování** nabídku, vyberte možnost úložiště pro trezor.

    ![Seznam trezorů záloh](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)


## <a name="download-vault-credentials"></a>Stažení přihlašovacích údajů trezoru

Přihlašovací údaje trezoru použijte při registraci serveru DPM v trezoru. 

- Soubor s přihlašovacími údaji trezoru je certifikát vytvořený portál pro každý trezor záloh.
- Portál poté odešle veřejný klíč do Access Control Service (ACS).
- Během pracovního postupu registrace počítače je privátní klíč certifikátu k dispozici pro uživatele, který ověřuje počítač.
- Na základě ověřování, služba Azure Backup odesílá data do identifikovaného trezoru.

 ### <a name="best-practices-for-vault-credentials"></a>Osvědčené postupy pro přihlašovací údaje trezoru

K získání přihlašovacích údajů pro stažení soubor s přihlašovacími údaji trezoru prostřednictvím zabezpečeného kanálu z portálu Azure portal:

- Přihlašovací údaje trezoru se používají pouze během pracovního postupu registrace.
- Je vaší povinností ujistit, že soubor s přihlašovacími údaji trezoru je bezpečné a není ohrožené.
    -  Pokud dojde ke ztrátě kontrolu nad přihlašovací údaje, je možné přihlašovací údaje trezoru k registraci dalších počítačů do trezoru.
    - Ale zálohovaná data šifrovaná pomocí hesla, která patří do zákazníků, takže nemůže k ohrožení bezpečnosti stávajících zálohovaných dat.
- Zkontrolujte, jestli že tento soubor je uložen v umístění, které je přístupné ze serveru DPM. Pokud je uložený ve sdílené složce souboru/SMB, zkontrolujte přístupová oprávnění.
- Přihlašovací údaje trezoru vyprší po 48 hodin. Můžete si stáhnout nové přihlašovací údaje trezoru tolikrát, kolikrát podle potřeby. Během pracovního postupu registrace lze však použít pouze nejnovější soubor přihlašovacích údajů trezoru.
- Služba Azure Backup není vědom privátní klíč certifikátu a privátní klíč není k dispozici na portálu nebo službu. 

Stažení souboru s přihlašovacími údaji do místního počítače následujícím způsobem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevřete trezor, ve kterém chcete registraci serveru DPM.
3. V **nastavení**, klikněte na tlačítko **vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. V **vlastnosti** > **zálohování pověření**, klikněte na tlačítko **Stáhnout**. Na portálu se vytvoří soubor přihlašovacích údajů trezoru pomocí kombinace název trezoru a aktuálním datem a je k dispozici ke stažení.

    ![Ke stažení](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Klikněte na tlačítko **Uložit** stažení přihlašovacích údajů trezoru do složky, nebo **uložit jako** a zadejte umístění. To bude trvat až jednu minutu pro soubor, který má být vygenerována.


## <a name="install-the-backup-agent"></a>Instalace agenta zálohování

Všechny počítače, které je zálohovat službou Azure Backup musí být nainstalován na něj agent zálohování (také označovaný jako agent Microsoft Azure Recovery Service (MARS)). Nainstalujte agenta na serveru DPM takto:

1. Otevřete trezor, ke kterému chcete registraci serveru DPM.
2. V **nastavení**, klikněte na tlačítko **vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na **vlastnosti** stránce, stáhněte si agenta Azure Backup.

    ![Ke stažení](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. Po stažení, spusťte MARSAgentInstaller.exe. Instalace agenta na počítači aplikace DPM. 
5. Vyberte instalační složku a složku mezipaměti pro agenta. Volné místo umístění mezipaměti musí být nejméně 5 % dat záloh.
6. Pokud používáte proxy server pro připojení k Internetu, v **konfiguraci proxy serveru** obrazovky, zadejte podrobnosti o proxy serveru. Pokud používáte ověřený proxy server, zadejte uživatelské jméno a heslo podrobnosti na této obrazovce.
7. Agent Azure Backup nainstaluje rozhraní .NET Framework 4.5 a prostředí Windows PowerShell (pokud tyto produkty nejsou nainstalovány) pro dokončení instalace.
8. Po instalaci agenta **Zavřít** okna.

   ![Zavřít](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)


## <a name="register-the-dpm-server-in-the-vault"></a>Registrace serveru DPM v trezoru

1. V konzole správce aplikace DPM > **správu**, klikněte na tlačítko **Online**. Vyberte **Zaregistrovat**. Otevře se Průvodce registrací serveru.
2. V **konfiguraci proxy serveru**, podle potřeby zadejte nastavení proxy serveru.

    ![Konfigurace proxy serveru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. V **trezoru služby Backup**, vyhledejte a vyberte soubor přihlašovacích údajů trezoru, který jste stáhli.

    ![Přihlašovací údaje trezoru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. V **nastavení omezení šířky pásma**, můžete volitelně povolit omezení šířky pásma pro zálohy. Můžete nastavit omezení rychlosti pro zadejte pracovní dobu a dny. 

    ![Nastavení omezení šířky pásma](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. V **nastavení složky pro obnovení**, zadejte umístění, které lze použít při obnovení dat.

    - Azure Backup používá toto umístění jako dočasné retenční oblast pro obnovená data.
    - Po dokončení obnovení dat Azure Backup se vyčistit data v této oblasti.
    - Umístění musí mít dostatek místa pro uložení položky, které paralle obnovování předpokládáte.

    ![Nastavení složky pro obnovení](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. V **nastavení šifrování** Generovat nebo zadat přístupové heslo.

    - Heslo se používá pro šifrování záloh do cloudu.
    - Zadejte alespoň 16 znaků.
    - Uložte soubor na bezpečné místo je potřeba pro obnovení.
    
    ![Šifrování](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Šifrovací heslo, které vlastníte a Microsoft nemá viditelnost do něj.
    > Pokud heslo ztratíte nebo zapomenete; Microsoft vám nemůže pomoci obnovit zálohovaná data. 

13. Klikněte na tlačítko **zaregistrovat** k registraci serveru DPM do trezoru.  


Po server je zaregistrovaný do trezoru a jsou teď jste připravení začít zálohování Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Řešení potíží s přihlašovacími údaji trezoru

### <a name="expiration-error"></a>Chyba vypršení platnosti

Soubor s přihlašovacími údaji trezoru je platná pouze pro 48 hodin (po jeho stažení z portálu). Pokud narazíte na jakékoli chyby v této obrazovce (například "přihlašovací údaje trezoru, které vypršela platnost zadaný soubor"), přihlaste se k webu Azure portal a stáhnout přihlašovací údaje trezoru znovu soubor.

### <a name="access-error"></a>Chyba přístupu

Ujistěte se, že soubor s přihlašovacími údaji trezoru je k dispozici v umístění, který se dá dostat pomocí instalačního programu. Pokud narazíte na přístup k související chyby, zkopírujte souboru s přihlašovacími údaji do dočasné složky na tomto počítači a zkuste operaci zopakovat.

### <a name="invalid-credentials-error"></a>Chyba neplatné přihlašovací údaje

Pokud dojde k chybě přihlašovacích údajů trezoru neplatná (například "Neplatný přihlašovacími údaji k trezoru za předpokladu") soubor je buď poškozený, nebo nemá mít nejnovější přihlašovací údaje nesouvisí s využitím služby recovery.

- Zkuste operaci zopakovat po stažení z portálu nový soubor přihlašovacích údajů trezoru.
- Tato chyba je obvykle vidět, když kliknete na **přihlašovací údaje trezoru Stáhnout** možnost na webu Azure Portal, v rychle po sobě dvakrát. V takovém případě je platný pouze druhý soubor přihlašovacích údajů trezoru.
