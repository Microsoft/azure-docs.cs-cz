---
title: Konfigurace prostředí runtime integrace s vlastním hostitelem jako proxy serveru pro SSIS
description: Zjistěte, jak nakonfigurovat runtime integrace s vlastním hostitelem jako proxy pro runtime integrace Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4cb5b84f3889dcf4e0f28d525afb42cfeac5b54c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605494"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurace infračerveného serveru hostovaného s vlastním hostitelem jako proxy serveru pro infračervený přenos Azure-SSIS v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak spustit balíčky sql server integration services (SSIS) na Modul runtime integrace Azure-SSIS (Azure-SSIS IR) v Azure Data Factory s runtime integrace s vlastním hostitelem (samoobslužné IR) nakonfigurované jako proxy server. 

Pomocí této funkce můžete přistupovat k místním datům, aniž byste museli [připojit infračervený přenos Azure-SSIS k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Tato funkce je užitečná, když vaše podniková síť má konfiguraci příliš složitou nebo zásadu příliš omezující pro vložení zařízení Azure-SSIS IR do ní.

Tato funkce rozdělí všechny úlohy toku dat SSIS, který má místní zdroj dat, na dvě pracovní úlohy: 
* První úloha, která běží na vašem infračerveného serveru s vlastním hostitelem, nejprve přesune data z místního zdroje dat do pracovní oblasti ve vašem úložišti objektů Blob Azure.
* Druhá úloha, která běží na Azure-SSIS IR, pak přesune data z pracovní oblasti do zamýšleného cíle dat.

Další výhody a možnosti této funkce umožňují například nastavit infračervený přenos hostovaný s vlastním hostitelem v oblastech, které ještě nejsou podporovány službou Azure-SSIS IR, a povolit veřejnou statickou IP adresu vašeho infračerveného zařízení hostovaného samostatně na bráně firewall vašich zdrojů dat.

## <a name="prepare-the-self-hosted-ir"></a>Příprava infračerveného zápisu hostovaného samostatně

Chcete-li použít tuto funkci, nejprve vytvořte továrnu dat a nastavte v ní IndIzí Azure-SSIS. Pokud jste tak ještě neučinili, postupujte podle pokynů v [části Nastavení indistru(Azure-SSIS IR).](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

Potom nastavit vlastní hostované infračervené ovládání ve stejné datové továrně, kde je nastavena vaše Azure-SSIS Ir. Chcete-li tak učinit, naleznete [v tématu Vytvoření infračerveného souboru s vlastním hostitelem](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Nakonec si stáhnete a nainstalujete nejnovější verzi infračerveného zařízení hostovaného na vlastním základě, stejně jako další ovladače a runtime, do místního počítače nebo virtuálního počítače Azure (VM), a to takto:
- Stáhněte a nainstalujte nejnovější verzi [samoobslužného infračerveného systému](https://www.microsoft.com/download/details.aspx?id=39717).
- Pokud ve svých balíčcích používáte konektory Object Linking and Embedding Database (OLEDB), stáhněte a nainstalujte příslušné ovladače OLEDB na stejný počítač, ve kterém je nainstalována vaše infračervená infračervená verze, pokud jste tak ještě neučinili.  

  Pokud používáte starší verzi ovladače OLEDB pro SQL Server (SQL Server Native Client [SQLNCLI]), [stáhněte 64bitovou verzi](https://www.microsoft.com/download/details.aspx?id=50402).  

  Pokud používáte nejnovější verzi ovladače OLEDB pro SQL Server (MSOLEDBSQL), [stáhněte si 64bitovou verzi](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Pokud používáte ovladače OLEDB pro jiné databázové systémy, jako je PostgreSQL, MySQL, Oracle a tak dále, můžete si stáhnout 64bitové verze z jejich webových stránek.
- Pokud jste tak ještě neučinili, [stáhněte a nainstalujte 64bitovou verzi runtime Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) do stejného počítače, ve kterém je nainstalována vaše infračervená zručná infračervená verze.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Příprava služby propojené s úložištěm objektů blob Azure pro přípravu

Pokud jste tak ještě neučinili, vytvořte službu propojenou s úložištěm Azure blob ve stejné datové továrně, kde je nastavena infračervená infračervená zisu Azure-SSIS. Pokud tak chcete provést, najdete [v tématu vytvoření služby propojené s azure data factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Ujistěte se, že děláte následující kroky:
- V **případě úložiště dat**vyberte Azure **Blob Storage**.  
- Pro **připojení prostřednictvím integračního běhu**vyberte Možnost **AutoResolveIntegrationRuntime** (ne azure-SSIS IR ani vaše infračervená zručná infračervená zem), protože k načtení přístupových přihlašovacích údajů pro úložiště objektů blob Azure používáme výchozí zařízení Azure IR.
- V **případě metody Authentication**vyberte klíč **účtu**, **identifikátor URI**nebo **instanční objekt .**  

    >[!TIP]
    >Pokud vyberete metodu **Instanční objekt,** udělte instančnímu objektu alespoň roli *přispěvatele dat objektu blob* úložiště. Další informace najdete v tom, že konektor [úložiště objektů blob Azure](connector-azure-blob-storage.md#linked-service-properties).

![Příprava služby propojené s úložištěm objektů blob Azure pro přípravu](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurace infračerveného serveru Azure-SSIS s vlastním infračerveným přenosem jako proxy serverem

Po přípravě služby INL s vlastním hostitelem a služby propojené úložištěm Azure blob pro pracovní přípravu teď můžete nakonfigurovat novou nebo stávající službu Azure-SSIS IR s infračerveným přenosem hostovaným na vlastním základě jako proxy serverem na portálu nebo v aplikaci datové továrny. Než tak učiníte, pokud vaše stávající Azure-SSIS IR je již spuštěna, zastavte ji a restartujte ji.

1. V podokně **nastavení modulu runtime integrace** přeskočte části **Obecné nastavení** a Nastavení **SQL** výběrem možnosti **Další**. 

1. V části **Upřesnit nastavení** postupujte takto:

   1. Zaškrtněte **políčko Nastavit runtime vlastní hostované integrace jako proxy pro prostředí Runtime integrace Azure-SSIS.** 

   1. V rozevíracím seznamu **Runtime s vlastní hostovcí** vyberte existující infračervený přenos hostovaný s vlastním hostitelem jako proxy server pro službu Azure-SSIS IR.

   1. V rozevíracím seznamu **Propojené služby pracovní úložiště** vyberte existující službu propojenou úložištěm Azure blob nebo vytvořte novou pro pracovní.

   1. V poli **Pracovní cesta** zadejte kontejner objektů blob ve vybraném účtu úložiště objektů Blob Azure nebo ho nechte prázdné, aby se použil oteplit ý výchozí objekt pro přípravu.

   1. Vyberte **Pokračovat**.

   ![Pokročilá nastavení s infračerveným přenosem s vlastním hostitelem](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Můžete také nakonfigurovat nové nebo stávající Azure-SSIS Ir s vlastní hostované infračerveného serveru jako proxy pomocí Prostředí PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Povolit balíčky SSIS pro připojení prostřednictvím proxy serveru

Pomocí nejnovější ssdt s rozšířením SSIS Projekty pro Visual Studio nebo samostatný `ConnectByProxy` instalační program, můžete najít novou vlastnost, která byla přidána v OLEDB nebo flat file connection managers.
* [Stažení rozšíření SSDT s projekty SSIS pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Stažení samostatného instalačního programu](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Při návrhu nové balíčky, které obsahují úlohy toku dat s OLEDB nebo ploché zdroje souborů, které umožňují přístup k databázím nebo souborům v místním prostředí, můžete tuto vlastnost povolit nastavením na *True* v podokně **Vlastnosti** příslušných správců připojení.

![Povolit vlastnost ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Tuto vlastnost můžete také povolit při spuštění existujících balíčků, aniž byste je museli ručně měnit jeden po druhém.  Existují dvě možnosti:
- **Možnost A**: Otevřete, znovu vytvořte a znovu nasaďte projekt obsahující tyto balíčky s nejnovějším ssdt, který se spustí na zařízení Azure-SSIS IR. Vlastnost pak můžete povolit nastavením na *hodnotu True* pro příslušné správce připojení. Když jsou spuštěny balíčky z SSMS, tyto správce připojení se zobrazí na kartě **Správce připojení** **spustit balíček** rozbalovací okno.

  ![Povolení vlastnosti ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vlastnost můžete také povolit nastavením na *hodnotu True* pro příslušné správce připojení, které se zobrazí na kartě **Správci připojení** [spouštět aktivitu balíčku SSIS,](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) když spouštějí balíčky v kanálech Data Factory.
  
  ![Povolení vlastnosti ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Možnost B:** Znovu nasaďte projekt obsahující tyto balíčky, které mají být spuštěny na zařízení SSIS IR. Vlastnost pak můžete povolit poskytnutím `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`její cesty vlastnosti a jejím nastavením na *hodnotu True* jako přepsání vlastnosti na kartě **Upřesnit** v rozbalovacím okně **Spustit balíček,** když spouštíte balíčky ze služby SSMS.

  ![Povolení vlastnosti ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vlastnost můžete také povolit poskytnutím `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`její cesty vlastnosti a jejím nastavením na *hodnotu True* jako přepsání vlastnosti na kartě **Přepsání vlastností** [aktivity Spouštění balíčku SSIS,](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) když spouštíte balíčky v kanálech Data Factory.
  
  ![Povolení vlastnosti ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Ladění první a druhé pracovní úlohy

Na vlastním hostovaném infračerveném počítači najdete protokoly za běhu ve složce *C:\ProgramData\SSISTelemetry* a protokoly spuštění prvních pracovních úloh ve složce *C:\ProgramData\SSISTelemetry\ExecutionLog.*  Protokoly spuštění druhé pracovní úlohy v SSISDB nebo zadané cesty protokolování, v závislosti na tom, zda ukládáte balíčky v SSISDB nebo systému souborů, sdílené složky nebo soubory Azure. Můžete také najít jedinečné ID prvních pracovních úloh v protokolech provádění druhé pracovní úkoly. 

![Jedinečné ID první pracovní úlohy](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Použití ověřování systému Windows v pracovních úlohách

Pokud pracovní úlohy na samoobslužné infračervené službě vyžadují ověřování systému Windows, [nakonfigurujte balíčky SSIS tak, aby používaly stejné ověřování systému Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Pracovní úlohy budou vyvolány pomocí samoobslužného účtu infračervené služby (*NT SERVICE\DIAHostService*, ve výchozím nastavení) a k úložištím dat bude přistupovat pomocí ověřovacího účtu systému Windows. Oba účty vyžadují, aby jim byly přiřazeny určité zásady zabezpečení. V počítači infračervených zařízení s vlastním hostitelem přejděte na **místní zásady místních** > **zásad** > **Přiřazení uživatelských práv**a proveďte následující kroky:

1. *Přiřaďte upravit kvóty paměti pro proces* a nahradit zásady *tokenu na úrovni procesu* k účtu služby IND s vlastním hostitelem. K tomu by mělo dojít automaticky při instalaci samoobslužné infračervené hodu s výchozím účtem služby. Pokud tomu tak není, přiřaďte tyto zásady ručně. Pokud používáte jiný účet služby, přiřaďte mu stejné zásady.

1. Přiřaďte *zásadu Přihlášení jako zásadu služby* k účtu ověřování systému Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fakturace pro první a druhé pracovní úlohy

První pracovní úlohy, které běží na vlastní hostované infračervené ovládání se účtuje zvlášť, stejně jako všechny aktivity přesunu dat, které běží na samostatně hostované infračervené hod. To je určeno v článku [o cenách datového kanálu Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

Druhé pracovní úlohy, které běží na Azure-SSIS IR se neúčtují samostatně, ale vaše spuštěné Azure-SSIS IR se účtuje tak, jak je uvedeno v článku [o cenách Azure-SSIS IR.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Povolení TLS 1,2

Pokud potřebujete použít silnou kryptografii/bezpečnější síťový protokol (TLS 1.2) a zakázat starší verze SSL/TLS na vašem samoobslužném infračerveného zařízení, můžete stáhnout a spustit skript *main.cmd,* který najdete ve složce *CustomSetupScript/UserScenarios/TLS 1.2* našeho kontejneru public preview.  Pomocí [Průzkumníka úložiště Azure](https://storageexplorer.com/)se můžete připojit k našemu kontejneru public preview zadáním následujícího identifikátoru URI SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuální omezení

- V současné době jsou podporovány pouze úlohy toku dat s otevřeným připojením k databázi (ODBC)/OLEDB/Flat File nebo cílem OLEDB. 
- Momentálně jsou podporovány pouze služby propojené s úložištěm objektů blob Azure, které jsou nakonfigurované pomocí *klíče účtu*, *identifikátoru URI s podpisem sdíleného přístupu (SAS)* nebo ověřování *matnina instančního objektu.*
- *Mapování parametrů* ve zdroji OLEDB ještě není podporováno. Jako řešení použijte *příkaz SQL Command From Variable* jako *AccessMode* a použijte *expression* k vložení proměnných/parametrů do příkazu SQL. Jako obrázek naleznete *balíček ParameterMappingSample.dtsx,* který najdete ve složce *SelfHostedIRProxy/Limitations* v našem kontejneru public preview. Pomocí Průzkumníka úložiště Azure se můžete připojit k našemu kontejneru public preview zadáním výše uvedeného identifikátoru URI SAS.

## <a name="next-steps"></a>Další kroky

Po konfiguraci samoobslužné infračervené hodu jako proxy serveru pro infračervený přenos Azure-SSIS můžete nasadit a spustit balíčky pro přístup k místním datům jako aktivity spouštění balíčků SSIS v kanálech Data Factory. Postup najdete v [tématu Spuštění balíčků SSIS jako Spuštění aktivit balíčku SSIS v kanálech Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
