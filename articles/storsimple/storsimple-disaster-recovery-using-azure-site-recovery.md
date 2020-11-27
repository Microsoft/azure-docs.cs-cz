---
title: Automatizace sdílené složky StorSimple DR pomocí Azure Site Recovery | Microsoft Docs
description: Popisuje kroky a osvědčené postupy pro vytvoření řešení zotavení po havárii pro sdílené složky hostované v úložišti Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: e2d89718d953f05b3e5500db412ac8ac03bfa00b
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301946"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatizované řešení zotavení po havárii s využitím Azure Site Recovery pro sdílené složky hostované v StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Microsoft Azure StorSimple je řešení hybridního cloudového úložiště, které řeší složitosti nestrukturovaných dat běžně přidružených ke sdíleným složkám souborů. StorSimple používá cloudové úložiště jako rozšíření místního řešení a automaticky využívá data napříč místním úložištěm a cloudovým úložištěm. Integrovaná ochrana dat s místními a cloudovým snímky eliminuje nutnost sprawling infrastruktury úložiště.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) je služba založená na Azure, která poskytuje funkce zotavení po havárii (Dr) prostřednictvím orchestrace replikace, převzetí služeb při selhání a obnovení virtuálních počítačů. Azure Site Recovery podporuje řadu replikačních technologií pro konzistentní replikaci, ochranu a bezproblémové převzetí služeb při selhání virtuálních počítačů a aplikací do privátních a veřejných nebo hostovaných cloudů.

S využitím Azure Site Recovery, replikace virtuálních počítačů a StorSimple možností snímků cloudu můžete chránit kompletní prostředí souborového serveru. V případě výpadku můžete pomocí jediného kliknutí přenést sdílené složky online v Azure během několika minut.

Tento dokument podrobně vysvětluje, jak můžete vytvořit řešení pro zotavení po havárii pro sdílené složky hostované v úložišti StorSimple a provádět plánované, neplánované a testovací převzetí služeb při selhání pomocí plánu obnovení jedním kliknutím. V podstatě se dozvíte, jak můžete upravit plán obnovení ve vašem úložišti Azure Site Recovery a povolit převzetí služeb při selhání StorSimple během scénářů havárie. Kromě toho popisuje podporované konfigurace a předpoklady. V tomto dokumentu se předpokládá, že jste obeznámeni se základy architektury Azure Site Recovery a StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Podporované možnosti nasazení Azure Site Recovery
Zákazníci mohou nasadit souborové servery jako fyzické servery nebo virtuální počítače běžící na technologii Hyper-V nebo VMware a pak vytvořit sdílené složky ze svazků Carved z úložiště StorSimple. Azure Site Recovery může chránit fyzické i virtuální nasazení buď na sekundární lokalitu, nebo na Azure. Tento dokument obsahuje podrobné informace o řešení zotavení po havárii s Azure jako lokalitu pro obnovení pro virtuální počítač souborového serveru hostovaný na Hyper-V a s sdílenými složkami v StorSimple Storage. Další scénáře, ve kterých je virtuální počítač souborového serveru na VIRTUÁLNÍm počítači VMware nebo fyzický počítač, se dají implementovat podobně.

## <a name="prerequisites"></a>Požadavky
Implementace řešení pro zotavení po havárii typu jedním kliknutím, který používá Azure Site Recovery pro sdílené složky hostované v úložišti StorSimple, má následující požadavky:

   - Místní virtuální počítač souborového serveru Windows Server 2012 R2 hostovaný na Hyper-V nebo VMware nebo na fyzickém počítači
   - Místní zařízení úložiště StorSimple zaregistrované ve službě Azure StorSimple Manager
   - StorSimple Cloud Appliance vytvořené v Azure StorSimple Manageru. Zařízení se může uchovávat v nefunkčním stavu.
   - Sdílené složky hostované na svazcích nakonfigurovaných na zařízení úložiště StorSimple
   - [Trezor služby Azure Site Recovery](/azure/site-recovery/hyper-v-vmm-azure-tutorial) se vytvořil v předplatném Microsoft Azure.

Kromě toho, pokud je Azure vaším webem pro obnovení, spusťte na virtuálních počítačích [Nástroj pro vyhodnocení připravenosti na virtuální počítače Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) , abyste zajistili, že jsou kompatibilní s virtuálními počítači azure a Azure Site Recovery službami.

Aby nedocházelo k problémům s latencí (což může způsobit vyšší náklady), ujistěte se, že jste vytvořili StorSimple Cloud Appliance, účet služby Automation a účty úložiště ve stejné oblasti.

## <a name="enable-dr-for-storsimple-file-shares"></a>Povolit DR pro sdílené složky StorSimple
Aby bylo možné dokončit replikaci a obnovení, je třeba chránit každou součást místního prostředí. V této části se dozvíte, jak:
    
   - Nastavení služby Active Directory a replikace DNS (volitelné)
   - Povolení ochrany virtuálního počítače souborového serveru pomocí Azure Site Recovery
   - Povolit ochranu StorSimple svazků
   - Konfigurace sítě

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Nastavení služby Active Directory a replikace DNS (volitelné)
Chcete-li chránit počítače se službou Active Directory a DNS tak, aby byly k dispozici na webu DR, je nutné je explicitně chránit (aby byly souborové servery přístupné po převzetí služeb při selhání s ověřováním). Existují dvě Doporučené možnosti založené na složitosti místního prostředí zákazníka.

#### <a name="option-1"></a>Možnost 1
Pokud má zákazník malý počet aplikací, jeden řadič domény pro celou místní lokalitu a převezme služby při selhání celou lokalitu, doporučujeme, abyste pomocí Azure Site Recovery replikace replikovat počítač řadiče domény do sekundární lokality (platí pro lokalitu i pro lokalitu i pro Azure).

#### <a name="option-2"></a>Možnost 2
Pokud má zákazník velký počet aplikací, je spuštěná doménová struktura služby Active Directory a při selhání dojde v několika aplikacích najednou, doporučujeme nastavit další řadič domény na webu DR (buď sekundární lokalita, nebo v Azure).

Pokyny k dispozici pro [automatické řešení zotavení po havárii pro Active Directory a DNS pomocí Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) najdete v tématu informace o tom, jak je řadič domény dostupný na webu Dr. Ve zbývající části tohoto dokumentu předpokládáme, že je na webu DR k dispozici řadič domény.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Povolení ochrany virtuálního počítače souborového serveru pomocí Azure Site Recovery
Tento krok vyžaduje, abyste připravili místní prostředí souborového serveru, vytvořili a připravili Azure Site Recovery trezor a povolili ochranu souboru virtuálního počítače.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Příprava místního prostředí souborového serveru
1. Nastavte **řízení uživatelských účtů** na **nikdy neupozorňovat**. To je nutné, abyste mohli pomocí skriptů Azure Automation po převzetí služeb při selhání pomocí Azure Site Recovery připojit cíle iSCSI.
   
   1. Stiskněte klávesu Windows + Q a vyhledejte **Nástroj Řízení uživatelských účtů**.  
   1. Vyberte **změnit nastavení řízení uživatelských účtů**.  
   1. Přetáhněte pruh dolů k hodnotě **nikdy neupozorňovat**.  
   1. Klikněte na **OK** a po zobrazení výzvy vyberte **Ano** .  
   
      ![Nastavení řízení uživatelských účtů](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Nainstalujte agenta virtuálního počítače na všechny virtuální počítače souborového serveru. To je nutné, abyste mohli spouštět skripty Azure Automation na virtuálních počítačích převzetí služeb při selhání.
   
   1. [Stáhněte si agenta](https://aka.ms/vmagentwin) do nástroje `C:\\Users\\<username>\\Downloads` .
   1. Otevřete prostředí Windows PowerShell v režimu správce (Spustit jako správce) a potom zadejte následující příkaz, který přejde do umístění pro stahování:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Název souboru se může změnit v závislosti na verzi.
      
1. Klikněte na **Next** (Další).
1. Přijměte **podmínky smlouvy** a klikněte na tlačítko **Další**.
1. Klikněte na **Finish** (Dokončit).
1. Vytvářejte sdílené složky pomocí svazků Carved z úložiště StorSimple. Další informace najdete v tématu [Správa svazků pomocí služby StorSimple Manager](./index.yml).
   
   1. Na místních virtuálních počítačích stiskněte klávesu Windows + Q a vyhledejte **iSCSI**.
   1. Vyberte **iniciátor iSCSI**.
   1. Vyberte kartu **Konfigurace** a zkopírujte název iniciátoru.
   1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
   1. Vyberte kartu **StorSimple** a pak vyberte službu StorSimple Manager, která obsahuje fyzické zařízení.
   1. Vytvořte kontejnery svazků a pak vytvořte svazky (celkem). (Tyto svazky jsou pro sdílené složky na virtuálních počítačích souborového serveru). Zkopírujte název iniciátoru a při vytváření svazků zadejte vhodný název záznamů Access Control.
   1. Vyberte kartu **Konfigurovat** a poznamenejte si IP adresu zařízení.
   1. Na místních virtuálních počítačích znovu přejděte do **iniciátoru iSCSI** a zadejte IP adresu do oddílu rychlé připojení. Klikněte na **rychlé připojení** (zařízení by teď mělo být připojené).
   1. Otevřete Azure Portal a vyberte kartu **svazky a zařízení** . Klikněte na možnost **automaticky konfigurovat**. Svazek, který jste vytvořili, by se měl zobrazit.
   1. Na portálu vyberte kartu **zařízení** a pak vyberte **vytvořit nové virtuální zařízení.** (Toto virtuální zařízení se použije, pokud dojde k převzetí služeb při selhání.) Toto nové virtuální zařízení může být ponecháno v offline stavu, aby nedocházelo k dodatečným nákladům. Pokud chcete virtuální zařízení převést do režimu offline, přejděte na portálu na část **Virtual Machines** a vypněte ji.
   1. Vraťte se k místním virtuálním počítačům a otevřete správu disků (stiskněte klávesu Windows + X a vyberte **Správa disků**).
   1. Všimnete si některých dalších disků (v závislosti na počtu vytvořených svazků). Klikněte na první z nich pravým tlačítkem, vyberte **inicializovat disk** a pak vyberte **OK**. Klikněte pravým tlačítkem na **nepřidělený** oddíl, vyberte **Nový jednoduchý svazek**, přiřaďte mu písmeno jednotky a dokončete průvodce.
   1. Opakujte krok l pro všechny disky. Všechny disky v **tomto počítači** teď můžete zobrazit v Průzkumníkovi Windows.
   1. Pomocí role Souborová služba a služba úložiště vytvořte sdílené složky na těchto svazcích.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Vytvoření a příprava trezoru Azure Site Recovery
Informace o Azure Site Recovery před ochranou virtuálního počítače souborového serveru najdete v [dokumentaci k Azure Site Recovery](/azure/site-recovery/) .

#### <a name="to-enable-protection"></a>Povolení ochrany
1. Odpojte cíle iSCSI od místních virtuálních počítačů, které chcete chránit pomocí Azure Site Recovery:
   
   1. Stiskněte klávesy Windows + Q a vyhledejte **iSCSI**.
   1. Vyberte **Nastavení iniciátoru iSCSI**.
   1. Odpojte zařízení StorSimple, ke kterému jste se připojili dříve. Případně můžete při povolování ochrany vypnout souborového serveru na několik minut.
      
   > [!NOTE]
   > Tato akce způsobí, že sdílené složky budou dočasně nedostupné.
   
1. [Povolte ochranu virtuálního počítače pro virtuální počítač](/azure/site-recovery/hyper-v-azure-tutorial) souborového serveru z portálu Azure Site Recovery.
1. Po zahájení počáteční synchronizace můžete cíl znovu připojit. Přejděte k iniciátoru iSCSI, vyberte zařízení StorSimple a klikněte na **připojit**.
1. Po dokončení synchronizace a stavu **ochrany** virtuálního počítače vyberte virtuální počítač, vyberte kartu **Konfigurovat** a odpovídajícím způsobem aktualizujte síť virtuálního počítače (Jedná se o síť, které virtuální počítače s převzetím služeb při selhání budou součástí). Pokud se síť nezobrazí, znamená to, že synchronizace stále probíhá.

### <a name="enable-protection-of-storsimple-volumes"></a>Povolit ochranu StorSimple svazků
Pokud jste pro StorSimple svazky nevybrali možnost **Povolit výchozí zálohování pro tento svazek** , ve službě StorSimple Manager klikněte na **zásady zálohování** a vytvořte vhodné zásady zálohování pro všechny svazky. Doporučujeme, abyste nastavili četnost záloh na cíl bodu obnovení (RPO), který byste chtěli pro aplikaci zobrazit.

### <a name="configure-the-network"></a>Konfigurace sítě
Pro virtuální počítač souborového serveru nakonfigurujte v Azure Site Recovery nastavení sítě tak, aby se sítě virtuálních počítačů po převzetí služeb při selhání připojily ke správné síti DR.

Virtuální počítač můžete vybrat na kartě **replikované položky** a nakonfigurovat tak nastavení sítě, jak je znázorněno na následujícím obrázku.

![Výpočty a síť](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
V ASR můžete vytvořit plán obnovení pro automatizaci procesu převzetí služeb při selhání u sdílených složek. Pokud dojde k výpadku, můžete tyto sdílené složky přenést do několika minut jediným kliknutím. Pokud chcete tuto automatizaci povolit, budete potřebovat účet Azure Automation.

#### <a name="to-create-an-automation-account"></a>Vytvoření účtu Automation
1. Přejít do části Azure Portal &gt; **Automation** .
1. Klikněte na tlačítko **+ Přidat** tlačítko, otevře se pod oknem.
   
   ![Přidání účtu Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Název – zadejte nový účet Automation.
   - Předplatné – volba předplatného
   - Skupina prostředků – vytvořit novou/zvolit existující skupinu prostředků
   - Umístění – vyberte umístění a nechte ho ve stejné geografické oblasti, ve které se vytvořily StorSimple Cloud Appliance a účty úložiště.
   - Vytvořte účet Spustit jako pro Azure – vyberte možnost **Ano** .
   
1. Přejděte na účet Automation, klikněte na **Runbooky** &gt; **Procházet galerii** a importujte všechny požadované Runbooky do účtu Automation.
1. Přidejte následující Runbooky tak, že v galerii najdete značku **zotavení po havárii** :
   
   - Vyčištění svazků StorSimple po testovacím převzetí služeb při selhání (TFO)
   - StorSimple kontejnery svazků s podporou převzetí služeb při selhání
   - Po převzetí služeb při selhání připojit svazky na zařízení StorSimple
   - Odinstalace rozšíření vlastních skriptů na virtuálním počítači Azure
   - Spustit virtuální zařízení StorSimple
   
      ![Procházet galerii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publikujte všechny skripty tak, že vyberete Runbook v účtu Automation a kliknete na **Upravit** &gt; **publikování** a pak na ověřovací zprávu **Ano** . Po provedení tohoto kroku se zobrazí karta **Runbooky** následujícím způsobem:
   
   ![Runbooky](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. V účtu Automation klikněte na **proměnné** &gt; **přidat proměnnou** a přidejte následující proměnné. Můžete zvolit šifrování těchto prostředků. Tyto proměnné jsou specifické pro plán obnovení. Pokud máte plán obnovení, který vytvoříte v dalším kroku, název je testovací plán, proměnné by měly být testovací plán-StorSimRegKey, testovací plán-AzureSubscriptionName a tak dále.

   - **Baseurl**: adresa URL správce prostředků pro cloud Azure. Použijte rutinu **Get-AzEnvironment | Select-Object název,** rutina ResourceManagerUrl.
   - _RecoveryPlanName_**-ResourceGroupName**: skupina Správce prostředků, která má prostředek StorSimple.
   - _RecoveryPlanName_**-Manager**: prostředek StorSimple, který má zařízení StorSimple.
   - _RecoveryPlanName_**-název_zařízení**: zařízení StorSimple, u kterého se má převzít převzetí služeb při selhání.
   - _RecoveryPlanName_**-DeviceIpAddress**: IP adresa zařízení (najdete ho na kartě **zařízení** v části StorSimple Device Manager &gt; **Nastavení** části &gt; **Síťová** &gt; **Služba DNS** ).
   - _RecoveryPlanName_**-VolumeContainers**: řetězec kontejnerů svazků oddělený čárkami v zařízení, které je potřeba převzít při selhání; Příklad: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: StorSimple Cloud Appliance, na kterých mají být kontejnery převzetí služeb při selhání.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: IP adresa cílového zařízení (dá se najít na kartě síť pro nastavení části **virtuálního počítače** &gt; **Settings** &gt; **Networking** ).
   - _RecoveryPlanName_**-StorageAccountName**: název účtu úložiště, ve kterém se bude ukládat skript (který musí být spuštěný na virtuálním počítači pro převzetí služeb při selhání). Může to být libovolný účet úložiště, který obsahuje nějaké místo pro dočasné uložení skriptu.
   - _RecoveryPlanName_**-StorageAccountKey**: přístupový klíč pro výše uvedený účet úložiště.
   - _RecoveryPlanName_**-VMGUIDS**: při ochraně virtuálního počítače Azure Site Recovery přiřadí každému virtuálnímu počítači jedinečné ID, které poskytuje podrobnosti o virtuálním počítači, u kterého došlo k převzetí služeb při selhání. VMGUID získáte tak, že vyberete kartu **Recovery Services** a kliknete na položku skupiny ochrany **chráněných položek** &gt; **Protection Groups** &gt; **Machines** &gt; **vlastnosti** počítače. Pokud máte více virtuálních počítačů, přidejte identifikátory GUID jako řetězec oddělený čárkami.

     Například pokud je název plánu obnovení fileServerpredayRP, pak se vaše **proměnné**, karty **připojení** a **certifikáty** by měly zobrazit takto po přidání všech prostředků.

      ![Prostředky](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Nahrajte do svého účtu Automation modul Runbooku řady StorSimple 8000. Pomocí následujících kroků přidejte modul:
   
   1. Otevřete PowerShell, vytvořte novou složku & změňte adresář do složky.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Stáhněte si NuGet CLI ve stejné složce v Krok 1.
      Na [stažení NuGet](https://www.nuget.org/downloads)jsou k dispozici různé verze nuget.exe. Každý odkaz ke stažení odkazuje přímo na soubor. exe, proto nezapomeňte kliknout pravým tlačítkem a uložit soubor do počítače, nikoli spustit z prohlížeče.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Stažení závislé sady SDK
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Vytvořte Azure Automation modul Runbooku pro správu zařízení řady StorSimple 8000. Pomocí níže uvedených příkazů vytvořte soubor zip modulu automatizace.
         
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
         
   1. Importujte soubor zip modulu Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) vytvořený v předchozím kroku. Můžete to udělat tak, že vyberete účet Automation, kliknete na **moduly** v části sdílené prostředky a pak na **Přidat modul**.
   
   Po importu modulu řady StorSimple 8000 by se měla karta **moduly** zobrazit takto:
   
      ![Moduly](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. V části **Recovery Services** vyberte trezor Azure Site Recovery, který jste vytvořili dříve.
1. V části **Spravovat** skupinu vyberte možnost **plány obnovení (Site Recovery)** a vytvořte nový plán obnovení následujícím způsobem:
   
   - Klikněte na tlačítko **+ Obnovit plán** , otevře se pod oknem.
      
      ![Vytvoření plánu obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Zadejte název plánu obnovení, vyberte zdroj, cílový & hodnoty modelu nasazení.
   
   - Vyberte virtuální počítače ze skupiny ochrany, kterou chcete zahrnout do plánu obnovení, a klikněte na tlačítko **OK** .
   
   - Vyberte plán obnovení, který jste vytvořili dříve, kliknutím na tlačítko **přizpůsobit** otevřete zobrazení přizpůsobení plánu obnovení.
   
   - Klikněte pravým tlačítkem na **všechny skupiny vypínání** a klikněte na **přidat akci před**.
   
   - Otevře okno Vložit akci, zadejte název, vyberte možnost **primární strana** v možnosti kde spustit, vyberte účet Automation (do kterého jste přidali runbooky) a pak vyberte sadu Runbook **s podporou převzetí služeb při selhání-StorSimple-Volume-Containers** .
   
   - Klikněte pravým tlačítkem na **Skupina 1: Start** a klikněte na možnost **Přidat chráněné položky** , vyberte virtuální počítače, které chcete chránit v plánu obnovení, a klikněte na tlačítko **OK** . Volitelné, pokud už jsou vybrané virtuální počítače.
   
   - Klikněte pravým tlačítkem na **Skupina 1: spustit** a pak klikněte na možnost **po akci** a přidejte všechny následující skripty:  
      
      - Spuštění – StorSimple – Runbook virtuálního zařízení  
      - Sada Runbook při selhání-StorSimple-Volume-Containers  
      - Sada připojení-svazky-po převzetí služeb při selhání  
      - Odinstalace – vlastní Runbook – rozšíření skriptu  
        
   - Přidejte ruční akci za předchozí 4 skripty ve stejné **skupině 1: kroky po kroku** . Tato akce je bod, ve kterém můžete ověřit, že vše funguje správně. Tuto akci je třeba přidat jenom jako součást testovacího převzetí služeb při selhání (proto zaškrtněte políčko **testovací převzetí služeb při selhání** ).
    
   - Po ruční akci přidejte skript pro **Vyčištění** pomocí stejného postupu, který jste použili pro ostatní sady Runbook. **Uložte** plán obnovení.
    
   > [!NOTE]
   > Při spuštění testovacího převzetí služeb při selhání byste měli ověřit všechno na kroku ruční akce, protože svazky StorSimple, které byly naklonované na cílovém zařízení, se po dokončení ruční akce odstraní jako součást vyčištění.
       
      ![Plán obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Provedení testovacího převzetí služeb při selhání
V doprovodné příručce k [řešení Active Directory Dr](../site-recovery/site-recovery-active-directory.md) najdete podrobné informace týkající se služby Active Directory během testovacího převzetí služeb při selhání. V případě, že dojde k testovacímu převzetí služeb při selhání, místní nastavení se neruší. Svazky StorSimple připojené k místnímu virtuálnímu počítači se naklonují do StorSimple Cloud Appliance v Azure. Virtuální počítač pro testovací účely se zaplní v Azure a naklonované svazky jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-the-test-failover"></a>Postup při provádění testovacího převzetí služeb při selhání
1. V Azure Portal vyberte svůj trezor Site Recovery.
1. Klikněte na plán obnovení vytvořený pro virtuální počítač souborového serveru.
1. Klikněte na **testovací převzetí služeb při selhání**.
1. Vyberte virtuální síť Azure, ke které se připojí virtuální počítače Azure po převzetí služeb při selhání.
   
   ![Spustit převzetí služeb při selhání](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat tak, že kliknete na virtuální počítač a otevřete jeho vlastnosti, nebo na **úlohu testovací převzetí služeb při selhání** v části název trezoru úlohy &gt; **Jobs** &gt; **Site Recovery úlohy**.
1. Po dokončení převzetí služeb při selhání byste měli být schopni vidět, že se počítač Azure repliky zobrazí v &gt; **Virtual Machines** Azure Portal. Můžete provádět ověření.
1. Po dokončení platnosti klikněte na **ověřování dokončeno**. Tím se odeberou svazky StorSimple a vypne se StorSimple Cloud Appliance.
1. Až skončíte, klikněte na **Vyčištění testovacího převzetí služeb při selhání** v plánu obnovení. V části Poznámky si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání. Tato akce odstraní virtuální počítač, který byl vytvořen během testovacího převzetí služeb při selhání.

## <a name="perform-a-planned-failover"></a>Provedení plánovaného převzetí služeb při selhání
   Během plánovaného převzetí služeb při selhání se místní virtuální počítač souborového serveru řádně ukončí a vytvoří se snímek záložního cloudu svazků na zařízení StorSimple. Virtuálnímu zařízení se převezmou StorSimple svazky, virtuální počítač repliky se načte do Azure a svazky se připojí k virtuálnímu počítači.

#### <a name="to-perform-a-planned-failover"></a>Provedení plánovaného převzetí služeb při selhání
1. V Azure Portal vyberte možnost plány obnovení trezoru **služby Recovery Services** &gt; **(Site Recovery)** &gt; **recoveryplan_name** vytvořené pro virtuální počítač souborového serveru.
1. V okně plán **obnovení klikněte na** &gt; **plánované převzetí služeb při selhání**.  

   ![Snímek obrazovky, který zvýrazní plánované možnosti převzetí služeb při selhání](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. V okně **potvrdit plánované převzetí služeb při selhání** zvolte zdrojové a cílové umístění a vyberte cílovou síť a kliknutím na ikonu zaškrtnutí ✓ spusťte proces převzetí služeb při selhání.
1. Po vytvoření virtuálních počítačů repliky jsou ve stavu čekání na potvrzení. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.
1. Po dokončení replikace se virtuální počítače spustí v sekundárním umístění.

## <a name="perform-a-failover"></a>Provedení převzetí služeb při selhání
Během neplánovaného převzetí služeb při selhání se virtuálnímu zařízení převezme StorSimple svazky, virtuální počítač repliky se zaplní do Azure a svazky se připojí k virtuálnímu počítači.

#### <a name="to-perform-a-failover"></a>Provedení převzetí služeb při selhání
1. V Azure Portal vyberte možnost plány obnovení trezoru **služby Recovery Services** &gt; **(Site Recovery)** &gt; **recoveryplan_name** vytvořené pro virtuální počítač souborového serveru.
1. V okně plán obnovení klikněte na **Další** &gt; **převzetí služeb při selhání**.  
1. V okně **Potvrdit převzetí služeb při selhání** vyberte zdrojové a cílové umístění.
1. Vyberte možnost **vypnout virtuální počítače a synchronizovat nejnovější data** , abyste určili, že Site Recovery by se měla pokusit vypnout chráněný virtuální počítač a synchronizovat data, aby se při selhání převzala nejnovější verze dat.
1. Po převzetí služeb při selhání jsou virtuální počítače ve stavu čekání na potvrzení. Kliknutím na **Potvrdit** potvrďte převzetí služeb při selhání.


## <a name="perform-a-failback"></a>Navrácení služeb po obnovení
Během navrácení služeb po obnovení dojde při převzetí služeb při selhání zpět na fyzické zařízení StorSimple kontejnery svazků.

#### <a name="to-perform-a-failback"></a>Provedení navrácení služeb po obnovení
1. V Azure Portal vyberte možnost plány obnovení trezoru **služby Recovery Services** &gt; **(Site Recovery)** &gt; **recoveryplan_name** vytvořené pro virtuální počítač souborového serveru.
1. V okně plán **obnovení klikněte na** &gt; **plánované převzetí služeb při selhání**.  
1. Zvolte zdrojové a cílové umístění, vyberte příslušné možnosti synchronizace dat a vytvoření virtuálního počítače.
1. Kliknutím na tlačítko **OK** spustíte proces navrácení služeb po obnovení.
   
   ![Spustit navrácení služeb po obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Osvědčené postupy
### <a name="capacity-planning-and-readiness-assessment"></a>Plánování kapacity a hodnocení připravenosti
#### <a name="hyper-v-site"></a>Lokalita Hyper-V
Pomocí [Nástroje pro plánování kapacity uživatelů](https://www.microsoft.com/download/details.aspx?id=39057) můžete navrhovat infrastrukturu serveru, úložiště a sítě pro prostředí repliky technologie Hyper-V.

#### <a name="azure"></a>Azure
Na virtuálních počítačích můžete spustit [Nástroj pro vyhodnocení připravenosti na virtuální počítače Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) , abyste měli jistotu, že jsou kompatibilní s virtuálními počítači azure a Azure Site Recovery službami. Nástroj pro vyhodnocení připravenosti zkontroluje konfigurace virtuálních počítačů a upozorní, že konfigurace jsou nekompatibilní s Azure. Například vydá upozornění, pokud je jednotka C: větší než 127 GB.

Plánování kapacity se skládá nejméně ze dvou důležitých procesů:

   - Mapování místních virtuálních počítačů Hyper-V na velikosti virtuálních počítačů Azure (například A6, A7, A8 a c/s).
   - Určení požadované šířky pásma internetu.

## <a name="limitations"></a>Omezení
- V současné době je možné převzít služeb při selhání jenom 1 zařízení StorSimple (do jednoho StorSimple Cloud Appliance). Scénář souborového serveru, který zahrnuje několik zařízení StorSimple, se ještě nepodporuje.
- Pokud při povolování ochrany virtuálního počítače dojde k chybě, ujistěte se, že jste provedli odpojení cílů iSCSI.
- Všechny kontejnery svazků, které byly seskupené dohromady, protože zásady zálohování, které pokrývá všechny kontejnery svazků, budou při selhání přenášet dohromady.
- U všech svazků v kontejnerech svazků, které jste zvolili, dojde k převzetí služeb při selhání.
- Svazky, které přidávají až 64 TB, nejde převzít při selhání, protože maximální kapacita jednoho StorSimple Cloud Appliance je 64 TB.
- Pokud se plánované/neplánované převzetí služeb při selhání nepovede a virtuální počítače se vytvoří v Azure, neprovádějte vyčištění virtuálních počítačů. Místo toho proveďte navrácení služeb po obnovení. Pokud virtuální počítače odstraníte, nebude možné místní virtuální počítače znovu zapnout.
- Pokud se po převzetí služeb při selhání nedaří zobrazit svazky, přejděte na virtuální počítače, spusťte správu disků, znovu zkontrolujte disky a pak je přeneste do režimu online.
- V některých případech se písmena jednotek na webu DR mohou lišit od písmen v místním prostředí. Pokud k tomu dojde, budete muset problém vyřešit ručně po dokončení převzetí služeb při selhání.
- Časový limit úlohy převzetí služeb při selhání: skript StorSimple vyprší, pokud převzetí služeb při selhání kontejnerů svazků trvá déle než limit Azure Site Recovery pro každý skript (aktuálně 120 minut).
- Časový limit úlohy zálohování: vyprší časový limit skriptu StorSimple, pokud zálohování svazků trvá déle než limit Azure Site Recovery na jeden skript (aktuálně 120 minut).
   
  > [!IMPORTANT]
  > Spusťte zálohování ručně z Azure Portal a pak znovu spusťte plán obnovení.
   
- Vypršel časový limit úlohy klonování: StorSimple skript vyprší, pokud klonování svazků trvá déle, než je limit Azure Site Recovery na jeden skript (aktuálně 120 minut).
- Chyba synchronizace času: ve skriptech StorSimple došlo k chybě, protože zálohy byly neúspěšné, i když je záloha na portálu úspěšná. Možnou příčinou může být to, že čas zařízení StorSimple nemusí být synchronizovaný s aktuálním časem v časovém pásmu.
   
  > [!IMPORTANT]
  > Synchronizuje čas zařízení s aktuálním časem v časovém pásmu.
   
- Chyba převzetí služeb při selhání: skript StorSimple může selhat, pokud dojde k převzetí služeb zařízení při selhání plánu obnovení.
   
  > [!IMPORTANT]
  > Po dokončení převzetí služeb při selhání zařízení znovu spusťte plán obnovení.


## <a name="summary"></a>Shrnutí
Pomocí Azure Site Recovery můžete vytvořit úplný automatizovaný plán zotavení po havárii pro virtuální počítač souborového serveru, který má sdílené složky hostované v úložišti StorSimple. Převzetí služeb při selhání můžete zahájit během několika sekund odkudkoli v případě výpadku a během několika minut aplikaci spustit.