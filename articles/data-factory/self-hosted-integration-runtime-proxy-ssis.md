---
title: Konfigurace prostředí Integration runtime v místním prostředí jako proxy pro SSIS
description: Přečtěte si, jak nakonfigurovat Integration runtime v místním prostředí jako proxy server pro Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 5f393e95a7d468ea2c6130077e42b25b038e839d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376220"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurace prostředí IR v místním prostředí jako proxy serveru pro Azure-SSIS IR v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak spustit balíčky služba SSIS (SQL Server Integration Services) (SSIS) na Azure-SSIS Integration Runtime (Azure-SSIS IR) v Azure Data Factory pomocí modulu runtime integrace (v místním prostředí IR), který je nakonfigurovaný jako proxy. 

Pomocí této funkce můžete získat přístup k místním datům, aniž byste se museli [připojit k Azure-SSIS IR k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md). Tato funkce je užitečná v případě, že vaše podniková síť má příliš složitou konfiguraci nebo je zásada příliš omezující, takže se do ní Azure-SSIS IR vložit.

Tato funkce rozdělí úlohu toku dat SSIS na dvě pracovní úlohy, kdykoli je to možné: 
* **Místní pracovní úloha**: Tato úloha spustí komponentu toku dat, která se připojuje k místnímu úložišti dat na místním prostředí IR. Přesouvá data z místního úložiště dat do pracovní oblasti ve službě Azure Blob Storage nebo naopak.
* **Pracovní úloha cloudu**: Tato úloha spustí komponentu toku dat, která se nepřipojuje k místnímu úložišti dat na vašem Azure-SSIS IR. Přesouvá data z pracovní oblasti v úložišti objektů BLOB v Azure do cloudového úložiště dat nebo naopak.

Pokud Váš úkol toku dat přesouvá data z místního prostředí do cloudu, pak první a druhá pracovní úkoly budou místní a cloudové úlohy v uvedeném pořadí. Pokud Váš úkol toku dat přesouvá data z cloudu do místního prostředí, pak první a druhá pracovní úkoly budou cloudové a místní pracovní úkoly, v uvedeném pořadí. Pokud úloha toku dat přesouvá data z místního prostředí do místního počítače, první a druhá pracovní úkoly budou místní pracovní úlohy. Pokud úloha toku dat přesouvá data z cloudu do cloudu, tato funkce se nedá použít.

Další výhody a funkce této funkce umožňují například nastavit vaše místní prostředí IR v oblastech, které ještě nejsou podporované Azure-SSIS IR, a umožnit veřejnou statickou IP adresu místního prostředí IR v bráně firewall vašich zdrojů dat.

## <a name="prepare-the-self-hosted-ir"></a>Příprava prostředí IR pro místní hostování

Pokud chcete tuto funkci používat, musíte nejdřív vytvořit datovou továrnu a nastavit v ní Azure-SSIS IR. Pokud jste to ještě neudělali, postupujte podle pokynů v části [nastavení Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

Potom nastavíte místní prostředí IR ve stejné datové továrně, kde je nastavená vaše Azure-SSIS IR. Postup najdete v tématu [vytvoření prostředí IR](./create-self-hosted-integration-runtime.md)v místním prostředí.

Nakonec stáhnete a nainstalujete nejnovější verzi prostředí IR v místním prostředí a také další ovladače a modul runtime, na místním počítači nebo virtuálním počítači Azure (VM) následujícím způsobem:
- Stáhněte a nainstalujte si nejnovější verzi prostředí IR pro místní [hostování](https://www.microsoft.com/download/details.aspx?id=39717).
- Pokud použijete funkci propojování a vkládání databáze (OLEDB), rozhraní ODBC (Open Database Connectivity) nebo konektory ADO.NET v balíčcích, Stáhněte a nainstalujte příslušné ovladače do stejného počítače, ve kterém je nainstalováno prostředí IR pro místní hostování, pokud jste to ještě neudělali.  

  Pokud používáte starší verzi ovladače OLEDB pro SQL Server (SQL Server Native Client [SQLNCLI]), [Stáhněte si 64 verzi](https://www.microsoft.com/download/details.aspx?id=50402).  

  Pokud používáte nejnovější verzi ovladače OLEDB pro SQL Server (MSOLEDBSQL), [Stáhněte si 64 verzi](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Pokud používáte ovladače OLEDB/ODBC/ADO. NET pro další databázové systémy, jako jsou PostgreSQL, MySQL, Oracle a tak dále, můžete si stáhnout 64 verzí ze svých webů.
- Pokud jste to ještě neudělali, [Stáhněte a nainstalujte 64 Visual C++ (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) na stejném počítači, na kterém je nainstalované prostředí IR pro místní hostování.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Povolit ověřování systému Windows pro místní pracovní úkoly

Pokud místní pracovní úkoly v místním prostředí IR vyžadují ověřování systému Windows, [nakonfigurujte balíčky SSIS tak, aby používaly stejné ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). 

Vaše místní pracovní úkoly budou vyvolány s účtem služby IR v místním prostředí (*NT SERVICE\DIAHostService* ve výchozím nastavení) a vaše úložiště dat budou k dispozici s účtem ověřování systému Windows. Oba účty vyžadují, aby jim byly přiřazeny určité zásady zabezpečení. V místním počítači IR použijte místní zásady **zabezpečení**  >  **místní zásady**  >  **přiřazení uživatelských práv** a pak postupujte takto:

1. Přiřaďte *kvóty pro úpravu paměti pro proces* a *nahraďte zásady tokenu na úrovni procesu* na účet služby IR pro místní hostování. Tato situace by se měla provádět automaticky při instalaci místního prostředí IR s výchozím účtem služby. Pokud tomu tak není, přiřaďte tyto zásady ručně. Pokud používáte jiný účet služby, přiřaďte k němu stejné zásady.

1. Přiřaďte k účtu ověřování systému Windows zásadu *přihlášení jako služby* .

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Příprava propojené služby Azure Blob Storage pro přípravu

Pokud jste to ještě neudělali, vytvořte propojenou službu Azure Blob Storage ve stejné datové továrně, kde je nastavená vaše Azure-SSIS IR. Postup najdete v tématu [vytvoření propojené služby Azure Data Factory](./quickstart-create-data-factory-portal.md#create-a-linked-service). Nezapomeňte provést následující akce:
- V případě **úložiště dat** vyberte **Azure Blob Storage**.  
- Pro **připojení prostřednictvím prostředí Integration runtime** vyberte **AutoResolveIntegrationRuntime** (ne váš Azure-SSIS IR ani v místním prostředí IR), protože k získání přihlašovacích údajů pro přístup do Azure Blob Storage používáme výchozí Azure IR.
- V případě **metody ověřování** vyberte **klíč účtu**, **identifikátor URI SAS**, **instanční objekt** nebo **spravovanou identitu**.  

>[!TIP]
>Pokud vyberete metodu **instančního objektu** , udělte instančnímu objektu aspoň roli *Přispěvatel dat objektu BLOB služby Storage* . Další informace najdete v tématu [konektor Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties). Pokud vyberete metodu **spravované identity** , udělte svým službám pro přístup k Azure Blob Storage správné role spravované identitou ADF. Další informace najdete v tématu věnovaném [přístupu k Azure Blob Storage pomocí ověřování Azure Active Directory pomocí spravované identity ADF](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication).

![Příprava služby Azure Blob Storage – propojená služba pro přípravu](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurace Azure-SSIS IR s využitím místního hostitele IR jako proxy

Připravili jste místní prostředí IR a službu Azure Blob Storage – propojená služba pro přípravu, teď můžete nakonfigurovat nové nebo existující Azure-SSIS IR s využitím místního prostředí IR jako proxy serveru na portálu Data Factory nebo v aplikaci. Předtím, než to uděláte, pokud už existující Azure-SSIS IR běží, zastavte ho a pak ho znovu spusťte.

1. V podokně **instalace prostředí Integration runtime** přeskočte předchozí části **Obecné nastavení** a **nastavení SQL** výběrem možnosti **Další**. 

1. V části **Upřesnit nastavení** proveďte tyto kroky:

   1. Zaškrtněte políčko **nastavit Self-Hosted Integration runtime jako proxy pro Azure-SSIS Integration runtime** . 

   1. V rozevíracím seznamu **Integration runtime** v místním prostředí vyberte pro Azure-SSIS IR existující místní prostředí IR jako proxy.

   1. V rozevíracím seznamu **pracovní služba s připojeným úložištěm** vyberte existující propojenou službu Azure Blob Storage, nebo vytvořte novou pro přípravu.

   1. V poli **pracovní cesta** Určete kontejner objektů BLOB ve vybraném účtu služby Azure Blob Storage nebo ho nechte prázdný, aby se pro přípravu použil výchozí.

   1. Vyberte **Pokračovat**.

   ![Rozšířená nastavení s místním prostředím IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Pomocí prostředí PowerShell můžete také nakonfigurovat nové nebo existující Azure-SSIS IR s využitím místního prostředí IR jako proxy.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Povolit SSIS balíčky pro připojení pomocí proxy

Pomocí nejnovějšího SSDT jako rozšíření projektů SSIS pro Visual Studio nebo samostatného instalačního programu můžete najít novou `ConnectByProxy` vlastnost, která byla přidána do Správce připojení pro podporované součásti toku dat.
* [Stažení rozšíření projektů SSIS pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Stažení samostatného instalačního programu](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

Když navrhujete nové balíčky obsahující úlohy toku dat pomocí komponent, které přistupují k datům v místním prostředí, můžete tuto vlastnost povolit nastavením na *hodnotu true* v podokně **vlastnosti** příslušných správců připojení.

![Povolit vlastnost ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Tuto vlastnost můžete povolit také při spuštění existujících balíčků, aniž byste je museli ručně změnit o jednu.  Existují dvě možnosti:
- **Možnost A**: otevřete, znovu sestavte a znovu nasaďte projekt obsahující tyto balíčky s nejnovějším SSDT pro spuštění ve vašem Azure-SSIS IR. Vlastnost pak můžete povolit nastavením na *hodnotu true* pro příslušné Správce připojení. Když spouštíte balíčky z SSMS, zobrazí se tito správci připojení na kartě **Správci připojení** v místním okně **spustit balíček** .

  ![Povolit ConnectByProxy Vlastnost2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vlastnost můžete povolit také tak, že ji nastavíte na *hodnotu true* pro příslušné Správce připojení, které se zobrazí na kartě **Správci připojení** v rámci [aktivity balíčku pro spuštění SSIS](./how-to-invoke-ssis-package-ssis-activity.md) , když spouštíte balíčky v kanálu Data Factory.
  
  ![Povolit ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Možnost B:** Znovu nasaďte projekt, který obsahuje tyto balíčky pro spuštění v SSIS IR. Vlastnost pak můžete povolit zadáním cesty k vlastnostem `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` a nastavením na *hodnotu true* jako přepsání vlastnosti na kartě **Upřesnit** v automaticky otevíraném okně **spustit balíček** při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vlastnost můžete povolit také tak, že `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` zadáte cestu k vlastnosti a nastavíte ji na *hodnotu true* jako přepsání vlastnosti na kartě **přepsání vlastností** [aktivity SSIS Package](./how-to-invoke-ssis-package-ssis-activity.md) při spouštění balíčků v kanálu Data Factory.
  
  ![Povolit ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Ladění pracovních úloh místních i cloudových úloh

V místním prostředí IR můžete najít protokoly za běhu ve složce *C:\ProgramData\SSISTelemetry* a protokoly spuštění místních pracovních úloh ve složce *C:\ProgramData\SSISTelemetry\ExecutionLog* .  Můžete najít protokoly spuštění pracovních úloh cloudu ve vašich SSISDB, zadané cesty k souboru protokolování nebo Azure Monitor v závislosti na tom, jestli vaše balíčky uložíte v SSISDB, povolíte [integraci Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)atd. Můžete také najít jedinečné identifikátory místních pracovních úloh v protokolech spouštění pracovních úloh cloudu. 

![Jedinečné ID první pracovní úlohy](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Pokud jste vyvolali lístky zákaznické podpory, můžete vybrat tlačítko **Odeslat protokoly** na kartě **Diagnostika** **Microsoft Integration runtime Configuration Manager** , které je nainstalováno v místním prostředí IR, aby bylo možné prozkoumat nejnovější protokoly operací a spouštění.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Fakturace za místní a cloudové úlohy pro pracovní účely

Místní pracovní úkoly, které běží na vašem místním prostředí IR, se účtují samostatně, stejně jako všechny aktivity přesunu dat, které běží na místním prostředí IR, se účtují. Tento údaj je uvedený v článku [Azure Data Factory ceny datového kanálu](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Pracovní úkoly v cloudu, které běží na vašem Azure-SSIS IR, se neúčtují samostatně, ale vaše běžící Azure-SSIS IR se účtují tak, jak je uvedeno v článku [Azure-SSIS IR ceny](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enable-custom3rd-party-components"></a>Povolit komponenty vlastní/třetí strany 

Pokud chcete, aby vaše vlastní komponenty/komponenty třetích stran měly přístup k datům místně pomocí prostředí IR jako proxy pro Azure-SSIS IR, postupujte podle těchto pokynů:

1. Nainstalujte vlastní součásti/komponenty od jiných výrobců, které cílí na SQL Server 2017 na Azure-SSIS IR prostřednictvím [vlastních nastavení Standard/Express](./how-to-configure-azure-ssis-ir-custom-setup.md).

1. Vytvořte následující klíče registru DTSPath v místním prostředí IR, pokud už neexistují:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` nastavit na `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` nastavit na `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Do DTSPath výše nainstalujte vlastní komponenty a součásti třetí strany, které cílí na SQL Server 2017, a ujistěte se, že proces instalace:

   1. Vytvoří `<DTSPath>` `<DTSPath>/Connections` složky,, `<DTSPath>/PipelineComponents` a, `<DTSPath>/UpgradeMappings` Pokud již neexistují.
   
   1. Vytvoří vlastní soubor XML pro mapování rozšíření ve `<DTSPath>/UpgradeMappings` složce.
   
   1. Nainstaluje všechna sestavení, na která odkazují vaše sestavení komponent vlastní/třetí strany v globální mezipaměti sestavení (GAC).

Tady jsou příklady od našich partnerů, [Theobald softwaru](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) a [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), kteří si přistavili své komponenty pro použití našeho expresního vlastního nastavení a místního prostředí IR jako proxy serveru pro Azure-SSIS IR.

## <a name="enforce-tls-12"></a>Vynucení protokolu TLS 1.2

Pokud potřebujete používat protokol TLS 1,2 a zakažte starší verze protokolu SSL/TLS v místním prostředí IR, můžete si stáhnout a spustit *hlavní skript. cmd* , který najdete ve složce *CustomSetupScript/UserScenarios/TLS 1,2* našeho kontejneru Public Preview.  Pomocí [Průzkumník služby Azure Storage](https://storageexplorer.com/)se můžete připojit k našemu kontejneru veřejné verze Preview zadáním následujícího identifikátoru URI SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuální omezení

- V současné době jsou podporovány pouze součásti toku dat, které jsou vestavěné/předinstalované v edici Azure-SSIS IR Standard, s výjimkou součástí Hadoop/HDFS/HDInsight, a to s [využitím všech vestavěných/předinstalovaných komponent v Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).
- V současné době jsou podporovány pouze součásti toku dat Custom/třetích stran, které jsou napsány ve spravovaném kódu (.NET Framework) – ty napsané v nativním kódu (C++) nejsou aktuálně podporovány.
- Změna hodnot proměnných v místních i cloudových úlohách není momentálně podporovaná.
- Změna hodnot proměnných typu Object v místních pracovních úlohách se neprojeví v jiných úlohách.
- *ParameterMapping* ve zdroji OLEDB se momentálně nepodporuje. Jako alternativní řešení prosím použijte *příkaz SQL z proměnné* jako *AccessMode* a použijte *výraz* pro vložení proměnných/parametrů do příkazu SQL. Ilustraci najdete v balíčku *ParameterMappingSample. dtsx* , který se nachází ve složce *SelfHostedIRProxy/omezení* našeho veřejného Preview kontejneru. Pomocí Průzkumník služby Azure Storage se můžete připojit k našemu kontejneru veřejné verze Preview tak, že zadáte výše uvedený identifikátor URI SAS.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování místního prostředí IR jako proxy serveru pro váš Azure-SSIS IR můžete nasadit a spustit balíčky pro přístup k místním datům, jako jsou spouštěné aktivity balíčků SSIS v Data Factorych kanálech. Informace o tom, jak najdete [v tématu spouštění balíčků SSIS jako aktivity balíčku SSIS v data Factorych kanálech](./how-to-invoke-ssis-package-ssis-activity.md).