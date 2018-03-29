---
title: Azure Site Recovery Deployment Planner pro nasazení VMware do Azure | Dokumentace Microsoftu
description: Tento článek popisuje režim spuštění Plánovače nasazení služby Azure Site Recovery pro scénář nasazení VMware do Azure.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: garavd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: d7b65771555fcada741b594c14b98e87e7448b24
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Spuštění Plánovače nasazení služby Azure Site Recovery pro nasazení VMware do Azure
Tento článek představuje uživatelskou příručku k nástroji Azure Site Recovery Deployment Planner pro produkční nasazení VMware do Azure.


## <a name="modes-of-running-deployment-planner"></a>Režimy spuštění plánovače nasazení
Nástroj příkazového řádku (ASRDeploymentPlanner.exe) můžete spustit v některém z následujících čtyř režimů:

1.  [Profilace](#profile-vmware-vms)
2.  [Generování sestav](#generate-report)
3.  [Zjištění propustnosti](#get-throughput)

Nejprve spusťte nástroj v režimu profilace, aby shromáždil četnost změn dat a počet IOPS virtuálních počítačů. Následně spusťte nástroj v režimu generování sestav, abyste zjistili požadavky na šířku pásma sítě a na úložiště a náklady na zotavení po havárii.

## <a name="profile-vmware-vms"></a>Profilace virtuálních počítačů VMware
V režimu profilace se Deployment Planner připojí k serveru vCenter nebo k hostiteli vSphere ESXi za účelem shromáždění dat o výkonu virtuálního počítače.

* Profilace nemá vliv na výkon produkčních virtuálních počítačů, protože se k nim nevytváří žádné přímé připojení. Veškerá data o výkonu se shromažďují ze serveru vCenter nebo z hostitele vSphere ESXi.
* Aby se zajistilo, že profilace bude mít na server zanedbatelný dopad, nástroj provádí dotazování serveru vCenter nebo hostitele vSphere ESXi každých 15 minut. Tento interval dotazování však nesnižuje přesnost profilace, protože nástroj ukládá data čítače výkonu pro každou minutu.

### <a name="create-a-list-of-vms-to-profile"></a>Vytvoření seznamu virtuálních počítačů určených k profilaci
Nejprve potřebujete seznam virtuálních počítačů určených k profilaci. Všechny názvy virtuálních počítačů na serveru vCenter nebo hostiteli vSphere ESXi můžete získat pomocí příkazů rozhraní VMware vSphere PowerCLI v následujícím postupu. Případně můžete v souboru ručně vytvořit seznam popisných názvů nebo IP adres virtuálních počítačů, které chcete profilovat.

1. Přihlaste se k virtuálnímu počítači, na kterém je nainstalované rozhraní VMware vSphere PowerCLI.
2. Otevřete konzolu VMware vSphere PowerCLI.
3. Zkontrolujte, že jsou pro skript povolené zásady spouštění. Pokud jsou zakázané, spusťte konzolu VMware vSphere PowerCLI v režimu správce a povolte je spuštěním následujícího příkazu:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Možná bude potřeba spustit následující příkaz, pokud Connect-VIServer nebude rozpoznán jako název rutiny.

            Add-PSSnapin VMware.VimAutomation.Core

5. Chcete-li získat všechny názvy virtuálních počítačů na serveru vCenter nebo hostiteli vSphere ESXi a jejich seznam uložit do souboru .txt, spusťte zde uvedené dva příkazy.
Nahraďte zástupné hodnoty &lsaquo;server name&rsaquo; (název serveru), &lsaquo;user name&rsaquo; (uživatelské jméno), &lsaquo;password&rsaquo; (heslo) a &lsaquo;outputfile.txt&rsaquo; (výstupní soubor) vlastními hodnotami.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. Otevřete výstupní soubor v Poznámkovém bloku a zkopírujte názvy všech virtuálních počítačů, které chcete profilovat, do jiného souboru (například ProfileVMList.txt), přičemž každý název virtuálního počítače musí být na samostatném řádku. Tento soubor se použije jako vstup pro parametr *-VMListFile* nástroje příkazového řádku.

    ![Seznam názvů virtuálních počítačů v Deployment Planneru
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Spuštění profilace
Jakmile budete mít seznam virtuálních počítačů určených k profilaci, můžete nástroj spustit v režimu profilace. Zde je seznam povinných a volitelných parametrů pro spuštění nástroje v režimu profilace.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Název parametru | Popis |
|---|---|
| -Operation | StartProfiling |
| -Server | Plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo hostitele vSphere ESXi, které obsahují virtuální počítače určené k profilaci.|
| -User | Uživatelské jméno pro připojení k serveru vCenter nebo hostiteli vSphere ESXi. Uživatel musí mít alespoň přístup jen ke čtení.|
| -VMListFile | Soubor se seznamem virtuálních počítačů určených k profilaci. Cesta k souboru může být absolutní nebo relativní. Soubor musí na každém řádku obsahovat jeden název nebo IP adresu virtuálního počítače. Název virtuálního počítače zadaný v souboru se musí shodovat s názvem virtuálního počítače na serveru vCenter nebo hostiteli vSphere ESXi.<br>Například soubor VMList.txt obsahuje následující virtuální počítače:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|Počet minut, po které má být profilace spuštěna. Minimální hodnota je 30 minut.|
|-NoOfHoursToProfile|Počet hodin, po které má být profilace spuštěna.|
| -NoOfDaysToProfile | Počet dní, po které má být profilace spuštěna. Doporučujeme profilaci spouštět na více než 7 dní, během kterých se zjistí vzorce úloh ve vašem prostředí a s jejich pomocí nástroj poskytne přesná doporučení. |
|-Virtualization|Určuje typ virtualizace (VMware nebo Hyper-V).|
| -Directory | (Volitelné) Název UNC (Universal Naming Convention) nebo cesta k místnímu adresáři, do kterého se během profilace budou ukládat data profilace. Pokud název adresáře není zadaný, jako výchozí se použije adresář ProfiledData v aktuální cestě. |
| -Password | (Volitelné) Heslo, které se použije pro připojení k serveru vCenter nebo hostiteli vSphere ESXi. Pokud heslo nezadáte teď, budete k tomu vyzváni při spuštění příkazu.|
|-Port|(Volitelné) Číslo portu pro připojení k hostiteli vCenter/ESXi. Výchozí port je 443.|
|-Protocol| (Volitelné) Určuje protokol (http nebo https) pro připojení k serveru vCenter. Výchozím protokolem je https.|
| -StorageAccountName | (Volitelné) Název účtu úložiště, který se použije k zjištění dosažitelné propustnost pro replikaci místních dat do Azure. Nástroj vypočítává propustnost tak, že do tohoto účtu úložiště nahrává testovací data. Účet úložiště musí být verze 1 pro obecné účely nebo úložiště verze 2 (verze 2 pro obecné účely).|
| -StorageAccountKey | (Volitelné) Klíč účtu úložiště, který se použije pro přístup k účtu úložiště. Přejděte na Azure Portal > Účty úložiště > <*název účtu služby Storage*> > Nastavení > Přístupové klíče > Klíč1. |
| -Environment | (Volitelné) Toto je vaše cílové prostředí účtu Azure Storage. Může to být jedna ze tří hodnot – AzureCloud, AzureUSGovernment a AzureChinaCloud. Výchozí hodnota je AzureCloud. Tento parametr použijte, pokud vaší cílovou oblastí Azure jsou cloudy Azure US Government nebo Azure China. |


Doporučujeme profilovat virtuální počítače po dobu delší než 7 dní. Pokud se vzor četnosti změn v měsíci mění, doporučujeme profilaci v týdnu, kdy je četnost změn maximální. Nejlepší způsob, jak získat lepší doporučení, je provádět profilaci 31 dní. Během období profilace je ASRDeploymentPlanner.exe stále spuštěný. Nástroj na vstupu přijímá zadání času profilace ve dnech. Pokud chcete rychle nástroj otestovat nebo potvrdit koncept, můžete provádět profilaci po několik hodin nebo minut. Minimální povolený čas profilace je 30 minut.

Během profilace můžete volitelně předat název a klíč účtu úložiště a zjistit tak propustnost, které může Site Recovery dosáhnout v době replikace z konfiguračního serveru nebo procesového serveru do Azure. Pokud název a klíč účtu úložiště během profilace nepředáte, nástroj dosažitelnou propustnost počítat nebude.

Můžete spouštět více instancí nástroje pro různé sady virtuálních počítačů. Zkontrolujte, že se názvy virtuálních počítačů v sadách profilace neopakují. Pokud například profilujete deset virtuálních počítačů (VM1–VM10) a po několika dnech chcete profilovat dalších pět virtuálních počítačů (VM11–VM15), můžete nástroj spustit z jiné konzoly příkazového řádku pro druhou sadu virtuálních počítačů (VM11–VM15). Zajistěte, aby druhá sada virtuálních počítačů neobsahovala žádné názvy virtuálních počítačů z první instance profilace, nebo pro druhé spuštění použijte jiný výstupní adresář. Pokud se k profilování stejných virtuálních počítačů používají dvě instance nástroje a stejný výstupní adresář, vygenerovaná sestava bude nepřesná.

Ve výchozím nastavení je tento nástroj konfigurovaný tak, aby profiloval a generoval sestavy až pro 1000 virtuálních počítačů. Tento limit můžete změnit nastavením hodnoty klíče MaxVMsSupported v souboru *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Pokud chcete s výchozím nastavením profilovat řekněme 1 500 virtuálních počítačů, vytvořte dva soubory VMList.txt. Jeden s 1000 virtuálních počítačů a druhý se seznamem 500 virtuálních počítačů. Spusťte dvě instance Plánovače nasazení ASR, jednu s VMList1.txt druhou s VMList2.txt. K uložení profilovaných dat virtuálních počítačů z obou seznamů VMList můžete použít stejnou adresářovou cestu.

Viděli jsme, že na základě konfigurace hardwaru, zejména velikosti RAM na serveru, ze kterého se spouští nástroj pro vygenerování sestavy, může operace selhat z důvodu nedostatku paměti. Pokud máte kvalitní hardware, můžete změnit MaxVMsSupported na libovolnou vyšší hodnotu.  

Pokud máte několik serverů vCenter, musíte pro každý z nich spustit jednu instanci ASRDeploymentPlanner pro účely profilování.

Konfigurace virtuálních počítačů se zachytí jednou na začátku operace profilace a uloží se do souboru VMDetailList.xml. Tyto informace se použijí při generování sestavy. Žádné změny v konfiguraci virtuálních počítačů (například navýšení počtu jader, disků nebo síťových adaptérů) od začátku do konce profilace se nezachytí. Pokud se během profilace změnila konfigurace některého profilovaného virtuálního počítače, ve verzi Public Preview existuje alternativní řešení, pomocí kterého můžete získat nejnovější podrobnosti o virtuálním počítači bez nutnosti generovat sestavu:

* Zálohujte soubor VMdetailList.xml a odstraňte ho z aktuálního umístění.
* V okamžiku generování sestav předejte argumenty -User a -Password.

Příkaz profilace vygeneruje v adresáři profilace několik souborů. Žádný z těchto souborů neodstraňujte, jinak to bude mít dopad na generování sestav.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Příklad 1: Profilování virtuálních počítačů po dobu 30 dnů a zjištění propustnosti z místního prostředí do Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Příklad 2: Profilování virtuálních počítačů po dobu 15 dnů

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Příklad 3: Rychlé otestování nástroje profilováním virtuálních počítačů po dobu 60 minut
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Příklad 4: Profilace virtuálních počítačů po dobu 2 hodin pro testování konceptu
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Pokud se server, na kterém je nástroj spuštěný, restartuje nebo na něm dojde k chybě, nebo pokud nástroj zavřete stisknutím kombinace kláves Ctrl + C, profilovaná data se zachovají. Může se však stát, že kvůli tomu bude chybět posledních 15 minut profilovaných dat. V takové situaci po restartování serveru znovu spusťte nástroj v režimu profilace.
>* Pokud předáte název a klíč účtu úložiště, nástroj bude měřit propustnost v posledním kroku profilace. Pokud bude nástroj zavřen před dokončením profilace, propustnost se nevypočítá. Pokud chcete zjistit propustnost před generováním sestavy, můžete z konzoly příkazového řádku spustit operaci GetThroughput. Jinak vygenerovaná sestava nebude obsahovat informace o propustnosti.


## <a name="generate-report"></a>Generování sestav
Nástroj jako výstup sestavy generuje soubor aplikace Microsoft Excel s podporou maker (soubor XLSM), který shrnuje veškerá doporučení pro nasazení. Sestava má název DeploymentPlannerReport_<unique numeric identifier>.xlsm a je umístěná v zadaném adresáři.

Po dokončení profilace můžete nástroj spustit v režimu generování sestav. Následující tabulka obsahuje seznam povinných a volitelných parametrů nástroje pro spuštění v režimu generování sestav.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Název parametru | Popis |
|-|-|
| -Operation | GenerateReport |
| -Server |  Plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo vSphere (použijte stejný název nebo IP adresu, jako jste použili při profilaci), na němž jsou umístěné profilované virtuální počítače, pro které se má vygenerovat sestava. Všimněte si, že pokud jste při profilaci použili server vCenter, nemůžete pro generování sestav použít server vSphere a naopak.|
| -VMListFile | Soubor se seznamem profilovaných virtuálních počítačů, pro které se má vygenerovat sestava. Cesta k souboru může být absolutní nebo relativní. Soubor musí na každém řádku obsahovat jeden název nebo IP adresu virtuálního počítače. Názvy virtuálních počítačů zadané v tomto souboru musí být stejné jako názvy virtuálních počítačů na serveru vCenter nebo hostiteli vSphere ESXi a musí se shodovat s názvy, které jste použili při profilaci.|
|-Virtualization|Určuje typ virtualizace (VMware nebo Hyper-V).|
| -Directory | (Volitelné) Název UNC nebo cesta k místnímu adresáři, ve kterém jsou uložena profilovaná data (soubory vytvořené během profilace). Tato data jsou vyžadovaná k vygenerování sestavy. Pokud název nezadáte, použije se adresář ProfiledData. |
| -GoalToCompleteIR | (Volitelné) Počet hodin, během kterých je potřeba dokončit prvotní replikaci profilovaných virtuálních počítačů. Vygenerovaná sestava obsahuje počet virtuálních počítačů, pro které lze v zadaném čase dokončit prvotní replikaci. Výchozí hodnota je 72 hodin. |
| -User | (Volitelné) Uživatelské jméno pro připojení k serveru vCenter nebo vSphere. Uživatelské jméno slouží k načtení nejnovějších informací o konfiguraci virtuálních počítačů (např. počet disků, počet jader a počet síťových adaptérů), které se použijí v sestavě. Pokud uživatelské jméno nezadáte, použijí se informace o konfiguraci shromážděné na začátku profilace. |
| -Password | (Volitelné) Heslo, které se použije pro připojení k serveru vCenter nebo hostiteli vSphere ESXi. Pokud heslo nezadáte jako parametr, budete k jeho zadání vyzváni později při spuštění příkazu. |
|-Port|(Volitelné) Číslo portu pro připojení k hostiteli vCenter/ESXi. Výchozí port je 443.|
|-Protocol|(Volitelné) Určuje protokol (http nebo https) pro připojení k serveru vCenter. Výchozím protokolem je https.|
| -DesiredRPO | (Volitelné) Požadovaný cíl bodu obnovení v minutách. Výchozí hodnota je 15 minut.|
| -Bandwidth | Šířka pásma v Mb/s. Tento parametr se použije k výpočtu cíle bodu obnovení, kterého lze pro zadanou šířku pásma dosáhnout. |
| -StartDate | (Volitelné) Počáteční datum a čas ve formátu MM-DD-YYYY:HH:MM (ve 24hodinovém formátu). Parametr *StartDate* je nutné zadat společně s parametrem *EndDate*. Pokud zadáte parametr StartDate, sestava se vygeneruje pro profilovaná data shromážděná mezi StartDate a EndDate. |
| -EndDate | (Volitelné) Koncové datum a čas ve formátu MM-DD-YYYY:HH:MM (ve 24hodinovém formátu). Parametr *EndDate* je nutné zadat společně s parametrem *StartDate*. Pokud zadáte parametr EndDate, sestava se vygeneruje pro profilovaná data shromážděná mezi StartDate a EndDate. |
| -GrowthFactor | (Volitelné) Faktor růstu vyjádřený v procentech. Výchozí hodnota je 30 procent. |
| -UseManagedDisks | (Volitelné) UseManagedDisks – Yes/No (Ano/Ne). Výchozí hodnota je Yes (Ano). Počet virtuálních počítačů, které lze umístit do jednoho účtu úložiště, se vypočítá s ohledem na to, že převzetí služeb při selhání nebo testovací převzetí služeb při selhání virtuálních počítačů se provádí na spravovaný disk namísto nespravovaného disku. |
|-SubscriptionId |(Volitelné) GUID předplatného. Tento parametr slouží k vytvoření sestavy odhadu nákladů s nejnovějšími cenami na základě vašeho předplatného a nabídky, která je přidružená k vašemu předplatnému, a pro zadanou cílovou oblast Azure v zadané měně.|
|-TargetRegion|(Volitelné) Oblast Azure, která je cílem replikace. Vzhledem k tomu, že Azure má různé náklady pro jednotlivé oblasti, pro generování sestavy s konkrétní cílovou oblastí Azure použijte tento parametr.<br>Výchozí hodnota je WestUS2 nebo poslední použitá cílová oblast.<br>K dispozici je seznam [podporovaných cílových oblastí](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Volitelné) Nabídka přidružená k danému předplatnému. Výchozí hodnota je MS-AZR-0003P (průběžné platby).|
|-Currency|(Volitelné) Měna, ve které se ve vygenerované sestavě zobrazí náklady. Výchozí hodnota je americký dolar ($) nebo poslední použitá měna.<br>K dispozici je seznam [podporovaných měn](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Ve výchozím nastavení je tento nástroj konfigurovaný tak, aby profiloval a generoval sestavy až pro 1000 virtuálních počítačů. Tento limit můžete změnit nastavením hodnoty klíče MaxVMsSupported v souboru *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Příklad 1: Generování sestavy s použitím výchozích hodnot pro profilovaná data umístěná na místním disku
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Příklad 2: Generování sestavy pro profilovaná data umístěná na vzdáleném serveru
Ke vzdálenému adresáři musíte mít přístup ke čtení a zápisu.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Příklad 3: Generování sestavy s použitím konkrétní šířky pásma a cíle dokončení prvotní replikace v zadaném čase
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Příklad 4: Generování sestavy s použitím 5% faktoru růstu namísto výchozích 30 %
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualzation VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Příklad 5: Generování sestavy s použitím podmnožiny profilovaných dat
Máte například profilovaná data za 30 dnů a chcete vygenerovat sestavu pouze pro 20 dnů.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Příklad 6: Generování sestavy s použitím 5minutového cíle bodu obnovení
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Příklad 7: Generování sestavy pro oblast Azure Indie – jih s indickými rupiemi a konkrétním ID nabídky
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Hodnota percentilu používaná k výpočtu
**Jakou výchozí hodnotu percentilu metrik výkonu shromážděných během profilace nástroj používá při generování sestavy?**

Nástroj ve výchozím nastavení používá hodnoty 95. percentilu počtu R/W IOPS, vstupně-výstupních operací zápisu za sekundu a četnosti změn dat shromážděných během profilace všech virtuálních počítačů. Tato metrika zajišťuje, že se k určení požadavků na cílový účet úložiště a zdrojovou šířku pásma nepoužijí hodnoty 100. percentilu (špičky), které se můžou objevovat na virtuálních počítačích následkem dočasných událostí. Příkladem dočasné události může být úloha zálohování spouštěná jednou denně, pravidelné indexování databáze, aktivita generování analytických sestav nebo další podobné krátkodobé a jednorázové události.

Použitím hodnot 95. percentilu získáte pravdivou představu o skutečných charakteristikách úloh a nejlepší výkon při spouštění těchto úloh v Azure. Neočekáváme, že byste toto číslo potřebovali měnit. Pokud tuto hodnotu přesto změníte (například na 90. percentil), můžete upravit konfigurační soubor *ASRDeploymentPlanner.exe.config* ve výchozí složce a uložit jej. Tím vygenerujete novou sestavu pro existující profilovaná data.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Aspekty faktoru růstu
**Proč bych při plánování nasazení měl brát v úvahu faktor růstu?**

Je důležité počítat s nárůstem v charakteristikách vašich úloh v důsledku možného zvýšení využití v průběhu času. Pokud se charakteristiky vašich úloh změní po zapnutí ochrany, nebudete moci přepnout na ochranu pod jiným účtem úložiště bez nutnosti ochranu zakázat a znovu povolit.

Řekněme například, že dnes se váš virtuální počítač vejde do účtu replikace služby Storage úrovně Standard. Během následujících tří měsíců pravděpodobně dojde k několika změnám:

* Zvýší se počet uživatelů aplikace spuštěné na virtuálním počítači.
* Výsledná zvýšená četnost změn na virtuálním počítači bude vyžadovat přesun virtuálního počítače do účtu služby Storage úrovně Premium, aby replikace Site Recovery mohla s četností změn držet krok.
* V důsledku toho budete muset zakázat a znovu povolit ochranu účtu služby Storage úrovně Premium.

Důrazně doporučujeme, abyste při plánování nasazení počítali s růstem, a přestože je jeho hodnota ve výchozím nastavení 30 %, sami nejlépe znáte vzorce používání vaší aplikace a očekávaný růst a během generování sestav můžete toto číslo odpovídajícím způsobem měnit. Kromě toho můžete generovat více sestav s použitím různých faktorů růstu pro stejná profilovaná data a určit, jaká doporučení ohledně cílového úložiště a zdrojové šířky pásma jsou pro vás nejvhodnější.

Vygenerovaná sestava aplikace Microsoft Excel obsahuje následující informace:

* [On-premises summary](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary) (Přehled místního prostředí)
* [Recommendations](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) (Doporučení)
* [VM&lt;-&gt;Storage Placement](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement) (Umístění virtuálních počítačů ve službě Storage)
* [Compatible VMs](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms) (Kompatibilní virtuální počítače)
* [Incompatible VMs](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms) (Nekompatibilní virtuální počítače)
* [Cost Estimation](site-recovery-vmware-deployment-planner-cost-estimation.md) (Odhad nákladů)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Zjištění propustnosti

Pokud chcete odhadnout propustnost, které může Site Recovery dosáhnout během replikace z místního prostředí do Azure, spusťte nástroj v režimu GetThroughput. Nástroj vypočítá propustnost ze serveru, na kterém je spuštěný. Ideálně je tento server vytvořený podle pokynů k nastavení konfiguračního serveru. Pokud jste již místně nasadili komponenty infrastruktury Site Recovery, spusťte nástroj na konfiguračním serveru.

Otevřete konzolu příkazového řádku a přejděte do složky nástroje pro plánování nasazení Site Recovery. Spusťte ASRDeploymentPlanner.exe s použitím následujících parametrů.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Název parametru | Popis |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Určuje typ virtualizace (VMware nebo Hyper-V).|
| -Directory | (Volitelné) Název UNC nebo cesta k místnímu adresáři, ve kterém jsou uložena profilovaná data (soubory vytvořené během profilace). Tato data jsou vyžadovaná k vygenerování sestavy. Pokud název adresáře není zadaný, použije se adresář ProfiledData. |
| -StorageAccountName | Název účtu úložiště, který se použije k zjištění využité šířky pásma pro replikaci místních dat do Azure. Nástroj zjistí využitou šířku pásma tak, že do tohoto účtu úložiště nahrává testovací data. Účet úložiště musí být verze 1 pro obecné účely nebo úložiště verze 2 (verze 2 pro obecné účely).|
| -StorageAccountKey | Klíč účtu úložiště, který se použije pro přístup k účtu úložiště. Přejděte na web Azure Portal > Účty úložiště > <*název účtu služby Storage*> > Nastavení > Přístupové klíče > Klíč1 (nebo primární přístupový klíč v případě klasického účtu úložiště). |
| -VMListFile | Soubor se seznamem virtuálních počítačů určených k profilaci pro výpočet využité šířky pásma. Cesta k souboru může být absolutní nebo relativní. Soubor musí na každém řádku obsahovat jeden název nebo IP adresu virtuálního počítače. Názvy virtuálních počítačů zadané v souboru se musí shodovat s názvy virtuálních počítačů na serveru vCenter nebo hostiteli vSphere ESXi.<br>Například soubor VMList.txt obsahuje následující virtuální počítače:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (Volitelné) Toto je vaše cílové prostředí účtu Azure Storage. Může to být jedna ze tří hodnot – AzureCloud, AzureUSGovernment a AzureChinaCloud. Výchozí hodnota je AzureCloud. Tento parametr použijte, pokud vaší cílovou oblastí Azure jsou cloudy Azure US Government nebo Azure China. |

Nástroj vytvoří v zadaném adresáři několik souborů „asrvhdfile<#>.vhd“ (kde # je počet souborů) o velikosti 64 MB. Nástroj tyto soubory nahraje do účtu úložiště a tak zjistí propustnost. Po změření propustnosti nástroj všechny tyto soubory odstraní z účtu úložiště i z místního serveru. Pokud se nástroj z nějakého důvodu během výpočtu propustnosti ukončí, z úložiště ani z místního serveru tyto soubory neodstraní. Budete je muset odstranit ručně.

Propustnost se měří k určitému bodu v čase a je to maximální propustnost, které Site Recovery může během replikace dosáhnout za předpokladu, že všechny ostatní okolnosti budou stejné. Například pokud ve stejné síti nějaká aplikace začne využívat větší šířku pásma, skutečná propustnost během replikace se bude lišit. Pokud spustíte příkaz GetThroughput z konfiguračního serveru, nástroj nebude vědět o žádných chráněných virtuálních počítačích ani probíhajících replikacích. Výsledná naměřená propustnost se bude lišit, když operaci GetThroughput spustíte v době vysoké četnosti změn dat na chráněných virtuálních počítačích. Doporučujeme spouštět nástroj během profilace v různých okamžicích, abyste zjistili, jaké úrovně propustnosti můžete dosáhnout v různou dobu. V sestavě zobrazí nástroj poslední naměřenou propustnost.

### <a name="example"></a>Příklad
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Spouštějte nástroj na serveru, který má stejné charakteristiky úložiště a procesoru jako konfigurační server.
>
> Pro účely replikace nastavte šířku pásma doporučenou pro splnění cíle bodu obnovení 100 % času. Pokud se po nastavení správné šířky pásma nezvýší dosažená propustnost, kterou nástroj hlásí, proveďte následující:
>
>  1. Zkontrolujte, jestli v síti není nastavená technologie QoS (Quality of Service), která by omezovala propustnost Site Recovery.
>
>  2. Zkontrolujte, jestli je váš trezor Site Recovery v nejbližší podporované fyzické oblasti Microsoft Azure kvůli minimalizaci latence sítě.
>
>  3. Zkontrolujte charakteristiky vašeho místního úložiště a zjistěte, jestli můžete vylepšit hardware (například použít jednotky SSD místo HDD).
>
>  4. Změňte nastavení Site Recovery na procesovém serveru a [zvětšete šířku pásma sítě používanou k replikaci](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Další kroky
* [Analýza vygenerované sestavy](site-recovery-vmware-deployment-planner-analyze-report.md)
