---
title: Řešení potíží se zálohováním stavu systému pomocí Azure Backup
description: Řešení potíží se zálohováním stavu systému.
author: srinathvasireddy
manager: sivan
keywords: Jak zálohovat; stav systému zálohování
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: srinathv
ms.openlocfilehash: 8996270acb1525697f29b4251bf4e11d2db62fdf
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465360"
---
# <a name="troubleshoot-system-state-backup"></a>Řešení potíží se zálohováním stavu systému

Tento článek popisuje řešení problémů, se kterými se můžete setkat při použití zálohování stavu systému.

## <a name="basic-troubleshooting"></a>Základní řešení potíží
Před zahájením odstraňování potíží se zálohováním stavu systému doporučujeme provést níže uvedené ověření:

- [Zajistěte, aby byl agent Microsoft Azure Recovery Services (MARS) aktuální.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ujistěte se, že existuje síťové propojení mezi agentem MARS a Azure.](https://aka.ms/AB-A4dp50)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby ji restartujte a opakujte operaci.
- [Ujistěte se, že je v umístění pomocné složky k dispozici 5 až 10 % volného místa.](https://aka.ms/AB-AA4dwtt)
- [Zkontrolujte, jestli službě Azure Backup nepřekáží jiný proces nebo antivirový software.](https://aka.ms/AB-AA4dwtk)
- [Plánované zálohování se nedaří, ale ruční zálohování funguje](https://aka.ms/ScheduledBackupFailManualWorks)
- Ujistěte se, že má váš operační systém nainstalované nejnovější aktualizace.
- [Zajistěte, aby nepodporované jednotky a soubory s nepodporovanými atributy byly ze zálohy vyloučen](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Ujistěte se, že **systémové hodiny** v chráněném systému jsou nakonfigurované na správné časové pásmo. <br>
- [Ujistěte se, že má server minimálně rozhraní .Net Framework verze 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Pokud se pokoušíte **znovu zaregistrovat server** k trezoru, postupujte takto: <br>
  - Ujistěte se, že je agent odinstalovaný ze serveru a odstraněný z portálu. <br>
  - Použijte stejné heslo, jaké jste původně použili k registraci serveru. <br>
- V případě offline zálohování zajistěte, aby byla na zdrojovém i kopírovacím počítači nainstalovaná Azure PowerShell verze 3.7.0, než začnete operaci offline zálohování.
- [Zvážení při spuštění agenta Zálohování na virtuálním počítači Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Omezení
- Microsoft nedoporučuje obnovování pomocí obnovení stavu systému na jiný hardware.
- Zálohování stavu systému aktuálně podporuje místní servery Windows, takže tato funkce není dostupná pro virtuální počítače Azure.

## <a name="pre-requisite"></a>Předpoklad

Než vyřešíte zálohování stavu systému pomocí Azure Backup, ujistěte se, že jste předvedli kontrolu níže uvedených požadavků.  

### <a name="verify-windows-server-backup-is-installed"></a>Ověřte, že je nainstalovaná Zálohování Windows Serveru.

Ujistěte se, že je na serveru nainstalovaná a povolená Zálohování Windows Serveru. Chcete-li zjistit stav instalace, spusťte následující příkaz prostředí PowerShell:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Pokud výstup zobrazuje **stav instalace** jako **k dispozici**, znamená to, že je funkce zálohování serveru k dispozici pro instalaci, ale není nainstalovaná na serveru. Pokud ale Zálohování Windows Serveru není nainstalovaná, použijte k její instalaci jednu z následujících metod.

**Metoda 1: Instalace Zálohování Windows Serveru pomocí prostředí PowerShell**

Pokud chcete nainstalovat Zálohování Windows Serveru pomocí PowerShellu, spusťte následující příkaz:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Metoda 2: Instalace Zálohování Windows Serveru pomocí Správce serveru**

Pokud chcete nainstalovat Zálohování Windows Serveru pomocí Správce serveru, udělejte toto:

1. V nástroji **správce** serveru klikněte na **Přidat role a funkce**. Zobrazí se **Průvodce přidáním rolí a funkcí** .

    ![Řídicí panel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Vyberte **typ instalace** a klikněte na **Další**.

    ![Typ instalace](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Vyberte server z fondu serverů a klikněte na **Další**. V roli serveru ponechte výchozí výběr a klikněte na **Další**.
4. Na kartě **funkce** vyberte **zálohování Windows serveru** a klikněte na **Další**.

    ![Database](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na kartě **potvrzení** kliknutím na **instalovat** spusťte proces instalace.
6. Na kartě **výsledky** se zobrazí funkce zálohování Windows serveru se úspěšně nainstalovala na Windows Server.

    ![výsledek](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Oprávnění k informacím o svazcích systému

Ujistěte se, že místní systém má úplnou kontrolu nad složkou **informací o systémovém svazku** ve svazku, ve kterém je nainstalovaný systém Windows. Obvykle se jedná **o C:\System informace o svazku**. Zálohování Windows serveru může selhat, pokud výše uvedená oprávnění nejsou správně nastavená.

### <a name="dependent-services"></a>Závislé služby

Ujistěte se, že níže uvedené služby jsou ve stavu spuštěno:

**Název služby** | **Typ spouštění**
--- | ---
Vzdálené volání procedur (RPC) | Automatické
Systém událostí COM+ (EventSystem) | Automatické
Služba oznamování systémových událostí (SENS) | Automatické
Stínová kopie svazku (VSS) | Ruční
Poskytovatel Microsoft software Stínová kopie (SWPRV) | Ruční

### <a name="validate-windows-server-backup-status"></a>Ověřit stav Zálohování Windows Serveru

Pokud chcete ověřit stav Zálohování Windows Serveru, proveďte následující:

  * Ujistěte se, že je spuštěný prostředí PowerShell WSB.

    -   Spusťte `Get-WBJob` z PowerShellu se zvýšenými oprávněními a ujistěte se, že nevrátí následující chybu:

    > [!WARNING]
    > Get-WBJob: Pojem Get-WBJob se nerozpoznal jako název rutiny, funkce, souboru skriptu nebo spustitelného programu. Zkontrolujte pravopis názvu, nebo pokud byla vložena cesta, ověřte, zda je cesta správná, a akci opakujte.

    -   Pokud dojde k chybě s touto chybou, pak na serverovém počítači znovu nainstalujte funkci Zálohování Windows Serveru, jak je uvedeno v kroku 1.

  * Ujistěte se, že zálohování WSB funguje správně, spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Nahraďte X písmenem jednotky svazku, kam chcete uložit image pro zálohování stavu systému.

    - Pravidelně kontrolujte stav úlohy spuštěním `Get-WBJob` příkazu z PowerShellu se zvýšenými oprávněními.        
    - Po dokončení úlohy zálohování ověřte konečný stav úlohy spuštěním `Get-WBJob -Previous 1` příkazu.

Pokud úloha selže, indikuje problém WSB, což by vedlo k selhání zálohování stavu systému agenta MARS.

## <a name="common-errors"></a>Běžné chyby

### <a name="vss-writer-timeout-error"></a>Chyba časového limitu zapisovače VSS

| Příznak | Příčina | Řešení
| -- | -- | --
| – Agent MARS se nezdařil s chybovou zprávou: Úloha WSB se nezdařila s chybami VSS. Ověřte protokoly událostí služby VSS a vyřešte selhání. "<br/><br/> -V protokolech událostí aplikace VSS je k dispozici následující protokol chyb: "Zapisovač VSS odmítl událost s chybou 0x800423f2. časový limit zapisovače vypršel mezi událostmi zablokování a rozmrazení."| Zapisovač VSS nejde v čase dokončit kvůli nedostatku prostředků procesoru a paměti v počítači. <br/><br/> Modul pro zápis stínové kopie svazku už používá jiný zálohovací software, protože pro tuto zálohu se nepovedlo dokončit operaci snímku. | Počkejte, až bude procesor nebo paměť uvolněna v systému, nebo přeruší procesy, které zabírají příliš mnoho paměti nebo procesoru, a operaci opakujte. <br/><br/>  Počkejte na dokončení probíhajícího zálohování a zkuste operaci provést později, až v počítači neběží žádné zálohy.


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nedostatek místa na disku pro zvětšení stínových kopií

| Příznak | Řešení
| -- | --
| – Agent MARS se nezdařil s chybovou zprávou: Zálohování nebylo úspěšné, protože svazek stínové kopie nešlo zvětšit, protože na svazcích obsahujících systémové soubory není dost místa na disku. <br/><br/> -V protokolech událostí systému Volsnap je k dispozici následující protokol chyb nebo upozornění: "Na svazku C není dostatek místa na disku: pro zvětšení úložiště stínových kopií pro stínové kopie jazyka C: z důvodu této chyby je nebezpečí odstranění všech stínových kopií svazku C:" | – Uvolněte místo na zvýrazněném svazku v protokolu událostí tak, aby bylo dost místa pro růst stínových kopií, zatímco probíhá zálohování. <br/><br/> – Při konfiguraci prostoru stínové kopie můžeme omezit množství místa používaného pro stínovou kopii, další informace najdete v tomto [článku](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax) .


### <a name="efi-partition-locked"></a>Oddíl EFI uzamčen

| Příznak | Řešení
| -- | --
| Agent MARS se nezdařil s chybovou zprávou: Zálohování stavu systému se nepovedlo, protože systémový oddíl EFI je zamčený. Důvodem může být přístup k systémovému oddílu pomocí zabezpečení nebo zálohování softwaru třetí strany. | – Pokud k problému dochází kvůli bezpečnostnímu softwaru jiného výrobce, musíte se obrátit na dodavatele antivirového programu, aby mohl agenta MARS umožnit. <br/><br/> – Pokud je spuštěný zálohovací software jiného výrobce, počkejte na jeho dokončení a pak zkuste zálohování zopakovat.


## <a name="next-steps"></a>Další postup

- Další informace o stavu systému Windows ve Správce prostředků nasazení najdete v tématu [zálohování stavu systému Windows Server](backup-azure-system-state.md)
