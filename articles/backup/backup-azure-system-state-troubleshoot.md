---
title: Poradce při potížích se zálohováním stavu systému
description: V tomto článku se dozvíte, jak řešit problémy v zálohování stavu systému pro místní servery windows.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969578"
---
# <a name="troubleshoot-system-state-backup"></a>Poradce při potížích se zálohováním stavu systému

Tento článek popisuje řešení problémů, které mohou narazit při použití zálohování stavu systému.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Doporučujeme provést níže uvedené ověření, než začnete řešit potíže se zálohováním stavu systému:

- [Zajistěte, aby byl agent služby Microsoft Azure Recovery Services (MARS) aktuální](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ujistěte se, že existuje síťové propojení mezi agentem MARS a Azure.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby restartujte a opakujte operaci
- [Ujistěte se, že je v umístění pomocné složky k dispozici 5 až 10 % volného místa.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Zkontrolujte, jestli službě Azure Backup nepřekáží jiný proces nebo antivirový software.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Plánované zálohování se nedaří, ale ruční zálohování funguje](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Ujistěte se, že má váš operační systém nainstalované nejnovější aktualizace.
- [Zajistěte, aby nepodporované jednotky a soubory s nepodporovanými atributy byly vyloučeny ze zálohy.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Zajistit, aby **systémové hodiny** v chráněném systému byly nakonfigurovány tak, aby opravovaly časové pásmo <br>
- [Ujistěte se, že má server minimálně rozhraní .Net Framework verze 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Pokud se pokoušíte **znovu zaregistrovat server** do trezoru, pak: <br>
  - Ujistěte se, že agent je odinstalován na serveru a je odstraněn z portálu <br>
  - Použijte stejné heslo, jaké jste původně použili k registraci serveru. <br>
- Pokud se jedná o zálohování offline, ujistěte se, že azure PowerShell verze 3.7.0 je nainstalován na zdrojovém i kopírovacím počítači, než začnete s operací zálohování offline.
- [Zvažování, když je na virtuálním počítači Azure spuštěný agent zálohování](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Omezení

- Microsoft nedoporučuje obnovování pomocí obnovení stavu systému na jiný hardware.
- Zálohování stavu systému aktuálně podporuje místní servery systému Windows. Tato funkce není dostupná pro virtuální počítače Azure.

## <a name="prerequisites"></a>Požadavky

Než vyřešujeme potíže se zálohováním stavu systému pomocí služby Azure Backup, proveďte níže uvedenou kontrolu požadavků.  

### <a name="verify-windows-server-backup-is-installed"></a>Ověření instalace programu Zálohování serveru

Zkontrolujte, zda je na serveru nainstalována a povolena funkce Zálohování serveru. Chcete-li zkontrolovat stav instalace, spusťte tento příkaz prostředí PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Pokud výstup zobrazuje **stav instalace** jako **dostupný**, znamená to, že funkce zálohování systému Windows Server je k dispozici pro instalaci, ale není nainstalována na serveru. Pokud však program Zálohování serveru není nainstalován, nainstalujte ji pomocí jedné z níže uvedených metod.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metoda 1: Instalace zálohování serveru pomocí prostředí PowerShell

Chcete-li nainstalovat zálohu serveru pomocí prostředí PowerShell, spusťte následující příkaz:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metoda 2: Instalace programu Zálohování serveru pomocí Správce serveru

Chcete-li nainstalovat program Zálohování serveru pomocí Správce serveru, proveďte následující kroky:

1. V **části Server Manger**klikněte na **Přidat role a funkce**. Zobrazí **se Průvodce přidáním rolí a funkcí.**

    ![Řídicí panel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Vyberte **Typ instalace** a klepněte na tlačítko **Další**.

    ![Typ instalace](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Vyberte server z fondu serverů a klepněte na tlačítko **Další**. V roli serveru ponechejte výchozí výběr a klepněte na tlačítko **Další**.
4. Vyberte kartu **Zálohování serveru** v části **Funkce** a klepněte na tlačítko **Další**.

    ![funkce](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na kartě **Potvrzení** klikněte na **Nainstalovat** a spusťte proces instalace.
6. Na kartě **Výsledky** se zobrazí funkce Zálohování serveru, která je úspěšně nainstalována v systému Windows Server.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Oprávnění k informacím o systémovém svazku

Zkontrolujte, zda má místní systém plnou kontrolu nad složkou **Informace o systémovém svazku** umístěném ve svazku, na kterém je nainstalován systém Windows. Obvykle se jedná **o c:\System Volume Information**. Zálohování systému Windows Server může selhat, pokud výše uvedená oprávnění nejsou nastavena správně

### <a name="dependent-services"></a>Závislé služby

Ujistěte se, že níže uvedené služby jsou ve stavu spuštění:

**Název služby** | **Typ spuštění**
--- | ---
Vzdálené volání procedur(RPC) | Automaticky
Systém událostí modelu COM+ (EventSystem) | Automaticky
Služba hlášení systémových událostí (SENS) | Automaticky
Stínová kopie svazku(VSS) | Ruční
Poskytovatel stínové kopie softwaru Microsoft (SWPRV) | Ruční

### <a name="validate-windows-server-backup-status"></a>Ověření stavu zálohování serveru systému Windows Server

Chcete-li ověřit stav zálohování serveru, proveďte následující kroky:

- Ujistěte se, že je spuštěno prostředí WSB PowerShell

  - Spusťte `Get-WBJob` ze zvýšeného prostředí PowerShell a ujistěte se, že nevrátí následující chybu:

    > [!WARNING]
    > Get-WBJob: Termín Get-WBJob není rozpoznán jako název rutiny, funkce, souboru skriptu nebo funkčního programu. Zkontrolujte pravopis názvu nebo pokud byla zahrnuta cesta, ověřte, zda je cesta správná, a akci opakujte.

    - Pokud se s touto chybou nezdaří, přeinstalujte funkci Zálohování serveru v počítači serveru, jak je uvedeno v kroku 1 požadavků.

  - Ujistěte se, že záloha WSB funguje správně, spuštěním níže uvedeného příkazu z příkazového řádku se zvýšenými oprávněními:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Nahraďte X písmenem jednotky svazku, do kterého chcete uložit záložní bitovou kopii stavu systému.

    - Pravidelně kontrolujte stav úlohy `Get-WBJob` spuštěním příkazu ze zvýšené houževnatého prostředí PowerShell
    - Po dokončení úlohy zálohování zkontrolujte konečný `Get-WBJob -Previous 1` stav úlohy spuštěním příkazu

Pokud se úloha nezdaří, označuje problém WSB, který by vedl k selhání zálohování systémového stavu agenta MARS.

## <a name="common-errors"></a>Běžné chyby

### <a name="vss-writer-timeout-error"></a>Chyba časového doby zapisovače VSS

| Příznak | Příčina | Řešení
| -- | -- | --
| - Agent MARS selže s chybovou zprávou: "Úloha WSB se nezdařila s chybami VSS. Zkontrolujte protokoly událostí VSS k vyřešení selhání"<br/><br/> - Následující protokol chyb je k dispozici v protokolech událostí aplikace VSS: "Zapisovač VSS odmítl událost s chybou 0x800423f2, vypršel časový limit zapisovatele mezi událostmi Zmrazení a rozmrazení."| Zapisovač VSS není schopen dokončit v čase z důvodu nedostatku procesoru a paměťových prostředků v počítači <br/><br/> Jiný zálohovací software již používá zapisovač VSS, v důsledku toho operace snímku nelze dokončit pro tuto zálohu | Počkejte na uvolnění procesoru/paměti v systému nebo přerušte procesy, které zabírají příliš mnoho paměti/procesoru, a zkuste operaci znovu. <br/><br/>  Počkejte na dokončení probíhající zálohy a zkuste operaci později, když v počítači nejsou spuštěny žádné zálohy.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nedostatek místa na disku pro růst stínových kopií

| Příznak | Řešení
| -- | --
| - Agent MARS selže s chybovou zprávou: Zálohování se nezdařilo, protože svazek stínové kopie nemohl růst kvůli nedostatku místa na disku na svazcích obsahujících systémové soubory <br/><br/> - Následující chyba / upozornění log je přítomen v protokolech událostí volsnap systému: "Na svazku C nebylo dostatek místa na disku: pro rozšíření úložiště stínové kopie kopie C: z důvodu této poruchy jsou všechny stínové kopie svazku C: ohroženy odstraněním" | - Uvolněte místo ve zvýrazněném svazku v protokolu událostí, takže je dostatek místa pro růst stínových kopií během zálohování <br/><br/> - Při konfiguraci stínové kopie prostoru můžeme omezit množství místa používaného pro stínovou kopii. Další informace naleznete v tomto [článku](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Oddíl EFI je uzamčen

| Příznak | Řešení
| -- | --
| Agent MARS selže s chybovou zprávou: "Zálohování stavu systému se nezdařilo, protože systémový oddíl EFI je uzamčen. To může být způsobeno přístupem k systémovým oddílům pomocí zabezpečení jiného výrobce nebo zálohovaného softwaru" | - Pokud je problém způsoben bezpečnostním softwarem jiného výrobce, musíte kontaktovat dodavatele Anti Virus, aby mohl povolit agenta MARS <br/><br/> - Pokud je spuštěn zálohovací software jiného výrobce, počkejte na jeho dokončení a opakujte zálohování

## <a name="next-steps"></a>Další kroky

- Další informace o stavu systému Windows v nasazení Správce prostředků naleznete v tématu [Zálohování stavu systému Windows Server](backup-azure-system-state.md)
