---
title: Příprava serveru DPM na zálohování úloh
description: V tomto článku se dozvíte, jak připravit na zálohování Správce dat system center (DPM) do Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 2119d46ca6102286ca879777058a49938b501ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273459"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Příprava na zálohování úloh do Azure pomocí systémového centra DPM

Tento článek vysvětluje, jak připravit na zálohování System Center Data Protection Manager (DPM) do Azure pomocí služby Azure Backup.

Článek obsahuje:

- Přehled nasazení dpm s Azure Backup.
- Požadavky a omezení pro používání služby Azure Backup s dpm.
- Kroky pro přípravu Azure, včetně nastavení trezoru zálohování služby Recovery Services a volitelně úpravy typu úložiště Azure pro trezor.
- Kroky pro přípravu serveru DPM, včetně stažení přihlašovacích údajů trezoru, instalace agenta Azure Backup a registrace serveru DPM v trezoru.
- Tipy pro řešení běžných chyb.

## <a name="why-back-up-dpm-to-azure"></a>Proč zálohovat dpm do Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) zálohuje data souborů a aplikací. Aplikace DPM spolupracuje se službou Azure Backup následujícím způsobem:

- **DPM spuštěné na fyzickém serveru nebo místním virtuálním počítači** – kromě zálohování disku a pásky můžete zálohovat data do trezoru zálohování v Azure.
- **DPM spuštěné na virtuálním počítači Azure** – Z System Center 2012 R2 s aktualizací 3 nebo novější, můžete nasadit DPM na virtuální ms Azure. Data můžete zálohovat na disky Azure připojené k virtuálnímu počítači nebo použít Azure Backup k zálohování dat do trezoru zálohování.

Mezi obchodní výhody zálohování serverů DPM do Azure patří:

- Pro místní dpm Azure Backup poskytuje alternativu k dlouhodobé nasazení na pásku.
- Pro DPM spuštěné na virtuálním počítači Azure, Azure Backup umožňuje offload úložiště z disku Azure. Ukládání starších dat do úložiště zálohování umožňuje vertikálně navýšit kapacitu vaší firmy ukládáním nových dat na disk.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

**Nastavení** | **Požadavek**
--- | ---
DPM na virtuálním počítači Azure | System Center 2012 R2 s dpm 2012 R2 Kumulativní aktualizace 3 nebo novější.
DPM na fyzickém serveru | System Center 2012 SP1 nebo novější; Systémové centrum 2012 R2.
DPM na virtuálním počítači Hyper-V | System Center 2012 SP1 nebo novější; Systémové centrum 2012 R2.
DPM na virtuálním počítači VMware | System Center 2012 R2 s kumulativní aktualizací 5 nebo novější.
Komponenty | Na serveru DPM by měl y mít nainstalovány prostředí Windows PowerShell a rozhraní .NET Framework 4.5.
Podporované aplikace | [Přečtěte si,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) co může aplikace DPM zálohovat.
Podporované typy souborů | Tyto typy souborů lze zálohovat pomocí služby Azure Backup: Encrypted (jenom úplné zálohy); Komprimované (podporovány přírůstkové zálohy); Řídké (podporovány přírůstkové zálohy); Stlačený a řídké (zachází jako řídké).
Nepodporované typy souborů | Servery v systémech souborů s malá a velká písmena; pevné odkazy (přeskočeno); zkrášlovací body (přeskočeno); šifrované a komprimované (přeskočeno); šifrované a řídké (přeskočeno); Stlačený datový proud; datový proud analýzy.
Místní úložiště | Každý počítač, který chcete zálohovat, musí mít místní bezplatné úložiště, které je alespoň 5 % velikosti zálohovaných dat. Zálohování 100 GB dat například vyžaduje minimálně 5 GB volného místa v odkládací ploše.
Úložiště trezoru | Neexistuje žádné omezení na množství dat, které můžete zálohovat do trezoru Zálohování Azure, ale velikost zdroje dat (například virtuální počítač nebo databáze) by neměla překročit 54 400 GB.
Azure ExpressRoute | Pokud azure expressroute je nakonfigurovaný s privátní nebo partnerský vztah Microsoftu, nelze použít k zálohování dat do Azure.<br/><br/> Pokud azure expressroute je nakonfigurovaný s veřejný partnerský vztah, lze použít k zálohování dat do Azure.<br/><br/> **Poznámka:** Veřejný partnerský vztah je zastaralé pro nové okruhy.
Agent Azure Backup | Pokud je aplikace DPM spuštěna v aktualizaci System Center 2012 SP1, nainstalujte kumulativní verzi 2 nebo novější pro aplikaci DPM SP1. To je nutné pro instalaci agenta.<br/><br/> Tento článek popisuje, jak nasadit nejnovější verzi agenta Azure Backup, označovaného také jako agent mars (Microsoft Azure Recovery Service). Pokud máte nasazenou starší verzi, aktualizujte na nejnovější verzi, abyste zajistili, že zálohování funguje podle očekávání.

Než začnete, potřebujete účet Azure s povolenou funkcí Azure Backup. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Přečtěte si o [cenách azure backup .](https://azure.microsoft.com/pricing/details/backup/)

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Změna nastavení úložiště

Můžete si vybrat mezi geograficky redundantním úložištěm a místně redundantním úložištěm.

- Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště.
- Pokud je trezor primární zálohou, ponechejte možnost nastavenou na geograficky redundantní úložiště. Pokud chcete levnější možnost, která není tak trvanlivá, použijte následující postup ke konfiguraci místně redundantního úložiště.
- Přečtěte si o [úložišti Azure](../storage/common/storage-redundancy.md)a [o geograficky redundantních](../storage/common/storage-redundancy-grs.md) a [místně redundantních](../storage/common/storage-redundancy-lrs.md) možnostech úložiště.
- Před počáteční zálohou upravte nastavení úložiště. Pokud jste již položku zálohovali, přestaňte ji zálohovat v trezoru před úpravou nastavení úložiště.

Chcete-li upravit nastavení replikace úložiště:

1. Otevřete řídicí panel trezoru.

2. V **seznamu Správa**klepněte na položku **Zálohovat infrastrukturu**.

3. V nabídce **Konfigurace zálohování** vyberte možnost úložiště pro trezor.

    ![Seznam trezorů záloh](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Stažení přihlašovacích údajů trezoru

Pověření trezoru se používají při registraci serveru DPM v úschovně.

- Soubor s přihlašovacími údaji trezoru je certifikát vytvořený portál pro každý trezor záloh.
- Portál poté odešle veřejný klíč do Access Control Service (ACS).
- Během pracovního postupu registrace počítače je uživateli zpřístupněn soukromý klíč certifikátu, který ověřuje počítač.
- Na základě ověřování služba Azure Backup odesílá data do identifikovaného trezoru.

### <a name="best-practices-for-vault-credentials"></a>Doporučené postupy pro pověření trezoru

Chcete-li získat pověření, stáhněte soubor přihlašovacích údajů trezoru prostřednictvím zabezpečeného kanálu z webu Azure:

- Pověření trezoru se používají pouze během pracovního postupu registrace.
- Je vaší odpovědností zajistit, aby byl soubor přihlašovacích údajů trezoru bezpečný a nebyl ohrožen.
  - Pokud dojde ke ztrátě řízení přihlašovacích údajů, pověření trezoru lze zaregistrovat další počítače do trezoru.
  - Záložní data jsou však šifrována pomocí přístupové fráze, která patří zákazníkovi, takže existující data zálohování nemohou být ohrožena.
- Ujistěte se, že soubor je uložen v umístění, které je přístupné ze serveru DPM. Pokud je uložen ve sdílené složce nebo SMB, zkontrolujte přístupová oprávnění.
- Platnost přihlašovacích údajů trezoru vyprší po 48 hodinách. Nová přihlašovací údaje trezoru si můžete stáhnout tolikrát, kolikrát potřebujete. Během pracovního postupu registrace však lze použít pouze nejnovější soubor pověření úschovny.
- Služba Azure Backup neví o soukromém klíči certifikátu a soukromý klíč není k dispozici na portálu ani ve službě.

Stáhněte soubor přihlašovacích údajů trezoru do místního počítače takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Otevřete úschovnu, ve které chcete zaregistrovat server DPM.
3. V **nastavení**klepněte na **položku Vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. V seznamu Pověření pro zálohování **vlastností** > **Backup Credentials**klepněte na tlačítko **Stáhnout**. Portál vygeneruje soubor pověření úschovny pomocí kombinace názvu úložiště a aktuálního data a zpřístupní jej ke stažení.

    ![Stáhnout](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kliknutím na **Uložit** stáhněte přihlašovací údaje trezoru do složky nebo **Uložit jako** a určete umístění. Bude trvat až minutu, než bude soubor vygenerován.

## <a name="install-the-backup-agent"></a>Instalace agenta zálohování

Každý počítač, který je zálohován službou Azure Backup, musí mít nainstalovaného agenta Backup (označovaného také jako Microsoft Azure Recovery Service) (MARS). Nainstalujte agenta na server DPM následujícím způsobem:

1. Otevřete úschovnu, do které chcete zaregistrovat server DPM.
2. V **nastavení**klepněte na **položku Vlastnosti**.

    ![Otevření nabídky trezoru](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stránce **Vlastnosti** stáhněte agenta zálohování Azure.

    ![Stáhnout](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Po stažení spusťte program MARSAgentInstaller.exe. nainstalujte agenta do počítače DPM.
5. Vyberte instalační složku a složku mezipaměti pro agenta. Volné místo umístění mezipaměti musí být alespoň 5 % záložních dat.
6. Pokud se k internetu připojujete pomocí proxy serveru, zadejte na **obrazovce konfigurace proxy** server podrobnosti o serveru proxy. Pokud používáte ověřený proxy server, zadejte na tuto obrazovku uživatelské jméno a heslo.
7. Agent Azure Backup nainstaluje rozhraní .NET Framework 4.5 a Windows PowerShell (pokud nejsou nainstalované) k dokončení instalace.
8. Po instalaci agenta **zavřete** okno.

    ![Zavřít](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrace serveru DPM v úschovně

1. V konzole správce aplikace DPM > **Správa**klepněte na tlačítko **Online**. Vyberte **Zaregistrovat**. Otevře se Průvodce rejstříkovým serverem.
2. V **konfiguraci proxy**zadejte nastavení serveru proxy podle potřeby.

    ![Konfigurace proxy serveru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. V **úložišti zálohování**vyhledejte a vyberte soubor přihlašovacích údajů úložiště, který jste stáhli.

    ![Pověření trezoru](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. V **nastavení omezení**můžete volitelně povolit omezení šířky pásma pro zálohování. Můžete nastavit rychlostní limity pro určení pracovní doby a dnů.

    ![Nastavení omezení](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. V **nastavení složky pro obnovení**zadejte umístění, které lze použít během obnovy dat.

    - Azure Backup používá toto umístění jako dočasné úložiště oblasti pro obnovená data.
    - Po dokončení obnovení dat Azure Backup vyčistí data v této oblasti.
    - Umístění musí mít dostatek místa pro uložení položek, které předpokládáte obnovení paralelně.

    ![Nastavení složky pro obnovení](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. V **nastavení Šifrování**vygenerujte nebo zadejte přístupové heslo.

    - Přístupové heslo se používá k šifrování záloh do cloudu.
    - Zadejte minimálně 16 znaků.
    - Uložte soubor na bezpečném místě, je potřeba pro obnovení.

    ![Šifrování](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Vlastníte šifrovací přístupové heslo a společnost Microsoft nemá přehled o něm.
    > Pokud dojde ke ztrátě nebo zapomenutí přístupového hesla; Společnost Microsoft nemůže pomoci při obnovení záložních dat.

7. Chcete-li zaregistrovat server DPM do úložiště, klepněte na **tlačítko Registrovat.**

Po úspěšné registraci serveru do trezoru a nyní jste připraveni začít zálohovat microsoft azure. Skupinu ochrany v konzole DPM budete muset nakonfigurovat tak, aby zálohovala úlohy do Azure. [Přečtěte si, jak](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) nasadit skupiny ochrany.

## <a name="troubleshoot-vault-credentials"></a>Poradce při potížích s pověřeními trezoru

### <a name="expiration-error"></a>Chyba vypršení platnosti

Soubor přihlašovacích údajů trezoru je platný pouze po dobu 48 hodin (po stažení z portálu). Pokud na této obrazovce narazíte na nějakou chybu (například vypršela platnost zadaný soubor přihlašovacích údajů v úložišti"), přihlaste se na portál Azure a znovu stáhněte soubor přihlašovacích údajů trezoru.

### <a name="access-error"></a>Chyba přístupu

Ujistěte se, že soubor pověření úschovny je k dispozici v umístění, které je přístupné instalační aplikace. Pokud narazíte na chyby související s přístupem, zkopírujte soubor pověření úložiště do dočasného umístění v tomto počítači a opakujte operaci.

### <a name="invalid-credentials-error"></a>Neplatná chyba pověření

Pokud narazíte na neplatnou chybu pověření trezoru (například "Neplatná pověření trezoru") je soubor poškozen nebo nemá nejnovější pověření přidružená ke službě pro obnovení.

- Opakujte operaci po stažení nového souboru přihlašovacích údajů trezoru z portálu.
- Tato chyba se obvykle zobrazí, když kliknete na možnost **Stáhnout trezor přihlašovací údaje** na webu Azure Portal, dvakrát za sebou. V tomto případě je platný pouze druhý soubor pověření úschovny.
