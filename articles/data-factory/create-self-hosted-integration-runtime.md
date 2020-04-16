---
title: Vytvoření místního prostředí Integration Runtime
description: Zjistěte, jak vytvořit runtime integrace s vlastním hostitelem v Azure Data Factory, který umožňuje továrnám na data přístup k datovým úložištím v privátní síti.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/13/2020
ms.openlocfilehash: 6bc0f002c6927cfd9a314797663e1dabbac392b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416646"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Vytvoření a konfigurace místního prostředí Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integrační runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování funkcí integrace dat v různých síťových prostředích. Podrobnosti o infračerveném provozu naleznete [v tématu Přehled za běhu integrace](concepts-integration-runtime.md).

Runtime integrace s vlastním hostitelem může spouštět aktivity kopírování mezi úložištěm cloudových dat a úložištěm dat v privátní síti. Může také odesílat aktivity transformace proti výpočetním prostředkům v místní síti nebo virtuální síti Azure. Instalace runtime integrace s vlastním hostitelem vyžaduje místní počítač nebo virtuální počítač v privátní síti.  

Tento článek popisuje, jak můžete vytvořit a nakonfigurovat samoobslužné infračervené ovládání.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Nastavení runtime integrace s vlastním hostitelem

Chcete-li vytvořit a nastavit prostředí runtime integrace s vlastním hostitelem, použijte následující postupy.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Vytvoření infračerveného počítače s vlastním hostitelem prostřednictvím Azure PowerShellu

1. Pro tento úkol můžete použít Azure PowerShell. Zde naleznete příklad:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Stáhněte](https://www.microsoft.com/download/details.aspx?id=39717) a nainstalujte runtime integrace s vlastním hostitelem do místního počítače.

3. Načtěte ověřovací klíč a zaregistrujte s klíčem vlastní hostovaný integrační runtime. Tady je příklad prostředí PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Vytvoření infračerveného počítače s vlastním hostitelem prostřednictvím uzly Azure Data Factory

Pomocí následujících kroků vytvořte infračervený přenos s vlastním hostitelem pomocí uzuliny Azure Data Factory.

1. Na stránce **Začínáme** v uzu Azure Data Factory vyberte kartu **Autor** v podokně zcela vlevo.

   ![Tlačítko Autor domovské stránky](media/doc-common-process/get-started-page-author-button.png)

1. V dolní části podokna zcela vlevo vyberte **Připojení** a v okně **Připojení** vyberte **Možnost Integrovat runtimes.** Vyberte **možnost +Nový**.

   ![Vytvoření prostředí Integration Runtime](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Na stránce **nastavení prostředí Runtime integrace** vyberte **Azure, Vlastní hostované**a pak vyberte **Pokračovat**. 

1. Na následující stránce vyberte **Možnost Vlastní hostované,** chcete-li vytvořit infračervené odznaženou infračervené odpočinkovou infračervené odpočinkovou infračervený přenos, a pak vyberte **Pokračovat**.
   ![Vytvoření infračerveného přenosu s vlastním hostitelem](media/create-self-hosted-integration-runtime/new-selfhosted-ir.png)

1. Zadejte název infračerveného zařízení a vyberte **Vytvořit**.

1. Na stránce **Nastavení prostředí Runtime integrace** vyberte odkaz v části Možnost **1** a otevřete expresní nastavení v počítači. Nebo postupujte podle pokynů v části **Možnost 2** nastavit ručně. Následující pokyny jsou založeny na ručním nastavení:

   ![Instalace prostředí Integration Runtime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Zkopírujte a vložte ověřovací klíč. Vyberte **možnost Stáhnout a nainstalovat integrační prostředí runtime**.

    1. Na místním počítači s Windows stáhněte místní prostředí Integration Runtime. Spusťte instalační program.

    1. Na stránce **Register Integration Runtime (Self-hosted)** vložte klíč, který jste uložili dříve, a vyberte **Registrovat**.
    
       ![Registrace prostředí Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na stránce **Nový integrační běh ový čas (samoobslužný) uzel** vyberte **Dokončit**.

1. Po úspěšné registraci runtime integrace s vlastním hostitelem se zobrazí následující okno:

    ![Úspěšná registrace](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Nastavení infračerveného počítače s vlastním hostitelem na virtuálním počítači Azure prostřednictvím šablony Azure Resource Manager

Nastavení infračervených zařízení s vlastním hostitelem na virtuálním počítači Azure můžete automatizovat pomocí [šablony Create self host IR](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Šablona poskytuje snadný způsob, jak mít plně funkční samoobslužné infračervené ovládání uvnitř virtuální sítě Azure. Infračervený přenos má funkce vysoké dostupnosti a škálovatelnosti, pokud nastavíte počet uzlů na 2 nebo vyšší.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Nastavení existujícího infračerveného systému hostovaného s vlastním hostitelem prostřednictvím místního prostředí PowerShell

Příkazový řádek můžete použít k nastavení nebo správě existující hospodařící infračervené nebo infračervené odvětvové indusionu s vlastním hostitelem. Toto použití může pomoci zejména k automatizaci instalace a registrace samoobslužných infračervených uzlů.

Dmgcmd.exe je součástí samoobslužného instalátoru. Obvykle se nachází ve složce C:\Program Files\Microsoft Integration Runtime\3.0\Shared\. Tato aplikace podporuje různé parametry a může být vyvolána pomocí příkazového řádku pomocí dávkových skriptů pro automatizaci.

Aplikaci použijte následujícím způsobem:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Zde jsou podrobnosti o parametrech a vlastnostech aplikace: 

| Vlastnost                                                    | Popis                                                  | Požaduje se |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | Zaregistrujte vlastní hostovaný uzel runtime integrace se zadaným ověřovacím klíčem. | Ne       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Zaregistrujte vlastní hostovaný uzel runtime integrace se zadaným ověřovacím klíčem a názvem uzlu. | Ne       |
| **Povolit vzdálený přístup** "`<port>`" ["`<thumbprint>`"]            | Povolte vzdálený přístup na aktuálním uzlu a nastavte cluster s vysokou dostupností. Nebo povolte nastavení přihlašovacích údajů přímo proti infračervenému počítači hostovanému sami sebou, aniž byste museli procházet Azure Data Factory. Můžete provést pomocí **rutiny New-AzDataFactoryV2LinkedServiceEncryptedCredential** ze vzdáleného počítače ve stejné síti. | Ne       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Povolte vzdálený přístup k aktuálnímu uzlu při spuštění uzlu v kontejneru. | Ne       |
| **Zakázat vzdálený přístup**                                         | Zakažte vzdálený přístup k aktuálnímu uzlu. Pro nastavení víceuzlů je nutný vzdálený přístup. Rutina **New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell stále funguje, i když je zakázán vzdálený přístup. Toto chování je pravda, pokud rutina je spuštěna na stejném počítači jako uzel IND Hosted s vlastním hostitelem. | Ne       |
| **Klíč** "`<AuthenticationKey>`"                                 | Přepište nebo aktualizujte předchozí ověřovací klíč. Buďte opatrní s touto akcí. Předchozí samoobslužný infračervený uzel může přejít do offline, pokud je klíč nového integračního běhu. | Ne       |
| **GenerateBackupFile** "`<filePath>`" "`<password>`"            | Vygenerujte záložní soubor pro aktuální uzel. Záložní soubor obsahuje klíč uzlu a přihlašovací údaje pro ukládání dat. | Ne       |
| **Soubor ImportBackupFile** "`<filePath>`" "`<password>`"              | Obnovte uzel ze záložního souboru.                          | Ne       |
| **Restartování**                                                     | Restartujte hostitelskou službu runtime integrace s vlastním hostitelem.   | Ne       |
| **Zahájení**                                                       | Spusťte hostitelskou službu runtime integrace s vlastním hostitelem.     | Ne       |
| **Zastavit**                                                        | Zastavte hostitelskou službu runtime integrace s vlastním hostitelem.        | Ne       |
| **StartUpgradeService**                                         | Spusťte službu runtime integračního prostředí s vlastním hostitelem.       | Ne       |
| **Služba StopUpgradeService**                                          | Zastavte službu runtime integračního prostředí s vlastním hostitelem.        | Ne       |
| **TurnonAutoUpdate**                                            | Zapněte automatickou aktualizaci runtime s vlastním hostitelem.        | Ne       |
| **TurnoffAutoUpdate**                                           | Vypněte automatickou aktualizaci runtime s vlastním hostitelem.       | Ne       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Nastavte DIAHostService spustit jako nový účet. Použijte prázdné heslo "" pro systémové účty a virtuální účty. | Ne       |


## <a name="command-flow-and-data-flow"></a>Tok příkazů a tok dat

Když přesouváte data mezi místním a cloudem, aktivita používá runtime integrace s vlastním hostitelem k přenosu dat mezi místním zdrojem dat a cloudem.

Zde je souhrn na vysoké úrovni kroků toku dat pro kopírování pomocí infračerveného přenosu hostovaného samostatně:

![Přehled toku dat na vysoké úrovni](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Vývojář dat vytvoří runtime integrace s vlastním hostitelem v rámci azure datové továrny pomocí rutiny Prostředí PowerShell. V současné době portál Azure nepodporuje tuto funkci.
1. Vývojář dat vytvoří propojenou službu pro místní úložiště dat. Vývojář tak činí zadáním runtime instance integrace s vlastním hostitelem, kterou by služba měla použít k připojení k úložištím dat.
1. Vlastní hostovaný uzel runtime integrace šifruje pověření pomocí windows data protection aplikační programovací rozhraní (DPAPI) a uloží pověření místně. Pokud je pro vysokou dostupnost nastaveno více uzlů, jsou pověření dále synchronizována mezi ostatními uzly. Každý uzel šifruje pověření pomocí DPAPI a ukládá je místně. Synchronizace pověření je transparentní pro vývojáře dat a je zpracována samoobslužnou infračerveným přenosem.
1. Azure Data Factory komunikuje s runtime integrace s vlastním hostitelem a plánuje a spravuje úlohy. Komunikace probíhá prostřednictvím řídicího kanálu, který používá sdílené připojení [Azure Service Bus Relay.](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) Při spuštění úlohy aktivity data factory fronty požadavek spolu s informacemi o pověření. Činí tak v případě, že přihlašovací údaje již nejsou uloženy v prostředí runtime integrace s vlastním hostitelem. Vlastní hostované integrace za běhu spustí úlohu poté, co dotazování fronty.
1. Prostředí runtime integrace s vlastním hostitelem kopíruje data mezi místním úložištěm a cloudovým úložištěm. Směr kopírování závisí na tom, jak je aktivita kopírování konfigurována v datovém kanálu. V tomto kroku prostředí runtime integrace s vlastním hostitelem přímo komunikuje se službami cloudového úložiště, jako je úložiště objektů blob Azure přes zabezpečený kanál HTTPS.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Důležité informace o použití infračerveného zápisu hostovaného samostatně

- Můžete použít jeden runtime integrace s vlastním hostitelem pro více místních zdrojů dat. Můžete ji také sdílet s jinou továrně dat v rámci stejného klienta Azure Active Directory (Azure AD). Další informace naleznete [v tématu Sdílení prostředí runtime integrace s vlastním hostitelem](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Do libovolného počítače můžete nainstalovat pouze jednu instanci prostředí integrace s vlastním hostitelem. Pokud máte dvě továrny na data, které potřebují přístup k místním zdrojům dat, použijte [funkci sdílení infračerveného přenosového zařízení s vlastním hostitelem](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) ke sdílení infračerveného počítače s vlastním hostitelem nebo nainstalujte infračervený přenos s vlastním hostitelem do dvou místních počítačů, jeden pro každou datovou továrnu.  
- Runtime integrace s vlastním hostitelem nemusí být ve stejném počítači jako zdroj dat. Však s vlastní hostované integrace runtime v blízkosti zdroje dat snižuje čas pro vlastní hostované integrace runtime pro připojení ke zdroji dat. Doporučujeme nainstalovat runtime integrace s vlastním hostitelem na počítači, který se liší od toho, který hostuje místní zdroj dat. Když se na různých počítačích nacházejí vlastní integrační runtime a zdroj dat, prostředí runtime integrace hostované sama o sobě nekonkuruje zdroji dat pro prostředky.
- Můžete mít více runtimes integrace s vlastním hostitelem na různých počítačích, které se připojují ke stejnému místnímu zdroji dat. Například pokud máte dva runtimes integrace s vlastním hostitelem, které slouží dvě továrny na data, stejný místní zdroj dat lze zaregistrovat u obou datových továren.
- Pokud už máte v počítači nainstalovanou bránu, která slouží scénáři Power BI, nainstalujte do jiného počítače samostatný runtime integrace s vlastním hostitelem pro Data Factory.
- Použijte runtime integrace s vlastním hostitelem pro podporu integrace dat v rámci virtuální sítě Azure.
- Zacházejte se zdrojem dat jako s místním zdrojem dat, který je za bránou firewall, i když používáte Azure ExpressRoute. Pomocí runtime integrace s vlastním hostitelem připojte službu ke zdroji dat.
- Použijte runtime integrace s vlastním hostitelem, i když je úložiště dat v cloudu na virtuálním počítači Azure Infrastructure as a Service (IaaS).
- Úlohy se mohou selhat v prostředí runtime integrace s vlastním hostitelem, který jste nainstalovali na server se systémem Windows, pro který je povoleno šifrování kompatibilní s FIPS. Chcete-li tento problém vyřešit, zakažte šifrování kompatibilní s FIPS na serveru. Chcete-li zakázat šifrování kompatibilní s FIPS, změňte hodnotu následujícího podklíče registru z 1 (povoleno) na 0 (zakázáno): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Požadavky

- Podporované verze systému Windows jsou:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   Instalace modulu runtime integrace s vlastním hostitelem na řadiči domény není podporována.
- Rozhraní .NET Framework 4.6.1 nebo novější je povinné. Pokud instalujete runtime integrace s vlastním hostitelem do počítače se systémem Windows 7, nainstalujte rozhraní .NET Framework 4.6.1 nebo novější. Podrobnosti naleznete [v části Požadavky na systém rozhraní .NET Framework.](/dotnet/framework/get-started/system-requirements)
- Doporučená minimální konfigurace pro zařízení runtime integrace s vlastním hostitelem je procesor 2 GHz se 4 jádry, 8 GB paměti RAM a 80 GB dostupného místa na pevném disku.
- Pokud hostitelský počítač přejde do režimu spánku, prostředí runtime integrace s vlastním hostitelem nereaguje na požadavky na data. Před instalací prostředí runtime integrace s vlastním hostitelem nakonfigurujte v počítači vhodné plán napájení. Pokud je počítač nakonfigurován pro hibernaci, zobrazí se zpráva s vlastní integrací za běhu.
- Chcete-li úspěšně nainstalovat a nakonfigurovat prostředí runtime integrace s vlastním hostitelem, musíte být správcem počítače.
- Spuštění aktivity kopírování se provádí s určitou frekvencí. Využití procesoru a paměti RAM na počítači se řídí stejným vzorem s maximálními a nečinné časy. Využití prostředků také závisí do značné míry na množství dat, která je přesunuta. Pokud probíhá více úloh kopírování, zobrazí se zvýšení využití prostředků v době špičky.
- Úlohy může selhat při extrakci dat ve formátech Parkety, ORC nebo Avro. Další informace o parketách najdete [v tématu Formát parket v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime). Vytváření souborů běží na počítači integrace s vlastním hostitelem. Chcete-li pracovat podle očekávání, vytvoření souboru vyžaduje následující požadavky:
    - [Redistribuovatelné vizuální c++ 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Balení (x64)
    - Java Runtime (JRE) verze 8 od poskytovatele JRE, jako je [přijmout OpenJDK](https://adoptopenjdk.net/). Ujistěte `JAVA_HOME` se, že je nastavena proměnná prostředí.

## <a name="installation-best-practices"></a>Osvědčené postupy instalace

Dobu instalace integrace s vlastním hostitelem můžete nainstalovat stažením instalačního balíčku spravované identity ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=39717). Podrobné pokyny najdete v článku [Přesun dat mezi místním a cloudem.](tutorial-hybrid-copy-powershell.md)

- Nakonfigurujte plán napájení na hostitelském počítači pro prostředí runtime integrace s vlastním hostitelem tak, aby počítač nepřezimoval režim spánku. Pokud hostitelský počítač přejde do režimu spánku, prostředí runtime integrace hostované na vlastní uživatele přejde do režimu offline.
- Pravidelně zálohujte pověření přidružená k prostředí runtime integrace s vlastním hostitelem.
- Chcete-li automatizovat operace instalace infračerveného zařízení hostovaného na vlastním hospo, přečtěte si informace o [nastavení prostřednictvím prostředí PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instalace a registrace samoobslužné infračervené odpočinky ze služby Stažení softwaru ze služby Microsoft Download Center

1. Přejděte na [stránku pro stažení za běhu integrace společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
1. Vyberte **Stáhnout**, vyberte 64bitovou verzi a vyberte **Další**. 32bitová verze není podporována.
1. Spusťte soubor spravované identity přímo nebo jej uložte na pevný disk a spusťte jej.
1. V **uvítacím** okně vyberte jazyk a vyberte **Další**.
1. Přijměte licenční podmínky pro software společnosti Microsoft a vyberte **možnost Další**.
1. Vyberte **složku,** chcete-li nainstalovat prostředí runtime integrace s vlastním hostitelem, a vyberte **Další**.
1. Na stránce **Připraveno k instalaci** vyberte **Instalovat**.
1. Chcete-li dokončit instalaci, vyberte **možnost Dokončit.**
1. Získejte ověřovací klíč pomocí PowerShellu. Tady je příklad Prostředí PowerShell pro načtení ověřovacího klíče:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. V okně **Register Integration Runtime (Self-hosted)** nástroje Microsoft Integration Runtime Configuration Manager spuštěného na vašem počítači postupujte takto:

    1. Vložte ověřovací klíč do textové oblasti.

    1. Volitelně můžete text klíče zobrazit tak, že vyberte **Zobrazit ověřovací klíč.**

    1. Vyberte **Zaregistrovat**.

## <a name="high-availability-and-scalability"></a>Vysoká dostupnost a škálovatelnost

Runtime integrace s vlastním hostitelem můžete přidružit k více místním počítačům nebo virtuálním počítačům v Azure. Tyto počítače se nazývají uzly. Můžete mít až čtyři uzly přidružené k prostředí runtime integrace s vlastním hostitelem. Výhody, které mají více uzlů v místních počítačích, které mají bránu nainstalovanou pro logickou bránu jsou:

* Vyšší dostupnost runtime integrace s vlastním hostitelem, takže už není jediným bodem selhání ve vašem řešení velkých objemů dat nebo integraci cloudových dat s Aplikací Dat. Tato dostupnost pomáhá zajistit kontinuitu při použití až čtyř uzlů.
* Zlepšený výkon a propustnost při přesunu dat mezi místními a cloudovými úložišti dat. Získejte další informace o [porovnání výkonu](copy-activity-performance.md).

Více uzlů můžete přidružit instalací softwaru runtime integrace s vlastním hostitelem ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=39717). Potom zaregistrujte pomocí některého z ověřovacích klíčů, které byly získány z rutiny **New-AzDataFactoryV2IntegrationRuntimeKey,** jak je popsáno v [kurzu](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Není nutné vytvořit nový runtime integrace s vlastním hostitelem pro přidružení každého uzlu. Runtime integrace s vlastním hostitelem můžete nainstalovat do jiného počítače a zaregistrovat pomocí stejného ověřovacího klíče.

> [!NOTE]
> Před přidáním dalšího uzlu pro vysokou dostupnost a škálovatelnost se ujistěte, že je v prvním uzlu povolena možnost **Vzdálený přístup k intranetu.** Chcete-li tak učinit, vyberte**možnost Nastavení** >  **nástroje Microsoft Integration Runtime Configuration Manager** > **Vzdálený přístup k intranetu**.

### <a name="scale-considerations"></a>Důležité informace o měřítku

#### <a name="scale-out"></a>Horizontální navýšení kapacity

Pokud je využití procesoru vysoké a dostupná paměť je nízká na samoobslužné infračervené ovládání, přidejte nový uzel, který vám pomůže horizontální navýšení kapacity zatížení napříč počítači. Pokud aktivity nezdaří, protože časový výběh nebo samoobslužný infračervený uzel je offline, pomůže, pokud přidáte uzel do brány.

#### <a name="scale-up"></a>Vertikální navýšení kapacity

Pokud procesor a dostupná paměť RAM nejsou dobře využity, ale provádění souběžných úloh dosáhne limitů uzlu, navýšit kapacitu zvýšením počtu souběžných úloh, které uzel může spustit. Můžete také navýšit kapacitu, když aktivity časový čas, protože je přetížena refračerveného přenosové rozlišení s vlastním hostitelem. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacitu uzlu:  

![Zvýšení počtu souběžných úloh, které lze spustit v uzlu](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL

Zde jsou požadavky na certifikát TLS/SSL, který používáte k zabezpečení komunikace mezi integračními runtime uzly:

- Certifikát musí být veřejně důvěryhodný certifikát X509 v3. Doporučujeme používat certifikáty vydané certifikačníautoritou veřejného partnera ( CA).
- Každý uzel prostředí runtime integrace musí důvěřovat tomuto certifikátu.
- Certifikáty alternativního názvu subjektu (SAN) nedoporučujeme, protože se používá pouze poslední položka sítě SAN. Všechny ostatní položky sítě SAN jsou ignorovány. Máte-li například certifikát SAN, jehož sítě SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete tento certifikát použít pouze v počítači, jehož plně kvalifikovaný název domény (Plně kvalifikovaný název domény) je **node2.domain.contoso.com**.
- Certifikát může používat libovolnou velikost klíče podporovanou systémem Windows Server 2012 R2 pro certifikáty TLS/SSL.
- Certifikáty, které používají klíče CNG, nejsou podporovány.  

> [!NOTE]
> Tento certifikát se používá:
>
> - Šifrování portů v samoobslužném infračerveném uzlu.
> - Pro komunikaci mezi uzly pro synchronizaci stavu, která zahrnuje synchronizaci pověření propojených služeb mezi uzly.
> - Pokud se rutina prostředí PowerShell používá pro nastavení pověření propojené služby z místní sítě.
>
> Tento certifikát doporučujeme použít, pokud vaše privátní síťové prostředí není zabezpečené nebo pokud chcete zabezpečit komunikaci mezi uzly v rámci privátní sítě.
>
> Přesun dat při přenosu z infračerveného přenosu s vlastním hostitelem do jiných úložišť dat probíhá vždy v rámci šifrovaného kanálu, bez ohledu na to, zda je tento certifikát nastaven.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Vytvoření sdíleného runtime integrace s vlastním hostitelem v Azure Data Factory

Můžete znovu použít existující infrastrukturu runtime integrace s vlastním hostitelem, kterou jste již nastavili v datové továrně. Toto opakované použití umožňuje vytvořit propojený za běhu integrace s vlastním hostitelem v jiné datové továrně odkazem na existující sdílenou samoobslužnou infračervený přenos.

Chcete-li zobrazit úvod a ukázku této funkce, podívejte se na následující 12minutové video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Sdílené infračervené ovládání**: Původní samoobslužná infračervená infračervená zkušená indiční, která běží na fyzické infrastruktuře.  
- **Propojená infračervená infračervená**infračervená infračervená infračervená infračervená zinspozicí, která odkazuje na jinou sdílenou infračervený Propojená infračervená infračervená infračervená infračervená infračervená infračervená infračervená zinsku a využívá infrastrukturu jiné sdílené infračervené hodované samostatně.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metody sdílení vlastního integračního běhu

Podrobnosti najdete v tématu Vytvoření sdíleného runtime integrace s vlastním hostitelem s více datovými továrnami v [tématu Vytvoření sdíleného prostředí runtime integrace s vlastním hostitelem.](create-shared-self-hosted-integration-runtime-powershell.md)

### <a name="monitoring"></a>Monitorování

#### <a name="shared-ir"></a>Sdílené infračervené hod

![Výběry pro nalezení za běhu sdílené integrace](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Sledování za běhu sdílené integrace](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Propojená infračervená infračervená

![Výběry pro nalezení propojenéintegrace runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Sledování propojeného integračního běhu](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Známá omezení sdílení infračervených souborů hostovaného samostatně

* Továrna dat, ve které je vytvořena propojená infračervená infračervená, musí mít [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ve výchozím nastavení mají datové továrny vytvořené na webu Azure Portal nebo rutinách PowerShellu implicitně vytvořenou spravovanou identitu. Ale když se vytvoří továrna na data prostřednictvím šablony Azure Resource Manager nebo Sady SDK, musíte explicitně nastavit vlastnost **Identity.** Toto nastavení zajišťuje, že Správce prostředků vytvoří továrnu dat, která obsahuje spravovanou identitu.

* Sada Data Factory .NET SDK, která tuto funkci podporuje, musí být verze 1.1.0 nebo novější.

* Chcete-li udělit oprávnění, potřebujete roli vlastníka nebo zděděnou roli vlastníka v datové továrně, kde existuje sdílená infračervená infračervená infračervená infračervená

* Funkce sdílení funguje jenom pro továrny na data v rámci stejného klienta Azure AD.

* Pro uživatele [typu Host](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)Azure AD , funkce vyhledávání v uživatelském uzpo, které uvádí všechny továrny na data pomocí klíčového slova vyhledávání, [nefunguje](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Ale tak dlouho, dokud uživatel hosta je vlastníkem datové továrny, můžete sdílet infračervený přenos bez funkce vyhledávání. Pro spravovanou identitu datové továrny, která potřebuje sdílet infračervený přenos, zadejte tuto spravovanou identitu do pole **Přiřadit oprávnění** a vyberte **Přidat** v unovém uzdu datové továrny.

  > [!NOTE]
  > Tato funkce je k dispozici pouze v datové továrně V2.

## <a name="notification-area-icons-and-notifications"></a>Ikony a oznámení ooznamovací oblasti

Pokud přesunete kurzor nad ikonu nebo zprávu v oznamovací oblasti, zobrazí se podrobnosti o stavu prostředí runtime integrace s vlastním hostitelem.

![Oznámení v oznamovací oblasti](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Porty a brány firewall

Existují dva brány firewall, které je třeba zvážit:

- *Podniková brána firewall* spuštěná na centrálním směrovači organizace
- Brána *firewall systému Windows,* která je nakonfigurována jako daemon v místním počítači, kde je nainstalován alokovaný integrační runtime

![Firewally](media/create-self-hosted-integration-runtime/firewall.png)

Na podnikové úrovni brány firewall je třeba nakonfigurovat následující domény a odchozí porty:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

Na úrovni brány firewall systému Windows nebo počítače jsou tyto odchozí porty obvykle povoleny. Pokud tomu tak není, můžete nakonfigurovat domény a porty v počítači runtime integrace s vlastním hostitelem.

> [!NOTE]
> Na základě zdroje a propadů může být nutné povolit další domény a odchozí porty v podnikové bráně firewall nebo bráně firewall systému Windows.
>
> U některých cloudových databází, jako je Azure SQL Database a Azure Data Lake, možná budete muset povolit IP adresy runtime počítačů s vlastním hostitelem integrace v jejich konfiguraci brány firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopírování dat ze zdroje do jímky

Ujistěte se, že správně povolíte pravidla brány firewall na podnikové bráně firewall, bránu firewall systému Windows v počítači runtime integrace s vlastním hostitelem a samotné úložiště dat. Povolení těchto pravidel umožňuje prostředí runtime integrace s vlastním hostitelem, který se úspěšně připojí ke zdroji i jímce. Povolte pravidla pro každé úložiště dat, které je součástí operace kopírování.

Chcete-li například zkopírovat z místního úložiště dat do jímky databáze SQL nebo jímky datového skladu Azure SQL, postupujte takto:

1. Povolte odchozí komunikaci TCP na portu 1433 pro bránu firewall systému Windows i podnikovou bránu firewall.
1. Nakonfigurujte nastavení brány firewall databáze SQL a přidejte ip adresu počítače runtime integrace s vlastním hostitelem do seznamu povolených adres IP.

> [!NOTE]
> Pokud brána firewall neumožňuje odchozí port 1433, modul runtime integrace s vlastním hostitelem nemá přímý přístup k databázi SQL. V takovém případě můžete použít [fázovanou kopii](copy-activity-performance.md) do databáze SQL a datového skladu SQL. V tomto scénáři vyžadujete pouze HTTPS (port 443) pro přesun dat.

## <a name="proxy-server-considerations"></a>Důležité informace o serveru proxy

Pokud vaše podnikové síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte prostředí integrace s vlastním hostitelem tak, aby používal příslušné nastavení proxy serveru. Proxy server můžete nastavit během počáteční fáze registrace.

![Určení proxy serveru](media/create-self-hosted-integration-runtime/specify-proxy.png)

Při konfiguraci používá prostředí runtime integrace s vlastním hostitelem proxy server pro připojení ke zdroji a cíli cloudové služby (které používají protokol HTTP nebo HTTPS). Proto vyberete **změnit odkaz** během počátečního nastavení.

![Nastavení proxy serveru](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existují tři možnosti konfigurace:

- **Nepoužívejte proxy server**: Prostředí runtime integrace s vlastním hostitelem explicitně nepoužívá žádný proxy server pro připojení ke cloudovým službám.
- **Použít systémový proxy**server : Prostředí runtime integrace s vlastním hostitelem používá nastavení proxy serveru, které je nakonfigurováno v souborech diahost.exe.config a diawp.exe.config. Pokud tyto soubory neurčují žádnou konfiguraci proxy serveru, prostředí runtime integrace s vlastním hostitelem se připojí ke cloudové službě přímo, aniž by procházelo proxy serverem.
- **Použití vlastního proxy serveru**: Nakonfigurujte nastavení proxy protokolu HTTP pro prostředí runtime integrace s vlastním hostitelem namísto použití konfigurací v souborech diahost.exe.config a diawp.exe.config. **Jsou vyžadovány** hodnoty adresy a **portu.** **Hodnoty uživatelského jména** a **hesla** jsou volitelné v závislosti na nastavení ověřování serveru proxy. Všechna nastavení jsou šifrována pomocí systému Windows DPAPI v prostředí runtime integrace s vlastním hostitelem a uložena místně v počítači.

Hostitelská služba integračního běhu se po uložení aktualizovaného nastavení proxy automaticky restartuje.

Po registraci runtime integrace s vlastním hostitelem, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte Microsoft Integration Runtime Configuration Manager.

1. Spusťte **nástroj Microsoft Integration Runtime Configuration Manager**.
1. Vyberte kartu **Settings** (Nastavení).
1. V části **HTTP Proxy**vyberte odkaz **Změnit** a otevřete dialogové okno Nastavit **http proxy.**
1. Vyberte **Další**. Potom se zobrazí upozornění, které vás požádá o oprávnění k uložení nastavení proxy serveru a restartování služby hostitele za běhu integračního běhu.

Pomocí nástroje správce konfigurace můžete zobrazit a aktualizovat proxy server HTTP.

![Zobrazení a aktualizace proxy serveru](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Pokud nastavíte proxy server s ověřováním NTLM, bude hostitelská služba integračního běhu spuštěna pod účtem domény. Pokud později změníte heslo pro účet domény, nezapomeňte aktualizovat nastavení konfigurace služby a restartovat službu. Z důvodu tohoto požadavku doporučujeme přístup k proxy serveru pomocí účtu vyhrazené domény, který nevyžaduje časté aktualizace hesla.

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení serveru proxy

Pokud vyberete možnost **Použít systémový proxy** server pro proxy server HTTP, použije prostředí runtime integrace s vlastním hostitelem nastavení proxy serveru v souborech diahost.exe.config a diawp.exe.config. Pokud tyto soubory neurčují žádný proxy server, prostředí runtime integrace s vlastním hostitelem se připojí ke cloudové službě přímo, aniž by procházelo proxy serverem. Následující postup obsahuje pokyny pro aktualizaci souboru diahost.exe.config:

1. V Průzkumníkovi souborů vytvořte bezpečnou kopii souboru C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config jako zálohy původního souboru.
1. Otevřete poznámkový blok spuštěný jako správce.
1. V programu Poznámkový blok otevřete textový soubor C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config.
1. Najděte výchozí **značku system.net,** jak je znázorněno v následujícím kódu:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Potom můžete přidat podrobnosti o serveru proxy, jak je znázorněno v následujícím příkladu:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Značka proxy umožňuje další vlastnosti `scriptLocation`určit požadovaná nastavení, jako je . Syntaxi naleznete [ \<v části Element\> proxy (Nastavení sítě).](https://msdn.microsoft.com/library/sa91de1e.aspx)

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Uložte konfigurační soubor do původního umístění. Potom restartujte hostitelskou službu runtime integrace s vlastním hostitelem, která snímá změny.

   Chcete-li službu restartovat, použijte aplet služeb z Ovládacích panelů. Nebo z nástroje Integration Runtime Configuration Manager vyberte tlačítko **Zastavit službu** a pak vyberte **Spustit službu**.

   Pokud se služba nespustí, pravděpodobně jste do konfiguračního souboru aplikace, který jste upravili, přidali nesprávnou syntaxi tagů XML.

> [!IMPORTANT]
> Nezapomeňte aktualizovat jak diahost.exe.config, tak diawp.exe.config.

Musíte se také ujistit, že Microsoft Azure je v seznamu povolených vašich společností. Seznam platných IP adres Azure si můžete stáhnout ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Možné příznaky problémů souvisejících s bránou firewall a proxy serverem

Pokud se zobrazí chybové zprávy, jako jsou následující, pravděpodobným důvodem je nesprávná konfigurace brány firewall nebo proxy serveru. Tato konfigurace zabraňuje runtime integrace s vlastním hostitelem v připojení k datové továrně a ověřit se. Chcete-li zajistit správnou konfiguraci brány firewall a serveru proxy, přečtěte si předchozí část.

* Při pokusu o registraci runtime integrace s vlastním hostitelem se zobrazí následující chybová zpráva: "Nepodařilo se zaregistrovat tento uzel integračního běhu! Zkontrolujte, zda je ověřovací klíč platný a že v tomto počítači běží hostitelská služba integrační služby."
* Při spuštění nástroje Integration Runtime Configuration Manager se zobrazí stav **Odpojeno** nebo **Připojení**. Při zobrazení protokolů událostí systému Windows se v**části Protokoly** > aplikací a služeb **Prohlížeče** > událostí microsoft**Integration Runtime**zobrazí chybové zprávy, jako je tento:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Povolení vzdáleného přístupu z intranetu

Pokud používáte Prostředí PowerShell k šifrování pověření z jiného síťového počítače, než kde jste nainstalovali prostředí runtime integrace s vlastním hostitelem, můžete povolit možnost **Vzdálený přístup z intranetu.** Pokud spustíte Prostředí PowerShell k šifrování přihlašovacích údajů v počítači, ve kterém jste nainstalovali prostředí runtime integrace s vlastním hostitelem, nemůžete povolit **vzdálený přístup z intranetu**.

Před přidáním dalšího uzlu pro vysokou dostupnost a škálovatelnost povolte **vzdálený přístup z intranetu.**  

Při spuštění instalace runtime integrace s vlastním hostitelem verze 3.3 nebo novější ve výchozím nastavení zakáže instalační program runtime integrace s vlastním hostitelem **vzdálený přístup z intranetu** v počítači runtime integrace s vlastním hostitelem.

Používáte-li bránu firewall od partnera nebo jiného partnera, můžete ručně otevřít port 8060 nebo uživatelem nakonfigurovaný port. Pokud máte problém s bránou firewall při nastavování prostředí runtime integrace s vlastním hostitelem, nainstalujte pomocí následujícího příkazu prostředí runtime integrace s vlastním hostitelem bez konfigurace brány firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Pokud se rozhodnete neotevírat port 8060 v počítači runtime integrace s vlastním hostitelem, použijte ke konfiguraci přihlašovacích údajů pro ukládání dat jiné mechanismy než aplikaci Nastavení pověření. Můžete například použít rutinu **Prostředí PowerShell New-AzDataFactoryV2LinkedServiceEncryptCredential.**

## <a name="next-steps"></a>Další kroky

Podrobné pokyny najdete v [tématu Výuka: Kopírování místních dat do cloudu](tutorial-hybrid-copy-powershell.md).
