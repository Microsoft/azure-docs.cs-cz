---
title: Nainstalujte Azure Backup Server v2
description: Azure v2 zálohování serveru poskytuje vylepšené možnosti zálohování pro ochranu virtuálních počítačů, soubory a složky, úlohy a další. Informace o instalaci nebo upgradu na Azure Backup Server v2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: 27da52a46b52e06a8ea4f80fa06487aca34c1c3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606949"
---
# <a name="install-azure-backup-server-v2"></a>Nainstalujte Azure Backup Server v2

Azure Backup Server pomáhá chránit vaše virtuální počítače (VM), úlohy, soubory a složky a další. Azure Backup Server v2 staví na serveru Azure Backup v1 a poskytuje nové funkce, které nejsou k dispozici v v1. Porovnání funkcí mezi v1 a v2 najdete v tématu [matice ochrany serveru Azure Backup](backup-mabs-protection-matrix.md). 

Další funkce v zálohování serveru v2 jsou upgrade z verze 1 zálohování serveru. V1 zálohování serveru ale není předpoklady pro instalaci v2 zálohování serveru. Pokud chcete upgradovat z v1 zálohování serveru k zálohování serveru v2, nainstalujte na server ochrany zálohovat Server v2 zálohování serveru. Existující nastavení zálohování serveru zůstanou beze změn.

Zálohování serveru v2 můžete nainstalovat na Windows Server 2012 R2 nebo Windows Server 2016. Abyste mohli využívat nové funkce, například System Center 2016 Data Protection Manager moderních úložiště záloh, je nutné nainstalovat v2 zálohování serveru v systému Windows Server 2016. Před upgradovat nebo instalací v2 Backup Server, přečtěte si informace o [požadavky pro instalaci](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Azure Backup Server má stejný kód základní jako System Center Data Protection Manager. Zálohování serveru v1 je ekvivalentní k Data Protection Manager 2012 R2 a v2 zálohování serveru je ekvivalentní k Data Protection Manager 2016. Tento článek příležitostně odkazuje dokumentace k Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Upgrade záložní Server pro v2
K upgradu na Backup Server v2 zálohovat Server v1, zkontrolujte, zda že má instalace požadovaných aktualizací:

- [Aktualizovat agenty ochrany](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) na chráněných serverech.
- Upgrade systému Windows Server 2012 R2 na Windows Server 2016.
- Správce vzdáleného serveru Azure Backup upgradujte na všech provozních serverech.
- Ujistěte se, že jsou zálohy nastaveny na pokračovat bez restartování provozním serveru.


### <a name="upgrade-steps-for-backup-server-v2"></a>Postup upgradu serveru zálohování v2

1. V centru stažení [stažení aktualizace instalačního programu](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Po extrahování Průvodce instalací, ujistěte se, že **spuštění setup.exe** vybrané, a pak vyberte možnost **Dokončit**.

  ![Instalační program instalaci - spustit instalační program](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. V průvodci Microsoft Azure Backup Server v části **nainstalovat**, vyberte **Microsoft Azure Backup Server**.

  ![Instalační program instalaci - vyberte možnost instalace](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Na **úvodní** , přečtěte si upozornění a potom vyberte **Další**.

  ![Instalační program instalaci – úvodní stránka](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. Průvodce instalací provádí kontroly předpokladů, abyste měli jistotu, že můžete upgradovat prostředí. Na **požadovaných součástí kontroluje** vyberte **zkontrolujte**.

  ![Instalační program instalaci - požadovaných součástí kontroluje stránky](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Prostředí musí projít kontrol požadovaných součástí. Pokud vaše prostředí neprojde kontrol, Všimněte si, problémy a opravit je. Pak vyberte **zkontrolujte znovu**. Když předáte kontroly předpokladů, vyberte **Další**.

  ![Instalační program instalace - zkontrolujte znovu tlačítko](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Na **nastavení SQL** vyberte příslušnou volbu pro instalaci SQL a potom vyberte **zkontrolovat a nainstalovat**.

  ![Instalační program instalaci - stránka nastavení SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Kontroly může trvat několik minut. Po dokončení kontroly vyberte **Další**.

  ![Instalační program instalace - zkontrolujte nastavení SQL a tlačítko Instalovat](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. Na **nastavení instalace** stránky, proveďte požadované změny umístění, kde je nainstalován Backup Server, nebo cesta k pomocnému umístění. Vyberte **Next** (Další).

  ![Instalační program instalaci - stránka nastavení instalace](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Chcete-li dokončit Průvodce instalací, vyberte **Dokončit**.

  ![Instalační program instalaci - dokončit](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Přidání úložiště pro moderní úložiště záloh

Pokud chcete zlepšit efektivitu úložiště záloh, v2 zálohování serveru přidává podporu pro svazky. Podobně jako Backup Server v1 v2 zálohování serveru podporuje disky.

### <a name="add-volumes-and-disks"></a>Přidat svazky a disků
Pokud spustíte v2 zálohování serveru v systému Windows Server 2016, můžete k uložení zálohy dat svazky. Svazky nabízejí úspory úložiště a rychlejší zálohování. Protože svazky jsou nové zálohování serveru, musíte je přidat. 

Když přidáte Server zálohování svazku, můžete přiřadit svazek popisný název. Klikněte **popisný název** sloupec chcete název svazku. V případě potřeby můžete později změnit název. Také můžete použít PowerShell k přidání nebo změna popisné názvy pro svazky.

Postup přidání svazku v konzole pro správu:

1. V konzole správce serveru zálohování Azure vyberte **správy** > **diskového úložiště** > **přidat**.

    ![Otevřete Průvodce přidáním úložiště disku](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Otevře se Průvodce přidáním úložiště disku.

2. Na **přidání disku úložiště** stránky v **dostupných svazků** , vyberte svazek a pak vyberte **přidat**.
3. V **vybrané svazky** pole, zadejte popisný název pro svazek a potom vyberte **OK**.

      ![Průvodce přidáním úložiště disku – Přidání svazku](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Pokud chcete přidat disk, disk musí patřit do skupiny ochrany, která má starší verze úložiště. Tyto disky použít jenom pro tyto skupiny ochrany. Pokud Backup Server nemá zdroje, které mají další ochranu starší verze, se neuvádějí disku.

  Další informace o přidávání disků, najdete v tématu [přidávání disků, pokud chcete zvýšit starší verze úložiště](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Disk nelze udělit popisný název.


### <a name="assign-workloads-to-volumes"></a>Přiřadit svazky úlohy

Zálohování serveru zadejte, které úlohy jsou přiřazeny k svazků, které. Například můžete nastavit nákladné svazky, které podporují vysoký počet vstupně výstupních operací za sekundu (IOPS) pro uložení pouze úlohy, které vyžadují časté, vysoký počet záloh. Příkladem je SQL Server s protokoly transakcí.

#### <a name="update-dpmdiskstorage"></a>Aktualizace DPMDiskStorage

Pokud chcete aktualizovat vlastnosti svazku ve fondu úložiště zálohování serveru, použijte rutinu prostředí PowerShell DPMDiskStorage aktualizace.

Syntaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

V uživatelském rozhraní se projeví všechny změny, které můžete provést pomocí prostředí PowerShell.


## <a name="protect-data-sources"></a>Chránit zdroje dat
Chcete-li začít chránit zdroje dat, vytvořte skupinu ochrany. Následující kroky zvýrazněte změny nebo přidání do Průvodce vytvořením nové skupiny ochrany.

Chcete-li vytvořit skupinu ochrany:

1. V konzole správce zálohování serveru vyberte **ochrany**.

2. Na pásu karet vyberte **nový**.

    Otevře se Průvodce vytvořením nové skupiny ochrany.

  ![Průvodce vytvořením nové skupiny ochrany](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Na **úvodní** stránce vyberte **Další**.
4. Na **vybrat typ skupiny ochrany** vyberte typ skupiny ochrany, kterou chcete vytvořit a potom vyberte **Další**.

  ![Stránka Typ vyberte skupinu ochrany](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na **vybrat členy skupiny** stránky v **Dostupní členové** podokně, členy s jsou uvedeny agenty ochrany. V tomto příkladu vyberte svazek D:\ a E:\ a přidat je do **vybrané členy** podokně. Vyberte **Next** (Další).

  ![Vyberte skupiny členy stránky](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na **vyberte způsob ochrany dat** stránky, zadejte **název skupiny ochrany**, zvolte metodu ochrany a pak vyberte **Další**. Pokud chcete krátkodobou ochranu, je nutné vybrat **disku** zálohování metoda.

  ![Vyberte způsob ochrany dat stránky](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na **zadat krátkodobé cíle** vyberte podrobnosti **rozsah uchování** a **četnost synchronizace**. Pak vyberte **Další**. Pokud chcete změnit plán, kdy jsou pořizovány body obnovení, vyberte **upravit**.

  ![Určení krátkodobých cílů stránky](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na **zkontrolovat přidělení diskového úložiště** stránka, zkontrolujte podrobnosti o zdroji dat jste vybrali, jejich velikosti a hodnoty pro místo, na které se má zřídit a cílový svazek úložiště.

  ![Stránka Kontrola přidělení diskového úložiště](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Svazky úložiště jsou založené na svazku přidělení zatížení (nastavte pomocí prostředí PowerShell) a úložiště k dispozici. Svazky úložiště můžete změnit výběrem jiných svazků v rozevírací nabídce. Pokud změníte hodnotu **úložiště v cíli**, hodnota **úložiště disku** dynamicky se změní podle hodnoty v části **volného místa** a  **Underprovisioned místo**.

  Pokud zdroje dat růst podle plánu, hodnota **Underprovisioned místo** sloupec v **úložiště disku** odráží množství další úložiště, které je potřeba. Tato hodnota se používá k pomohou naplánovat požadavky na ukládání smooth zálohování. Pokud je hodnota nula, neexistují v blízké budoucnosti potenciální problémy s úložištěm. Pokud je hodnota číslo větší než nula, nemáte dostatečné úložiště přidělené (podle zásady ochrany vaší a velikost dat chráněných členů).

  ![Nevytížených diskového úložiště](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Dokončete vytváření skupinu ochrany, dokončete průvodce.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrovat starší verze úložiště do moderní úložiště záloh
Po upgradu na Backup Server v2 a upgradovat operační systém na Windows Server 2016, aktualizujte skupin ochrany používat moderní úložiště záloh. Ve výchozím nastavení se nezmění skupin ochrany. Budou i nadále fungovat jako původně určené. 

Aktualizace skupiny ochrany a používat moderní úložiště zálohy je volitelné. Aktualizace skupiny ochrany, ukončete ochranu všech zdrojů dat pomocí možnosti zachovat data. Pak přidejte zdroje dat do nové skupiny ochrany.

1. V konzole pro správu, vyberte **ochrany** funkce. V **člena skupiny ochrany** seznamu, klikněte pravým tlačítkem na člen a pak vyberte **zastavit ochranu člena**.

  ![Zastavit ochranu člena](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. V **odebrat ze skupiny** dialogové okno pole, zkontrolujte použité místo na disku a dostupné volné místo pro fond úložiště. Ve výchozím nastavení se nechte body obnovení na disku a povolení jejich platnost vyprší za jejich zásady uchovávání informací přidružených. Klikněte na **OK**.

  Pokud chcete hned vrátí do fondu úložiště volné použité místo na disku, vyberte **odstranit repliku na disku** políčko Odstranit záložní data (a bodů obnovení) přidružené k tohoto člena.

  ![Odebrat ze skupiny, dialogové okno](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Vytvořte skupinu ochrany, která používá moderní úložiště záloh. Zahrnout nechráněné datových zdrojů.


## <a name="add-disks-to-increase-legacy-storage"></a>Přidat disky, které chcete zvýšit velikost úložiště starší verze

Pokud chcete používat starší verze úložiště s zálohování serveru, vám může být nutné přidat disky, které chcete zvýšit starší verze úložiště. 

Přidání disku úložiště:

1. V konzole pro správu vyberte **správy** > **diskového úložiště** > **přidat**.

    ![Přidat dialogové okno diskového úložiště](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. V **přidání disku úložiště** dialogovém okně, vyberte **přidat disky**.

5. Vyberte disky, které chcete přidat, vyberte v seznamu dostupných disků, **přidat**a potom vyberte **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Aktualizujte agenta ochrany aplikace Data Protection Manager

Zálohování serveru System Center Data Protection Manager protection agent používá pro aktualizace. Pokud provádíte upgrade agenta ochrany, který není připojen k síti, nelze použít konzolu nástroje Data Protection Manager Administrator k dokončení upgradu připojeného agenta. Je nutné upgradovat agenta ochrany v prostředí neaktivní domény. Dokud klientský počítač je připojený k síti, konzolu nástroje Data Protection Manager Administrator ukazuje, že aktualizace agenta ochrany čeká na vyřízení.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače, které jsou připojené a klientské počítače, které nejsou připojené.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Aktualizace agenta ochrany pro připojené klientské počítače

1. V konzole správce zálohování serveru vyberte **správy** > **agenti**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.

  > [!NOTE]
  > **Aktualizací agenta** sloupec zobrazuje, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce je dostupná, pouze když je vybrán chráněný počítač a jsou k dispozici aktualizace.
  >
  >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, v **akce** podokně, vyberte **aktualizace**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Aktualizace agenta ochrany na klientském počítači, který není připojen

1. V konzole správce zálohování serveru vyberte **správy** > **agenti**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.

  > [!NOTE]
   > **Aktualizací agenta** sloupec zobrazuje, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce není dostupná, když je vybrán chráněný počítač, pokud jsou k dispozici aktualizace.
  >
  >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, vyberte **aktualizace**.

4. Pro klientské počítače, který není připojen k síti, dokud je počítač připojen k síti **stav agenta** sloupci se zobrazuje stav **aktualizace čeká na vyřízení**.

  Až se klientský počítač připojí k síti, **aktualizací agenta** sloupci pro klientský počítač se zobrazuje stav **aktualizace**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Přesunout z původní verze starší verze skupin ochrany a synchronizovat s Azure na novou verzi

Jakmile serveru Azure Backup a operačního systému jsou obě aktualizovány, jste připraveni k ochraně nové zdroje dat pomocí moderní úložiště záloh. Ale již chráněných zdrojů dat nadále chráněné starší verze způsobem, jako byly v serveru Azure Backup, ale všechny nové ochrany použije moderní úložiště záloh.

Následující kroky jsou při migraci zdroje dat z režimu starší verze ochrany do moderní úložiště záloh.

• Přidat nové svazky do fondu úložiště aplikace DPM a přiřadit popisné názvy a data zdroj značky v případě potřeby.
• Pro každý zdroj dat, který je v režimu starší verze, zastavte ochranu zdroje dat a "Uchovat chráněná Data".  To vám umožní obnovení starých bodů obnovení po migraci.

• Vytvořte nové PG a vyberte zdroje dat, které mají být uloženy pomocí nový formát.
• Aplikace DPM provede kopie repliky ze starší verze zálohování úložiště do úložiště moderní zálohování svazku místně.
Poznámka: Toto se zobrazí jako • operaci po obnovení úlohy, které všechny nové synchronizace a bodů obnovení bude uložen v moderní úložiště záloh.
• Staré body obnovení se vyřazují, protože platnost a nakonec uvolněte místo na disku.
• Po všechny starší verze svazky jsou odstraněny z původní úložiště, disk může odebrat z Azure backup a v systému.
• Proveďte zálohu databáze dpmdb Azure.

Část 2:-důležité položky > Nový server se musí mít stejný název jako původní server Azure Backup. Název nový server Azure backup nelze změnit, pokud chcete zachovat pomocí staré fondu úložiště a databáze aplikace DPMDB bodů obnovení - musí mít zálohu DPMDB ji budou muset obnovit

1) Vypnutí původní Azure zálohování serveru nebo trvat vypnout drátové síti.
2) Resetovat účet počítače ve službě active directory.
3) Nainstalujte Server 2016 na nový počítač s názvem stejný název počítače jako původní server Azure Backup.
4) Připojení k doméně
5) Nainstalujte server Azure Backup V2 (disků fondu úložiště aplikace DPM přesunout z původní server a import)
6) Obnovte DPMDB prováděné od konce část 2
7) Připojení úložiště z původního zálohování serveru na nový server.
8) Z obnovení SQL databáze DPMDB
9) Z příkazového řádku správce na nový disk cd server Microsoft Azure Backup nainstalujte umístění a složky koše

Příklad cesty: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
zálohování do Azure, spusťte příkaz DPMSYNC-SYNC

10) Spusťte synchronizaci DPMSYNC-SYNC Poznámka: Pokud jste přidali nové disky do fondu úložiště DPM místo přesunutím staré, spusťte příkaz DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Nové rutiny prostředí PowerShell v v2

Při instalaci serveru Azure Backup v2 jsou k dispozici dvě nové rutiny: 
* [DPMRecoveryPoint připojení](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Další postup

Zjistěte, jak připravit server nebo začít chránit zatížení:
- [Příprava úlohy zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru zálohovat VMware server](backup-azure-backup-server-vmware.md)
- [Použít zálohování serveru k zálohování systému SQL Server](backup-azure-sql-mabs.md)
- [Moderní úložiště zálohování pomocí zálohování serveru](backup-mabs-add-storage.md)

