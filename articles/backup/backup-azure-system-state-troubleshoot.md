---
title: Řešení potíží se zálohováním stavu systému
description: V tomto článku se dozvíte, jak řešit problémy v zálohování stavu systému pro místní servery Windows.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7c8e68da1c5da7b25d1385a82bf7dcc2f876306d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376277"
---
# <a name="troubleshoot-system-state-backup"></a>Řešení potíží se zálohováním stavu systému

Tento článek popisuje řešení problémů, ke kterým může docházet při používání zálohy stavu systému.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Než začnete s odstraňováním potíží se zálohováním stavu systému, doporučujeme provést následující kroky ověření:

- [Zajistěte, aby byl agent Microsoft Azure Recovery Services (MARS) aktuální.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zajistěte, aby mezi agentem MARS a Azure bylo síťové připojení.](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby restartujte operaci a operaci opakujte.
- [Zajistěte, aby v umístění pomocné složky bylo k dispozici 5-10% volného místa na disku](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Zkontrolujte, jestli službě Azure Backup nepřekáží jiný proces nebo antivirový software.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Plánované zálohování se nedaří, ale ruční zálohování funguje](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Ujistěte se, že má váš operační systém nainstalované nejnovější aktualizace.
- [Zajistěte, aby nepodporované jednotky a soubory s nepodporovanými atributy byly ze zálohy vyloučen](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Zajistěte, aby byly **systémové hodiny** v chráněném systému nakonfigurované na správné časové pásmo. <br>
- [Ujistěte se, že má server minimálně rozhraní .Net Framework verze 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Pokud se pokoušíte znovu **zaregistrovat server** do trezoru, pak: <br>
  - Ujistěte se, že je agent na serveru odinstalován a že je odstraněný z portálu. <br>
  - Použijte stejné heslo, jaké jste původně použili k registraci serveru. <br>
- Pokud se jedná o offline zálohování, ujistěte se, že je na zdrojovém i kopírovacím počítači nainstalovaná Azure PowerShell verze 3.7.0, než začnete operaci offline zálohování.
- [Zvážení při spuštění agenta Zálohování na virtuálním počítači Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Omezení

- Obnovení na jiný hardware pomocí obnovení stavu systému nedoporučuje Microsoft
- Zálohování stavu systému aktuálně podporuje místní servery Windows. Tato funkce není k dispozici pro virtuální počítače Azure.

## <a name="prerequisites"></a>Předpoklady

Před odstraňováním potíží se zálohováním stavu systému pomocí Azure Backup proveďte následující kontrolu předpokladů.  

### <a name="verify-windows-server-backup-is-installed"></a>Ověřte, že je nainstalovaná Zálohování Windows Serveru.

Ujistěte se, že je na serveru nainstalovaná a povolená Zálohování Windows Serveru. Chcete-li zjistit stav instalace, spusťte tento příkaz prostředí PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Pokud výstup zobrazuje **stav instalace** jako **k dispozici**, znamená to, že je funkce zálohování serveru k dispozici pro instalaci, ale není nainstalovaná na serveru. Pokud ale Zálohování Windows Serveru není nainstalovaná, použijte k její instalaci jednu z níže uvedených metod.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metoda 1: instalace Zálohování Windows Serveru pomocí prostředí PowerShell

Pokud chcete nainstalovat Zálohování Windows Serveru pomocí PowerShellu, spusťte následující příkaz:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metoda 2: instalace Zálohování Windows Serveru pomocí Správce serveru

Pokud chcete nainstalovat Zálohování Windows Serveru pomocí Správce serveru, proveďte následující kroky:

1. V nástroji **Správce serveru** vyberte **Přidat role a funkce**. Zobrazí se **Průvodce přidáním rolí a funkcí** .

    ![Řídicí panel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Vyberte **typ instalace** a vyberte **Další**.

    ![Typ instalace](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Vyberte server z fondu serverů a vyberte **Další**. V roli serveru ponechte výchozí výběr a vyberte **Další**.
4. Na kartě **funkce** vyberte **zálohování Windows serveru** a vyberte **Další**.

    ![Okno pro výběr funkcí](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na kartě **potvrzení** vyberte **instalovat** a spusťte proces instalace.
6. Na kartě **výsledky** se zobrazí funkce zálohování Windows serveru se úspěšně nainstalovala na Windows Server.

    ![Výsledky instalace](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Oprávnění k informacím o svazcích systému

Ujistěte se, že místní systém má úplnou kontrolu nad složkou **informací o systémovém svazku** umístěnou ve svazku, ve kterém je nainstalovaný systém Windows. Obvykle se jedná **o C:\System informace o svazku**. Zálohování Windows serveru může selhat, pokud výše uvedená oprávnění nejsou správně nastavená.

### <a name="dependent-services"></a>Závislé služby

Ujistěte se, že níže uvedené služby jsou ve stavu spuštěno:

**Název služby** | **Typ spouštění**
--- | ---
Vzdálené volání procedur (RPC) | Automaticky
Systém událostí COM+ (EventSystem) | Automaticky
Služba oznamování systémových událostí (SENS) | Automaticky
Stínová kopie svazku (VSS) | Ruční
Poskytovatel Microsoft software Stínová kopie (SWPRV) | Ruční

### <a name="validate-windows-server-backup-status"></a>Ověřit stav Zálohování Windows Serveru

Pokud chcete ověřit stav Zálohování Windows Serveru, proveďte následující kroky:

- Ujistěte se, že je spuštěný prostředí PowerShell WSB.

  - Spusťte `Get-WBJob` z PowerShellu se zvýšenými oprávněními a ujistěte se, že nevrátí následující chybu:

    > [!WARNING]
    > Get-WBJob: pojem Get-WBJob se nerozpoznal jako název rutiny, funkce, souboru skriptu nebo spustitelného programu. Zkontrolujte pravopis názvu, nebo pokud byla vložena cesta, ověřte, zda je cesta správná, a akci opakujte.

    - Pokud dojde k chybě s touto chybou, přeinstalujte funkci Zálohování Windows Serveru na serverovém počítači, jak je uvedeno v kroku 1 v části požadavky.

  - Ujistěte se, že zálohování WSB funguje správně, spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Nahraďte X písmenem jednotky svazku, kam chcete uložit image pro zálohování stavu systému.

    - Pravidelně kontrolujte stav úlohy spuštěním `Get-WBJob` příkazu z PowerShellu se zvýšenými oprávněními.
    - Po dokončení úlohy zálohování ověřte konečný stav úlohy spuštěním `Get-WBJob -Previous 1` příkazu.

Pokud úloha selže, indikuje problém WSB, který by způsobil selhání zálohování stavu systému agenta MARS.

## <a name="common-errors"></a>Běžné chyby

### <a name="vss-writer-timeout-error"></a>Chyba časového limitu zapisovače VSS

| Příznak | Příčina | Řešení
| -- | -- | --
| -Agent MARS se nezdařil s chybovou zprávou: "úloha WSB selhala s chybami VSS. Ověřte protokoly událostí služby VSS a vyřešte selhání. "<br/><br/> -V protokolech událostí aplikace VSS je k dispozici následující protokol chyb: "modul pro zápis stínové kopie svazku odmítl událost s chybou 0x800423f2. časový limit zapisovače vypršel mezi událostmi zablokování a rozmrazení."| Zapisovač VSS nejde v čase dokončit kvůli nedostatku prostředků procesoru a paměti v počítači. <br/><br/> Modul pro zápis stínové kopie svazku už používá jiný zálohovací software, protože pro tuto zálohu se nepovedlo dokončit operaci snímku. | Počkejte, až se v systému uvolní procesor/paměť, nebo přerušte procesy, které zabírají příliš mnoho paměti nebo procesoru, a operaci opakujte. <br/><br/>  Počkejte na dokončení probíhajícího zálohování a zkuste operaci provést později, až v počítači neběží žádné zálohy.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nedostatek místa na disku pro zvětšení stínových kopií

| Příznak | Řešení
| -- | --
| -Agent MARS se nezdařil s chybovou zprávou: zálohování se nezdařilo, protože svazek stínové kopie nemohl být zvětšen, protože na svazcích obsahujících systémové soubory není dostatek místa na disku. <br/><br/> -V protokolech událostí systému Volsnap jsou k dispozici následující protokol chyb nebo upozornění: "na svazku C není dostatek místa na disku: pro zvýšení úložiště stínové kopie pro stínové kopie C: z důvodu této chyby je riziko odstranění všech stínových kopií svazku C:" | – Uvolněte místo na zvýrazněném svazku v protokolu událostí tak, aby bylo dost místa pro růst stínových kopií, zatímco probíhá zálohování. <br/><br/> – Při konfiguraci prostoru stínových kopií můžeme omezit množství místa využitého pro stínovou kopii. Další informace najdete v tomto [článku](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage) .

### <a name="efi-partition-locked"></a>Oddíl EFI uzamčen

| Příznak | Řešení
| -- | --
| Agent MARS se nezdařil s chybovou zprávou: "záloha stavu systému se nezdařila, protože systémový oddíl EFI je uzamčen. Důvodem může být přístup k systémovému oddílu pomocí zabezpečení nebo zálohování softwaru třetí strany. | – Pokud k problému dochází kvůli bezpečnostnímu softwaru jiného výrobce, musíte se obrátit na dodavatele antivirového programu, aby mohl agenta MARS umožnit. <br/><br/> – Pokud je spuštěný zálohovací software jiného výrobce, počkejte na jeho dokončení a pak zkuste zálohování zopakovat.

## <a name="next-steps"></a>Další kroky

- Další informace o stavu systému Windows ve Správce prostředků nasazení najdete v tématu [zálohování stavu systému Windows Server](backup-azure-system-state.md)
