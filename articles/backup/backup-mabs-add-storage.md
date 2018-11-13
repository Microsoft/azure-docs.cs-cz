---
title: Pomocí moderního úložiště záloh Azure Backup serveru
description: Informace o nových funkcích v Azure Backup serveru. Tento článek popisuje, jak upgradovat Backup Server instalaci.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: markgal; adigan; kasinh
ms.openlocfilehash: daa7d6ee13cf55703b71bea321e65d2518a59979
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578514"
---
# <a name="add-storage-to-azure-backup-server"></a>Přidání úložiště do Azure Backup Serveru

Azure Backup Server V2 a později se dodává s systému System Center 2016 Data Protection Manager moderní úložiště záloh. Moderní úložiště záloh nabízí úspory úložiště o 50 procent, zálohování, které jsou úložiště odpovídala trojnásobku rychlejší a efektivnější. Nabízí také úložiště zohledňující úlohy.

> [!NOTE]
> Na používání moderního úložiště záloh, musíte spustit zálohování serveru V2 nebo V3 na Windows Server 2016 nebo V3 na Windows Server 2019.
> Pokud spustíte zálohování serveru V2 na starší verzi Windows serveru, Azure Backup serveru nelze využít moderní úložiště záloh. Místo toho chrání pracovní postupy, jak to funguje se službou Backup Server V1. Další informace najdete v tématu Zálohování serveru verze [systém ochrany](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server"></a>Svazky v zálohování serveru

Zálohování serveru V2 nebo později přijímá svazky úložiště. Pokud chcete přidat svazek, zálohování serveru svazek naformátuje na pro odolný systém souborů (ReFS), který vyžaduje moderní úložiště záloh. Chcete přidat svazek a rozbalte ho později, pokud je potřeba, doporučujeme použít tento pracovní postup:

1.  Nastavení zálohování serveru na virtuálním počítači.
2.  Vytvoření svazku na virtuálním disku ve fondu úložiště:
    1.  Přidejte disk do fondu úložiště a vytvořte virtuální disk s jednoduchým rozložením.
    2.  Přidejte všechny další disky a rozšiřte virtuální disk.
    3.  Vytvořte na virtuálním disku svazky.
3.  Přidáte svazky do zálohování serveru.
4.  Nakonfigurujte úložiště zohledňující úlohy.

## <a name="create-a-volume-for-modern-backup-storage"></a>Vytvoření svazku pro moderní úložiště záloh

Pomocí zálohování serveru verze 2 nebo novější pomocí svazků úložiště disku vám pomůže udržet kontrolu nad úložištěm. Svazek může být jeden disk. Pokud budete chtít v budoucnu rozšířit úložiště, ale vytvořte svazek z disku vytvořené pomocí prostorů úložiště. To může pomoct, pokud chcete rozšířit svazek pro úložiště záloh. Tato část nabízí osvědčené postupy pro vytvoření svazku s tímto nastavením.

1. Ve Správci serveru vyberte **Souborová služba a služba úložiště** > **svazky** > **fondy úložiště**. V části **fyzické disky**vyberte **nový fond úložiště**.

    ![Vytvořit nový fond úložiště](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. V **úlohy** rozevíracího seznamu vyberte **nový virtuální Disk**.

    ![Přidání virtuálního disku](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Vyberte fond úložiště a pak vyberte **přidat fyzický Disk**.

    ![Přidat fyzický disk](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Vyberte fyzický disk a pak vyberte **rozšiřte virtuální Disk**.

    ![Rozšiřte virtuální disk.](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Vyberte virtuální disk a potom vyberte **nový svazek**.

    ![Vytvořte nový svazek](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. V **vyberte server a disk** dialogového okna, vyberte server a nový disk. Pak vyberte **Next** (Další).

    ![Vyberte server a disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Přidání svazků do zálohování serveru diskové úložiště

Chcete přidat svazek k zálohování serveru v **správu** podokně znovu prohledat úložiště a pak vyberte **přidat**. Zobrazí se seznam všech svazků, které přidají za službu Backup Server. Po dostupné svazky se přidají do seznamu vybraných svazků, můžete přiřadit popisný název, který vám pomůžou spravovat je. Chcete-li formátovat tyto svazky na souborový systém ReFS, Backup Server můžete využívat výhody moderního úložiště záloh, vyberte **OK**.

![Přidejte dostupné svazky](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Nastavení úložiště zohledňující úlohy.

U služby úložiště zohledňující úlohy můžete vybrat svazky, které ukládají přednostně určité druhy úloh. Můžete například nastavit nákladné svazky, které podporují vysoký počet vstupně výstupní operace za sekundu (IOPS), která ukládaly jenom úlohy vyžadující častá zálohování velkého rozsahu. Příkladem je SQL Server s transakčními protokoly. Jiné úlohy, které se zálohují méně často, jako jsou virtuální počítače, můžete zálohovat na svazky s nízkými náklady.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Úložiště zohledňující úlohy můžete nastavit pomocí rutiny Powershellu Update-DPMDiskStorage, která aktualizuje vlastnosti svazku ve fondu úložiště na serveru aplikace Data Protection Manager.

Syntaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Následující snímek obrazovky ukazuje rutiny Update-DPMDiskStorage v okně Powershellu.

![Příkaz Update-DPMDiskStorage v okně prostředí PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Změny provedené s použitím prostředí PowerShell se projeví v konzole pro správu zálohování serveru.

![Disky a svazky v konzole pro správu](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrace staršího úložiště na moderní úložiště záloh
Po upgradu na zálohování serveru V2 nebo upgrade operačního systému na Windows serveru 2016 se aktualizace skupin ochrany na používání moderního úložiště záloh. Ve výchozím nastavení se nezmění skupin ochrany. Nadále fungovat, jak byl zpočátku nastavené.

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

## <a name="next-steps"></a>Další postup
Po instalaci serveru pro zálohování zjistěte, jak připravit váš server, nebo začít chránit úlohy.

- [Příprava úloh zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Pomocí zálohování serveru k zálohování SQL serveru](backup-azure-sql-mabs.md)
