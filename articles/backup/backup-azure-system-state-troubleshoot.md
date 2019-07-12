---
title: Řešení potíží s zálohování stavu systému pomocí služby Azure Backup
description: Řešení potíží v zálohování stavu systému.
services: backup
author: srinathvasireddy
manager: sivan
keywords: Postup zálohování; Stav zálohování systému
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathv
ms.openlocfilehash: 87b5fff58ecf9e89bc94f31a0bc3a591c146c88f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704997"
---
# <a name="troubleshoot-system-state-backup"></a>Řešení potíží s zálohování stavu systému

Tento článek popisuje řešení problémů, které se můžete setkat při používání zálohování stavu systému.

## <a name="basic-troubleshooting"></a>Základní řešení potíží
Doporučujeme provést ověření, než začnete řešení potíží s zálohování stavu systému:

- [Ujistěte se, že Agent Microsoft Azure Recovery Services (MARS) je aktuální.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ujistěte se, že existuje síťové propojení mezi agentem MARS a Azure.](https://aka.ms/AB-A4dp50)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby ji restartujte a opakujte operaci.
- [Ujistěte se, že je v umístění pomocné složky k dispozici 5 až 10 % volného místa.](https://aka.ms/AB-AA4dwtt)
- [Zkontrolujte, jestli službě Azure Backup nepřekáží jiný proces nebo antivirový software.](https://aka.ms/AB-AA4dwtk)
- [Plánované zálohování se nedaří, ale ruční zálohování funguje](https://aka.ms/ScheduledBackupFailManualWorks)
- Ujistěte se, že má váš operační systém nainstalované nejnovější aktualizace.
- [Zajištění nepodporované disky a soubory s nepodporované atributy jsou vyloučeny ze zálohy](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Ujistěte se, že **systémové hodiny** v chráněném systému jsou nakonfigurované na správné časové pásmo. <br>
- [Ujistěte se, že má server minimálně rozhraní .Net Framework verze 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Pokud se pokoušíte **znovu zaregistrovat server** k trezoru, postupujte takto: <br>
  - Ujistěte se, že je agent odinstalovaný ze serveru a odstraněný z portálu. <br>
  - Použijte stejné heslo, jaké jste původně použili k registraci serveru. <br>
- V případě offline zálohování zkontrolujte, zda prostředí Azure PowerShell verze 3.7.0 je nainstalován na počítači zdroje a zkopírujte před zahájením práce offline zálohování
- [Poslední informací Backup agent běží na virtuálním počítači Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Omezení
- Microsoft nedoporučuje obnovování pomocí obnovení stavu systému na jiný hardware.
- Zálohování stavu systému v současné době podporuje "místní" servery Windows, tato funkce není k dispozici pro virtuální počítače Azure.

## <a name="pre-requisite"></a>Předpoklad

Než jsme řešení zálohování stavu systému pomocí služby Azure Backup, ujistěte se, je provedení akce zkontrolujte následující požadavky.  

### <a name="verify-windows-server-backup-is-installed"></a>Ověřte, že je nainstalované zálohování Windows serveru

Zajištění zálohování Windows serveru je nainstalovaný a povolený na serveru. Chcete-li zkontrolovat stav instalace, spusťte následující příkaz Powershellu:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Pokud ve výstupu nezobrazí **stav instalace** jako **dostupné**, pak to znamená, že funkce zálohování Windows serveru je k dispozici pro instalaci, ale není nainstalované na serveru. Nicméně pokud není nainstalované zálohování Windows serveru, pak použijte jednu z následujících metod k její instalaci.

**Metoda 1: Nainstalujte zálohování Windows serveru pomocí prostředí PowerShell**

Chcete-li nainstalujte zálohování Windows serveru pomocí prostředí PowerShell, spusťte následující příkaz:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Metoda 2: Nainstalujte zálohování Windows serveru pomocí Správce serveru**

Nainstalujte zálohování Windows serveru pomocí Správce serveru, proveďte níže:

1. V **správce serveru** a klikněte na tlačítko **přidat role a funkce**. **Průvodce přidáním rolí a funkcí** se zobrazí.

    ![Řídicí panel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Vyberte **typ instalace** a klikněte na tlačítko **Další**.

    ![Typ instalace](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Vyberte server z fondu serverů a klikněte na tlačítko **Další**. V roli serveru, ponechte výchozí výběr a klikněte na tlačítko **Další**.
4. Vyberte **zálohování Windows serveru** v **funkce** kartě a klikněte na tlačítko **Další**.

    ![Database](./media/backup-azure-system-state-troubleshoot/features.png)

5. V **potvrzení** klikněte na tlačítko **nainstalovat** spustit proces instalace.
6. V **výsledky** kartě, zobrazí se zálohování Windows serveru v systému Windows Server je úspěšně nainstalovaná funkce.

    ![výsledek](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Oprávnění informace o svazku systému

Ujistěte se, že místní systém má úplné řízení na **informací o systémovém svazku** složka je umístěna ve svazku, kde je nainstalován systém windows. To je obvykle **C:\System Volume Information**. Zálohování Windows serveru může selhat, pokud se výše uvedená oprávnění nejsou správně nastavené.

### <a name="dependent-services"></a>Závislé služby

Zkontrolujte níže služby je v běžícím stavu:

**Název služby** | **Typ spuštění**
--- | ---
Vzdálené volání procedur | Automatické
System(EventSystem) událostí modelu COM + | Automatické
Systém Service(SENS) oznámení událostí | Automatické
Svazek stínové Copy(VSS) | Manual
Provider(SWPRV) softwaru stínové kopie | Manual

### <a name="validate-windows-server-backup-status"></a>Ověřit stav zálohování Windows serveru

Pokud chcete ověřit stav zálohování Windows serveru, proveďte níže:

  * Ujistěte se, že je spuštěná WSB Powershellu

    -   Spustit `Get-WBJob` z Powershellu se zvýšenými oprávněními a ujistěte se, že nevrací následující chybu:

    > [!WARNING]
    > Get-WBJob: Termín 'Get-WBJob' nebyl rozpoznán jako název rutiny, funkce, soubor skriptu nebo spustitelného programu. Zkontrolujte, zda název, nebo pokud cesty byl zahrnut, ověřte správnost cesty a zkuste to znovu.

    -   Pokud selže s touto chybou znovu nainstalujte funkci Zálohování serveru na počítači serveru, jak je uvedeno v kroku 1 požadavky.

  * Ověřte spuštěním pracuje správně, zálohování WSB následující příkaz z příkazového řádku se zvýšenými oprávněními:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Nahradit X s písmenem jednotky svazku, kam chcete uložit stav systému zálohování bitové kopie.

    - Pravidelně kontrolovat stav úlohy spuštěním `Get-WBJob` příkaz z Powershellu se zvýšenými oprávněními        
    - Po dokončení úlohy zálohování zkontrolujte stav poslední úlohy spuštěním `Get-WBJob -Previous 1` příkaz

Pokud úloha selže, znamená to problém zálohování WSB, což by vytvořilo agenta MARS selhání zálohování stavu systému.

## <a name="common-errors"></a>Běžné chyby

### <a name="vss-writer-timeout-error"></a>Chyba časového limitu zapisovače VSS

| Příznak | Příčina | Řešení
| -- | -- | --
| -Agenta MARS selže s chybovou zprávou: "Úlohu WSB selhalo s chybami stínové kopie svazku. Zkontrolujte protokoly událostí služby VSS, chcete-li vyřešit chybu"<br/><br/> -Následující chybu je k dispozici v protokolech událostí aplikace služby VSS protokolu: "Zapisovač VSS vícesměrového vysílání odmítla událost s chybou 0x800423f2, vypršel časový limit modulu pro zápis mezi událostmi blokovat a odblokovat."| Zapisovač VSS není schopen dokončit v čase z důvodu nedostatku prostředků procesoru a paměti v počítači <br/><br/> Jiné zálohovací software je už pomocí zapisovače služby VSS, v důsledku operace snímku nebyla dokončena, pro tuto zálohu | Počkejte procesoru nebo paměti uvolnit v systému nebo abort procesů trvá příliš mnoho paměti a procesoru a operaci opakujte <br/><br/>  Počkejte probíhající zálohování dokončí, a zkuste operaci zopakovat později neopravňují žádné zálohy na počítači


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nedostatek místa na disku k rozvoji stínové kopie

| Příznak | Řešení
| -- | --
| -Agenta MARS selže s chybovou zprávou: Zálohování selhalo, protože svazek stínové kopie nelze rozšiřovat z důvodu nedostatku místa na disku ve svazcích soubory systému <br/><br/> -Následující chyba/upozornění protokolů je k dispozici v volsnap v protokolu událostí systému: "Došlo nedostatku místa na disku na jednotce C: růst úložiště stínových kopií pro stínové kopie C: kvůli této chybě všechny stínové kopie svazku C: hrozí odstraňuje" | -Uvolněte místo ve svazku zvýrazněné v protokolu událostí tak, aby je dostatek místa pro stínové kopie rozrůstá, zatímco probíhá zálohování <br/><br/> – Při konfiguraci prostoru stínové kopie Omezujeme množství využité pro stínové kopie, další informace najdete v tomto [článku](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>Oddíl EFI uzamčen

| Příznak | Řešení
| -- | --
| MARS agent se nezdaří s chybovou zprávou: "Zpět stav systému až se nezdařila, protože systémový oddíl EFI je uzamčen. K tomu může být způsobeno přístup oddíl systému zabezpečení třetích stran nebo zálohování softwaru" | – Pokud je problém způsobený softwaru zabezpečení třetích stran, pak budete muset požádat dodavatele virů Anti tak, aby se povolit agenta MARS <br/><br/> – Pokud je spuštěna zálohovací software třetích stran, potom počkejte na dokončení a poté opakujte back up


## <a name="next-steps"></a>Další postup

- Další informace o stavu systému Windows v nasazení podle modelu Resource Manager najdete v tématu [zálohování stavu systému Windows Server](backup-azure-system-state.md)
