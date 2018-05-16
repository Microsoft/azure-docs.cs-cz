---
title: Spravovat kapacita úložiště v Azure zásobníku | Microsoft Docs
description: Monitorovat a spravovat adresní prostor úložiště k dispozici pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: da6bb00d7538c1a26e1ed4be29d3c882aa378e9e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Spravovat kapacita úložiště pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Informace v tomto článku pomáhá monitorování operátor cloudu Azure zásobníku a spravovat úložnou kapacitu jejich nasazení Azure zásobníku. Infrastrukturu úložiště Azure zásobníku přiděluje podmnožinu celkový úložnou kapacitu zásobník Azure nasazení, který se má použít pro **služby úložiště**. Služby úložiště ukládat data klienta v sdílených složek na svazcích, které odpovídají uzly nasazení.

Jako operátor cloudu máte omezené množství úložiště pro práci s. Velikost úložiště je definována řešení, které můžete implementovat. Řešení je k dispozici od dodavatele OEM při použití řešení více uzly nebo hardwarem, na který instalujete Development Kit zásobník Azure.

Protože Azure zásobník nepodporuje rozšíření kapacity úložiště, je důležité [monitorování](#monitor-shares) úložiště k dispozici k zajištění efektivní operace jsou zachována.  

Když se stane omezené zbývající volné kapacity sdílené složky, naplánujte [spravovat místo](#manage-available-space) k zabránění spuštění mimo kapacity sdílené složky.

Možnosti pro správu kapacity:
- Opětovné využití kapacity
- Migrace kontejner

Po 100 % údajů úložiště sdílené složky služby už funkce pro tuto sdílenou složku. Nápovědu k obnovení operací pro sdílenou složku, obraťte se na podporu společnosti Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Pochopení svazků a sdílených složek, kontejnery a disků
### <a name="volumes-and-shares"></a>Svazků a sdílených složek
*Služba úložiště* oddíly úložiště k dispozici do samostatné a rovna svazky, které jsou přiděleny k ukládání dat klienta. Počet svazků je stejný jako počet uzlů v nasazení Azure zásobníku:

- Při nasazení čtyřmi uzly jsou čtyři svazky. Každý svazek obsahuje jednu sdílenou složku. V nasazení s více uzly není snížil počet sdílených složek, pokud uzel je odebrána nebo nepracuje správně.
- Pokud používáte sady pro vývojáře Azure zásobníku, je na jednom svazku s jednou sdílenou složkou.

Protože jsou sdílené složky pro služby úložiště pro výhradní použití služby úložiště, musí ne přímo změnit, přidat nebo odebrat všechny soubory na sdílené složky. Pouze úložiště služby by měly fungovat na soubory uložené v těchto svazků.

Sdílené složky na svazcích obsahovat data klientů. Data klienta obsahuje objekty BLOB stránky, objekty BLOB bloků, připojit objekty BLOB, tabulek, front, databází a související metadata úložiště. Vzhledem k tomu, že úložiště objektů (BLOB atd.) jsou jednotlivě obsažené v rámci jednu sdílenou složku, maximální velikost každého objektu nesmí překročit velikost sdílené složky. Maximální velikost nové objekty závisí na kapacitu, které zůstává ve sdílené složce, jako nevyužité místo při vytvoření nového objektu.

Když sdílenou není dostatek volného místa a akcí pro [uvolnit](#reclaim-capacity) místa nejsou úspěšné, nebo k dispozici, můžete operátor cloudu Azure zásobníku [migrovat](#migrate-a-container-between) kontejnery objektů blob z jedné složky do jiné.

- Další informace o kontejnery a objekty BLOB najdete v tématu [úložiště objektů Blob](azure-stack-key-features.md#blob-storage) v klíč funkce a koncepty v zásobníku Azure.
- Informace o fungování uživatelé klienta pomocí úložiště objektů blob v Azure zásobníku najdete v tématu [služby Azure zásobníku úložiště](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Containers
Uživatelé klienta vytvářet kontejnery, které se pak používají k ukládání dat objektů blob. Když se uživatel rozhodne, ve které kontejner objektů BLOB umístit službu úložiště používá nepodporovaný algoritmus k určení, na který svazek uvést kontejneru. Algoritmus obvykle zvolí svazek s nejvíce dostupného místa.  

Po objekt blob je umístěn v kontejneru, můžou růst tomuto objektu blob využívá víc místa na disku. Při přidávání nové objekty BLOB a existující objekty BLOB růst, dostupné místo na svazku, který obsahuje, že se zmenší tohoto kontejneru.  

Kontejnery nejsou omezeny na jednu sdílenou složku. Když jsou data kombinované objektu blob v kontejneru vzroste použijte 80 % nebo více volného místa, zadá kontejneru *přetečení* režimu. V režimu přetečení jsou přiděleny nové objekty BLOB, které jsou vytvořené v tomto kontejneru na jiný svazek, který má dost místa. V čase může mít kontejner v režimu přetečení objekty BLOB, které jsou rozmístěny v několika svazky.

Pokud je použita 80 % a 90 % dostupného místa na svazku, systém vyvolá výstrahy v portálu správce Azure zásobníku. Operátoři cloudu zkontrolujte dostupné kapacity úložiště a chcete znovu vyvážit obsah. Služby úložiště přestane pracovat, když disk je 100 % využití a jsou vyvolány žádné další výstrahy.

### <a name="disks"></a>Disky
Disky virtuálních počítačů jsou přidány do kontejnerů klienty a obsahují disk operačního systému. Virtuální počítače může mít jeden nebo více datových disků. Oba typy disků jsou uloženy jako objekty BLOB stránky. Pokyny, které klientům je každého disku, umístěte do samostatné kontejner ke zlepšení výkonu virtuálního počítače.
- Každý kontejner, který obsahuje disk (objekt blob stránky) z virtuálního počítače je považován za kontejner připojené k virtuálnímu počítači, který vlastní disk.
- Kontejner, který neobsahuje žádné disk z virtuálního počítače je považován za kontejner volné.

Možnosti pro uvolnění místa na kontejner připojené [jsou omezené](#move-vm-disks).
> [!TIP]  
> Operátoři cloudu nespravujte přímo disky, které jsou připojené k virtuální počítače (VM), které může klientům přidat do kontejneru. Ale při plánování ke správě adresního prostoru ve sdílených složkách pro úložiště, se může být použita pochopit, jak disky vztahují ke kontejnerům a sdílené složky.

## <a name="monitor-shares"></a>Monitorování sdílených složek
Ke sledování sdílených složek, můžete porozumět, když volné místo je omezená použijte PowerShell nebo portálu pro správu. Pokud používáte portál, obdržíte oznámení o sdílených složkách, které mají nedostatek místa.    

### <a name="use-powershell"></a>Použití prostředí PowerShell
Jako operátor cloudu, můžete monitorovat úložnou kapacitu sdílenou složku pomocí prostředí PowerShell **Get-AzsStorageShare** rutiny. Rutinu Get-AzsStorageShare vrátí celkový počet, přidělené a volné místo v bajtech v každé sdílené složky.   
![Příklad: Vrátit volného místa pro sdílené složky](media/azure-stack-manage-storage-shares/free-space.png)

- **Celková kapacita** je místo na celkový počet bajtů, které jsou dostupné ve sdílené složce. Tento prostor se používá pro data a metadata, která se spravuje pomocí služby storage.
- **Použít kapacity** je množství dat v bajtech, který je používán všechny rozsah ze souborů, které ukládají data klienta a přidružených metadat.

### <a name="use-the-administrator-portal"></a>Pomocí portálu správce
Jako operátor cloudu můžete v portálu pro správu zobrazit úložnou kapacitu všechny sdílené složky. **Přejděte do úložiště** > **sdílené složky** otevřete seznam souborů sdílenou složku, kde můžete zobrazit informace o využití.
![Příklad: Úložiště sdílené složky](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **Celkový počet** je místo na celkový počet bajtů, které jsou dostupné ve sdílené složce. Tento prostor se používá pro data a metadata, která se spravuje pomocí služby storage.
- **POUŽÍT** je množství dat v bajtech, který je používán všechny rozsah ze souborů, které ukládají data klienta a přidružených metadat.

### <a name="storage-space-alerts"></a>Výstrahy prostor úložiště
Pokud používáte portál pro správu, můžete dostávat výstrahy o sdílených složkách, které mají nedostatek místa.

> [!IMPORTANT]
> Operátor cloudu udržovat dosáhly úplné využití sdílených složek. Po 100 % údajů úložiště sdílené složky služby už funkce pro tuto sdílenou složku. Pokud chcete obnovit volného místa a obnovit operace ve sdílené složce, která je 100 % využívat, musí kontaktujte podporu společnosti Microsoft.

**Upozornění**: když sdílené složky je více než 80 % využité, zobrazí se *upozornění* výstrah v portálu pro správu: ![příklad: upozornění](media/azure-stack-manage-storage-shares/alert-warning.png)


**Kritické**: když sdílené složky je více než 90 % využité, zobrazí se *kritické* výstrah v portálu pro správu: ![příklad: Kritická výstraha](media/azure-stack-manage-storage-shares/alert-critical.png)

**Zobrazení podrobností**: V portálu pro správu můžete otevřít podrobnosti výstrahy zobrazíte možnosti omezení rizik: ![příklad: zobrazení podrobností o výstrahách](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Spravovat volné místo
Pokud je nutné na volné místo ve sdílené složce, použijte první nejméně invazivní metody. Zkuste například uvolnění místa předtím, než se rozhodnete převést kontejner.  

### <a name="reclaim-capacity"></a>Opětovné využití kapacity
*Tato možnost se vztahuje k nasazení na víc uzlů a Development Kit zásobník Azure.*

Můžete získat kapacity používané účty klientů, které byly odstraněny. Tato schopnost je automaticky uvolnit, když data [dobu uchování](azure-stack-manage-storage-accounts.md#set-the-retention-period) je dosaženo, nebo může fungovat okamžitě získat zpět.

Další informace najdete v tématu [opětovné využití kapacity](azure-stack-manage-storage-accounts.md#reclaim) v spravovat prostředky úložiště.

### <a name="migrate-a-container-between-volumes"></a>Migrace kontejner mezi svazky
*Tato možnost se vztahuje pouze k nasazení na víc uzlů.*

Z důvodu vzorů využití klienta použít nějaké sdílené složky klienta více místa než jiné. Výsledkem může být nedostatek místa než jiné sdílené složky, které jsou relativně nepoužívané na sdílené složce.

Můžete zkusit uvolněte místo na Nepromyšlené sdílenou složku ručně migrací některé kontejnery objektů blob jinou sdílenou složku. Několik menších kontejnery můžete migrovat do jednu sdílenou složku, která má kapacitu pro uložení je všechny. Migrace můžete přesunout *volné* kontejnery. Volné kontejnery jsou kontejnery, které neobsahují žádný disk pro virtuální počítač.   

Migrace slučuje všechny kontejnery blob na nové sdílené složky.

- Pokud kontejner přešel do režimu přetečení a má umístit objekty BLOB na další svazky, nové sdílené složky musí mít dostatečnou kapacitu pro uložení všech objektů blob pro kontejner, který migrujete. To zahrnuje objekty BLOB, které jsou umístěny na další sdílené složky.

- Rutiny prostředí PowerShell *Get-AzsStorageContainer* identifikuje pouze místo ve využívaném na počáteční svazku pro kontejner. Rutina neidentifikuje prostor, který používá objekty BLOB umístí další svazky. Plná velikost kontejner proto nemusí být zřejmé. Je možné, že konsolidace kontejner na nové sdílené složky můžete odeslat tuto novou sdílenou složku do přetečení kde umístí dat do další sdílené složky. V důsledku toho budete muset znovu znovu vyvážit sdílené složky.

- Pokud nemají oprávnění pro skupinu prostředků a pomocí prostředí PowerShell nelze vyhledat další svazky pro přetečení data, pracujete s vlastníkem tyto skupiny prostředků a kontejnery pochopit celková velikost dat k migraci před migrací dat.  

> [!IMPORTANT]
> Migrace objektů blob pro kontejner je offline operace, která vyžaduje použití prostředí PowerShell. Až do dokončení migrace všech objektů blob pro kontejner, který migrujete zůstat offline a nelze jej použít. Neměli byste také, upgrade zásobník Azure, dokud se nedokončí všechny probíhající migrace.

#### <a name="to-migrate-containers-using-powershell"></a>K migraci kontejnery pomocí prostředí PowerShell
1. Potvrďte, že máte [prostředí Azure PowerShell nainstalovaný a nakonfigurovaný](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](http://go.microsoft.com/fwlink/?LinkId=394767).
2.  Zkontrolujte kontejner, aby pochopili, jaká data jsou ve sdílené složce, která chcete migrovat. Chcete-li identifikovat nejlepší kontejnery kandidáta pro migraci na svazku, použijte **Get-AzsStorageContainer** rutiny:

    ````PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ````
    Potom si prohlédněte $containers:

    ````PowerShell
    $containers
    ````

    ![Příklad: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Identifikujte nejlepší cílové složky pro uložení kontejneru, které můžete migrovat:

    ````PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ````

    Potom si prohlédněte $destinationshares:

    "" $Destinationshares prostředí PowerShell
    ````

    ![Example: $destination shares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Start migration for a container. Migration is asynchronous. If you start migration of additional containers before the first migration completes, use the job id to track the status of each.

  ````PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ````

  Potom si prohlédněte $jobId. V následujícím příkladu nahraďte *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* s id úlohy, které chcete prověřit:

  ````PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ````

5. Zkontrolujte stav úlohy migrace pomocí id úlohy. Po dokončení migrace kontejneru **MigrationStatus** je nastaven na **Complete**.

  ````PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ````

  ![Příklad: Migrace stavu](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Můžete zrušit úlohu v průběhu migrace. Došlo ke zrušení migrace, které úlohy jsou zpracovávány asynchronně. Zrušení můžete sledovat pomocí $jobid:

  ````PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ````

  ![Příklad: Stav vrácení zpět](media/azure-stack-manage-storage-shares/rollback.png)

7. Spuštěním příkazu v kroku 6 znovu, dokud se potvrdí stav úlohy migrace je **zrušeno**:  

    ![Příklad: Stav zrušeno](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Přesunout disky virtuálních počítačů
*Tato možnost se vztahuje pouze k nasazení na víc uzlů.*

Většina extrémně metoda ke správě adresního prostoru zahrnuje přesun disky virtuálního počítače. Protože je komplexní přesunutí kontejner připojené (ten, který obsahuje disk virtuálního počítače), obraťte se na Microsoft Support k provedení této akce.

## <a name="next-steps"></a>Další kroky
Další informace o [nabídky virtuálních počítačů pro uživatele](azure-stack-tutorial-tenant-vm.md).
