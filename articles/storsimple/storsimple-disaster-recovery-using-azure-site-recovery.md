---
title: Automatizace služby StorSimple sdílení souborů DR s azure site recovery | Dokumenty společnosti Microsoft
description: Popisuje kroky a osvědčené postupy pro vytvoření řešení zotavení po havárii pro sdílené složky hostované v úložišti Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875394"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatizované řešení zotavení po havárii pomocí Azure Site Recovery pro sdílené složky hostované na StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Microsoft Azure StorSimple je řešení hybridního cloudového úložiště, které řeší složitosti nestrukturovaných dat běžně spojených se sdílenými složkami. StorSimple používá cloudové úložiště jako rozšíření místního řešení a automaticky vrství data napříč místním úložištěm a cloudovým úložištěm. Integrovaná ochrana dat s místními a cloudovými snímky eliminuje potřebu rozsáhlé infrastruktury úložišť.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) je služba založená na Azure, která poskytuje funkce zotavení po havárii (DR) orchestrací replikace, převzetí služeb při selhání a obnovení virtuálních počítačů. Azure Site Recovery podporuje řadu replikačních technologií konzistentně replikovat, chránit a bezproblémově převzetí služeb při selhání virtuálních počítačů a aplikací do privátních/veřejných nebo hostovaných cloudů.

Pomocí Azure Site Recovery, replikace virtuálního počítače a možností snímkového snímku StorSimple můžete chránit celé prostředí souborového serveru. V případě přerušení můžete pomocí jediného kliknutí převést sdílené složky online v Azure během několika minut.

Tento dokument podrobně vysvětluje, jak můžete vytvořit řešení pro zotavení po havárii pro sdílené složky hostované v úložišti StorSimple a provést plánované, neplánované a testovací převzetí služeb při selhání pomocí plánu obnovení jedním kliknutím. V podstatě ukazuje, jak můžete upravit plán obnovení v trezoru obnovení webu Azure povolit StorSimple převzetí služeb při selhání během scénáře katastrofy. Kromě toho popisuje podporované konfigurace a požadavky. Tento dokument předpokládá, že jste obeznámeni se základy Azure Site Recovery a StorSimple architektury.

## <a name="supported-azure-site-recovery-deployment-options"></a>Podporované možnosti nasazení azure site recovery
Zákazníci mohou nasadit souborové servery jako fyzické servery nebo virtuální počítače (VM) spuštěné v technologii Hyper-V nebo VMware a potom vytvářet sdílené složky ze svazků vytesaných z úložiště StorSimple. Azure Site Recovery můžete chránit fyzické i virtuální nasazení do sekundární lokality nebo do Azure. Tento dokument obsahuje podrobnosti o řešení zotavení po havárii s Azure jako lokalita pro obnovení pro virtuální počítač souborového serveru hostované na Hyper-V a sdílené složky na storSimple úložiště. Další scénáře, ve kterých je virtuální počítač se souborovým serverem na virtuálním počítači VMware nebo fyzickém počítači, lze implementovat podobně.

## <a name="prerequisites"></a>Požadavky
Implementace řešení zotavení po havárii jedním kliknutím, které používá Azure Site Recovery pro sdílené složky hostované v úložišti StorSimple, má následující předpoklady:

   - Místní virtuální počítač se souborovým serverem Windows Server 2012 R2 hostovaný v hyperv nebo vmware nebo ve fyzickém počítači
   - Místní paměťové zařízení StorSimple registrované u správce Azure StorSimple
   - StorSimple Cloud Appliance vytvořené ve správci Azure StorSimple. Přístroj lze uchovávat v vypnutém stavu.
   - Sdílené složky hostované na svazcích nakonfigurovaných na paměťovém zařízení StorSimple
   - [Trezor služeb Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) vytvořený v předplaceném Microsoft Azure

Navíc pokud je Azure vaším místem pro obnovení, spusťte [nástroj Azure Virtual Machine Readiness Assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na virtuálních počítačích, abyste zajistili, že jsou kompatibilní s virtuálními počítači Azure a službami Azure Site Recovery.

Chcete-li se vyhnout problémům s latencí (což může mít za následek vyšší náklady), vytvořte si StorSimple Cloud Appliance, účet automatizace a účty úložiště ve stejné oblasti.

## <a name="enable-dr-for-storsimple-file-shares"></a>Povolení zotavení po havárii pro sdílené složky StorSimple
Každá součást místního prostředí musí být chráněna, aby bylo možné provést úplnou replikaci a obnovení. Tato část popisuje, jak:
    
   - Nastavení replikace služby Active Directory a DNS (volitelné)
   - Povolení ochrany virtuálního počítače se souborovým serverem pomocí Azure Site Recovery
   - Povolit ochranu svazků StorSimple
   - Konfigurace sítě

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Nastavení replikace služby Active Directory a DNS (volitelné)
Chcete-li chránit počítače se službou Active Directory a službou DNS tak, aby byly k dispozici na webu zotavení po Havárii, je třeba je explicitně chránit (aby byly souborové servery přístupné po převzetí služeb při selhání s ověřováním). Existují dvě doporučené možnosti založené na složitosti místního prostředí zákazníka.

#### <a name="option-1"></a>Možnost 1
Pokud má zákazník malý počet aplikací, jeden řadič domény pro celou místní lokalitu a bude selhání celé lokality, doporučujeme použít replikaci Azure Site Recovery k replikaci počítače řadiče domény do sekundárního (to platí jak pro web-site, tak site-to-Azure).

#### <a name="option-2"></a>2. možnost
Pokud má zákazník velký počet aplikací, používá doménovou strukturu služby Active Directory a bude najednou překračovat některé aplikace, doporučujeme nastavit další řadič domény v lokalitě zotavení po Havárii (sekundární lokalita nebo v Azure).

Pokyny při zpřístupňování řadiče domény na webu zotavení po Havárii naleznete v části [Automatické řešení zotavení po havárii pro službu Active Directory a DNS pomocí azure site recovery.](../site-recovery/site-recovery-active-directory.md) Pro zbytek tohoto dokumentu předpokládáme, že řadič domény je k dispozici na webu zotavení po Havárii.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Povolení ochrany virtuálního počítače se souborovým serverem pomocí Azure Site Recovery
Tento krok vyžaduje, abyste připravili místní prostředí souborového serveru, vytvořili a připravili trezor obnovení webu Azure a povolili ochranu souborů virtuálního počítače.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Příprava místního prostředí souborového serveru
1. Nastavte **řízení uživatelských účtů** na **Nikdy neupozorňovat**. To je nutné, abyste mohli pomocí skriptů azure automatizace připojit cíle iSCSI po převzetí služeb při selhání pomocí Azure Site Recovery.
   
   1. Stiskněte klávesu Windows +Q a vyhledejte **kód UAC**.  
   1. Vyberte **možnost Změnit nastavení řízení uživatelských účtů**.  
   1. Přetáhněte pruh dolů směrem k **nikdy neupozornit**.  
   1. Klepněte na tlačítko **OK** a po zobrazení výzvy vyberte **možnost Ano.**  
   
      ![Nastavení řízení uživatelských účtů](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Nainstalujte agenta virtuálního počítače na každý virtuální počítač souborového serveru. To je povinné, aby bylo možné spouštět skripty automatizace Azure na službách se selháním virtuálních počítačů.
   
   1. [Stáhněte si agenta](https://aka.ms/vmagentwin) do . `C:\\Users\\<username>\\Downloads`
   1. Spusťte prostředí Windows PowerShell v režimu správce (Spustit jako správce) a zadejte následující příkaz pro přechod do umístění pro stahování:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Název souboru se může změnit v závislosti na verzi.
      
1. Klikněte na **Další**.
1. Přijměte **smluvní podmínky** a klepněte na tlačítko **Další**.
1. Klikněte na **Finish** (Dokončit).
1. Vytvořte sdílené složky pomocí svazků vytesaných z úložiště StorSimple. Další informace naleznete [v tématu Správa svazků pomocí služby StorSimple Manager](storsimple-manage-volumes.md).
   
   1. Na místních virtuálních počítačích stiskněte klávesu Windows +Q a vyhledejte **iSCSI**.
   1. Vyberte **iniciátor iSCSI**.
   1. Vyberte kartu **Konfigurace** a zkopírujte název iniciátoru.
   1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
   1. Vyberte kartu **StorSimple** a pak vyberte službu StorSimple Manager, která obsahuje fyzické zařízení.
   1. Vytvořte kontejnery svazků a potom vytvořte objem(y). (Tyto svazky jsou určeny pro sdílené složky na virtuálních počítačích souborového serveru). Zkopírujte název iniciátoru a při vytváření svazků uveďte odpovídající název záznamů řízení přístupu.
   1. Vyberte kartu **Konfigurovat** a poznamenejte si adresu IP zařízení.
   1. Na místních virtuálních počítačích přejděte znovu na **iniciátor iSCSI** a zadejte IP adresu v části Rychlé připojení. Klikněte na **Rychlé připojení** (zařízení by teď mělo být připojeno).
   1. Otevřete portál Azure a vyberte kartu **Auto Configure** **Svazky a zařízení.** Svazek, který jste vytvořili, by se měl zobrazit.
   1. Na portálu vyberte kartu **Zařízení** a pak vyberte **Vytvořit nové virtuální zařízení.** (Toto virtuální zařízení se použije, pokud dojde k převzetí služeb při selhání). Toto nové virtuální zařízení lze uchovávat v režimu offline, aby se zabránilo dodatečným nákladům. Pokud chcete virtuální zařízení převést do režimu offline, přejděte do části **Virtuální počítače** na portálu a vypněte ho.
   1. Vraťte se k místním virtuálním počítačům a otevřete správu disků (stiskněte klávesu Windows + X a vyberte **Správu disků).**
   1. Všimněte si některých dalších disků (v závislosti na počtu svazků, které jste vytvořili). Klepněte pravým tlačítkem myši na první, vyberte **možnost Inicializovat disk**a vyberte **ok**. Klepněte pravým tlačítkem myši na oddíl **Nepřiděleno,** vyberte **nový jednoduchý svazek**, přiřaďte mu písmeno jednotky a dokončete průvodce.
   1. Opakujte krok l pro všechny disky. Nyní můžete zobrazit všechny disky v **tomto počítači** v Průzkumníkovi Windows.
   1. K vytvoření sdílených složek na těchto svazcích použijte roli Soubor a služba úložiště.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Vytvoření a příprava trezoru obnovení webu Azure
Před ochranou virtuálního počítače na [souborovém](../site-recovery/site-recovery-hyper-v-site-to-azure.md) serveru najdete v dokumentaci k obnovení webu Azure.

#### <a name="to-enable-protection"></a>Chcete-li povolit ochranu
1. Odpojte cíle iSCSI od místních virtuálních počítačů, které chcete chránit prostřednictvím Azure Site Recovery:
   
   1. Stiskněte klávesu Windows + Q a vyhledejte **iSCSI**.
   1. Vyberte **Nastavit iniciátor iSCSI**.
   1. Odpojte zařízení StorSimple, které jste připojili dříve. Případně můžete při povolení ochrany na několik minut vypnout souborový server.
      
   > [!NOTE]
   > To způsobí, že sdílené složky budou dočasně nedostupné.
   
1. [Povolte ochranu virtuálního počítače](../site-recovery/site-recovery-hyper-v-site-to-azure.md) virtuálního počítače na portálu Azure Site Recovery.
1. Po zahájení počáteční synchronizace můžete cíl znovu připojit. Přejděte na iniciátor iSCSI, vyberte zařízení StorSimple a klepněte na **tlačítko Připojit**.
1. Po dokončení synchronizace a stav virtuálního počítače je **chráněn ,** vyberte virtuální počítač, vyberte **kartu Konfigurovat** a odpovídajícím způsobem aktualizujte síť virtuálního počítače (toje síť, jejíž součástí bude převzetí počítače s připojením k selhání). Pokud se síť nezobrazí, znamená to, že synchronizace stále probíhá.

### <a name="enable-protection-of-storsimple-volumes"></a>Povolit ochranu svazků StorSimple
Pokud jste nevybrali možnost **Povolit výchozí zálohu pro tuto možnost svazku** StorSimple, přejděte na **zásady zálohování** ve službě StorSimple Manager a vytvořte vhodné zásady zálohování pro všechny svazky. Doporučujeme nastavit frekvenci zálohování na cíl bodu obnovení (RPO), který chcete zobrazit pro aplikaci.

### <a name="configure-the-network"></a>Konfigurace sítě
Pro virtuální počítač se souborovým serverem nakonfigurujte nastavení sítě v Azure Site Recovery tak, aby sítě virtuálních počítače byly připojené ke správné síti ZOTAVENÍ po převzetí služeb při selhání.

Virtuální počítače můžete vybrat na kartě **Replikované položky** a nakonfigurovat nastavení sítě, jak je znázorněno na následujícím obrázku.

![Výpočetní prostředky a síť](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
Můžete vytvořit plán obnovení v asr automatizovat proces převzetí služeb při selhání sdílených složek. Pokud dojde k narušení, můžete soubory během několika minut vyvolat jediným kliknutím. K povolení této automatizace budete potřebovat účet azure automatizace.

#### <a name="to-create-an-automation-account"></a>Vytvoření účtu automatizace
1. Přejděte do &gt; části **Automatizace** portálu Azure.
1. Klikněte na **tlačítko + Přidat,** otevře se pod nožem.
   
   ![Přidání účtu Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Název - zadejte nový účet automatizace
   - Předplatné – zvolte předplatné
   - Skupina prostředků – vytvoření nové/výběr existující skupiny prostředků
   - Umístění – zvolte umístění, uchovávejte ji ve stejné geograficky/oblasti, ve které byly vytvořeny účty StorSimple Cloud Appliance a Storage.
   - Vytvořit účet Azure Run As – možnost **Ano.**
   
1. Přejděte na účet Automation a kliknutím na **Galerii procházení** **sad Runbook** &gt; importujte všechny požadované sady Runbook do účtu automatizace.
1. Přidejte následující runbooky vyhledáním značky **zotavení po havárii** v galerii:
   
   - Vyčištění svazků StorSimple po selhání testu (TFO)
   - Převzetí služeb při selhání StorJednoduché kontejnery svazků
   - Připojení svazků na zařízení StorSimple po převzetí služeb při selhání
   - Odinstalace rozšíření vlastního skriptu ve virtuálním počítači Azure
   - Spuštění virtuálního zařízení StorSimple
   
      ![Galerie Procházení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publikujte všechny skripty výběrem runbooku v účtu automatizace a klikněte na **Upravit** &gt; **publikovat** a potom na **Ano** na ověřovací zprávu. Po tomto kroku se karta **Runbook** zobrazí takto:
   
   ![Runbooky](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. V účtu automatizace klikněte na **Proměnné** &gt; **Přidat proměnnou** a přidejte následující proměnné. Tyto datové zdroje můžete zašifrovat. Tyto proměnné jsou specifické pro plán obnovení. Pokud váš plán obnovení, který vytvoříte v dalším kroku, název je TestPlan, pak proměnné by měly být TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName a tak dále.

   - **BaseUrl:** Adresa URL Správce prostředků pro cloud Azure. Získejte pomocí **Get-AzEnvironment | Příkaz Select-Object, rutina ResourceManagerUrl.**
   - _RecoveryPlanName_**-ResourceGroupName**: Skupina Správce prostředků, která má prostředek StorSimple.
   - _RecoveryPlanName_**-ManagerName**: Prostředek StorSimple, který má zařízení StorSimple.
   - _RecoveryPlanName_**-DeviceName:** Zařízení StorSimple, které musí být převzetí služeb při selhání.
   - _RecoveryPlanName_**-DeviceIpAddress**: IP adresa zařízení (to lze nalézt na kartě **Zařízení** v části &gt; **StorSimple** &gt; Device Manager nastavení **skupiny Nastavení síťového** &gt; **DNS nastavení).**
   - _RecoveryPlanName_**-VolumeContainers**: Řetězec kontejnerů svazků oddělených čárkami, který se vyskytuje v zařízení a které je třeba provést převzetí služeb při selhání. například: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName:** StorSimple Cloud Appliance, na kterém kontejnery mají být převzetí služeb při selhání.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: IP adresa cílového zařízení (to lze &gt; nalézt &gt; na **kartě** Nastavení **virtuálního počítače** ve skupině **Sítě).**
   - _RecoveryPlanName_**-StorageAccountName**: Název účtu úložiště, ve kterém bude uložen skript (který má spustit na počítači s selháním přes virtuální počítač). Může se jednalo o libovolný účet úložiště, který má určité místo pro dočasné uložení skriptu.
   - _RecoveryPlanName_**-StorageAccountKey**: Přístupový klíč pro výše uvedený účet úložiště.
   - _RecoveryPlanName_**-VMGUIDS**: Po ochraně virtuálního počítače azure site recovery přiřadí každému virtuálnímu počítači jedinečné ID, které poskytuje podrobnosti o převzetí služeb při selhání nad virtuálním počítačem. Chcete-li získat identifikátor VMGUID, vyberte kartu **Služby pro obnovení** a klepněte na **položku** &gt; **Vlastnosti počítačů** &gt; **pro chráněné skupiny položek** &gt; **.** Pokud máte více virtuálních počítačů, přidejte identifikátory GUID jako řetězec oddělený čárkou.

     Pokud je například název plánu obnovení fileServerpredayRP, měla by se karta **Proměnné**, **Připojení** a **Certifikáty** po přidání všech datových zdrojů zobrazit následujícím způsobem.

      ![Prostředky](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Nahrajte modul Runbook řady StorSimple 8000 do svého účtu Automation. Pomocí následujících kroků přidejte modul:
   
   1. Otevřete powershell, vytvořte novou složku & změnit adresář do složky.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Stáhněte si nuget CLI pod stejnou složku v kroku 1.
      Různé verze nuget.exe jsou k dispozici na [nuget ke stažení](https://www.nuget.org/downloads). Každý odkaz ke stažení odkazuje přímo na soubor EXE, proto soubor klepněte pravým tlačítkem myši a uložte jej do počítače, nikoli jej spouštějte z prohlížeče.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Stažení závislé sady SDK
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Vytvořte modul runbooku Azure Automation pro správu zařízení řady StorSimple 8000. Pomocí níže uvedených příkazů vytvořte soubor zip modulu automatizace.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importujte soubor zip modulu Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) vytvořený ve výše uvedeném kroku. To lze provést výběrem účtu automatizace, klepněte na **položku Moduly** v části SDÍLENÉ ZDROJE a potom klepněte na tlačítko **Přidat modul**.
   
   Po importu modulu řady StorSimple 8000 by se měla karta **Moduly** zobrazit následujícím způsobem:
   
      ![Moduly](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Přejděte do části **Služby pro obnovení** a vyberte trezor obnovení webu Azure, který jste vytvořili dříve.
1. Vyberte možnost **Plány obnovení (Obnovení webu)** ze skupiny **Spravovat** a vytvořte nový plán obnovení následujícím způsobem:
   
   - Klikněte na **tlačítko + Obnovit plán,** otevře se pod nožem.
      
      ![Vytvoření plánu obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Zadejte název plánu obnovení, zvolte Zdroj, Cíl & hodnoty modelu nasazení.
   
   - Vyberte virtuální zařízení ze skupiny ochrany, kterou chcete zahrnout do plánu obnovení, a klikněte na tlačítko **OK.**
   
   - Vyberte Plán obnovení, který jste vytvořili dříve, kliknutím na **tlačítko Přizpůsobit** otevřete zobrazení vlastního nastavení plánu obnovení.
   
   - Klikněte pravým tlačítkem myši na **příkaz Všechny skupiny vypnout** a klepněte na tlačítko **Přidat předběžnou akci**.
   
   - Otevře okno akce Vložení, zadejte název, vyberte možnost **Primární strana** v části Kde spustit možnost, vyberte účet automatizace (ve kterém jste přidali sady Runbook) a pak vyberte runbook **Failover-StorSimple-Volume-Containers.**
   
   - Klikněte pravým tlačítkem myši na **skupinu 1: Start** a klikněte na **možnost Přidat chráněné položky,** pak vyberte virtuální počítače, které mají být chráněny v plánu obnovení a klepněte na tlačítko **Ok.** Volitelné, pokud je to už vybrané virtuální chodů.
   
   - Klikněte pravým tlačítkem myši na **skupinu 1: Start** a klikněte na **možnost Přidat akci** a pak přidat všechny následující skripty:  
      
      - Runbook Start-StorSimple-Virtual-Appliance  
      - Runbook s přepojem nad-StorJednoduché svazkové kontejnery  
      - Runbook s připojením svazků po převzetí služeb při selhání  
      - Runbook Odinstalovat vlastní skript-rozšíření  
        
   - Přidejte ruční akci za výše uvedené skripty 4 ve stejné části **Skupina 1: Post-steps.** Tato akce je bod, ve kterém můžete ověřit, že vše funguje správně. Tuto akci je třeba přidat pouze jako součást test převzetí služeb při selhání (proto zaškrtněte pouze **testovací převzetí služeb při selhání** zaškrtnutí).
    
   - Po ruční akci přidejte skript **Vyčištění** stejným postupem, který jste použili pro ostatní sady Runbook. **Uložte** plán obnovení.
    
   > [!NOTE]
   > Při spuštění testu převzetí služeb při selhání, měli byste ověřit vše, co v kroku ruční akce, protože Svazky StorSimple, které byly klonovány na cílovém zařízení budou odstraněny jako součást vyčištění po dokončení ruční akce.
       
      ![Plán obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Provedení zkušebního převzetí služeb při selhání
Informace specifické pro službu Active Directory během převzetí služeb při selhání testu naleznete v doprovodné příručce [řešení služby Dr.](../site-recovery/site-recovery-active-directory.md) služby Active Directory. Místní nastavení není narušena vůbec, když dojde k převzetí služeb při selhání testu. Svazky StorSimple, které byly připojené k místnímu virtuálnímu počítači, jsou klonovány do StorSimple Cloud Appliance v Azure. Virtuální počítač pro testovací účely se zvěčňuje v Azure a klonované svazky jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-the-test-failover"></a>Provedení zkušebního převzetí služeb při selhání
1. Na webu Azure Portal vyberte trezor obnovení webu.
1. Klikněte na plán obnovení vytvořený pro virtuální server souborového serveru.
1. Klepněte na **tlačítko Testovat převzetí služeb při selhání**.
1. Vyberte virtuální síť Azure, ke které budou virtuální počítače Azure připojeny po převzetí služeb při selhání.
   
   ![Zahájit převzetí služeb při selhání](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat kliknutím na virtuální počítač a otevřete jeho vlastnosti nebo na **úloze převzetí služeb při selhání test** v úloze &gt; **Jobs** &gt; **obnovení**webu s názvem úložiště .
1. Po dokončení převzetí služeb při selhání, měli byste také být schopni &gt; zobrazit repliku počítače Azure se zobrazí ve **virtuálních počítačích**portálu Azure . Můžete provést ověření.
1. Po dokončení ověření klepněte na **tlačítko Ověření dokončeno**. Tím odeberete svazky StorSimple a vypnete zařízení StorSimple Cloud Appliance.
1. Po dokončení klikněte na **možnost Převzetí služeb při selhání testu vyčištění** v plánu obnovení. V části Poznámky si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání. Tím odstraníte virtuální počítač, který byl vytvořen během převzetí služeb při selhání testu.

## <a name="perform-a-planned-failover"></a>Provedení plánovaného převzetí služeb při selhání
   Během plánovanépřevzetí služeb při selhání je virtuální počítač místního souborového serveru řádně vypnut a je pořízen snímek cloudové zálohy svazků na zařízení StorSimple. Svazky StorSimple se nezdaří virtuálnímu zařízení, virtuální počítač repliky se zvěčňuje v Azure a svazky jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-a-planned-failover"></a>Provedení plánovaného převzetí služeb při selhání
1. Na webu Azure Portal vyberte **plány** obnovení úložiště &gt; služeb obnovení **(Site Recovery)** &gt; **recoveryplan_name** vytvořené pro virtuální počítač se souborovým serverem.
1. V okně Plán obnovení klepněte na tlačítko **Další** &gt; **plánované převzetí služeb při selhání**.  

   ![Plán obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. V okně **Potvrdit plánované převzetí služeb při selhání** zvolte zdrojová a cílová umístění a vyberte cílovou síť a klepnutím na ikonu kontroly ✓ spusťte proces převzetí služeb při selhání.
1. Po vytvoření repliky virtuálních počítačů jsou ve stavu čeká na potvrzení čeká. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.
1. Po dokončení replikace se virtuální počítače spustí v sekundárním umístění.

## <a name="perform-a-failover"></a>Provedení převzetí služeb při selhání
Během neplánované převzetí služeb při selhání svazky StorSimple se nezdaří virtuální zařízení, virtuální počítač repliky se bude zvěčnit v Azure a svazky jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-a-failover"></a>Provedení převzetí služeb při selhání
1. Na webu Azure Portal vyberte **plány** obnovení úložiště &gt; služeb obnovení **(Site Recovery)** &gt; **recoveryplan_name** vytvořené pro virtuální počítač se souborovým serverem.
1. V okně Plán obnovení klepněte na **tlačítko Další** &gt; **převzetí služeb při selhání**.  
1. V okně **Potvrdit převzetí služeb při selhání** zvolte zdrojová a cílová umístění.
1. Vyberte **Vypnout virtuální počítače a synchronizovat nejnovější data,** abyste určili, že site recovery by se měl pokusit vypnout chráněný virtuální počítač a synchronizovat data tak, aby nejnovější verze dat byla převzetí mů ly za předpokladu.
1. Po převzetí služeb při selhání jsou virtuální počítače ve stavu čekající na potvrzení. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.


## <a name="perform-a-failback"></a>Navrácení služeb po obnovení
Během navrácení služeb po službě 14.

#### <a name="to-perform-a-failback"></a>Provedení navrácení služeb po selhání
1. Na webu Azure Portal vyberte **plány** obnovení úložiště &gt; služeb obnovení **(Site Recovery)** &gt; **recoveryplan_name** vytvořené pro virtuální počítač se souborovým serverem.
1. V okně Plán obnovení klepněte na tlačítko **Další** &gt; **plánované převzetí služeb při selhání**.  
1. Vyberte zdrojová a cílová umístění, vyberte příslušné možnosti synchronizace dat a vytvoření virtuálního zařízení.
1. Klepnutím na tlačítko **OK** spusťte proces navrácení služeb po selhání.
   
   ![Spustit navrácení služeb po selhání](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Osvědčené postupy
### <a name="capacity-planning-and-readiness-assessment"></a>Plánování kapacity a posouzení připravenosti
#### <a name="hyper-v-site"></a>Lokalita Hyper-V
Pomocí [nástroje plánovače uživatelské kapacity](https://www.microsoft.com/download/details.aspx?id=39057) můžete navrhnout serverovou, úložnou a síťovou infrastrukturu pro prostředí replik hyperv.

#### <a name="azure"></a>Azure
Můžete spustit [nástroj azure virtual machine připravenosti assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na virtuálních počítačích a ujistěte se, že jsou kompatibilní s virtuálními počítači Azure a služby Obnovení webu Azure. Nástroj pro posouzení připravenosti kontroluje konfigurace virtuálních počítačů a varuje, když jsou konfigurace nekompatibilní s Azure. Například vydává upozornění, pokud je jednotka C: větší než 127 GB.

Plánování kapacity se skládá nejméně ze dvou důležitých procesů:

   - Mapování místních virtuálních počítačů Hyper-V na velikosti virtuálních zařízení Azure (například A6, A7, A8 a A9).
   - Určení požadované šířky pásma Internetu.

## <a name="limitations"></a>Omezení
- V současné době pouze 1 StorSimple zařízení může být převzetí služeb při selhání (na jeden StorSimple Cloud Appliance). Scénář souborového serveru, který zahrnuje několik zařízení StorSimple, ještě není podporován.
- Pokud se při povolení ochrany virtuálního počítače zobrazí chyba, ujistěte se, že jste odpojili cíle iSCSI.
- Všechny kontejnery svazku, které byly seskupeny z důvodu zásad zálohování zahrnující chod kontejnerů svazků, budou převzetí mů e-up společně.
- Všechny svazky v kontejnerech svazků, které jste vybrali, budou převzetí mů e-lau.
- Svazky, které přidávají více než 64 TB, nelze převést na selhání, protože maximální kapacita jednoho zařízení StorSimple Cloud Appliance je 64 TB.
- Pokud plánované/neplánované převzetí služeb při selhání selže a virtuální počítače jsou vytvořeny v Azure, pak nečistěte virtuální počítače. Místo toho proveďte navrácení služeb po selhání. Pokud odstraníte virtuální chod, nelze znovu zapnout místní virtuální počítač.
- Pokud po převzetí služeb při selhání nevidíte svazky, přejděte na virtuální počítače, otevřete správu disků, proskenujte disky a přepnotujte je do režimu online.
- V některých případech se písmena jednotek na webu zotavení po Havárii mohou lišit od písmen v místním prostředí. Pokud k tomu dojde, bude nutné problém po dokončení převzetí služeb při selhání opravit ručně.
- Časový limit úlohy převzetí služeb při selhání: Časový limit skriptu StorSimple bude časový limit, pokud převzetí služeb při selhání kontejnerů svazku trvá déle než limit obnovení webu Azure na skript (aktuálně 120 minut).
- Časový limit úlohy zálohování: Časový limit skriptu StorSimple out, pokud zálohování svazků trvá déle než limit obnovení webu Azure na skript (aktuálně 120 minut).
   
  > [!IMPORTANT]
  > Spusťte zálohování ručně z portálu Azure a pak znovu spusťte plán obnovení.
   
- Časový limit úlohy klonování: Časový limit skriptu StorSimple out, pokud klonování svazků trvá déle než limit obnovení webu Azure na skript (aktuálně 120 minut).
- Chyba synchronizace času: Skripty StorSimple chyby se říká, že zálohy byly neúspěšné, i když záloha je úspěšná na portálu. Možnou příčinou může být, že čas zařízení StorSimple může být synchronizován s aktuálním časem v časovém pásmu.
   
  > [!IMPORTANT]
  > Synchronizujte čas zařízení s aktuálním časem v časovém pásmu.
   
- Chyba převzetí služeb při selhání zařízení: Skript StorSimple může selhat, pokud je převzetí služeb při selhání zařízení při spuštění plánu obnovení.
   
  > [!IMPORTANT]
  > Po dokončení převzetí služeb při selhání zařízení znovu spusťte plán obnovení.


## <a name="summary"></a>Souhrn
Pomocí Azure Site Recovery můžete vytvořit kompletní automatizovaný plán zotavení po havárii pro virtuální počítač se souborovým serverem, který má sdílené složky hostované v úložišti StorSimple. Převzetí služeb při selhání můžete zahájit během několika sekund z libovolného místa v případě přerušení a zprovoznit aplikaci během několika minut.
