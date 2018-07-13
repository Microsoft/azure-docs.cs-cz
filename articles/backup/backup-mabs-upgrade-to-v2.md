---
title: Instalace Azure Backup Server v2
description: Azure Backup Server v2 nabízí rozšířené možnosti zálohování pro ochranu virtuálních počítačů, souborů a složek, úloh a další. Zjistěte, jak nainstalovat nebo upgradovat na Azure Backup serveru v2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: fdf69003566f704354a17335b1f46fc3077aedbc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598386"
---
# <a name="install-azure-backup-server-v2"></a>Instalace Azure Backup Server v2

Azure Backup serveru k ochraně virtuálních počítačů (VM), úlohy, soubory a složky a další. Azure Backup serveru v2 vychází v1 Azure Backup serveru a nabízí nové funkce, které nejsou k dispozici ve v1. Porovnání funkcí mezi v1 a v2 najdete v tématu [systém ochrany Azure Backup serveru](backup-mabs-protection-matrix.md). 

Další funkce ve verzi v2 zálohování serveru se upgrade z verze 1 zálohování serveru. V1 zálohování serveru ale není předpoklady pro instalaci serveru Backup v2. Pokud chcete upgradovat z verze zálohy serveru v1 na v2 zálohování serveru, nainstalujte na server ochrany zálohování serveru v2 zálohování serveru. Existující nastavení zálohy serveru nijak nezmění.

Zálohování serveru v2 můžete nainstalovat na Windows Server 2012 R2 nebo Windows Server 2016. Abyste mohli využívat nové funkce, jako je systém System Center 2016 Data Protection Manager moderní úložiště záloh, je nutné nainstalovat v2 zálohování serveru ve Windows serveru 2016. Před upgradováním na nebo instalace zálohování serveru v2, přečtěte si informace o [požadavky na instalaci](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Azure Backup serveru má stejný kód základní jako System Center Data Protection Manager. Zálohování serveru v1 je ekvivalentní k Data Protection Manager 2012 R2 a Backup Server v2 je ekvivalentní k Data Protection Manager 2016. Tento článek odkazuje příležitostně dokumentace k Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Upgrade záložního serveru v2
Chcete-li upgradovat Backup Server v1 na v2 zálohování serveru, ujistěte se, jestli že instalace obsahuje potřebné aktualizace:

- [Aktualizovat agenty ochrany](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) na chráněných serverech.
- Upgrade Windows serveru 2012 R2 na Windows Server 2016.
- Upgradujte vzdáleného správce služby Azure Backup serveru na všech provozních serverech.
- Ujistěte se, že jsou zálohy nastaveny na pokračovat bez restartování provozního serveru.


### <a name="upgrade-steps-for-backup-server-v2"></a>Postup upgradu pro Backup Server v2

1. V Centru pro stahování [stáhněte instalační program upgradu](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Po extrahování Průvodce instalací, ujistěte se, že **spusťte setup.exe** je vybrané a pak vyberte **Dokončit**.

  ![Instalační program – Instalační program spusťte instalaci](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. V průvodci Microsoft Azure Backup serveru v části **nainstalovat**vyberte **Microsoft Azure Backup serveru**.

   ![Instalační program Instalační program – vyberte možnost instalace](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Na **úvodní** stránky, přečtěte si upozornění a potom vyberte **Další**.

   ![Instalační program Instalační program – úvodní stránka](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. Průvodce instalací provede kontroly splnění podmínek, abyste měli jistotu, že můžete upgradovat prostředí. Na **kontrol požadovaných součástí** stránce **zkontrolujte**.

   ![Instalační program Instalační program – stránka kontrol požadovaných součástí](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Vaše prostředí musí úspěšně projít kontroly splnění podmínek. Pokud vaše prostředí není úspěšný kontroly, mějte na paměti tyto problémy a opravte je. Vyberte **zkontrolovat znovu**. Po úspěšném absolvování kontroly splnění podmínek, vyberte **Další**.

  ![Instalační program Instalační program – tlačítko Zkontrolovat znovu](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Na **nastavení SQL** stránky, vyberte odpovídající možnost pro instalaci SQL a pak vyberte **zkontrolovat a nainstalovat**.

   ![Instalační program Instalační program – stránka nastavení SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Kontroly může trvat několik minut. Po dokončení kontroly vyberte **Další**.

   ![Instalační program Instalační program – SQL zkontrolujte nastavení a tlačítko nainstalovat](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. Na **nastavení instalace** stránce, proveďte požadované změny do umístění, kde je nainstalován Server pro zálohování nebo pro odkládací umístění. Vyberte **Next** (Další).

  ![Instalační program Instalační program – stránka nastavení instalace](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Chcete-li dokončit Průvodce instalací, vyberte **Dokončit**.

  ![Instalační program Instalační program – dokončení](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Přidání úložiště pro moderní úložiště záloh

Ke zvýšení efektivity úložiště pro zálohování, zálohování serveru v2 přidává podporu pro svazky. Jako Backup Server v1 v2 zálohovat Server podporuje disky.

### <a name="add-volumes-and-disks"></a>Přidání svazků a disků
Pokud spustíte v2 zálohování serveru ve Windows serveru 2016, můžete k ukládání dat záloh svazky. Svazky nabízejí úspory úložiště a rychlejší zálohování. Protože svazky jsou nové pro zálohování serveru, musíte je přidat. 

Při přidání svazku do Backup Server, můžete dát svazku popisný název. Klikněte na tlačítko **popisný název** sloupec budete chtít název svazku. V případě potřeby můžete později změnit název. Prostředí PowerShell můžete použít také k přidání nebo změně popisných názvů svazků.

Přidání svazku v konzole pro správu:

1. V konzole správce zálohování serveru Azure vyberte **správu** > **diskové úložiště** > **přidat**.

    ![Otevřete průvodce přidat diskové úložiště](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Otevře se průvodce přidat diskové úložiště.

2. Na **přidat diskové úložiště** stránku, **dostupné svazky** , vyberte svazek a potom vyberte **přidat**.
3. V **vybrané svazky** , zadejte popisný název svazku a pak vyberte **OK**.

      ![Průvodce přidáním úložiště disku – Přidání svazku](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Pokud chcete přidat disk, disk musí patřit do skupiny ochrany, které mají starší verzi úložiště. Tyto disky jde použít jenom pro tyto skupiny ochrany. Pokud Backup Server nemá zdroje, které mají starší verzí ochrany, disk není uvedený.

  Další informace o přidávání disků najdete v tématu [přidání disků k navýšení starší verze úložiště](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Disk není možné udělit popisný název.


### <a name="assign-workloads-to-volumes"></a>Přiřazení úloh ke svazkům

V zálohování serveru zadejte úlohy, které jsou jednotlivým svazkům přiřazené. Můžete například nastavit nákladné svazky, které podporují vysoký počet vstupně výstupní operace za sekundu (IOPS) k uložení jenom úlohy vyžadující častá zálohování, vysoký počet. Příkladem je SQL Server s transakčními protokoly.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

K aktualizaci vlastností svazku ve fondu úložiště do zálohování serveru, použijte rutinu Powershellu Update-DPMDiskStorage.

Syntaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

V uživatelském rozhraní se projeví všechny změny provedené s použitím prostředí PowerShell.


## <a name="protect-data-sources"></a>Ochranu zdrojů dat
Pokud chcete začít, ochrana zdrojů dat, vytvořte skupinu ochrany. Následující kroky zvýrazněte změny nebo doplnění do Průvodce vytvořením nové skupiny ochrany.

Chcete-li vytvořit skupinu ochrany:

1. V konzole správce zálohování serveru vyberte **ochrany**.

2. Na pásu karet vyberte **nový**.

    Otevře se Průvodce vytvořením nové skupiny ochrany.

  ![Průvodce vytvořením nové skupiny ochrany](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Na **úvodní** stránce vyberte **Další**.
4. Na **vybrat typ skupiny ochrany** stránky, vyberte typ skupiny ochrany, kterou chcete vytvořit a pak vyberte **Další**.

  ![Typ stránky vyberte skupinu ochrany](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na **vybrat členy skupiny** stránku, **Dostupní členové** podokno, členové s ochranou, jsou uvedeny agentů. V tomto příkladu vyberte svazky D:\ a E:\ a přidat je do **Vybraní členové** podokně. Vyberte **Next** (Další).

  ![Stránka pro členy vybrat skupinu](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na **vybrat způsob ochrany dat** stránky, zadejte **název skupiny ochrany**, vyberte způsob ochrany a pak vyberte **Další**. Pokud chcete krátkodobou ochranu, musíte vybrat **disku** zálohování metody.

  ![Vyberte způsob ochrany dat stránku](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na **zadat krátkodobé cíle** vyberte podrobnosti o **rozsah uchování** a **četnost synchronizací**. Pak vyberte **Next** (Další). Volitelně můžete změnit plán, kdy jsou pořizovány body obnovení, vyberte **změnit**.

  ![Zadat krátkodobé cíle stránky](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na **Kontrola přidělení diskového úložiště** stránky, zkontrolujte podrobnosti o zdrojích dat jste vybrali, jejich velikost a hodnoty pro místo, které se mají zřídit a cílový svazek pro uložení.

  ![Stránka Kontrola přidělení diskového úložiště](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Svazky úložiště jsou na základě úlohy přidělené svazku (nastavení s použitím prostředí PowerShell) a dostupné úložiště. Svazky úložiště můžete změnit výběrem jiných svazků v rozevírací nabídce. Pokud změníte hodnotu **cílové úložiště**, hodnota **dostupné diskové úložiště** dynamicky měnit podle hodnoty v rámci **volného místa** a  **Nevytížené místo**.

  Pokud zdroje dat rozrůstají podle plánu, hodnota **Nevytíženého místa** sloupec v **dostupné diskové úložiště** odráží velikost dalšího úložiště, který je nezbytný. Tuto hodnotu použijte, abyste mohli naplánovat požadavky na ukládání pro plynulého zálohování. Pokud je hodnota nula, nejsou v předvídatelné budoucnosti žádné potenciální problémy s úložištěm. Pokud je hodnota jiné číslo než nula, nemáte přidělené dostatečné úložiště (podle vašich zásad ochrany a velikosti dat chráněných členů).

  ![Nevytížené diskové úložiště](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Dokončete vytváření skupiny ochrany, dokončete průvodce.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrace staršího úložiště na moderní úložiště záloh
Po upgradu na Backup Server v2 nebo upgrade operačního systému na Windows serveru 2016 se aktualizace skupin ochrany na používání moderního úložiště záloh. Ve výchozím nastavení se nezmění skupin ochrany. Nadále fungovat, jak byl zpočátku nastavené. 

Aktualizace skupin ochrany na používání moderního úložiště záloh je volitelná. Aktualizovat skupinu ochrany, zastavte ochranu všech zdrojů dat pomocí možnosti zachovat data. Pak přidejte zdroje dat do nové skupiny ochrany.

1. V konzole pro správu, vyberte **ochrany** funkce. V **člena skupiny ochrany** seznamu, klikněte pravým tlačítkem na člena a pak vyberte **zastavit ochranu člena**.

  ![Zastavit ochranu člena](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. V **odebrat ze skupiny** dialogové okno pole, zkontrolujte, využité místo na disku a dostupné volné místo pro fond úložiště. Ve výchozím nastavení je nechat body obnovení na disku a umožnit jim vypršení platnosti podle přidružené zásady uchovávání. Klikněte na **OK**.

  Pokud chcete využité místo na disku ihned vrátit do volného fondu úložiště, vyberte **odstranit repliku na disku** zaškrtávací políčko, chcete-li odstranit zálohovaná data (a body obnovení) přidružené k tohoto člena.

  ![Odebrat ze skupiny, dialogové okno](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Vytvořte skupinu ochrany, která používá moderní úložiště záloh. Zahrnout ní nechráněné zdroje dat.


## <a name="add-disks-to-increase-legacy-storage"></a>Přidání disků k navýšení starší verze úložiště

Pokud chcete používat starší verzi úložiště pomocí zálohování serveru, můžete potřebovat přidat disky do starší verze úložiště zvětšila. 

Chcete-li přidat diskové úložiště:

1. V konzole pro správu, vyberte **správu** > **diskové úložiště** > **přidat**.

    ![Přidat dialog diskové úložiště](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. V **přidat diskové úložiště** dialogového okna, vyberte **přidat disky**.

5. V seznamu dostupných disků, vyberte disky, které chcete přidat, vyberte **přidat**a pak vyberte **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Aktualizace agenta ochrany aplikace Data Protection Manager

Zálohování serveru System Center Data Protection Manager protection agent používá pro aktualizace. Pokud provádíte upgrade agenta ochrany, které není připojené k síti, nelze použít konzolu Správce Data Protection Manager k dokončení upgradu připojeného agenta. Je nutné upgradovat agenta ochrany v prostředí neaktivní domény. Dokud klientský počítač je připojený k síti, konzole správce Data Protection Manager ukazuje, že aktualizace agenta ochrany čeká na vyřízení.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače, které jsou připojené a klientské počítače, které nejsou připojené.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Aktualizace agenta ochrany pro připojené klientské počítače

1. V konzole správce zálohování serveru vyberte **správu** > **agentů**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenty ochrany.

  > [!NOTE]
  > **Aktualizací agenta** sloupec znamená, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce je dostupná, jenom když je vybrán chráněný počítač a jsou k dispozici aktualizace.
  >
  >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, **akce** vyberte **aktualizace**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Aktualizace agenta ochrany na klientském počítači, který není připojen

1. V konzole správce zálohování serveru vyberte **správu** > **agentů**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenty ochrany.

  > [!NOTE]
   > **Aktualizací agenta** sloupec znamená, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce není dostupná, když je vybrán chráněný počítač, pokud jsou k dispozici aktualizace.
  >
  >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, vyberte **aktualizace**.

4. Abyste klientskému počítači, který není připojen k síti, dokud je počítač připojen k síti **stav agenta** sloupci se zobrazuje stav **aktualizace čeká na vyřízení**.

  Až klientský počítač je připojený k síti, **aktualizací agenta** sloupec pro klientský počítač zobrazuje stav **aktualizace**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Přesuňte starší verze skupin ochrany z předchozí verzi aplikace a synchronizovat nová verze s využitím Azure

Jakmile Azure Backup serveru a operační systém jsou obě aktualizované, jste připraveni k ochraně nové zdroje dat pomocí moderního úložiště záloh. Ale už chráněných zdrojů dat budou i nadále být chráněné ve starším způsobem, jako kdyby byly v Azure Backup Server, ale všechny novou ochranu, se používání moderního úložiště záloh.

Následující kroky se migrace ze starší režim ochrany zdroje dat do moderního úložiště záloh.

• Přidat nové svazky fondu úložiště DPM a v případě potřeby přiřaďte značky popisné názvy a datové zdroje.
• Pro každý zdroj dat, který je v režimu starší verze, zastavte ochranu zdroje dat a "Uchovat chráněná Data".  To vám umožní obnovení starých bodů obnovení po migraci.

• Vytvořit nové PG a vyberte zdroje dat, které mají být uloženy pomocí nového formátu.
• Aplikace DPM provede kopie repliky ze starší verze úložiště záloh na svazek moderního úložiště záloh místně.
Poznámka: To se projeví jako • operace po obnovení úlohy, které všechny nové synchronizace a bodů obnovení bude uložen v moderní úložiště záloh.
• Staré body obnovení se vyřazují si platnost a nakonec uvolněte místo na disku.
• Odstraněný všechny starší verze svazky z původní úložiště, disk lze odebrat z Azure backup a systému.
• Proveďte zálohu databáze dpmdb Azure.

Část 2:-důležité položky > Nový server bude muset mít stejný název jako původní server Azure Backup. Název nového serveru Azure backup nelze změnit, pokud chcete používat původní úložiště fondu a databáze aplikace DPMDB zachovat body obnovení – musí mít zálohování databáze aplikace dpmdb, protože ji budete potřebovat obnovit

1) Vypnutí původní Azure backup server nebo trvat vypnutí přenosu.
2) Obnovit účet počítače ve službě active directory.
3) Nainstalujte Server 2016 na nový počítač a pojmenujte ho stejný název počítače jako původní server Azure Backup.
4) Připojení k doméně
5) Instalace Azure Backup serveru V2 (disky fondu úložiště DPM přesunout z původní server a import)
6) Obnovení databáze aplikace DPMDB z konce roku 2. část
7) Připojte úložiště z původního zálohování serveru na nový server.
8) Obnovení SQL databázi DPMDB
9) Z příkazového řádku správce na nový disk cd serveru do služby Microsoft Azure Backup nainstalujte umístění a složky bin

Příklad cesty: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
do Azure zálohovat spusťte DPMSYNC-SYNC

10) Spusťte nástroj DPMSYNC-SYNC Poznámka: Pokud nové disky, které jste přidali do fondu úložiště DPM namísto přesouvání staré, pak spusťte DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Nové rutiny Powershellu ve verzi v2

Při instalaci Azure Backup serveru v2 jsou k dispozici dvě nové rutiny: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Další postup

Další informace o přípravě serveru nebo začít chránit úlohy:
- [Příprava úloh zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Pomocí zálohování serveru k zálohování SQL serveru](backup-azure-sql-mabs.md)
- [Moderní úložiště záloh pomocí zálohování serveru](backup-mabs-add-storage.md)

