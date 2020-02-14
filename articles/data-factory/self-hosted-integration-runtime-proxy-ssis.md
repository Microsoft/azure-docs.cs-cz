---
title: Konfigurace prostředí Integration runtime v místním prostředí jako proxy pro SSIS
description: Přečtěte si, jak nakonfigurovat Integration runtime v místním prostředí jako proxy server pro Azure-SSIS Integration Runtime.
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
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187687"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurace prostředí IR v místním prostředí jako proxy serveru pro Azure-SSIS IR v Azure Data Factory

Tento článek popisuje, jak spustit balíčky služba SSIS (SQL Server Integration Services) (SSIS) na Azure-SSIS Integration Runtime (Azure-SSIS IR) v Azure Data Factory pomocí modulu runtime integrace (v místním prostředí IR), který je nakonfigurovaný jako proxy. 

Pomocí této funkce můžete získat přístup k místním datům, aniž byste se museli [připojit k Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Tato funkce je užitečná v případě, že vaše podniková síť má příliš složitou konfiguraci nebo je zásada příliš omezující, takže se do ní Azure-SSIS IR vložit.

Tato funkce rozdělí balíčky, které obsahují úlohu toku dat s místním zdrojem dat, na dvě pracovní úlohy: 
* První úkol, který běží v místním prostředí IR, nejprve přesune data z místního zdroje dat do pracovní oblasti v úložišti objektů BLOB v Azure.
* Druhý úkol, který běží na vašem Azure-SSIS IR, pak přesune data z pracovní oblasti do zamýšleného data cíle.

Další výhody a funkce této funkce umožňují například nastavit vaše místní prostředí IR v oblastech, které ještě nejsou podporované Azure-SSIS IR, a umožnit veřejnou statickou IP adresu místního prostředí IR v bráně firewall vašich zdrojů dat.

## <a name="prepare-the-self-hosted-ir"></a>Příprava prostředí IR pro místní hostování

Pokud chcete tuto funkci používat, musíte nejdřív vytvořit datovou továrnu a nastavit v ní Azure-SSIS IR. Pokud jste to ještě neudělali, postupujte podle pokynů v části [nastavení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Potom nastavíte místní prostředí IR ve stejné datové továrně, kde je nastavená vaše Azure-SSIS IR. Postup najdete v tématu [vytvoření prostředí IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)v místním prostředí.

Nakonec stáhnete a nainstalujete nejnovější verzi prostředí IR v místním prostředí a také další ovladače a modul runtime, na místním počítači nebo virtuálním počítači Azure (VM) následujícím způsobem:
- Stáhněte a nainstalujte si nejnovější verzi prostředí IR pro místní [hostování](https://www.microsoft.com/download/details.aspx?id=39717).
- Pokud ve svých balíčcích používáte konektory objektů Linking and Embedding Database (OLEDB), Stáhněte a nainstalujte příslušné ovladače OLEDB do stejného počítače, ve kterém je nainstalovaný modul IR pro místní hostování, pokud jste to ještě neudělali.  

  Pokud používáte starší verzi ovladače OLEDB pro SQL Server (SQL Server Native Client [SQLNCLI]), [Stáhněte si 64 verzi](https://www.microsoft.com/download/details.aspx?id=50402).  

  Pokud používáte nejnovější verzi ovladače OLEDB pro SQL Server (MSOLEDBSQL), [Stáhněte si 64 verzi](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Pokud používáte ovladače OLEDB pro jiné databázové systémy, jako jsou PostgreSQL, MySQL, Oracle a tak dále, můžete si stáhnout 64 verzí ze svých webů.
- Pokud jste to ještě neudělali, [Stáhněte a nainstalujte verzi 64 sady Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na stejném počítači, na kterém je nainstalovaný modul IR pro místní hostování.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Příprava služby Azure Blob Storage – propojená služba pro přípravu

Pokud jste to ještě neudělali, vytvořte propojenou službu Azure Blob Storage ve stejné datové továrně, kde je nastavená vaše Azure-SSIS IR. Postup najdete v tématu [vytvoření propojené služby Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Nezapomeňte provést následující akce:
- V případě **úložiště dat**vyberte **Azure Blob Storage**.  
- Pro **připojení prostřednictvím prostředí Integration runtime**vyberte **AutoResolveIntegrationRuntime**.  
- V případě **metody ověřování**vyberte **klíč účtu**, **identifikátor URI SAS**nebo **instanční objekt**.  

    >[!TIP]
    >Pokud vyberete **instanční objekt**, udělte aspoň roli  *Přispěvatel dat objektů BLOB úložiště* . Další informace najdete v tématu [Azure Blob Storage Connector](connector-azure-blob-storage.md#linked-service-properties).

![Příprava služby Azure Blob Storage – propojená služba pro přípravu](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurace Azure-SSIS IR s využitím místního hostitele IR jako proxy

Připravili jste místní prostředí IR a službu Azure Blob Storage – propojená služba pro přípravu, teď můžete nakonfigurovat nové nebo existující Azure-SSIS IR s využitím místního prostředí IR jako proxy serveru na portálu Data Factory nebo v aplikaci. Předtím, než to uděláte, pokud už existující Azure-SSIS IR běží, zastavte ho a pak ho znovu spusťte.

1. V podokně **instalace prostředí Integration runtime** přeskočte předchozí části **Obecné nastavení** a **nastavení SQL** výběrem možnosti **Další**. 

1. V části **Upřesnit nastavení** proveďte tyto kroky:

   1. Zaškrtněte políčko **nastavit Integration runtime pro místní hostování jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko. 

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

Pomocí nejnovější SSDT s rozšířením projektů SSIS pro Visual Studio nebo samostatného instalačního programu můžete najít novou vlastnost `ConnectByProxy`, která byla přidána do Správce připojení OLEDB nebo plochých souborů.
* [Stažení SSDT s rozšířením projektů SSIS pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Stažení samostatného instalačního programu](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Když navrhujete nové balíčky, které obsahují úlohy toku dat se zdroji OLEDB nebo plochých souborů, které umožňují přístup k databázím nebo souborům v místním prostředí, můžete tuto vlastnost povolit nastavením na *hodnotu true* v podokně **vlastnosti** příslušných správců připojení.

![Povolit vlastnost ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Tuto vlastnost můžete povolit také při spuštění existujících balíčků, aniž byste je museli ručně změnit o jednu.  Existují dvě možnosti:
- **Možnost A**: otevřete, znovu sestavte a znovu nasaďte projekt obsahující tyto balíčky s nejnovějším SSDT pro spuštění ve vašem Azure-SSIS IR. Vlastnost pak můžete povolit nastavením na *hodnotu true* pro příslušné Správce připojení. Když spouštíte balíčky z SSMS, zobrazí se tito správci připojení na kartě **Správci připojení** v místním okně **spustit balíček** .

  ![Povolit ConnectByProxy Vlastnost2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vlastnost můžete povolit také tak, že ji nastavíte na *hodnotu true* pro příslušné Správce připojení, které se zobrazí na kartě **Správci připojení** v rámci [aktivity balíčku pro spuštění SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) při spouštění balíčků v kanálu Data Factory.
  
  ![Povolit ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Možnost B:** Znovu nasaďte projekt, který obsahuje tyto balíčky pro spuštění v SSIS IR. Vlastnost pak můžete povolit zadáním cesty k vlastnostem, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a nastavením na *hodnotu true* jako přepsání vlastnosti na kartě **Upřesnit** v automaticky otevíraném okně **spustit balíček** při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vlastnost můžete povolit také tak, že zadáte její cestu k vlastnostem, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a nastavíte ji na *true* jako přepsání vlastnosti na kartě **přepsání vlastností** [aktivity SSIS Package](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) , když spouštíte balíčky v data Factorych kanálech.
  
  ![Povolit ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Ladění první a druhé pracovní úlohy

V místním prostředí IR můžete najít protokoly za běhu ve složce *C:\ProgramData\SSISTelemetry* a protokoly spouštění pro první pracovní úlohy ve složce *C:\ProgramData\SSISTelemetry\ExecutionLog* .  V závislosti na tom, jestli vaše balíčky ukládáte do SSISDB nebo systému souborů, sdílených složek nebo souborů Azure, můžete najít protokoly spouštění druhých pracovních úloh v SSISDB nebo zadané cesty protokolování. Můžete také najít jedinečné identifikátory prvních pracovních úloh v protokolech spouštění druhé pracovní úlohy. 

![Jedinečné ID první pracovní úlohy](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Použití ověřování systému Windows v pracovních úlohách

Pokud pracovní úkoly v místním prostředí IR vyžadují ověřování systému Windows, [nakonfigurujte balíčky SSIS tak, aby používaly stejné ověřování systému Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Vaše pracovní úkoly budou vyvolány s účtem služby IR v místním prostředí (*NT SERVICE\DIAHostService*ve výchozím nastavení) a vaše úložiště dat budou k dispozici s účtem ověřování systému Windows. Oba účty vyžadují, aby jim byly přiřazeny určité zásady zabezpečení. V místním počítači IR spusťte místní zásady **zabezpečení** > **místní zásady** > **přiřazení uživatelských práv**a pak postupujte takto:

1. Přiřaďte *kvóty pro úpravu paměti pro proces* a *nahraďte zásady tokenu na úrovni procesu* na účet služby IR pro místní hostování. Tato situace by se měla provádět automaticky při instalaci místního prostředí IR s výchozím účtem služby. Pokud používáte jiný účet služby, přiřaďte k němu stejné zásady.

1. Přiřaďte k účtu ověřování systému Windows zásadu *přihlášení jako služby* .

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faktura za první a druhý pracovní úkol

První pracovní úkoly, které běží na vašem místním prostředí IR, se účtují samostatně, stejně jako všechny aktivity přesunu dat, které běží v místním prostředí IR, se účtují. Tento údaj je uvedený v článku [Azure Data Factory ceny datového kanálu](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Druhé pracovní úkoly, které běží na vašem Azure-SSIS IR, se neúčtují samostatně, ale vaše běžící Azure-SSIS IR se fakturují tak, jak je uvedeno v článku [Azure-SSIS IR ceny](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuální omezení

- V současné době jsou podporovány pouze úlohy toku dat s použitím rozhraní ODBC (Open Database Connectivity), OLEDB nebo plochých souborů a zdrojů ODBC, OLEDB a plochých souborů nebo cíle OLEDB. 
- V současné době jsou podporovány pouze propojené služby Azure Blob Storage, které jsou konfigurovány pomocí *klíče účtu*, *identifikátoru URI sdíleného přístupového podpisu (SAS)* nebo ověřování *instančního objektu* .

## <a name="next-steps"></a>Další kroky

Po nakonfigurování místního prostředí IR jako proxy serveru pro váš Azure-SSIS IR můžete nasadit a spustit balíčky pro přístup k místním datům, jako jsou spouštěné aktivity balíčků SSIS v Data Factorych kanálech. Informace o tom, jak najdete [v tématu spouštění balíčků SSIS jako aktivity balíčku SSIS v data Factorych kanálech](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
