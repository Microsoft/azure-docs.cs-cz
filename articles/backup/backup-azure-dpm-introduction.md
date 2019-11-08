---
title: Příprava serveru DPM pro zálohování úloh do Azure
description: V tomto článku se dozvíte, jak připravit zálohy aplikace System Center Data Protection Manager (DPM) do Azure pomocí služby Azure Backup.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 5c89dc8b5c8ee420c94d61763770cd37e763f2df
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747514"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Příprava zálohování úloh do Azure pomocí System Center DPM

Tento článek vysvětluje, jak připravit zálohy aplikace System Center Data Protection Manager (DPM) do Azure pomocí služby Azure Backup.

Článek poskytuje:

- Přehled nasazení aplikace DPM pomocí Azure Backup.
- Předpoklady a omezení pro použití Azure Backup s DPM.
- Kroky pro přípravu Azure, včetně nastavení trezoru služby Backup Recovery Services a volitelně úprava typu úložiště Azure pro trezor.
- Postup přípravy serveru DPM, včetně stažení přihlašovacích údajů trezoru, instalace agenta Azure Backup a registrace serveru DPM v trezoru.
- Tipy pro řešení běžných chyb.

## <a name="why-back-up-dpm-to-azure"></a>Proč zálohovat DPM do Azure?

[Aplikace System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) zálohuje data souborů a aplikací. DPM komunikuje s Azure Backup následujícím způsobem:

- **DPM běžící na fyzickém serveru nebo místním virtuálním počítači** – data můžete zálohovat do trezoru záloh v Azure, kromě zálohování na disk a na pásku.
- **DPM běžící na virtuálním počítači Azure** – od verze System Center 2012 R2 s aktualizací Update 3 nebo novější můžete nasadit DPM na virtuálním počítači Azure. Data můžete zálohovat na disky Azure připojené k virtuálnímu počítači nebo použít Azure Backup k zálohování dat do trezoru služby Backup.

Mezi obchodní výhody zálohování serverů DPM do Azure patří:

- Pro místní DPM Azure Backup poskytuje alternativu k dlouhodobému nasazení na pásku.
- Pro DPM běžící na virtuálním počítači Azure Azure Backup umožňuje přesměrování úložiště z disku Azure. Ukládání starších dat do trezoru záloh vám umožní škálovat vaše podnikání tím, že se na disk uloží nová data.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

**Nastavení** | **Požadavek**
--- | ---
DPM na virtuálním počítači Azure | System Center 2012 R2 s DPM 2012 R2 Update Rollup 3 nebo novější.
DPM na fyzickém serveru | System Center 2012 SP1 nebo novější; System Center 2012 R2.
DPM na virtuálním počítači s technologií Hyper-V | System Center 2012 SP1 nebo novější; System Center 2012 R2.
DPM na virtuálním počítači VMware | System Center 2012 R2 s kumulativní aktualizací 5 nebo novější.
Komponenty | Na serveru DPM musí být nainstalováno prostředí Windows PowerShell a .NET Framework 4,5.
Podporované aplikace | [Přečtěte si](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) , co může aplikace DPM zálohovat.
Podporované typy souborů | Tyto typy souborů je možné zálohovat pomocí Azure Backup: šifrované (pouze úplné zálohy); Komprimovaná (podpora přírůstkových záloh); Zhuštěné (podporované přírůstkové zálohy); Komprimovaný a zhuštěný (je považován za zhuštěný).
Nepodporované typy souborů | Servery se systémy souborů s rozlišováním velkých a malých písmen; pevné odkazy (vynecháno); body rozboru (přeskočeny); šifrované a komprimované (vynechané); šifrované a zhuštěné (přeskočené); Komprimovaný datový proud; analyzovat datový proud.
Místní úložiště | Každý počítač, který chcete zálohovat, musí mít místní volné úložiště, které má nejméně 5% velikosti zálohovaných dat. Například zálohování 100 GB dat vyžaduje minimálně 5 GB volného místa v umístění od začátku.
Úložiště trezoru | Neexistuje žádné omezení na množství dat, která můžete zálohovat do trezoru Azure Backup, ale velikost zdroje dat (například virtuální počítač nebo databáze) by neměla překročit 54 400 GB.
Azure ExpressRoute | Pokud je Azure ExpressRoute nakonfigurovaný s privátním nebo veřejným partnerským vztahem Microsoftu, nejde ho použít k zálohování dat do Azure.<br/><br/> Pokud je Azure ExpressRoute nakonfigurovaný s veřejným partnerským vztahem, dá se použít k zálohování dat do Azure.<br/><br/> **Poznámka:** Veřejné partnerské vztahy se pro nové okruhy zastaraly.
Agent Azure Backup | Je-li aplikace DPM spuštěna v produktu System Center 2012 SP1, nainstalujte kumulativní aktualizaci 2 nebo novější pro aplikaci DPM SP1. To je vyžadováno pro instalaci agenta.<br/><br/> Tento článek popisuje, jak nasadit nejnovější verzi agenta Azure Backup, označovaného také jako agent služby Microsoft Azure Recovery Services (MARS). Pokud máte nasazenou starší verzi, aktualizujte na nejnovější verzi, abyste zajistili, že zálohování funguje podle očekávání.

Než začnete, potřebujete účet Azure s povolenou funkcí Azure Backup. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Přečtěte si o [cenách Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Úprava nastavení úložiště

Můžete si vybrat mezi geograficky redundantním úložištěm a místně redundantním úložištěm.

- Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště.
- Pokud je trezor vaší primární zálohou, ponechte možnost nastavenou na geograficky redundantní úložiště. Pokud chcete levnější možnost, která není úplně stejná jako trvalá, nakonfigurujte místně redundantní úložiště pomocí následujícího postupu.
- Přečtěte si o [Azure Storage](../storage/common/storage-redundancy.md)a možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště.
- Úprava nastavení úložiště před prvotním zálohováním. Pokud jste už zálohované položky zálohovali, před úpravou nastavení úložiště zastavte zálohování v trezoru.

Chcete-li upravit nastavení replikace úložiště:

1. Otevřete řídicí panel trezoru.

2. V nabídce **Spravovat**klikněte na **infrastruktura zálohování**.

3. V nabídce **Konfigurace zálohování** vyberte možnost úložiště pro trezor.

    ![Seznam trezorů záloh](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Stažení přihlašovacích údajů trezoru

Přihlašovací údaje trezoru použijete při registraci serveru DPM v trezoru.

- Soubor s přihlašovacími údaji trezoru je certifikát vytvořený portál pro každý trezor záloh.
- Portál poté odešle veřejný klíč do Access Control Service (ACS).
- Během pracovního postupu registrace počítače je uživateli k dispozici privátní klíč certifikátu, který ověřuje počítač.
- Na základě ověření služba Azure Backup odesílá data do identifikovaného trezoru.

### <a name="best-practices-for-vault-credentials"></a>Osvědčené postupy pro přihlašovací údaje trezoru

Pokud chcete získat přihlašovací údaje, Stáhněte si soubor s přihlašovacími údaji trezoru přes zabezpečený kanál z Azure Portal:

- Přihlašovací údaje trezoru se používají jenom během pracovního postupu registrace.
- Je vaše zodpovědnost za to, že soubor s přihlašovacími údaji trezoru je bezpečný a neohrožuje.
  - Pokud se kontrola přihlašovacích údajů ztratí, můžou se přihlašovací údaje k trezoru použít k registraci dalších počítačů do trezoru.
  - Záložní data se ale šifrují pomocí přístupového hesla, který patří zákazníkovi, takže stávající zálohovaná data nejdou ohrozit.
- Zajistěte, aby byl soubor uložen v umístění, ke kterému lze přistupovat ze serveru aplikace DPM. Pokud je soubor uložený ve sdílené složce nebo v protokolu SMB, vyhledejte přístupová oprávnění.
- Platnost přihlašovacích údajů trezoru vyprší po 48 hodinách. Nové přihlašovací údaje trezoru můžete stáhnout tolikrát, kolikrát potřebujete. Během pracovního postupu registrace se ale dá použít jenom nejnovější soubor s přihlašovacími údaji k trezoru.
- Služba Azure Backup neznáte s privátním klíčem certifikátu a privátní klíč není k dispozici na portálu ani v této službě.

Stáhněte si soubor s přihlašovacími údaji trezoru do místního počítače následujícím způsobem:

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).
2. Otevřete trezor, ve kterém chcete zaregistrovat server DPM.
3. V **Nastavení**klikněte na **vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. V **vlastnosti** > **přihlašovací údaje zálohování**klikněte na **Stáhnout**. Portál vygeneruje soubor s přihlašovacími údaji trezoru pomocí kombinace názvu trezoru a aktuálního data a zpřístupní ho ke stažení.

    ![Stáhnout](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kliknutím na **Uložit** Stáhněte přihlašovací údaje trezoru do složky, nebo je **uložte jako** a zadejte umístění. Vygenerování souboru bude trvat až minutu.

## <a name="install-the-backup-agent"></a>Instalace agenta Zálohování

Každý počítač, který je zálohovaný pomocí Azure Backup musí mít nainstalovaného agenta Zálohování (také označovaný jako agent služby obnovení Microsoft Azure (MARS). Nainstalujte agenta na server DPM následujícím způsobem:

1. Otevřete trezor, do kterého chcete zaregistrovat server aplikace DPM.
2. V **Nastavení**klikněte na **vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stránce **vlastnosti** stáhněte agenta Azure Backup.

    ![Stáhnout](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Po stažení spusťte soubor marsagentinstaller. exe. Instalace agenta do počítače aplikace DPM.
5. Vyberte instalační složku a složku mezipaměti pro agenta. Volné místo na umístění mezipaměti musí být alespoň 5% dat zálohy.
6. Pokud se k Internetu připojujete pomocí proxy server, zadejte na obrazovce **konfigurace proxy serveru** proxy server podrobnosti. Pokud používáte ověřený proxy server, zadejte na této obrazovce Podrobnosti o uživatelském jménu a hesle.
7. Agent Azure Backup nainstaluje .NET Framework 4,5 a prostředí Windows PowerShell (pokud nejsou nainstalované), aby se instalace dokončila.
8. Po instalaci agenta **zavřete** okno.

    ![Zavřít](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrace serveru DPM v trezoru

1. V konzole správce aplikace DPM > **Správa**klikněte na možnost **online**. Vyberte **Zaregistrovat**. Otevře se Průvodce registrací serveru.
2. V části **konfigurace proxy**zadejte požadovaná nastavení proxy serveru.

    ![Konfigurace proxy serveru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. V **trezoru služby Backup**vyhledejte a vyberte soubor s přihlašovacími údaji trezoru, který jste stáhli.

    ![Přihlašovací údaje trezoru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. V **nastavení omezování**můžete volitelně povolit omezení šířky pásma pro zálohy. Můžete nastavit omezení rychlosti pro možnost zadat pracovní dobu a dny.

    ![Nastavení omezování](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. V **Nastavení složka pro obnovení**zadejte umístění, které se dá použít při obnovování dat.

    - Azure Backup používá toto umístění jako dočasnou oblast pro obnovená data.
    - Po dokončení obnovení dat Azure Backup vyčistí data v této oblasti.
    - Umístění musí mít dostatek místa pro položky, které předpokládáte paralelní obnovování.

    ![Nastavení složky pro obnovení](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. V **nastavení šifrování**vygenerujte nebo poskytněte přístupové heslo.

    - Přístupové heslo slouží k zašifrování záloh do cloudu.
    - Zadejte minimálně 16 znaků.
    - Uložte soubor na bezpečném místě, je potřeba pro obnovení.

    ![Šifrování](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Vlastníte šifrovací heslo a společnost Microsoft k němu nemá přehled.
    > Pokud je přístupové heslo ztraceno nebo zapomenuto; Microsoft nemůže při obnovování zálohovaných dat pomáhat.

7. Kliknutím na **Registrovat** zaregistrujete server DPM do trezoru.

Po úspěšném zaregistrování serveru do trezoru jste nyní připraveni začít s zálohováním Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Řešení potíží s přihlašovacími údaji trezoru

### <a name="expiration-error"></a>Chyba vypršení platnosti

Soubor s přihlašovacími údaji trezoru je platný jenom pro 48 hodiny (po stažení z portálu). Pokud dojde k nějaké chybě na této obrazovce (například vypršela platnost poskytnutého souboru s přihlašovacími údaji k trezoru), přihlaste se k Azure Portal a znovu stáhněte soubor s přihlašovacími údaji trezoru.

### <a name="access-error"></a>Chyba přístupu

Zajistěte, aby byl soubor s přihlašovacími údaji úložiště k dispozici v umístění, ke kterému může instalační aplikace získat přístup. Pokud narazíte na chyby související s přístupem, zkopírujte soubor s přihlašovacími údaji trezoru do dočasného umístění v tomto počítači a zkuste operaci zopakovat.

### <a name="invalid-credentials-error"></a>Chyba neplatných přihlašovacích údajů

Pokud narazíte na neplatnou chybu přihlašovacích údajů trezoru (například "neplatné přihlašovací údaje trezoru"), soubor je buď poškozený, nebo nemá k dispozici nejnovější přihlašovací údaje přidružené ke službě obnovení.

- Po stažení nového souboru přihlašovacích údajů k úložišti z portálu zkuste operaci zopakovat.
- K této chybě obvykle dochází v případě, že kliknete na možnost **Stáhnout přihlašovací údaje trezoru** v Azure Portal dvakrát v rychlém úspěchu. V tomto případě je platný jenom druhý soubor přihlašovacích údajů k trezoru.
