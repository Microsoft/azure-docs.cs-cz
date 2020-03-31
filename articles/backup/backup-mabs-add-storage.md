---
title: Použití moderního úložiště záloh se serverem Azure Backup Server
description: Přečtěte si o nových funkcích v Azure Backup Server. Tento článek popisuje, jak inovovat instalaci zálohovacího serveru.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: c6346d7b0275a00271c1787b378a63b8365edf2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172379"
---
# <a name="add-storage-to-azure-backup-server"></a>Přidání úložiště do Azure Backup Serveru

Azure Backup Server V2 a novější podporuje moderní úložiště zálohování, které nabízí úsporu úložiště 50 procent, zálohy, které jsou třikrát rychlejší a efektivnější úložiště. Nabízí také úložiště s vědomím úlohy.

> [!NOTE]
> Chcete-li používat moderní úložiště záloh, musíte v systému Windows Server 2016 nebo V3 v systému Windows Server 2019 spustit systém Backup Server V2 nebo V3.
> Pokud spustíte Backup Server V2 ve starší verzi systému Windows Server, azure backup server nemůže využít výhod moderního úložiště zálohování. Místo toho chrání úlohy stejně jako u serveru Backup V1. Další informace naleznete v [matici ochrany](backup-mabs-protection-matrix.md)verzí systému Backup Server .
>
> Chcete-li dosáhnout vyššího výkonu zálohování, doporučujeme nasadit MABS v3 s vrstveným úložištěm v systému Windows Server 2019. Postup konfigurace vrstveného úložiště naleznete v článku aplikace DPM "[Nastavit MBS s vrstveným úložištěm](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)".

## <a name="volumes-in-backup-server"></a>Svazky na zálohovacím serveru

Backup Server V2 nebo novější přijímá svazky úložiště. Když přidáte svazek, backup server zformátuje svazek do odolného systému souborů (ReFS), který vyžaduje moderní úložiště záloh. Chcete-li přidat svazek a v případě potřeby jej později rozbalit, doporučujeme použít tento pracovní postup:

1. Nastavte záložní server na virtuálním počítači.
2. Vytvoření svazku na virtuálním disku ve fondu úložiště:
    1. Přidejte disk do fondu úložiště a vytvořte virtuální disk s jednoduchým rozložením.
    2. Přidejte další disky a rozšiřte jej.
    3. Vytvořte svazky na virtuálním disku.
3. Přidejte svazky do zálohovacího serveru.
4. Konfigurace úložiště podporujícího úlohu.

## <a name="create-a-volume-for-modern-backup-storage"></a>Vytvoření svazku pro moderní úložiště záloh

Použití zálohovacího serveru V2 nebo novějšího s svazky jako diskovým úložištěm vám může pomoci udržet kontrolu nad úložištěm. Svazek může být jeden disk. Pokud však chcete v budoucnu rozšířit úložiště, vytvořte svazek z disku vytvořeného pomocí prostorů úložiště. To může pomoci, pokud chcete rozšířit svazek pro úložiště záloh. Tato část nabízí osvědčené postupy pro vytvoření svazku s tímto nastavením.

1. Ve Správci serveru vyberte > **fondy úložiště****svazků** **souborů a úložišť** > . V části **FYZICKÉ DISKY**vyberte **Nový fond úložiště**.

    ![Vytvoření nového fondu úložiště](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. V rozevíracím seznamu **ÚKOLY** vyberte **Nový virtuální disk**.

    ![Přidání virtuálního disku](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Vyberte fond úložiště a pak vyberte **Přidat fyzický disk**.

    ![Přidání fyzického disku](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Vyberte fyzický disk a pak vyberte **rozšířit virtuální disk**.

    ![Rozšíření virtuálního disku](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Vyberte virtuální disk a potom vyberte **Nový svazek**.

    ![Vytvoření nového svazku](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. V **dialogovém** okně Vybrat server a disk vyberte server a nový disk. Potom vyberte **Další**.

    ![Výběr serveru a disku](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Přidání svazků do úložiště disků zálohovacího serveru

> [!NOTE]
>
> - Přidejte do fondu pouze jeden disk, aby se počet sloupců udržel na 1. Potom můžete přidat disky podle potřeby později.
> - Pokud přidáte více disků do fondu úložiště na cestách, počet disků se uloží jako počet sloupců. Po přidání více disků může být pouze násobkem počtu sloupců.

Chcete-li přidat svazek do zálohovacího serveru, proskenujte úložiště v podokně **Správa** a pak vyberte **Přidat**. Zobrazí se seznam všech svazků, které jsou k dispozici pro přidání pro úložiště serveru Backup Server. Po přidání dostupných svazků do seznamu vybraných svazků jim můžete dát popisný název, který vám pomůže je spravovat. Chcete-li tyto svazky naformátovat do systému ReFS, aby mohl server Backup Server využívat výhody moderního úložiště záloh, vyberte **možnost OK**.

![Přidat dostupné svazky](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Nastavení úložiště podporujícího úlohu

Pomocí úložiště s obsluhou pracovního vytížení můžete vybrat svazky, které přednostně ukládají určité druhy úloh. Můžete například nastavit nákladné svazky, které podporují vysoký počet vstupních a výstupních operací za sekundu (IOPS) pro ukládání pouze úloh, které vyžadují časté zálohování na vysoké objemy. Příkladem je SQL Server s protokoly transakcí. Ostatní úlohy, které jsou zálohovány méně často, jako jsou virtuální aplikace, lze zálohovat na nízkonákladové svazky.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Úložiště podporující úlohy můžete nastavit pomocí rutiny Rutina PowerShell Update-DPMDiskStorage, která aktualizuje vlastnosti svazku ve fondu úložiště na zálohovacím serveru Azure.

Syntaxe:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Následující snímek obrazovky ukazuje rutinu Update-DPMDiskStorage v okně Prostředí PowerShell.

![Příkaz Aktualizovat-DPMDiskStorage v okně Prostředí PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Změny provedené pomocí prostředí PowerShell se projeví v konzole správce záložního serveru.

![Disky a svazky v konzole správce](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrace staršího úložiště do moderního úložiště zálohování

Po upgradu na nebo instalaci serveru Backup Server V2 a upgradu operačního systému na systém Windows Server 2016 aktualizujte skupiny ochrany tak, aby používaly moderní úložiště záloh. Ve výchozím nastavení se skupiny ochrany nezmění. Nadále fungují tak, jak byly původně nastaveny.

Aktualizace skupin ochrany na používání moderního úložiště záloh je volitelná. Chcete-li aktualizovat skupinu ochrany, zastavte ochranu všech zdrojů dat pomocí možnosti zachovat data. Potom přidejte zdroje dat do nové skupiny ochrany.

1. V konzole Správce vyberte funkci **Ochrana.** V seznamu **Členové skupiny ochrany** klikněte pravým tlačítkem myši na člena a potom vyberte **příkaz Zastavit ochranu člena**.

   ![Zastavit ochranu člena](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. V dialogovém okně **Odebrat ze skupiny** zkontrolujte využité místo na disku a dostupné volné místo pro fond úložiště. Výchozím postupem je nechat body obnovení na disku a umožnit jim vypršení platnosti podle přidružené zásady uchovávání. Klikněte na tlačítko **OK**.

   Pokud chcete okamžitě vrátit využité místo na disku do fondu volného úložiště, zaškrtněte políčko **Odstranit repliku na disku,** chcete-li odstranit záložní data (a body obnovení) přidružená k tomuto členu.

   ![Dialogové okno Odebrat ze skupiny](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Vytvořte skupinu ochrany, která používá moderní úložiště záloh. Zahrňte nechráněné zdroje dat.

## <a name="add-disks-to-increase-legacy-storage"></a>Přidání disků pro zvýšení staršího úložiště

Pokud chcete používat starší úložiště se zálohovacím serverem, možná budete muset přidat disky, abyste zvýšili starší úložiště.

Přidání diskového úložiště:

1. V konzole Správce vyberte **položku Přidání** > **úložiště disku** > správy **.**

    ![Dialogové okno Přidat diskové úložiště](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. V dialogovém okně **Přidat diskové úložiště** vyberte **Přidat disky**.

3. V seznamu dostupných disků vyberte disky, které chcete přidat, vyberte **Přidat**a pak vyberte **OK**.

## <a name="next-steps"></a>Další kroky

Po instalaci zálohovacího serveru se dozvíte, jak připravit server nebo jak začít chránit pracovní vytížení.

- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Zálohování serveru VMware pomocí zálohovacího serveru](backup-azure-backup-server-vmware.md)
- [Zálohování serveru SQL Server pomocí zálohovacího serveru](backup-azure-sql-mabs.md)
