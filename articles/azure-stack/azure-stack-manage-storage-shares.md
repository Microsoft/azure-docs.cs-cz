---
title: Správa kapacity úložiště ve službě Azure Stack | Dokumentace Microsoftu
description: Monitorovat a spravovat adresní prostor úložiště k dispozici pro službu Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7536913817e10fbc2b46eadd1cebdfb98079c759
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819514"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Správa kapacity úložiště pro službu Azure Stack 

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Informace v tomto článku pomáhá monitorování – operátor cloudu Azure Stack a spravovat je kapacita jejich nasazení Azure Stack. Infrastruktura Azure stacku úložiště přiděluje podmnožinu celkové úložnou kapacitu nasazení Azure Stack, má být použit pro **služby storage**. Služba úložiště ukládání dat klienta ve sdílených složkách na svazcích, které odpovídají uzly nasazení.

Jako operátor cloudu budete mít omezené množství úložiště pro práci s. Velikost úložiště je definována na řešení, které můžete implementovat. Vaše řešení je k dispozici od dodavatele OEM při použití řešení několika uzly nebo hardware, na kterých je nainstalován Azure Stack Development Kit.

Protože Azure Stack nepodporuje rozšíření kapacity úložiště, je potřeba [monitorování](#monitor-shares) úložiště k dispozici pro zajištění efektivních operací se zachovají.  

Jakmile zbývající Volná kapacita sdílenou složku je omezené, naplánujte [správě adresního prostoru](#manage-available-space) sdíleným složkám zabránit došly kapacity.

Možnosti pro správu kapacity:
- Uvolnit kapacity
- Migrace kontejner

Když sdílenou složku je 100 % optimalizováno úložiště služby už funkce pro tuto sdílenou složku. Chcete-li získat pomoc s obnovením operací pro sdílenou složku, kontaktujte podporu Microsoftu.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Pochopení svazky, sdílené složky, kontejnery a disky
### <a name="volumes-and-shares"></a>Svazků a sdílených složek
*Službu storage* rozděluje úložiště k dispozici do samostatné a stejné svazků, které jsou přiděleny k ukládání dat tenantů. Počet svazků je rovna počtu uzlů v nasazení Azure Stack:

- U nasazení čtyřmi uzly jsou čtyři svazky. Každý svazek má jednou sdílenou složkou. V nasazení s více uzly počet sdílených složek nesníží, pokud uzel je odebrán nebo nepracuje správně.
- Pokud používáte sady pro vývojáře Azure Stack, je na jednom svazku s jednou sdílenou složkou.

Vzhledem k tomu složky služby úložiště pro výhradní použití služby úložiště, třeba přímo změnit, přidat či odebrat všechny soubory na sdílených složek na. Pouze služba úložiště by měl pracovat na soubory uložené v těchto svazcích.

Sdílené složky ve svazcích uchovávání dat tenanta. Data tenanta zahrnuje objekty BLOB stránky, objekty BLOB bloku, doplňovací objekty BLOB, tabulky, fronty, databází a související metadata úložiště. Protože objekty úložiště (objekty BLOB, atd.) jsou jednotlivě obsaženy v rámci jediné sdílené složky, maximální velikost každého objektu nesmí překročit velikost sdílené složky. Maximální velikost nové objekty závisí na kapacitě, která zůstává ve sdílené složce, jako nevyužité místo při vytvoření nového objektu.

Když sdílenou složku není dostatek volného místa a akce, které [uvolnit](#reclaim-capacity) místa nejsou úspěšné, nebo k dispozici, operátor cloudu Azure Stack můžete migrovat kontejnery objektů blob z jedné sdílené složky do jiné.

- Další informace o kontejnerům a objektům BLOB najdete v tématu [úložiště objektů Blob](azure-stack-key-features.md#blob-storage) v klíč funkcích a konceptech v Azure stacku.
- Informace o tom, jak tenanta uživatelé pracují s úložištěm objektů blob ve službě Azure Stack najdete v tématu [služby Azure Stack úložiště](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Containers
Uživatelé tenanta vytvářet kontejnery, které se následně použijí k ukládání dat objektů blob. Když se uživatel rozhodne, ve které kontejneru umístit objekty BLOB služby storage používá algoritmus Pokud chcete určit, na který svazek do kontejneru. Algoritmus obvykle zvolí svazek s nejvíce dostupného místa.  

Po umístění objektu blob v kontejneru, můžou používat více místa na růst tohoto objektu blob. Při přidávání nové objekty BLOB a existující objekty BLOB růst, k dispozici místo ve svazku, který obsahuje zmenšuje tohoto kontejneru.  

Kontejnery nejsou omezená na jednu sdílenou složku. Objem dat kombinované objektů blob v kontejneru použijte 80 % nebo více volného místa, přejde do kontejneru *přetečení* režimu. V režimu přetečení, jsou přiděleny nové objekty BLOB, které jsou vytvořené v tomto kontejneru na jiný svazek, který má dost volného místa. V průběhu času může mít kontejner ve službě přetečení režim objekty BLOB, které jsou distribuovány napříč více svazků.

Při použití 80 % a 90 % volného místa na svazku, systém vyvolá upozornění na portálu Správce služby Azure Stack. Operátorům cloudu by měl zkontrolovat dostupné kapacity úložiště a chcete obnovit rovnováhu obsah. Služby storage pracovat se 100 % využití disku a žádné další výstrahy jsou generovány.

### <a name="disks"></a>Disky
Disky virtuálních počítačů jsou přidány do kontejnerů tenanty a obsahují disk s operačním systémem. Virtuální počítače mohou mít také jeden nebo více datových disků. Oba typy disky jsou uložené jako objekty BLOB stránky. Pokyny pro tenanty, je umístit každého disku do samostatného kontejneru pro zlepšení výkonu virtuálního počítače.
- Každý kontejner, který obsahuje disk z virtuálního počítače (objekt blob stránky) je považován za kontejner připojené k virtuálnímu počítači, který vlastní disk.
- Kontejner, který neobsahuje žádný disk z virtuálního počítače je považován za kontejner zdarma.

Možnosti pro uvolnění místa na připojené kontejneru [jsou omezené](#move-vm-disks).
> [!TIP]  
> Operátory cloud přímo nedokáže spravovat disky, které jsou připojené k virtuálním počítačům (VM), které může klientům přidat do kontejneru. Ale při plánování ke správě adresního prostoru ve sdílených složkách úložiště, může být použití pochopit, jak disky se vztahují ke kontejnerům a sdílené složky.

## <a name="monitor-shares"></a>Monitorování složky
Pomocí Powershellu nebo portálu pro správu k monitorování sdílených složek to umožní pochopit, když volné místo je omezený. Při použití na portálu se zobrazí oznámení o sdílených složkách, které je nedostatek volného místa.    

### <a name="use-powershell"></a>Použití prostředí PowerShell
Jako operátor cloudu, můžete sledovat kapacitu úložiště sdílené složky pomocí Powershellu **Get-AzsStorageShare** rutiny. Rutina Get-AzsStorageShare vrátí celkový počet, přidělené a volné místo v bajtech ve všech sdílených složek na.   
![Příklad: Vrátí volného místa pro sdílené složky](media/azure-stack-manage-storage-shares/free-space.png)

- **Celková kapacita** je místo na celkový počet bajtů, které jsou k dispozici na sdílené složce. Zde se používá pro data a metadata, která se spravuje pomocí služby úložiště.
- **Kapacita využitá** je množství dat v bajtech, která se používá všechny rozsahy ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="use-the-administrator-portal"></a>Použití portálu správce
Jako operátor cloudu můžete na portálu pro správu k zobrazení všech sdílených složek kapacitu úložiště.

1. Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external).
2. Vyberte **všechny služby** > **úložiště** otevřete seznam souborů sdílené složky, kde můžete zobrazit informace o využití. 

  ![Příklad: Sdílené složky úložiště](media/azure-stack-manage-storage-shares/storage-file-shares.png)

  - **Celkový počet** je místo na celkový počet bajtů, které jsou k dispozici na sdílené složce. Zde se používá pro data a metadata, která se spravuje pomocí služby úložiště.
  - **POUŽÍT** je množství dat v bajtech, která se používá všechny rozsahy ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="storage-space-alerts"></a>Upozornění prostor úložiště
Při použití portálu pro správu se zobrazí oznámení o sdílených složkách, které je nedostatek volného místa.

> [!IMPORTANT]
> Jako operátor cloudu zachovat dosažení úplné využití sdílené složky. Když sdílenou složku je 100 % optimalizováno úložiště služby už funkce pro tuto sdílenou složku. Volné místo obnovení a obnovení operací ve sdílené složce, která je 100 % využít, je nutné kontaktovat podporu Microsoftu.

**Upozornění**: Když sdílenou složku je více než 80 % využít, zobrazí se *upozornění* výstrah v portálu pro správu: ![Příklad: Upozorňující výstraha](media/azure-stack-manage-storage-shares/alert-warning.png)


**Kritické**: Když sdílenou složku je více než 90 % využít, zobrazí se *kritický* výstrah v portálu pro správu: ![Příklad: Kritická výstraha](media/azure-stack-manage-storage-shares/alert-critical.png)

**Zobrazit podrobnosti o**: V portálu pro správu můžete otevřít podrobnosti o výstraze zobrazíte možnosti omezení rizik: ![Příklad: Zobrazit podrobnosti výstrahy](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Správa dostupného místa
Když je potřeba volné místo ve sdílené složce, použijte nejprve nejméně invazivní metody. Zkuste například uvolnění místa, než se rozhodnete pro migraci kontejneru.  

### <a name="reclaim-capacity"></a>Uvolnit kapacity
*Tato možnost se vztahuje k nasazení na víc uzlů a sady Azure Stack Development Kit.*

Můžete získat zpět kapacita použitá účtům tenantů, které byly odstraněny. Tato kapacita je automaticky získány zpět, když data [dobu uchování](azure-stack-manage-storage-accounts.md#set-the-retention-period) je dosaženo, nebo může fungovat okamžitě získat zpět.

Další informace najdete v tématu [uvolnit kapacity](azure-stack-manage-storage-accounts.md#reclaim) v spravovat prostředky úložiště.

### <a name="migrate-a-container-between-volumes"></a>Migrace kontejneru mezi svazky
*Tato možnost se vztahuje pouze na nasazení na víc uzlů.*

Z důvodu vzorů využití tenanta nějaké sdílené složky klienta pomocí více místa než jiné. Výsledkem může být sdílenou složku, která běží na mezeru před další sdílené složky, které nejsou používány relativně nízký.

Zkuste uvolnit místo ve sdílené složce Nepromyšlené ručně migrací některé kontejnery objektů blob do jiné sdílené složky. Několik menších kontejnery můžete migrovat do jedné sdílené složky, který má dostatečnou kapacitu k uložení je všechny. Migraci můžete přesunout *bezplatné* kontejnery. Bezplatné kontejnery jsou kontejnery, které neobsahují disk pro virtuální počítač.   

Migrace konsoliduje všechny kontejnery blob na novou sdílenou složku.

- Pokud kontejner přešel do režimu přetečení a objekty BLOB se umístí na další svazky, Nová sdílená složka musí mít dostatečnou kapacitu k uložení všech objektů blob pro kontejner, který migrujete. To zahrnuje objekty BLOB, které se nacházejí na další sdílené složky.

- Rutiny Powershellu *Get-AzsStorageContainer* identifikuje pouze místo na svazku počáteční pro kontejner. Rutina neidentifikuje prostor, který používá objekty BLOB do další svazky. Plnou velikost kontejneru proto nemusí být zřejmé. Je možné, že konsolidace kontejneru na novou sdílenou složku můžete odeslat tuto novou sdílenou složku do přetečení ve kterém je umístí data na další sdílené složky. V důsledku toho možná budete muset vyrovnat sdílené složky znovu.

- Pokud nemají oprávnění pro skupinu prostředků a možné pomocí Powershellu vyhledat další svazky pro přetečení dat, pracujete s vlastníkem těchto prostředků a skupin kontejnerů o celkové velikosti dat k migraci před migrací dat.  

> [!IMPORTANT]
> Migrace objektů BLOB v kontejneru je v režimu offline operace vyžadující použití prostředí PowerShell. Dokud se nedokončí migrace všech objektů blob pro kontejner, který migrujete zůstat offline a nelze jej použít. Byste se měli vyhnout také upgrade služby Azure Stack, dokud se nedokončí všechny probíhající migrace.

#### <a name="to-migrate-containers-using-powershell"></a>K migraci kontejnerů pomocí Powershellu
1. Potvrďte, že máte [prostředí Azure PowerShell nainstalovaný a nakonfigurovaný](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](https://go.microsoft.com/fwlink/?LinkId=394767).
2.  Prozkoumejte kontejneru pochopit, jaká data jsou ve sdílené složce, kterou chcete použít k migraci. Chcete-li určit nejlepší kandidát kontejnery pro migraci na svazku, použijte **Get-AzsStorageContainer** rutiny:

    ```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ```
    Pak zkontrolujte $containers:

    ```PowerShell
    $containers
    ```

    ![Příklad: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Určení nejlepší cílové složky pro uložení kontejneru, který migrujete:

    ```PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```

    Pak zkontrolujte $destinationshares:

    ```PowerShell 
    $destinationshares
    ```

    ![Příklad: $destination sdílené složky](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Zahájení migrace pro kontejner. Migrace je asynchronní. Pokud před dokončením migrace první spuštění migrace dalších kontejnerů, sledovat stav každého pomocí id úlohy.

  ```PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ```

  Pak zkontrolujte $jobId. V následujícím příkladu nahraďte *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* s id úlohy, které chcete prověřit:

  ```PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```

5. Id úlohy můžete zkontrolovat stav úlohy migrace. Po dokončení migrace kontejneru **MigrationStatus** je nastavena na **Complete**.

  ```PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ```

  ![Příklad: Stav migrace](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Zrušit úlohu migrace probíhá. Zrušit migraci, které úlohy jsou zpracovávány asynchronně. Zrušení můžete sledovat pomocí $jobid:

  ```PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ```

  ![Příklad: Stav odvolání](media/azure-stack-manage-storage-shares/rollback.png)

7. Spuštěním příkazu v kroku 6 znovu, dokud se potvrdí stav úlohy migrace je **zrušeno**:  

    ![Příklad: Stav zrušeno](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Přesunout disky virtuálních počítačů
*Tato možnost se vztahuje pouze na nasazení na víc uzlů.*

Metodu nejextrémnějších ke správě adresního prostoru zahrnuje přesunutí disků virtuálních počítačů. Přechod kontejneru připojené (jedna, která obsahuje disku virtuálního počítače) je komplexní, obraťte se na Microsoft Support k provedení této akce.

## <a name="next-steps"></a>Další kroky
Další informace o [nabídky virtuálních počítačů na uživatele](azure-stack-tutorial-tenant-vm.md).
