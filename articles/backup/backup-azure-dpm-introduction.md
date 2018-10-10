---
title: Použití DPM k zálohování úloh do Azure
description: Úvod k zálohování dat DPM do trezoru služby Azure Recovery Services.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, aplikace data protection manager, zálohy aplikace dpm
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 2da5b04f56a5746fb77de6bc954bb5971eb4664b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885166"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Příprava zálohování úloh do Azure pomocí DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Tento článek vysvětluje, jak zálohovat data System Center Data Protection Manager (DPM) do Azure; včetně:

* Jak zálohovat data na serveru aplikace DPM do Azure
* Požadavky zajistit hladký zálohování
* Typické došlo k chybám a jak zacházet s nimi
* Podporované scénáře

> [!NOTE]
> Azure nabízí dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek obsahuje informace a postupy pro obnovení virtuální počítače nasazené pomocí modelu Resource Manager.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) zálohuje data souborů a aplikací. Další informace o podporovaných úloh najdete [tady](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Data zálohovaná na aplikaci DPM lze uložit na pásky na disk, nebo zálohovaných do Azure pomocí služby Microsoft Azure Backup. Aplikace DPM komunikuje s Azure Backup následujícím způsobem:

* **Aplikace DPM nasazená jako fyzický server nebo místní virtuální počítač** – aplikace DPM nasazená jako fyzický server nebo virtuálního počítače s technologií Hyper-V v místním zálohuje data do trezoru služby Recovery Services, kromě diskové a páskové zálohování.
* **Aplikace DPM nasazená jako virtuální počítač Azure** – ze System Center 2012 R2 s aktualizací Update 3 na DPM můžete nasadit na virtuální počítač Azure. Pokud je aplikace DPM nasazená jako virtuální počítač Azure, můžete data zálohovat na disky Azure připojené k virtuálnímu počítači nebo přesměrovat datové úložiště pomocí zálohování do trezoru služby Recovery Services.

## <a name="why-back-up-dpm-to-azure"></a>Proč zálohování virtuálních počítačů do Azure?
Zálohování serverů aplikace DPM do Azure obchodní výhody patří:

* Azure pro místní nasazení aplikace DPM, můžete použijte jako alternativu k nasazení dlouhodobé na pásku.
* Pro nasazení aplikace DPM ve virtuálním počítači v Azure, přenést úložiště z disku Azure. Uložením starších data v trezoru služby Recovery Services umožňuje byznysu tak škálování uložením nových dat na disk.

## <a name="prerequisites"></a>Požadavky
Příprava Azure Backup k zálohování dat DPM následujícím způsobem:

1. **Vytvořte trezor služby Recovery Services** – vytvoření trezoru na webu Azure portal.
2. **Stažení přihlašovacích údajů trezoru** – stáhnout přihlašovací údaje použijete k registraci serveru DPM v trezoru služby Recovery Services.
3. **Nainstalujte agenta Azure Backup** – nainstalujte agenta na každý server DPM.
4. **Registrace serveru** – registrace serveru DPM v trezoru služby Recovery Services.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Klíčové definice
Tady jsou některé klíčové definice pro zálohování Azure pro aplikaci DPM:

1. **Přihlašovací údaje úložiště** – přihlašovací údaje trezoru jsou potřebné k ověření počítače pro odesílání zálohovaných dat do identifikovaného trezoru ve službě Azure Backup. Je možné stáhnout z trezoru a je platný po dobu 48 hodin.
2. **Heslo** – heslo se používá pro šifrování záloh do cloudu. Uložte soubor na bezpečné místo, jak je vyžadováno během operace obnovení.
3. **Bezpečnostní kód PIN** – Pokud jste povolili [nastavení zabezpečení](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) trezoru, je potřeba bezpečnostní kód PIN pro provádění důležité zálohovací operace. Toto ověřování službou Multi-Factor Authentication přidá další vrstvu zabezpečení. 
4. **Složku pro obnovení** – je slovní spojení, které zálohy z cloudu jsou dočasně stáhnout do během obnovení cloudu. Jeho velikost musí být zhruba odpovídá velikosti zálohované položky, kterou chcete obnovit paralelně.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Úprava replikace úložiště

Replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantní úložiště. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud se úložiště vaší primární zálohou, ponechte tato možnost nastavená na geograficky redundantní úložiště. Pokud chcete levnější možnost, která není úplně jako trvalý, použijte následující postup ke konfiguraci místně redundantní úložiště. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

> [!NOTE] 
> Konfigurace replikace úložiště před aktivací prvotní zálohy. Pokud se rozhodnete změnit konfiguraci replikace úložiště po zálohování chráněné položky, musíte zastavit ochranu pro trezor před přepnutím konfiguraci úložiště.
>  

1. Vyberte svůj trezor a otevřete jeho řídicí panel trezoru.

2. V **spravovat** klikněte na tlačítko **infrastruktura zálohování**.

3. Na **konfigurace zálohování** nabídek, zvolte pro svůj trezor možnost replikace úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Po výběru možnosti úložiště pro svůj trezor jste připraveni k přidružení virtuálního počítače k trezoru. Chcete-li začít přidružení, měli byste vyhledat a zaregistrovat virtuální počítače Azure.

## <a name="download-vault-credentials"></a>Stažení přihlašovacích údajů trezoru
Soubor s přihlašovacími údaji trezoru je certifikát vytvořený portál pro každý trezor záloh. Portál poté odešle veřejný klíč do Access Control Service (ACS). Během pracovního postupu registrace počítače je privátní klíč certifikátu k dispozici pro uživatele, který ověřuje počítač. Na základě ověřování, služba Azure Backup odesílá data do identifikovaného trezoru.

Přihlašovací údajů úložiště se používají pouze během pracovního postupu registrace. Je uživatele povinností ujistit se, že není dojde k ohrožení bezpečnosti souboru s přihlašovacími údaji. Pokud dojde ke ztrátě kontrolu nad přihlašovací údaje, je možné přihlašovací údaje trezoru k registraci dalších počítačů do trezoru. Ale zálohovaná data šifrovaná pomocí hesla, která patří do zákazníků, takže nemůže k ohrožení bezpečnosti stávajících zálohovaných dat. Pro snížení tohoto rizika přihlašovací údaje trezoru vyprší po 48 hodin. Stáhněte si nové přihlašovací údaje trezoru tolikrát, kolikrát podle potřeby. Během pracovního postupu registrace lze však použít pouze nejnovější soubor přihlašovacích údajů trezoru.

Soubor s přihlašovacími údaji trezoru je stáhnout prostřednictvím zabezpečeného kanálu z portálu Azure portal. Služba Azure Backup není vědom privátní klíč certifikátu a privátní klíč není k dispozici na portálu nebo službu. Následujícím postupem stáhnout soubor s přihlašovacími údaji trezoru do místního počítače.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Otevřete trezor služby Recovery Services, kterou chcete zaregistrovat k serveru aplikace DPM.

3. Nabídka nastavení otevře ve výchozím nastavení. Pokud se zavře, klikněte na tlačítko **nastavení** na řídicím panelu trezoru otevřete nabídku. V **nastavení** nabídky, klikněte na tlačítko **vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Na stránce vlastnosti v části **zálohování pověření** klikněte na tlačítko **Stáhnout**. Na portálu vygeneruje soubor přihlašovacích údajů trezoru, který je k dispozici ke stažení.

    ![Ke stažení](./media/backup-azure-dpm-introduction/vault-credentials.png)

Na portálu vygeneruje přihlašovací údaje úložiště pomocí kombinace názvu úložiště a aktuální datum. Klikněte na tlačítko **Uložit** přihlašovací údaje trezoru stáhnout do složky Stažené soubory místní účet nebo uložit jako uložit nabídce a zadejte umístění pro přihlašovací údaje trezoru. To bude trvat až jednu minutu pro soubor, který má být vygenerována.

### <a name="note"></a>Poznámka
* Ujistěte se, že soubor s přihlašovacími údaji trezoru je uložena v umístění, které je přístupné z počítače. Pokud je uložený ve sdílené složce souboru/SMB, zkontrolujte přístupová oprávnění.
* Soubor s přihlašovacími údaji trezoru se používá pouze během pracovního postupu registrace.
* Soubor s přihlašovacími údaji trezoru vyprší po 48hrs a si můžete stáhnout z portálu.

## <a name="install-backup-agent"></a>Instalace agenta zálohování
Po vytvoření trezoru služby Azure Backup, musí být agent nainstalován na jednotlivých počítačů Windows (Windows serveru, klienta Windows, server System Center Data Protection Manager nebo Azure Backup serveru počítače), která umožňuje zálohování dat a aplikací do Azure .

1. Otevřete trezor služby Recovery Services, kterou chcete zaregistrovat počítač aplikace DPM.
2. Nabídka nastavení otevře ve výchozím nastavení. Pokud je zavřená, klikněte na **nastavení** otevřete nabídku nastavení. V nabídce nastavení, klikněte na **vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stránce nastavení, klikněte na tlačítko **Stáhnout** pod **Azure Backup Agent**.

    ![Ke stažení](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Po stažení agenta spusťte MARSAgentInstaller.exe ke spuštění instalace agenta Azure Backup. Zvolte instalační složku a pomocnou složku vyžadované pro agenta. Zadané umístění mezipaměti musí mít volné místo, které je minimálně 5 % zálohovaných dat.

4. Pokud používáte proxy server pro připojení k Internetu, v **konfiguraci proxy serveru** obrazovky, zadejte podrobnosti o proxy serveru. Pokud používáte ověřený proxy server, zadejte uživatelské jméno a heslo podrobnosti na této obrazovce.

5. Agent Azure Backup nainstaluje rozhraní .NET Framework 4.5 a prostředí Windows PowerShell (Pokud není k dispozici již) k dokončení instalace.

6. Po instalaci agenta **Zavřít** okna.

   ![Zavřít](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. K **registraci serveru DPM** do trezoru, v **správu** kartu, klepněte na **Online**. Vyberte **zaregistrovat**. Otevře se Průvodce instalací zaregistrovat.

8. Pokud používáte proxy server pro připojení k Internetu, v **konfiguraci proxy serveru** obrazovky, zadejte podrobnosti o proxy serveru. Pokud používáte ověřený proxy server, zadejte uživatelské jméno a heslo podrobnosti na této obrazovce.

    ![Konfigurace proxy serveru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Na obrazovce přihlašovacích údajů trezoru vyhledejte a vyberte soubor s přihlašovacími údaji trezoru, který byl dříve stáhli.

    ![Přihlašovací údaje trezoru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Soubor s přihlašovacími údaji trezoru je platná pouze pro 48 hodin (po jeho stažení z portálu). Pokud narazíte na jakékoli chyby v této obrazovce (například "přihlašovací údaje trezoru, které vypršela platnost zadaný soubor"), přihlaste se k webu Azure portal a stáhnout přihlašovací údaje trezoru znovu soubor.

    Ujistěte se, že soubor s přihlašovacími údaji trezoru je k dispozici v umístění, který se dá dostat pomocí instalačního programu. Pokud narazíte na přístup k související chyby, zkopírujte souboru s přihlašovacími údaji do dočasné složky na tomto počítači a zkuste operaci zopakovat.

    Pokud dojde k chybě přihlašovacích údajů trezoru neplatná (například "Neplatný přihlašovacími údaji k trezoru za předpokladu") soubor je buď poškozený, nebo nemá mít nejnovější přihlašovací údaje nesouvisí s využitím služby recovery. Zkuste operaci zopakovat po stažení z portálu nový soubor přihlašovacích údajů trezoru. Tato chyba je obvykle vidět, když uživatel klikne na **přihlašovací údaje trezoru Stáhnout** možnost na webu Azure Portal, rychle po sobě. V takovém případě je platný pouze druhý soubor přihlašovacích údajů trezoru.

10. K řízení využití šířky pásma sítě během pracovní a nepracovní dobu v **nastavení omezení šířky pásma** obrazovky, můžete nastavit omezení využití šířky pásma a definovat pracovní a nepracovní hodiny.

    ![Nastavení omezení šířky pásma](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. V **nastavení složky pro obnovení** obrazovky, procházet pro složku, ve kterém soubory stáhnou z Azure budou dočasně připravené.

    ![Nastavení složky pro obnovení](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. V **nastavení šifrování** obrazovky, můžete generovat přístupové heslo, nebo zadejte heslo (minimálně 16 znaků). Nezapomeňte si uložit heslo v zabezpečeném umístění.

    ![Šifrování](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Pokud heslo ztratíte nebo zapomenete; Microsoft vám nemůže pomoci obnovit zálohovaná data. Koncový uživatel vlastní šifrovací heslo a Microsoft nevidí heslo použité koncovým uživatelem. Jak je vyžadováno během operace obnovení, uložte prosím soubor na bezpečné místo.
    >
    >

13. Když kliknete **zaregistrovat** tlačítko, na počítači se úspěšně zaregistrují do trezoru a nyní jste připraveni spustit zálohování Microsoft Azure.

14. Při použití aplikace Data Protection Manager, můžete upravit nastavení určené během pracovního postupu registrace kliknutím **konfigurovat** tak, že vyberete možnost **Online** pod **správy**  Kartu.

## <a name="requirements-and-limitations"></a>Požadavky (a omezení)
* Aplikace DPM může běžet jako fyzický server nebo virtuální počítač Hyper-V v nástroji System Center 2012 SP1 nebo System Center 2012 R2 nainstalována. Může taky běžet jako virtuální počítač Azure s v nástroji System Center 2012 R2 s DPM 2012 R2 Update Rollup 3 nebo virtuální počítač Windows ve VMware běžící na System Center 2012 R2 s kumulativní aktualizací 5.
* Pokud spouštíte aplikaci DPM se System Center 2012 SP1 nainstalujte kumulativní aktualizaci 2 pro System Center Data Protection Manager SP1. To je potřeba, abyste mohli nainstalovat agenta Azure Backup.
* Server aplikace DPM musí mít Windows PowerShell a rozhraní .net Framework 4.5 nainstalované.
* DPM může zálohovat většinu úloh Azure Backup. Úplný seznam najdete v tématu Co je podporováno podporuje služba Azure Backup položky dole.
* Data uložená ve službě Azure Backup není možné obnovit pomocí možnosti "Kopírovat na pásku".
* Budete potřebovat účet Azure s povolenou funkcí zálohování Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Přečtěte si informace o [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Pomocí služby Azure Backup vyžaduje Azure Backup Agent nainstalovaný na serverech, které chcete zálohovat. Každý server musí mít minimálně 5 % množství dat, který se zálohuje, k dispozici jako místní volné úložiště. Například zálohování 100 GB dat vyžaduje minimálně 5 GB volného místa v pomocné umístění.
* Data se uloží v trezoru Azure storage. Neexistuje žádné omezení množství dat, která vám může zálohovat do Azure Backup vault, ale velikost zdroje dat (třeba virtuální počítač nebo databázi) nepřekročí 54400 GB.

Tyto typy souborů jsou podporovány pro zálohování do Azure:

* Šifrované (pouze úplné zálohování)
* Komprimované (je podporováno přírůstkové zálohování)
* Zhuštěné (je podporováno přírůstkové zálohování)
* Komprimované a zhuštěné (zpracovány jako zhuštěné)

A jedná se o nepodporovaný:

* Servery v systémech souborů s malá a velká písmena nejsou podporovány.
* Pevné odkazy (vynecháno)
* Body rozboru (vynecháno)
* Zašifrované a komprimované (vynecháno)
* Šifrované a zhuštěné (vynecháno)
* Komprimovaný datový proud
* Zhuštěný datový proud

> [!NOTE]
> Z nástroje System Center DPM 2012 s aktualizací SP1 a vyšší můžete zálohovat chráněné úlohy do Azure.
>
>
