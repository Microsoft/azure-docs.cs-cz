---
title: Moderní úložiště zálohy pomocí v2 Azure Backup Server
description: Další informace o nových funkcích v v2 serveru Azure Backup. Tento článek popisuje postup upgradu instalaci zálohování serveru.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: 7c583ea048ed1837c662869c62039165aaa3c024
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606750"
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Přidání úložiště do serveru Azure Backup v2

Azure Backup Server v2 se dodává s System Center 2016 Data Protection Manager moderních úložiště záloh. Moderní úložiště záloh nabízí úspory úložiště 50 procent, zálohování, které jsou třikrát rychlejší a efektivnější úložiště. Nabízí také deklaracemi pracovního vytížení úložiště. 

> [!NOTE]
> Pokud chcete použít moderní úložiště záloh, musíte spustit v2 zálohování serveru v systému Windows Server 2016. Pokud spustíte v2 zálohování serveru ve starší verzi systému Windows Server, Azure Backup Server nelze využít výhod moderní úložiště záloh. Místo toho chrání úlohy, stejně jako s v1 zálohování serveru. Další informace najdete v tématu verze zálohování serveru [ochrany matice](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server-v2"></a>Svazky v v2 zálohování serveru

Zálohování serveru v2 přijme svazky úložiště. Když přidáte svazek, zálohovat Server naformátuje svazek odolný systém souborů (ReFS), který vyžaduje moderní úložiště záloh. Můžete přidat svazek a rozbalte ho později, pokud je potřeba, doporučujeme použít tento pracovní postup:

1.  Nastavte zálohování serveru v2 na virtuálním počítači.
2.  Vytvoření svazku na virtuálním disku ve fondu úložiště:
    1.  Přidejte disk do fondu úložiště a virtuální disk vytvořit s jednoduché rozložení.
    2.  Přidejte všechny další disky a rozšířit virtuální disk.
    3.  Vytvořte svazky na virtuálním disku.
3.  Přidáte svazky do zálohování serveru.
4.  Konfigurace úlohy používající úložiště.

## <a name="create-a-volume-for-modern-backup-storage"></a>Vytvoření svazku pro moderní úložiště záloh

Pomocí zálohování serveru v2 pro svazky, diskového úložiště vám pomůže udržet kontrolu nad úložištěm. Svazek může být jediný disk. Pokud budete chtít v budoucnu rozšířit úložiště, ale vytvořte svazek mimo disk, který vytvořili pomocí prostorů úložiště. To může pomoct, pokud chcete rozšířit svazek pro zálohování úložiště. Tato část nabízí osvědčené postupy pro vytváření svazku s tímto nastavením.

1. Ve Správci serveru vyberte **Souborová služba a služba úložiště** > **svazky** > **fondy úložiště**. V části **fyzické disky**, vyberte **nový fond úložiště**. 

    ![Vytvořit nový fond úložiště](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. V **úlohy** rozevíracího seznamu vyberte **nový virtuální Disk**.

    ![Přidání virtuálního disku](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Vyberte fond úložiště a pak vyberte **přidat fyzický Disk**.

    ![Přidat fyzický disk](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Vyberte fyzický disk a pak vyberte **zvětšit virtuální Disk**.

    ![Rozšíření virtuálního disku](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Vyberte virtuální disk a pak vyberte **nový svazek**.

    ![Vytvořte nový svazek](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. V **vyberte server a disk** dialogovém okně, vyberte server a nový disk. Pak vyberte **Další**.

    ![Vyberte server a disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Přidat svazky do úložiště disku zálohování serveru

K přidání svazku do zálohování serveru v **správy** podokně znovu prohledat úložiště a potom vyberte **přidat**. Zobrazí se seznam všech svazků, které jsou k dispozici přidávaného pro úložiště záloh serveru. Po dostupných svazků jsou přidány do seznamu vybraných svazků, můžete přiřadit popisný název, který vám pomůžou spravovat je. K formátování tyto svazky odolném systému souborů, takže zálohování serveru můžete použít výhod moderní úložiště záloh, vyberte **OK**.

![Přidat k dispozici svazky](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Nastavení úlohy používající úložiště

S deklaracemi zatížení úložiště můžete vybrat svazků, které ukládají přednostně určité druhy zátěží. Například můžete nastavit nákladné svazky, které podporují vysoký počet vstupně výstupních operací za sekundu (IOPS) pro uložení pouze úlohy, které vyžadují časté, vysoký počet záloh. Příkladem je SQL Server s protokoly transakcí. Jiné úlohy, které se dají zálohovat méně často, jako jsou virtuální počítače, můžete zálohovat do svazků nízkými náklady.

### <a name="update-dpmdiskstorage"></a>Aktualizace DPMDiskStorage

Úlohy využívající úložiště můžete nastavit pomocí rutiny prostředí PowerShell aktualizace DPMDiskStorage, která aktualizuje vlastnosti svazku ve fondu úložiště na serveru Data Protection Manager.

Syntaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Následující snímek obrazovky ukazuje rutinu Update-DPMDiskStorage v okně prostředí PowerShell.

![Příkaz Update-DPMDiskStorage v okně prostředí PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

V konzole správce zálohování serveru se projeví změny, které provedete pomocí prostředí PowerShell.

![Disky a svazky v konzole pro správu](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Další postup
Po instalaci serveru zálohování, zjistěte, jak připravit server nebo začít chránit zatížení.

- [Příprava úlohy zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru zálohovat VMware server](backup-azure-backup-server-vmware.md)
- [Použít zálohování serveru k zálohování systému SQL Server](backup-azure-sql-mabs.md)

