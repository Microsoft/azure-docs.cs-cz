---
title: Použití Moderní úložiště zálohování s Azure Backup Server
description: Seznamte se s novými funkcemi v Azure Backup Server. Tento článek popisuje, jak upgradovat instalaci záložního serveru.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b077296e58e1193e454a686a392d802e905500a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292893"
---
# <a name="add-storage-to-azure-backup-server"></a>Přidání úložiště do Azure Backup Serveru

Azure Backup Server v2 a novější podporuje Moderní úložiště zálohování, která nabízí úspory úložiště 50%, zálohování, které jsou třikrát rychlejší a efektivnější úložiště. Nabízí také úložiště s podporou úloh.

> [!NOTE]
> Chcete-li použít Moderní úložiště zálohování, je nutné spustit záložní server v2 nebo V3 v systému Windows Server 2016 nebo V3 v systému Windows Server 2019.
> Pokud spustíte záložní server V2 v dřívější verzi Windows serveru, Azure Backup Server nemůže využít Moderní úložiště zálohování. Místo toho chrání úlohy stejně jako u záložního serveru v1. Další informace najdete v tématu [matice ochrany](backup-mabs-protection-matrix.md)verzí záložního serveru.
>
> Aby se dosáhlo lepších výkonů služby Backup, doporučujeme nasadit MABS V3 se vrstveným úložištěm v systému Windows Server 2019. Postup konfigurace vrstveného úložiště najdete v článku aplikace DPM "[Nastavení MB pomocí Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)".

## <a name="volumes-in-backup-server"></a>Svazky na záložním serveru

Záložní server v2 nebo novější akceptuje svazky úložiště. Když přidáte svazek, záložní server naformátuje svazek na odolný systém souborů (ReFS), který Moderní úložiště zálohování vyžaduje. Pokud chcete přidat svazek a později ho rozšířit, pokud potřebujete, doporučujeme použít tento pracovní postup:

1. Nastavení záložního serveru na virtuálním počítači.
2. Vytvoření svazku na virtuálním disku ve fondu úložiště:
    1. Přidejte disk do fondu úložiště a vytvořte virtuální disk s jednoduchým rozložením.
    2. Přidejte další disky a virtuální disk prodlužte.
    3. Vytvořte svazky na virtuálním disku.
3. Přidejte svazky na záložní server.
4. Nakonfigurujte úložiště s podporou úloh.

## <a name="create-a-volume-for-modern-backup-storage"></a>Vytvořit svazek pro Moderní úložiště zálohování

Používání služby Backup Server v2 nebo novější se svazky jako diskové úložiště vám může pomoci udržet si kontrolu nad úložištěm. Svazek může být jeden disk. Pokud ale chcete úložiště v budoucnu zvětšit, vytvořte svazek z disku vytvořeného pomocí prostorů úložiště. To může být užitečné, pokud chcete rozšířit svazek pro úložiště zálohování. Tato část nabízí osvědčené postupy pro vytvoření svazku s tímto nastavením.

1. V správce serveru vyberte souborové fondy úložiště **souborové služby a služby úložiště**  >    >  . V části **fyzické disky** vyberte **Nový fond úložiště**.

    ![Vytvořit nový fond úložiště](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. V rozevíracím seznamu **úlohy** vyberte **Nový virtuální disk**.

    ![Přidat virtuální disk](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Vyberte fond úložiště a pak vyberte **Přidat fyzický disk**.

    ![Přidání fyzického disku](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Vyberte fyzický disk a pak vyberte možnost **Zvětšit virtuální disk**.

    ![Rozšiřování virtuálního disku](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Vyberte virtuální disk a pak vyberte **nový svazek**.

    ![Vytvořit nový svazek](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. V dialogovém okně **Výběr serveru a disku** vyberte server a nový disk. Pak vyberte **Další**.

    ![Vybrat server a disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Přidání svazků do úložiště disku záložního serveru

> [!NOTE]
>
> - Přidejte do fondu jenom jeden disk, abyste zachovali počet sloupců na 1. Pak můžete podle potřeby přidat disky.
> - Pokud do fondu úložiště přidáte více disků na cestách, bude počet disků uložen jako počet sloupců. Když přidáte více disků, může to být pouze násobek počtu sloupců.

Pokud chcete přidat svazek na záložní server, v podokně **Správa** znovu prověřte úložiště a pak vyberte **Přidat**. Zobrazí se seznam všech svazků, které mají být přidány pro úložiště záložního serveru. Až budou dostupné svazky přidány do seznamu vybraných svazků, můžete jim poskytnout popisný název, který vám bude pomáhat s jejich správou. Pokud chcete tyto svazky naformátovat na ReFS, může záložní server využívat výhod Moderní úložiště zálohování, vyberte **OK**.

![Přidat dostupné svazky](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Nastavení úložiště s podporou úloh

V případě úložiště s podporou úloh můžete vybrat svazky, které mají přednostně ukládat určité druhy úloh. Můžete například nastavit nákladné svazky, které podporují vysoký počet vstupně-výstupních operací za sekundu (IOPS), aby se ukládaly jenom úlohy, které vyžadují časté zálohování s vysokým objemem. Příklad je SQL Server s protokoly transakcí. Jiné úlohy, které se zálohují méně často, jako jsou virtuální počítače, se dají zálohovat na objemy s nízkými náklady.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Úložiště s podporou úloh můžete nastavit pomocí rutiny PowerShellu Update-DPMDiskStorage, která aktualizuje vlastnosti svazku ve fondu úložiště na Azure Backup Server.

Syntaxe:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Následující snímek obrazovky ukazuje rutinu Update-DPMDiskStorage v okně PowerShellu.

![Příkaz Update-DPMDiskStorage v okně PowerShellu](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Změny, které provedete pomocí PowerShellu, se projeví v konzole správce záložního serveru.

![Disky a svazky v konzole pro správu](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrace starší verze úložiště do Moderní úložiště zálohování

Po upgradu na nebo instalaci záložního serveru v2 a upgradu operačního systému na Windows Server 2016 aktualizujte skupiny ochrany tak, aby používaly Moderní úložiště zálohování. Ve výchozím nastavení se skupiny ochrany nezměnily. Budou i nadále fungovat, jak byly původně nastaveny.

Aktualizace skupin ochrany na používání moderního úložiště záloh je volitelná. Chcete-li aktualizovat skupinu ochrany, zastavte ochranu všech zdrojů dat pomocí možnosti zachovat data. Pak přidejte zdroje dat do nové skupiny ochrany.

1. V konzole pro správu vyberte funkci **ochrana** . V seznamu **členů skupiny ochrany** klikněte pravým tlačítkem myši na člena a vyberte možnost **Zastavit ochranu člena**.

   ![Zastavení ochrany člena](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. V dialogovém okně **Odebrat ze skupiny** zkontrolujte využité místo na disku a dostupné volné místo pro fond úložiště. Výchozím postupem je nechat body obnovení na disku a umožnit jim vypršení platnosti podle přidružené zásady uchovávání. Vyberte **OK**.

   Pokud chcete hned vrátit využité místo na disku do fondu volných úložišť, zaškrtněte políčko **Odstranit repliku na disku** , chcete-li odstranit data zálohy (a body obnovení) přidružené k tomuto členu.

   ![Dialogové okno odebrat ze skupiny](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Vytvořte skupinu ochrany, která používá Moderní úložiště zálohování. Zahrňte nechráněné zdroje dat.

## <a name="add-disks-to-increase-legacy-storage"></a>Přidání disků pro zvýšení starší verze úložiště

Pokud chcete používat starší verze úložiště se záložním serverem, možná budete muset přidat disky a zvýšit tak starší verzi úložiště.

Přidání diskového úložiště:

1. V konzole pro správu vyberte **Správa**  >  **Disk Storage**  >  **Přidat**.

    ![Přidat Disk Storage – dialogové okno](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. V dialogovém okně **přidat disk Storage** vyberte **Přidat disky**.

3. V seznamu dostupných disků vyberte disky, které chcete přidat, vyberte **Přidat** a pak vyberte **OK**.

## <a name="next-steps"></a>Další kroky

Po instalaci záložního serveru se dozvíte, jak připravit server nebo začít chránit úlohu.

- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Použití Backup Serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Použití Backup Serveru k zálohování SQL Serveru](backup-azure-sql-mabs.md)
