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
ms.openlocfilehash: a458a46f3775a593f369d5acb967fc90d61efde8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628337"
---
# <a name="install-azure-backup-server-v2"></a>Instalace Azure Backup Server v2

Azure Backup serveru k ochraně virtuálních počítačů (VM), úlohy, soubory a složky a další. Azure Backup serveru v2 vychází v1 Azure Backup serveru a nabízí nové funkce, které nejsou k dispozici ve v1. Porovnání v1 a v2 funkcí najdete v tématu [systém ochrany Azure Backup serveru](backup-mabs-protection-matrix.md). 

Další funkce ve verzi v2 zálohování serveru se upgrade z verze 1 zálohování serveru. V1 zálohování serveru ale není předpoklady pro instalaci serveru Backup v2. Pokud chcete upgradovat z verze zálohy serveru v1 na v2 zálohování serveru, nainstalujte na server ochrany zálohování serveru v2 zálohování serveru. Existující nastavení zálohy serveru nijak nezmění.

Zálohování serveru v2 můžete nainstalovat na Windows Server 2016 nebo Windows Server 2012 R2. Abyste mohli využívat nové funkce, jako je systém System Center 2016 Data Protection Manager moderní úložiště záloh, je nutné nainstalovat v2 zálohování serveru ve Windows serveru 2016. Před upgradováním na nebo instalace zálohování serveru v2, přečtěte si informace o [požadavky na instalaci](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Azure Backup serveru má stejný kód základní jako System Center Data Protection Manager. Zálohování serveru v1 je ekvivalentní k Data Protection Manager 2012 R2. Zálohování serveru v2 je ekvivalentní k Data Protection Manager 2016. Tento článek odkazuje příležitostně dokumentace k Data Protection Manager.
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

  ![Nastavení instalačním programem – spuštěním instalačního programu](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. V průvodci Microsoft Azure Backup serveru v části **nainstalovat**vyberte **Microsoft Azure Backup serveru**.

  ![Nastavení instalačním programem – vyberte instalace](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

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

8. Na **nastavení instalace** stránce, proveďte požadované změny do umístění, kde je nainstalován Server pro zálohování nebo pro odkládací umístění. Vyberte **Další**.

  ![Instalační program Instalační program – stránka nastavení instalace](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Chcete-li dokončit Průvodce instalací, vyberte **Dokončit**.

  ![Instalační program Instalační program – dokončení](./media/backup-mabs-upgrade-to-v2/run-setup.png)

## <a name="add-storage-for-modern-backup-storage"></a>Přidání úložiště pro moderní úložiště záloh

Ke zvýšení efektivity úložiště pro zálohování, zálohování serveru v2 přidává podporu pro svazky. Zálohování serveru v1 a v2 zálohování serveru podpora disků.

### <a name="add-volumes-and-disks"></a>Přidání svazků a disků

Pokud spustíte v2 zálohování serveru ve Windows serveru 2016, můžete k ukládání dat záloh svazky. Svazky nabízejí úspory úložiště a rychlejší zálohování. Protože svazky jsou nové pro zálohování serveru, musíte je přidat. 

Při přidání svazku do Backup Server, můžete dát svazku popisný název. Vyberte **popisný název** sloupec budete chtít název svazku. V případě potřeby můžete později změnit název. Prostředí PowerShell můžete použít také k přidání nebo změně popisných názvů svazků.

Přidání svazku v konzole pro správu:

1. V konzole správce zálohování serveru Azure vyberte **správu** > **diskové úložiště** > **přidat**.

  ![Otevřete průvodce přidat diskové úložiště](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

  Otevře se průvodce přidat diskové úložiště.

2. Na **přidat diskové úložiště** stránku, **dostupné svazky** , vyberte svazek a potom vyberte **přidat**.

3. V **vybrané svazky** , zadejte popisný název svazku a pak vyberte **OK**.

  ![Průvodce přidáním úložiště disku – Přidání svazku](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Pokud chcete přidat disk, disk musí patřit do skupiny ochrany, které mají starší verzi úložiště. Tyto disky můžete použít pouze pro tyto skupiny ochrany. Pokud Backup Server nemá zdroje, které mají starší verzí ochrany, disk není uvedený.

  Další informace o přidávání disků najdete v tématu [přidejte disky do starší verze úložiště zvětšila](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Disk není možné udělit popisný název.


### <a name="assign-workloads-to-volumes"></a>Přiřazení úloh ke svazkům

V zálohování serveru zadejte úlohy, které jsou jednotlivým svazkům přiřazené. Můžete například nastavit nákladné svazky, které podporují vysoký počet vstupně výstupní operace za sekundu (IOPS) k uložení jenom úlohy vyžadující častá zálohování, vysoký počet. Příkladem je SQL Server s transakčními protokoly.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

K aktualizaci vlastností svazku ve fondu úložiště do zálohování serveru, použijte rutinu prostředí PowerShell **Update-DPMDiskStorage**.

Syntaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [<CommonParameters>]
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

5. Na **vybrat členy skupiny** stránku, **Dostupní členové** pole z členů jsou uvedeny agenty ochrany. V tomto příkladu vyberte svazky D:\ a E:\, a přidejte je do **Vybraní členové**. Vyberte **Další**.

  ![Stránka pro členy vybrat skupinu](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na **vybrat způsob ochrany dat** stránky, zadejte **název skupiny ochrany**, vyberte způsob ochrany a pak vyberte **Další**. Pokud chcete krátkodobou ochranu, vyberte **disku** zálohování metody.

  ![Vyberte způsob ochrany dat stránku](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na **zadat krátkodobé cíle** vyberte podrobnosti o **rozsah uchování** a **četnost synchronizací**. Pak vyberte **Next** (Další). Volitelně můžete změnit plán, kdy jsou pořizovány body obnovení, vyberte **změnit**.

  ![Zadat krátkodobé cíle stránky](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na **Kontrola přidělení diskového úložiště** stránky, zkontrolujte podrobnosti o zdrojích dat, který jste vybrali velikost zdroje dat, včetně hodnot pro místo, které se mají zřídit a cílový svazek pro uložení.

  ![Stránka Kontrola přidělení diskového úložiště](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Svazky úložiště jsou na základě úlohy přidělené svazku (nastavení s použitím prostředí PowerShell) a dostupné úložiště. Svazky úložiště můžete změnit výběrem jiných svazků v rozevírací nabídce. Pokud změníte hodnotu **cílové úložiště**, hodnota **dostupné diskové úložiště** dynamicky měnit podle hodnoty v rámci **volného místa** a ** Nevytížené místo**.

  Pokud zdroje dat rozrůstají podle plánu, hodnota **Nevytíženého místa** sloupec v **dostupné diskové úložiště** odráží velikost dalšího úložiště, který je nezbytný. Tuto hodnotu použijte, abyste mohli naplánovat požadavky na ukládání pro plynulého zálohování. Pokud je hodnota nula, nejsou v předvídatelné budoucnosti žádné potenciální problémy s úložištěm. Pokud je hodnota jiné číslo než nula, nemáte přidělené dostatečné úložiště (podle vašich zásad ochrany a velikosti dat chráněných členů).

  ![Nevytížené diskové úložiště](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

  Dokončete vytváření skupiny ochrany, dokončete průvodce.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrace staršího úložiště na moderní úložiště záloh
Po upgradu na v2 zálohování serveru a pak upgradovat operační systém na Windows serveru 2016 se aktualizace skupin ochrany na používání moderního úložiště záloh. Ve výchozím nastavení se nezmění skupin ochrany. Nadále fungovat, jak byl zpočátku nastavené. 

Aktualizace skupin ochrany na používání moderního úložiště záloh je volitelná. Aktualizovat skupinu ochrany, zastavte ochranu všech zdrojů dat pomocí možnosti zachovat data. Pak přidejte zdroje dat do nové skupiny ochrany.

1. V System Center 2016 konzole správce aplikace DPM, vyberte **ochrany** funkce. V **člena skupiny ochrany** seznamu, klikněte pravým tlačítkem na člena a pak vyberte **zastavit ochranu člena**.

  ![Zastavit ochranu člena](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. V **odebrat ze skupiny** dialogové okno pole, zkontrolujte, využité místo na disku a dostupné volné místo pro fond úložiště. Ve výchozím nastavení je nechat body obnovení na disku a umožnit jim vypršení platnosti podle přidružené zásady uchovávání. Vyberte **OK**.

  Pokud chcete využité místo na disku ihned vrátit do volného fondu úložiště, vyberte **odstranit repliku na disku** zaškrtávací políčko, chcete-li odstranit zálohovaná data (a body obnovení) přidružené k tohoto člena.

  ![Odebrat ze skupiny, dialogové okno](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Vytvořte skupinu ochrany, která používá moderní úložiště záloh. Zahrnout ní nechráněné zdroje dat.


## <a name="add-disks-to-increase-legacy-storage"></a>Přidání disků k navýšení starší verze úložiště

Pokud chcete používat starší verzi úložiště pomocí zálohování serveru, můžete potřebovat přidat disky do starší verze úložiště zvětšila. 

Chcete-li přidat diskové úložiště:

1. V System Center 2016 konzole správce aplikace DPM, vyberte **správu** > **diskové úložiště** > **přidat**.

  ![Přidat dialog diskové úložiště](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. V **přidat diskové úložiště** dialogu **přidat disky**.

3. V seznamu dostupných disků vyberte disky, které chcete přidat. Vyberte **přidat**a pak vyberte **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Aktualizace agenta ochrany aplikace Data Protection Manager

Zálohování serveru System Center Data Protection Manager protection agent používá pro aktualizace. Pokud provádíte upgrade agenta ochrany, které není připojené k síti, nelze použít konzolu Správce Data Protection Manager k dokončení upgradu připojeného agenta. Je nutné upgradovat agenta ochrany v prostředí neaktivní domény. Dokud klientský počítač je připojený k síti, konzole správce Data Protection Manager ukazuje, že aktualizace agenta ochrany čeká na vyřízení.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače, které jsou připojené a pro klientské počítače, které nejsou připojené.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Aktualizace agenta ochrany pro připojené klientské počítače

1. V konzole správce zálohování serveru vyberte **správu** > **agentů**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenty ochrany.

  > [!NOTE]
  > **Aktualizací agenta** sloupec znamená, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce je dostupná, jenom když je vybrán chráněný počítač a jsou k dispozici aktualizace.

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, **akce** vyberte **aktualizace**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Aktualizace agenta ochrany na klientském počítači, který není připojen

1. V konzole správce zálohování serveru vyberte **správu** > **agentů**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenty ochrany.

  > [!NOTE]
  > **Aktualizací agenta** sloupec znamená, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce není k dispozici, když je vybrán chráněný počítač, pokud jsou k dispozici aktualizace.

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, vyberte **aktualizace**.

4. Abyste klientskému počítači, který není připojen k síti, dokud je počítač připojen k síti **stav agenta** sloupci se zobrazuje stav **aktualizace čeká na vyřízení**.

  Když klientský počítač je připojený k síti, **aktualizací agenta** sloupec pro klientský počítač zobrazuje stav **aktualizace**.
  
### <a name="move-legacy-protection-groups-from-the-old-version-and-sync-the-new-version-with-azure"></a>Přesunutí skupin starší verzí ochrany z předchozí verzi aplikace a synchronizovat nová verze s využitím Azure

Když se Azure Backup serveru a operační systém jsou obě aktualizuje, budete připraveni k ochraně nové zdroje dat s využitím moderního úložiště záloh. Zdroje dat, které jsou již chráněny i nadále ochranu, protože se nacházely v Azure Backup serveru (starší verze). Všechny nové skupiny ochrany používání moderního úložiště záloh.

K migraci zdrojů dat ze starší režim ochrany na moderní úložiště záloh:

1.  Přidáte nové svazky do fondu úložiště aplikace Data Protection Manager. Můžete také přiřadit popisný název a vybrat značky datového zdroje.

2. Pro každý zdroj dat, který je v režimu starší verze zastavte ochranu datových zdrojů. Vyberte **uchovat chráněná Data**.  Tato funkce umožňuje obnovení starých bodů obnovení po migraci.

3. Vytvořte novou skupinu ochrany. Vyberte zdroje dat, které chcete uložit pomocí nového formátu.

  Aplikace Data Protection Manager ukládá jejich kopii repliky ze starší verze úložiště záloh ve svazku moderního úložiště záloh místně.
    > [!NOTE] 
    > Vytvoření kopie se zobrazí jako operace po obnovení úlohy.

  Všechny nové synchronizace a body obnovení jsou pak uloženy v moderní úložiště záloh.

  Staré body obnovení jsou vyřazeny si, jak vypršení jejich platnosti. Jak se odstraní staré body obnovení, je uvolnit místo na disku.

  Pokud všechny starší verze svazky budou odstraněny z původní úložiště, můžete odebrat disk ze zálohy Azure. Pak můžete odebrat disk ze systému.

4. Zazálohujte databázi aplikace Data Protection Manager.

  > [!IMPORTANT]
  > - Nový název serveru musí být stejný název jako původní instanci Azure Backup serveru. Název nové instance Azure Backup serveru nelze změnit, pokud chcete zachovat body obnovení pomocí předchozího fondu úložiště a databáze aplikace Data Protection Manager.
  > - Musíte mít zálohu databáze aplikace Data Protection Manager. Budete potřebovat k obnovení databáze.

5. Vypněte původní instanci Azure Backup serveru nebo serveru převést do režimu offline.

6. Obnovit účet počítače ve službě Active Directory.

7. Nainstalujte Windows Server 2016 na nový počítač. Pro název serveru použijte stejný název počítače jako původní instanci Azure Backup serveru.

8. Připojení k doméně.

9. Instalace Azure Backup Server v2. (Disky fondu úložiště aplikace Data Protection Manager odebrat starý server a importujte je do nového serveru.)

10. Obnovení databáze aplikace Data Protection Manager, který jste vytvořili v kroku 4.

11. Připojte úložiště z původního zálohování serveru na nový server.

12. V systému SQL Server obnovení databáze aplikace Data Protection Manager.

13. Na příkazovém řádku správce na novém serveru, použijte `cd` přejděte do složky instalace Microsoft Azure Backup umístění a bin.  

  Příklad:  
  C:\Windows\System32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\ do služby Azure Backup

14. Spusťte `DPMSYNC -SYNC`.
  
  > [!NOTE]
  > Pokud jste přidali *nové* disky do fondu úložiště aplikace Data Protection Manager, namísto přesouvání staré, spusťte `DPMSYNC -Reallocatereplica`.

## <a name="new-powershell-cmdlets-in-backup-server-v2"></a>Nové rutiny Powershellu ve verzi v2 zálohování serveru

Při instalaci Azure Backup serveru v2 jsou k dispozici dvě nové rutiny: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Další postup

Další informace o přípravě serveru nebo začít chránit úlohy:
- [Příprava úloh zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Pomocí zálohování serveru k zálohování SQL serveru](backup-azure-sql-mabs.md)
- [Moderní úložiště záloh pomocí zálohování serveru](backup-mabs-add-storage.md)

