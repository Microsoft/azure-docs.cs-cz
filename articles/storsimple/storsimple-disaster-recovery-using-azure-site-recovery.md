---
title: Automatizace StorSimple fileshare zotavení po Havárii pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje kroky a osvědčené postupy pro vytvoření řešení zotavení po havárii pro sdílené složky hostované na úložiště Microsoft Azure StorSimple.
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
ms.author: vidarmsft
ms.openlocfilehash: f5b128306389a87c432b869b4756a6d232dc903c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566036"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatizované řešení zotavení po havárii pomocí Azure Site Recovery pro sdílené složky hostované na StorSimple
## <a name="overview"></a>Přehled
Microsoft Azure StorSimple je hybridní cloudové řešení úložiště, který adresuje složitosti nestrukturovaných dat, běžně spojené s využitím sdílených složek. StorSimple využívá cloudové úložiště jako rozšíření místního řešení a automaticky úrovně dat napříč úložiště v místním a cloudovým úložištěm. Integrovaná ochrana dat s místní a cloudové snímky, eliminuje potřebu rozrůstající infrastruktury úložiště.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) je služba, která poskytuje možnosti zotavení po havárii tím, že orchestruje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů založených na Azure. Azure Site Recovery podporuje řadu technologií replikace konzistentně replikovat, ochranu a bez problémů převzetí služeb při selhání virtuálních počítačů a aplikací do privátní nebo veřejné nebo hostovaných cloudů.

Pomocí Azure Site Recovery, replikace virtuálních počítačů a práci se StorSimple cloudové snímky, můžete chránit prostředí celý server. V případě přerušení můžete jedním kliknutím pro online sdílených složek Azure během několika minut.

Tento dokument podrobně vysvětluje, jak můžete vytvořit řešení zotavení po havárii pro vaše sdílené složky hostované na úložiště StorSimple a provést plánované, neplánované a testovací převzetí služeb při selhání použít plán obnovení jedním kliknutím pomocí. V podstatě ukazuje, jak můžete upravit plán obnovení v trezoru Azure Site Recovery umožňuje StorSimple převzetí služeb při selhání během scénáře po havárii. Kromě toho popisuje podporované konfigurace a požadavky. Tento dokument předpokládá, že jste obeznámení se základy architektury Azure Site Recovery a StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Podporované možnosti nasazení Azure Site Recovery
Zákazníci můžou nasazení souborových serverů se jako fyzických serverech nebo virtuálních počítačích (VM) běží na Hyper-V nebo VMware a pak vytvořte sdílené složky ze svazků carved mimo úložiště StorSimple. Azure Site Recovery může chránit fyzických i virtuálních nasazení do sekundární lokality nebo do Azure. Tento dokument popisuje podrobnosti řešení zotavení po Havárii s využitím Azure jako lokalitu pro zotavení pro souborový server, který virtuální počítač hostovaný na Hyper-V a s využitím sdílených složek na úložiště StorSimple. Další scénáře, ve kterých je souborový server virtuálního počítače na virtuální počítač VMware nebo fyzický počítač, který je možné implementovat podobně.

## <a name="prerequisites"></a>Požadavky
Implementace řešení pro zotavení po havárii jedním kliknutím, který používá Azure Site Recovery pro sdílené složky hostované na úložiště StorSimple má následující požadavky:

   - Místní server soubor systému Windows Server 2012 R2, který virtuální počítač hostovaný na Hyper-V nebo VMware nebo fyzický počítač
   - StorSimple zařízení místním úložišti zaregistrované pomocí nástroje Azure StorSimple manager
   - Řešení StorSimple Cloud Appliance v Azure StorSimple manager vytvoří. Zařízení můžete uchovávat ve vypnutém stavu.
   - Sdílené složky hostované na svazcích, které jsou nakonfigurované na paměťovém zařízení StorSimple
   - [Azure Site Recovery services vault](../site-recovery/site-recovery-vmm-to-vmm.md) vytvořené v rámci předplatného Microsoft Azure

Kromě toho pokud Azure je svůj záložní web, spusťte [nástroj pro vyhodnocení připravenosti na virtuální počítač Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na virtuálních počítačů pro zajištění, že jsou kompatibilní s virtuálními počítači Azure a Azure Site Recovery services.

Aby se zabránilo latenci problémy (které můžou mít za následek vyšší náklady), ujistěte se, že vytvoříte řešení StorSimple Cloud Appliance, účet automation a úložiště účtů ve stejné oblasti.

## <a name="enable-dr-for-storsimple-file-shares"></a>Povolit zotavení po Havárii pro StorSimple sdílené složky
Jednotlivé komponenty v místním prostředí je potřeba chránit povolit úplnou replikaci a obnovení. Tato část popisuje, jak:
    
   - Nastavení replikace služby Active Directory a DNS (volitelné)
   - Povolení ochrany souborového serveru virtuálního počítače pomocí Azure Site Recovery
   - Povolit ochranu svazky zařízení StorSimple
   - Konfigurace sítě

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Nastavení replikace služby Active Directory a DNS (volitelné)
Pokud chcete chránit počítače s Active Directory a DNS tak, aby byly k dispozici v lokalitě zotavení po Havárii, je třeba explicitně je chránit (tak, aby po převzetí služeb při selhání ověřování jsou dostupné na souborových serverech). Existují dvě doporučené možnosti založené na složitosti zákazníka v místním prostředí.

#### <a name="option-1"></a>Možnost 1
Pokud zákazník má malý počet aplikací, jeden řadič domény pro celý místní lokality a bude se převzetí služeb při selhání celý web, pak doporučujeme používat replikace Azure Site Recovery k replikaci na sekundární počítače řadiče domény lokality (týká se to site-to-site a site do Azure).

#### <a name="option-2"></a>Možnost 2
Pokud má zákazník má velký počet aplikací, běží doménové struktury služby Active Directory a bude možné převzetí služeb při selhání několik aplikací najednou, pak doporučujeme nastavit další řadič domény v lokalitě zotavení po Havárii (sekundární lokality nebo v Azure).

Odkazovat na [řešení automatizované zotavení po Havárii pro Active Directory a DNS pomocí Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) pokyny případě zpřístupnění řadiče domény v lokalitě zotavení po Havárii. Pro zbývající část tohoto dokumentu předpokládáme, že řadič domény je k dispozici v lokalitě zotavení po Havárii.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Povolení ochrany souborového serveru virtuálního počítače pomocí Azure Site Recovery
Tento krok vyžaduje, abyste připravili serveru soubor v místním prostředí, vytvářet a připraví trezor služby Azure Site Recovery a povolte ochranu souborů virtuálního počítače.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Příprava prostředí serveru v místním souboru
1. Nastavte **řízení uživatelských účtů** k **Nikdy neupozorňovat**. To je potřeba, tak, aby skripty služby Azure automation můžete používat pro připojení cíle iSCSI po převzetí služeb při selhání Azure Site Recovery.
   
   1. Stisknutím kláves Windows + Q a vyhledejte **nástroje Řízení uživatelských účtů**.  
   1. Vyberte **řízení uživatelských účtů změnu nastavení**.  
   1. Přetažením dolů směrem k panelu **Nikdy neupozorňovat**.  
   1. Klikněte na tlačítko **OK** a pak vyberte **Ano** po zobrazení výzvy.  
   
      ![Nastavení nástroje Řízení uživatelských účtů](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Nainstalujte agenta virtuálního počítače na jednotlivé virtuální počítače souborového serveru. To je potřeba, takže můžete spouštět skripty služby Azure automation se virtuální počítače.
   
   1. [Stáhnout agenta](https://aka.ms/vmagentwin) k `C:\\Users\\<username>\\Downloads`.
   1. Otevřete prostředí Windows PowerShell v režimu správce (Spustit jako správce) a potom zadejte následující příkaz, který přejděte do umístění pro stahování:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Název souboru může měnit v závislosti na verzi.
      
1. Klikněte na **Další**.
1. Přijměte **podmínky smlouvy** a potom klikněte na tlačítko **Další**.
1. Klikněte na **Dokončit**.
1. Vytvoření sdílené složky pomocí svazků carved mimo úložiště StorSimple. Další informace najdete v tématu [použití služby StorSimple Manager pro správu svazků](storsimple-manage-volumes.md).
   
   1. Na vaše místní virtuální počítače, stisknutím kláves Windows + Q a vyhledejte **iSCSI**.
   1. Vyberte **iniciátor iSCSI**.
   1. Vyberte **konfigurace** kartu a zkopírujte název iniciátoru.
   1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
   1. Vyberte **StorSimple** kartu a potom vyberte službu StorSimple Manager, která obsahuje fyzické zařízení.
   1. Vytvoření kontejnerů svazků a pak vytvořte svazky. (Tyto svazky se pro sdílené položky souboru na souborovém serveru se virtuální počítače). Kopírování název iniciátoru a nastavte vhodný název pro záznamy řízení přístupu při vytváření svazků.
   1. Vyberte **konfigurovat** kartu a Všimněte si dolů IP adresu zařízení.
   1. Na vaše místní virtuální počítače, přejděte **iniciátor iSCSI** znovu a zadejte IP adresu v oddílu rychlé připojení. Klikněte na tlačítko **rychlé připojení** (zařízení by měla nyní být připojeno).
   1. Otevřete Azure portal a vyberte **zařízení a svazky** kartu. Klikněte na tlačítko **automaticky nakonfigurují**. By se zobrazit na svazek, který jste vytvořili.
   1. Na portálu, vyberte **zařízení** kartu a potom vyberte **vytvoření nového virtuálního zařízení.** (Toto virtuální zařízení se použijí, pokud dojde k selhání). Toto nové virtuální zařízení můžete uchovávat ve stavu offline, aby se zabránilo další náklady. Uvedení virtuálního zařízení do režimu offline, přejděte **virtuálních počítačů** části na portálu a vypnete ji.
   1. Přejděte zpět do místních virtuálních počítačů a otevřete správu disků (stisknutím kláves Windows + X a vyberte **Správa disků**).
   1. Můžete si všimnout některých dalších disků (v závislosti na počtu svazků, které jste vytvořili). Klikněte pravým tlačítkem na první z nich, vyberte **inicializovat Disk**a vyberte **OK**. Klikněte pravým tlačítkem myši **Unallocated** vyberte **nový jednoduchý svazek**ji přiřadit písmeno jednotky a dokončete průvodce.
   1. Opakujte krok l pro všechny disky. Nyní je vidět všechny disky na **tento počítač** v Průzkumníku Windows.
   1. Pomocí role Souborová služba a služba úložiště pro vytvoření sdílených složek na těchto svazcích.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>K vytvoření a přípravě trezoru služby Azure Site Recovery
Odkazovat [dokumentace ke službě Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) Začínáme s Azure Site Recovery, než začnete chránit souborový server virtuálního počítače.

#### <a name="to-enable-protection"></a>K povolení ochrany
1. Odpojte se od místních virtuálních počítačů, které chcete chránit pomocí Azure Site Recovery cíle iSCSI:
   
   1. Stisknutím kláves Windows + Q a vyhledejte **iSCSI**.
   1. Vyberte **nastavení iniciátoru iSCSI**.
   1. Odpojte dříve připojeného zařízení StorSimple. Alternativně můžete vypnout souborový server pro několik minut, než při povolování ochrany.
      
   > [!NOTE]
   > To způsobí, že sdílené složky dočasně nedostupný.
   
1. [Povolení ochrany virtuálního počítače](../site-recovery/site-recovery-hyper-v-site-to-azure.md) souborového serveru virtuálního počítače z portálu Azure Site Recovery.
1. Po zahájení počáteční synchronizace se můžete připojit cíl znovu. Přejděte k iniciátoru iSCSI, vyberte zařízení StorSimple a klikněte na tlačítko **připojit**.
1. Pokud se synchronizace dokončí a stav virtuálního počítače je **chráněné**, vyberte virtuální počítač, vyberte **konfigurovat** kartu a síť virtuálního počítače aktualizovat odpovídajícím způsobem (jedná se o síť, která se přes Virtuální počítač bude součástí). Pokud síť není uveden, znamená to, že se stále děje synchronizace.

### <a name="enable-protection-of-storsimple-volumes"></a>Povolit ochranu svazky zařízení StorSimple
Pokud jste nevybrali **povolit výchozí zálohování pro tento svazek** možnost pro svazky zařízení StorSimple, přejděte na **zásady zálohování** ve StorSimple Manageru služby a vytvořte zásadu zálohování vhodný pro všechny svazky. Doporučujeme nastavit frekvenci záloh na cíl bodu obnovení (RPO), který chcete zobrazit pro aplikaci.

### <a name="configure-the-network"></a>Konfigurace sítě
Pro souborový server virtuálního počítače Konfigurace nastavení sítě v Azure Site Recovery, tak, aby sítě virtuálních počítačů po převzetí služeb při selhání připojeny ke správné síti zotavení po Havárii.

Můžete vybrat virtuální počítač v **replikované položky** kartu Konfigurace nastavení sítě, jak je znázorněno na následujícím obrázku.

![Výpočty a síť](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
V Azure Site Recovery k automatizaci procesu převzetí služeb při selhání sdílené složky můžete vytvořit plán obnovení. Pokud dojde k přerušení, připravíte sdílených složek za pár minut s jedním kliknutím. Pokud chcete povolit tento automatizace, musíte účtu Azure automation.

#### <a name="to-create-an-automation-account"></a>Vytvoření účtu služby Automation
1. Přejděte na web Azure Portal &gt; **automatizace** oddílu.
1. Klikněte na tlačítko **+ přidat** tlačítko, otevře se následující okno.
   
   ![Přidání účtu Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Name – zadejte nový účet služby automation
   - Předplatné – zvolte předplatné
   - Prostředek group - vytvořit nové nebo zvolte existující skupinu prostředků
   - Umístění, zvolte umístění, Uchovávejte ve stejné geografické/oblasti, ve kterém byly vytvořeny řešení StorSimple Cloud Appliance a účty úložiště.
   - Vytvoření účtu spustit jako pro Azure – vyberte **Ano** možnost.
   
1. Přejděte do účtu Automation, klikněte na tlačítko **sady Runbook** &gt; **procházet galerii** import požadovaných sad runbook do účtu automation.
1. Přidejte následující sady runbook hledáním **zotavení po havárii** značka ve galerii:
   
   - Vyčištění svazky zařízení StorSimple po testu převzetí služeb při selhání (TFO)
   - Kontejnery svazků StorSimple převzetí služeb při selhání
   - Připojit svazky na zařízení StorSimple po převzetí služeb při selhání
   - Odinstalace rozšíření vlastních skriptů na virtuálním počítači Azure
   - Spuštění virtuálního zařízení StorSimple
   
      ![Procházet galerii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publikovat všechny skripty v účtu automation vyberte sadu runbook a klikněte na tlačítko **upravit** &gt; **publikovat** a potom **Ano** zprávy pro ověření. Po provedení tohoto kroku **sady Runbook** kartě se zobrazí takto:
   
   ![Runbooky](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. V účtu automation, klikněte na tlačítko **proměnné** &gt; **přidat proměnnou** a přidejte následující proměnné. Můžete se k šifrování tyto prostředky. Tyto proměnné jsou konkrétní plán obnovení. Pokud v plánu služby recovery, které vytvoříte v dalším kroku název je testovací plány, proměnných by měl být StorSimRegKey testovací plány, testovací plán – AzureSubscriptionName a tak dále.

   - **BaseUrl**: Adresa url správce prostředků cloudu Azure. Získáte rutinou **Get-AzureRmEnvironment | Název Select-Object, ResourceManagerUrl** rutiny.
   - *RecoveryPlanName***-ResourceGroupName**: Skupina Resource Manageru, který se má prostředek StorSimple.
   - *RecoveryPlanName***-ManagerName**: StorSimple prostředek, který má zařízení StorSimple.
   - * RecoveryPlanName ***- DeviceName**: Zařízení StorSimple, který má být převzetí služeb při selhání.
   - *RecoveryPlanName***-DeviceIpAddress**: IP adresa zařízení (lze najít v **zařízení** kartu v části Správce zařízení StorSimple &gt; **nastavení** &gt; **sítě** &gt; **Nastavení DNS** skupiny).
   - *RecoveryPlanName***-VolumeContainers**: Čárkou oddělený řetězec kontejnerů svazků, které jsou k dispozici na na zařízení, musí být; převzetí služeb při selhání Příklad: volcon1 volcon2, volcon3.
   - * RecoveryPlanName ***- TargetDeviceName**: Řešení StorSimple Cloud Appliance ve kterém mají být převzetí služeb při selhání kontejnerů.
   - *RecoveryPlanName***-TargetDeviceIpAddress**: IP adresa cílového zařízení (lze najít v **virtuálního počítače** části &gt; **nastavení** skupiny &gt; **sítě** kartu).
   - *RecoveryPlanName***-StorageAccountName**: Název účtu úložiště, ve kterém se uloží skriptu (který se má spouštět se přes virtuální počítač). To může být libovolný účet úložiště, který má nějaké místo k uložení skriptu dočasně.
   - *RecoveryPlanName***-StorageAccountKey**: Přístupový klíč pro výše uvedené účtu úložiště.
   - * RecoveryPlanName ***- VMGUIDS**: Při ochraně virtuálního počítače, Azure Site Recovery přiřadí každému virtuálnímu počítači jedinečné ID, které poskytuje podrobné informace o se selhání pro virtuální počítač. Chcete-li získat VMGUID, vyberte **služby Recovery Services** kartě a klikněte na tlačítko **chráněné položky** &gt; **skupin ochrany** &gt;  **Počítače** &gt; **vlastnosti**. Pokud máte několik virtuálních počítačů, přidejte identifikátory GUID jako řetězec oddělených čárkou.

    Například, pokud se název plánu obnovení fileServerpredayRP pak vaše **proměnné**, **připojení** a **certifikáty** karta by měla vypadat následovně, po přidání všechny prostředky.

      ![Prostředky](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Nahrání modulu sady Runbook StorSimple 8000 series ve vašem účtu Automation. Použití níže uvedený postup k přidání modulu:
   
   1. Otevřete prostředí powershell, vytvořte novou složku a změňte adresář na složku.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Stáhněte si rozhraní příkazového řádku nuget ve stejné složce v stažená v kroku 1.
      Jsou k dispozici na různé verze programu nuget.exe [stáhne nuget](https://www.nuget.org/downloads). Každý odkaz ke stažení odkazuje přímo na soubor s příponou .exe, proto nezapomeňte klikněte pravým tlačítkem a uložte soubor do počítače místo spouštění z prohlížeče.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Stáhnout sadu SDK závislá
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Vytvořte modul Azure Automation Runbook ke správě zařízení StorSimple řady 8000. Použití následujících příkazů vytvořte soubor zip modulu služby Automation.
         
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
         
   1. Importujte Azure Automation. soubor zip modulu (Microsoft.Azure.Management.StorSimple8000Series.zip) vytvořené v nad krok. To můžete udělat tak, že vyberete účet služby Automation, klikněte na tlačítko **moduly** v části SDÍLENÉ prostředky a pak klikněte na tlačítko **přidat modul**.
   
   Po importování modulu řady StorSimple 8000 **moduly** karta by měla vypadat následovně:
   
      ![Moduly](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Přejděte **služby Recovery Services** a vyberte trezor Azure Site Recovery, který jste vytvořili dříve.
1. Vyberte **plány obnovení (služba Site Recovery)** možnost **spravovat** skupiny a vytvořte nový plán obnovení následujícím způsobem:
   
   - Klikněte na tlačítko **+ plánu obnovení** tlačítko, otevře se následující okno.
      
      ![Vytvoření plánu obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Zadejte název plánu obnovení, zvolte zdroj, cíl & nasazení hodnoty modelu.
   
   - Vyberte virtuální počítače ze skupiny ochrany, které chcete zahrnout do plánu obnovení a klikněte na tlačítko **OK** tlačítko.
   
   - Vyberte plán obnovení, který jste předtím vytvořili, klikněte na **vlastní** tlačítko Otevřít zobrazení vlastního nastavení plánu obnovení.
   
   - Klikněte pravým tlačítkem na **vypínání všech skupin** a klikněte na tlačítko **přidat akci před**.
   
   - Otevře se okno akce vložit, zadejte název, vyberte **primární straně** možnost tam, kde možnost spustit, vyberte účet Automation (ve kterém jste přidali sady runbook) a potom vyberte **převzetí služeb při selhání StorSimple svazku kontejnerů**  sady runbook.
   
   - Klikněte pravým tlačítkem na **1. skupina: Spustit** a klikněte na tlačítko **přidat chráněné položky** možnost a vyberte virtuální počítače, které se mají chránit v plánu obnovení a klikněte na **Ok** tlačítko. Volitelný parametr, pokud je už vybrané virtuální počítače.
   
   - Klikněte pravým tlačítkem na **1. skupina: Spustit** a klikněte na tlačítko **akce odeslání** možnost pak přidejte následující skriptů:  
      
      - Sada runbook Start--virtuální-zařízení StorSimple  
      - Selhání sady runbook přes StorSimple svazku kontejnerů  
      - Připojení svazků po selhání sady runbook  
      - Odinstalace--rozšíření vlastních skriptů runbook  
        
   - Přidejte ruční akce po výše uvedené 4 skripty ve stejném **1. skupina: Kroky prováděné po zpracování** oddílu. Tato akce je bod, ve kterém můžete ověřit, zda vše funguje správně. Tato akce musí být přidán pouze jako součást testovacího převzetí služeb při selhání (tedy vyberte pouze **testovací převzetí služeb při selhání** zaškrtávací políčko).
    
   - Po ruční akci, přidejte **vyčištění** skriptu pomocí stejného postupu, který jste použili pro sady runbook. **Uložit** plánu obnovení.
    
   > [!NOTE]
   > Při spuštění testovací převzetí služeb při selhání, by všechno, co v kroku ručně prováděné akce ověřit, protože budou odstraněny svazky zařízení StorSimple, které kdyby byly klonovat na cílovém zařízení jako součást vyčištění po dokončení ručně prováděné akce.
       
      ![Plán obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Provést testovací převzetí služeb
Odkazovat [řešení zotavení po Havárii Active Directory](../site-recovery/site-recovery-active-directory.md) doprovodná Příručka pro důležité informace o konkrétní do služby Active Directory během testu převzetí služeb. Nastavení místního není vůbec narušen, když se testovací převzetí služeb při selhání. Svazky zařízení StorSimple připojené k místní virtuální počítač se klonují do řešení StorSimple Cloud Appliance v Azure. Virtuální počítač pro účely testování se aktivují v Azure a naklonované svazky jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-the-test-failover"></a>K provedení testu převzetí služeb
1. Na webu Azure Portal vyberte svůj trezor Site Recovery.
1. Klikněte na tlačítko vytvořit pro souborový server virtuálního počítače plánu obnovení.
1. Klikněte na tlačítko **testovací převzetí služeb při selhání**.
1. Vyberte virtuální síť Azure, ke které se připojí virtuální počítače Azure po převzetí služeb při selhání.
   
   ![Spuštění převzetí služeb při selhání](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat kliknutím na virtuálním počítači otevřete jeho vlastnosti, nebo na **testovací převzetí služeb při selhání úlohy** v části název_trezoru &gt; **úlohy** &gt; **úlohy Site Recovery**.
1. Po dokončení převzetí služeb byste také měli vidět Azure repliky na počítač se zobrazí na webu Azure Portal &gt; **virtuálních počítačů**. Vaše ověření můžete provést.
1. Po dokončení ověření klikněte na tlačítko **dokončení ověření**. Tím odeberete svazky zařízení StorSimple a vypnout řešení StorSimple Cloud Appliance.
1. Až budete hotovi, klikněte na tlačítko **vyčištění testovacího převzetí služeb při selhání** v plánu obnovení. V poznámkách k zaznamenejte a uložte jakékoli připomínky související s testovací převzetí služeb. Tato akce odstraní virtuální počítač, které se vytvořily během testu převzetí služeb při selhání.

## <a name="perform-a-planned-failover"></a>Proveďte plánované převzetí služeb při selhání
   Během plánované převzetí služeb při souboru v místním serveru, který elegantně vypnutí virtuálního počítače a cloud, pořízení snímku zálohy svazků na zařízení StorSimple. Svazky zařízení StorSimple se převzetí služeb při selhání virtuálního zařízení, repliku virtuálního počítače se aktivují v Azure a svazky, které jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-a-planned-failover"></a>K provedení plánovaného převzetí služeb při selhání
1. Na webu Azure Portal, vyberte **služby Recovery services** trezor &gt; **plány obnovení (služba Site Recovery)** &gt; **recoveryplan_name** vytvořené pro Souborový server virtuálního počítače.
1. V okně plán obnovení, klikněte na **Další** &gt; **plánované převzetí služeb při selhání**.

   ![Plán obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Na **potvrďte plánované převzetí služeb při selhání** okně zvolte zdrojové a cílové umístění a vyberte cílovou síť a klikněte na ikonu zaškrtnutí ✓ zahájíte proces převzetí služeb při selhání.
1. Po vytvoření virtuálního počítače repliky jsou stavu čekání na potvrzení. Klikněte na tlačítko **potvrzení** potvrzení převzetí služeb při selhání.
1. Po dokončení replikace virtuálních počítačů spuštění v sekundárním umístění.

## <a name="perform-a-failover"></a>Převzetí služeb
Při neplánovaném převzetí služeb při selhání svazky zařízení StorSimple se převzetí služeb při selhání virtuálního zařízení, repliku virtuálního počítače se aktivují v Azure a svazky, které jsou připojené k virtuálnímu počítači.

#### <a name="to-perform-a-failover"></a>K provedení převzetí služeb při selhání
1. Na webu Azure Portal, vyberte **služby Recovery services** trezor &gt; **plány obnovení (služba Site Recovery)** &gt; **recoveryplan_name** vytvořené pro Souborový server virtuálního počítače.
1. V okně plán obnovení, klikněte na **Další** &gt; **převzetí služeb při selhání**.
1. Na **potvrzení převzetí služeb při selhání** okně zvolte zdrojové a cílové umístění.
1. Vyberte **vypne virtuální počítače a synchronizuje nejnovější data** k určení, Site Recovery se pokuste vypne chráněný virtuální počítač a synchronizuje data tak, že nejnovější verze dat se převzal.
1. Po převzetí služeb při selhání jsou virtuální počítače stavu čekání na potvrzení. Klikněte na tlačítko **potvrzení** potvrzení převzetí služeb při selhání.


## <a name="perform-a-failback"></a>Navrácení služeb po obnovení
Během navrácení služeb po obnovení kontejnery svazků StorSimple jsou převzalo zpět do fyzického zařízení po zálohování se používá.

#### <a name="to-perform-a-failback"></a>K provedení navrácení služeb po obnovení
1. Na webu Azure Portal, vyberte **služby Recovery services** trezor &gt; **plány obnovení (služba Site Recovery)** &gt; **recoveryplan_name** vytvořené pro Souborový server virtuálního počítače.
1. V okně plán obnovení, klikněte na **Další** &gt; **plánované převzetí služeb při selhání**.
1. Vyberte zdrojové a cílové umístění, vyberte příslušné synchronizace dat a možnosti pro vytvoření virtuálního počítače.
1. Klikněte na tlačítko **OK** tlačítko pro spuštění procesu navrácení služeb po obnovení.
   
   ![Spuštění navrácení služeb po obnovení](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Osvědčené postupy
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitní plánování a připravenost na posouzení
#### <a name="hyper-v-site"></a>Lokalita Hyper-V
Použití [nástroj Plánovač kapacity uživatele](https://www.microsoft.com/download/details.aspx?id=39057) navrhnout servery a úložným a síťovou infrastrukturu pro vaše prostředí repliky technologie Hyper-V.

#### <a name="azure"></a>Azure
Můžete spustit [nástroj pro vyhodnocení připravenosti na virtuální počítač Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na virtuálních počítačů pro zajištění, že jsou kompatibilní s virtuálními počítači Azure a Azure Site Recovery Services. Nástroj pro vyhodnocení připravenosti kontroluje konfigurací virtuálních počítačů a upozorní, když konfigurace nejsou kompatibilní s Azure. Například vyvolá upozornění, pokud je větší než 127 GB jednotce C:.

Plánování kapacity se skládá z alespoň dva důležité procesy:

   - Mapování místních virtuálních počítačů Hyper-V do velikosti virtuálních počítačů Azure (například A6 A7, instance A8 a A9).
   - Určení požadované šířky pásma Internetu.

## <a name="limitations"></a>Omezení
   - V současné době mohou být přebrány pouze 1 zařízení StorSimple (na jednoho řešení StorSimple Cloud Appliance). Scénář souborový server, který zahrnuje několik zařízení StorSimple se ještě nepodporuje.
   - Pokud dojde k chybě při zapínání ochrany pro virtuální počítač, ujistěte se, že jste odpojeni cíle iSCSI.
   - Všechny kontejnery svazků, které byly seskupeny dohromady z důvodu zásad zálohování, které pokrývá kontejnery svazků převezme služby společně.
   - Všechny svazky v kontejnerech svazků, kterou jste zvolili převezme služby.
   - Svazky, které přidáte do více než 64 TB nejde přes se nezdařila, protože maximální kapacita jednu řešení StorSimple Cloud Appliance je 64 TB.
   - Pokud se nepovede plánované/neplánované převzetí služeb při selhání a virtuální počítače se vytvoří v Azure, pak nevyčišťujte virtuálních počítačů. Místo toho proveďte navrácení služeb po obnovení. Pokud odstraníte virtuální počítače pak místní virtuální počítače nelze zapnout znovu.
   - Po selhání Pokud se vám nedaří zobrazit svazky, přejděte na virtuální počítače, správu disků otevřete tak, zkontrolujte znovu disky a pak je převedení do online režimu.
   - V některých případech může být jiná než písmena v místním písmena jednotek v lokalitě zotavení po Havárii. Pokud k tomu dojde, je potřeba ručně po dokončení převzetí služeb při odstranění problému.
   - Časový limit úlohy převzetí služeb při selhání: StorSimple skriptu vyprší časový limit, pokud převzetí služeb při selhání kontejnerů svazků trvá déle než omezení Azure Site Recovery na skript (aktuálně 120 minut).
   - Časový limit úlohy zálohování: StorSimple skriptu vyprší časový limit, pokud zálohování svazků trvá déle než omezení Azure Site Recovery na skript (aktuálně 120 minut).
   
   > [!IMPORTANT]
   > Zálohování spustit ručně z portálu Azure portal a potom znovu spusťte plánu obnovení.
   
   - Časový limit úlohy klonu: StorSimple skriptu vyprší časový limit, pokud se klonování svazků trvá déle než omezení Azure Site Recovery na skript (aktuálně 120 minut).
   - Chyba synchronizace času: StorSimple skripty chyby si informacemi o tom, že zálohování nebyly úspěšné, i když je na portálu pro úspěšné zálohování. Možnou příčinou to může být, že čas zařízení StorSimple může být synchronizován s aktuální čas v časovém pásmu.
   
   > [!IMPORTANT]
   > Synchronizaci času zařízení se aktuální čas v časovém pásmu.
   
   - Chyba převzetí služeb při selhání zařízení: Skript StorSimple může selhat, pokud dojde převzetí služeb při selhání zařízení při spuštění plánu obnovení.
   
   > [!IMPORTANT]
   > Plán obnovení znovu po dokončení převzetí služeb při selhání zařízení.


## <a name="summary"></a>Souhrn
Pomocí Azure Site Recovery, můžete vytvořit kompletní automatizované zotavení po havárii pro souborový server virtuálního počítače s sdílené složky hostované na úložiště StorSimple. Můžete zahájit převzetí služeb při selhání během několika sekund z libovolného místa v případě přerušení a získejte aplikaci rychle zprovoznit za pár minut.
