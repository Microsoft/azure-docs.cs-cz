---
title: Monitorování prostředí Integration runtime v Azure Data Factory
description: Naučte se monitorovat různé typy prostředí Integration runtime v Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: fa71dc1e6b3a09827f2ad3d9f714622da5a36222
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862441"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorování prostředí Integration Runtime ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration runtime** je výpočetní infrastruktura, kterou používá Azure Data Factory (ADF) k poskytování různých možností integrace dat napříč různými síťovými prostředími. Existují tři typy prostředí Integration runtime, které nabízí Data Factory:

- Prostředí Azure Integration Runtime
- Prostředí Integration Runtime v místním prostředí
- Prostředí Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li získat stav instance prostředí Integration runtime (IR), spusťte následující příkaz prostředí PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Rutina vrátí různé informace pro různé typy prostředí Integration runtime. Tento článek vysvětluje vlastnosti a stavy pro jednotlivé typy prostředí Integration runtime.  

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime

Výpočetní prostředek pro Azure Integration runtime je plně spravovaný elastickě v Azure. Následující tabulka uvádí popisy vlastností vrácených příkazem **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Vlastnosti

Následující tabulka uvádí popis vlastností vrácených rutinou pro prostředí Azure Integration Runtime:

| Vlastnost | Popis |
-------- | ------------- | 
| Název | Název prostředí Azure Integration runtime. |  
| Stav | Stav prostředí Azure Integration runtime. | 
| Umístění | Umístění prostředí Azure Integration runtime. Podrobnosti o umístění prostředí Azure Integration runtime najdete v tématu [Úvod do prostředí Integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Název objektu pro vytváření dat, ke kterému patří Azure Integration runtime | 
| ResourceGroupName | Název skupiny prostředků, do které patří objekt pro vytváření dat.  |
| Popis | Popis prostředí Integration runtime.  |

### <a name="status"></a>Status

Následující tabulka nabízí možné stavy prostředí Azure Integration Runtime:

| Status | Komentáře a scénáře | 
| ------ | ------------------ |
| Online | Prostředí Azure Integration runtime je online a připravené k použití. | 
| Offline | Prostředí Azure Integration runtime je offline kvůli vnitřní chybě. |

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí

V této části najdete popis vlastností vrácených rutinou Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Vrácené vlastnosti a stav obsahují informace o celkovém prostředí Integration runtime a všech uzlech modulu runtime.  

### <a name="properties"></a>Vlastnosti

Následující tabulka uvádí popisy vlastností monitorování pro **každý uzel**:

| Vlastnost | Popis | 
| -------- | ----------- | 
| Název | Název místního prostředí Integration runtime a uzlů, které jsou k němu přidružené. Uzel je místní počítač s Windows, na kterém je nainstalovaný modul Integration runtime v místním prostředí. |  
| Status | Stav celkového místního prostředí Integration runtime a každého uzlu. Příklad: online/offline/omezený/atd. Informace o těchto stavech najdete v další části. | 
| Verze | Verze místního prostředí Integration runtime a každého uzlu. Verze prostředí Integration runtime v místním prostředí je určena na základě verze většiny uzlů ve skupině. Pokud v místním prostředí Integration runtime hostují uzly s různými verzemi, budou správně fungovat pouze uzly se stejným číslem verze jako logická funkce místního prostředí Integration runtime. Ostatní jsou v omezeném režimu a je potřeba je ručně aktualizovat (jenom v případě, že se automatická aktualizace nezdařila). | 
| Dostupná paměť | Dostupná paměť v uzlu Integration runtime v místním prostředí. Tato hodnota je snímkem téměř v reálném čase. | 
| Využití procesoru | Využití procesoru uzlu Integration runtime v místním prostředí. Tato hodnota je snímkem téměř v reálném čase. |
| Sítě (za sekundu) | Využití sítě uzlu Integration runtime v místním prostředí. Tato hodnota je snímkem téměř v reálném čase. | 
| Souběžné úlohy (spuštěné/omezení) | **Spuštěno**. Počet úloh nebo úloh, které jsou spuštěny na jednotlivých uzlech. Tato hodnota je snímkem téměř v reálném čase. <br/><br/>**Omezení**. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definovaná na základě velikosti počítače. Tento limit můžete zvýšit tak, aby se při provádění úloh v pokročilých scénářích narůstat na horizontální navýšení kapacity, a to i v případě, že je procesor, paměť nebo síť přetížená. Tato funkce je také k dispozici v místním prostředí Integration runtime s jedním uzlem. |
| Role | Existují dva typy rolí v prostředí Integration runtime ve více uzlech – dispečer a pracovní proces. Všechny uzly jsou pracovní procesy, což znamená, že je můžete použít ke spouštění úloh. K dispozici je jen jeden uzel dispečera, který se používá k vyžádání úkolů nebo úloh z cloudových služeb a jejich odeslání do různých pracovních uzlů. Uzel dispečera je také pracovním uzlem. |

Některá nastavení vlastností jsou smysluplnější, když v místním prostředí Integration runtime (to znamená ve scénáři horizontálního navýšení kapacity) jsou dva nebo více uzlů.

#### <a name="concurrent-jobs-limit"></a>Omezení souběžných úloh

Výchozí hodnota limitu souběžných úloh je nastavená na základě velikosti počítače. Faktory použité k výpočtu této hodnoty závisí na velikosti paměti RAM a počtu PROCESORových jader počítače. Čím více jader a čím více paměti, tím vyšší je výchozí limit souběžných úloh.

Horizontální navýšení kapacity můžete zvýšit zvýšením počtu uzlů. Pokud zvýšíte počet uzlů, limit souběžných úloh je součtem hodnot souběžných úloh limitu všech dostupných uzlů.  Například pokud jeden uzel umožňuje spustit maximálně dvanáct souběžných úloh, pak přidání tří podobných uzlů vám umožní spustit maximálně 48 souběžných úloh (tj. 4 x 12). Maximální počet souběžných úloh doporučujeme zvýšit jenom v případě, že se na každém uzlu zobrazí nízké využití prostředků s výchozími hodnotami.

Vypočtenou výchozí hodnotu můžete přepsat v Azure Portal. Vyberte vytvořit > připojení > prostředí Integration runtime > upravte > uzly > změňte hodnotu souběžných úloh na uzel. Můžete použít také příkaz PowerShell [Update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) .
  
### <a name="status-per-node"></a>Stav (na jeden uzel)

Následující tabulka uvádí možné stavy uzlu Integration runtime v místním prostředí:

| Status | Popis |
| ------ | ------------------ | 
| Online | Uzel je připojen ke službě Data Factory. |
| Offline | Uzel je offline. |
| Inovován | Uzel se automaticky aktualizuje. |
| Omezeně | Kvůli problému s připojením. Může to být kvůli problému s portem HTTP 8060, potížím s připojením k Service Bus nebo problémům s synchronizací přihlašovacích údajů. |
| Inactive | Uzel je v konfiguraci odlišnou od konfigurace jiných majoritní uzlů. |

Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stav (celkový místní prostředí Integration Runtime)

Následující tabulka uvádí možné stavy prostředí Integration runtime v místním prostředí. Tento stav závisí na stavech všech uzlů, které patří do modulu runtime. 

| Status | Popis |
| ------ | ----------- | 
| Vyžaduje registraci | Do tohoto místního prostředí Integration runtime ještě není zaregistrovaný žádný uzel. |
| Online | Všechny uzly jsou online. |
| Offline | Žádný uzel není online. |
| Omezeně | Ne všechny uzly v tomto místním prostředí Integration runtime jsou v dobrém stavu. Tento stav je upozorněním, že některé uzly mohou být mimo provoz. Důvodem může být problém synchronizace přihlašovacích údajů v uzlu Dispatcher nebo Worker. |

Pomocí rutiny **Get-AzDataFactoryV2IntegrationRuntimeMetric** načtěte datovou část JSON obsahující podrobné vlastnosti místního prostředí Integration runtime a jejich hodnoty snímku v době spuštění rutiny.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Ukázkový výstup (předpokládá, že jsou k tomuto prostředí Integration runtime v místním prostředí přidruženy dva uzly):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Prostředí Azure-SSIS Integration Runtime

Azure-SSIS IR je plně spravovaný cluster virtuálních počítačů Azure (virtuálních počítačů nebo uzlů) vyhrazených ke spouštění balíčků SSIS. Spouštění balíčků SSIS můžete vyvolat na Azure-SSIS IR pomocí různých metod, například prostřednictvím nástroje Azure-SQL Server Data Tools (SSDT), nástroje příkazového řádku AzureDTExec, T-SQL on SQL Server Management Studio (SSMS)/SQL serveru a provádět aktivity balíčku SSIS v kanálech ADF. Azure-SSIS IR nespouští žádné jiné aktivity ADF. Po zřízení můžete monitorovat jeho celkové vlastnosti a stavy specifické pro uzel prostřednictvím Azure PowerShell, Azure Portal a Azure Monitor.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Monitorování prostředí Azure-SSIS Integration runtime pomocí Azure PowerShell

Pomocí následující rutiny Azure PowerShell můžete monitorovat vlastnosti a stavy Azure-SSIS IR specifické pro jednotlivé uzly.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Vlastnosti

Následující tabulka uvádí popisy vlastností vrácených výše uvedenou rutinou pro Azure-SSIS IR.

| Vlastnost/stav              | Popis                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Čas UTC, kdy byl vytvořen Azure-SSIS IR. |
| Uzly                        | Přidělené nebo dostupné uzly vašeho Azure-SSIS IR se stavem specifickým pro uzel (spuštění/k dispozici/recyklace/nedostupné) a chybami, které lze provést. |
| OtherErrors                  | Chyby, které nejsou specifické pro uzel v Azure-SSIS IR. |
| LastOperation                | Výsledek poslední operace spuštění/zastavení ve vašem Azure-SSIS IR s chybami, pokud se nezdařila. |
| Stav                        | Celkový stav vašeho Azure-SSIS IR (počáteční/počáteční/spuštěný/zastavný/zastavený/zastavený). |
| Umístění                     | Umístění vašeho Azure-SSIS IR. |
| NodeSize                     | Velikost každého uzlu v Azure-SSIS IR. |
| NodeCount                    | Počet uzlů v Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | Maximální počet paralelních spuštění na uzel v Azure-SSIS IR. |
| CatalogServerEndpoint        | Koncový bod stávajícího Azure SQL Database serveru nebo spravované instance pro hostování katalogu SSIS (SSISDB). |
| CatalogAdminUserName         | Uživatelské jméno správce pro existující Azure SQL Database Server nebo spravovanou instanci. ADF používá tyto informace k přípravě a správě SSISDB vaším jménem. |
| CatalogAdminPassword         | Heslo správce pro existující Azure SQL Database Server nebo spravovanou instanci. |
| CatalogPricingTier           | Cenová úroveň pro SSISDB hostovaná serverem Azure SQL Database.  Neplatí pro hostování SSISDB spravované instance Azure SQL. |
| VNetId                       | ID prostředku virtuální sítě, ke kterému se má Azure-SSIS IR připojit |
| Podsíť                       | Název podsítě, ke které se má Azure-SSIS IR připojit. |
| ID                           | ID prostředku Azure-SSIS IR. |
| Typ                         | Typ IR (spravovaný nebo v místním prostředí) vašeho Azure-SSIS IR. |
| ResourceGroupName            | Název skupiny prostředků Azure, ve které byly vytvořeny ADF a Azure-SSIS IR. |
| DataFactoryName              | Název vašeho ADF. |
| Název                         | Název vašeho Azure-SSIS IR. |
| Popis                  | Popis Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Stav (na uzel Azure-SSIS IR)

Následující tabulka uvádí možné stavy Azure-SSIS IR uzlu:

| Stav specifický pro uzel | Popis |
| -------------------- | ----------- | 
| Spouštění             | Připravuje se tento uzel. |
| K dispozici            | Tento uzel je připravený na nasazení/spouštění balíčků SSIS. |
| Recyklován            | Probíhá oprava/restartování tohoto uzlu. |
| Neaktivní          | Tento uzel není připravený na nasazení/spouštění balíčků SSIS a má možné chyby nebo problémy, které byste mohli vyřešit. |

#### <a name="status-overall-azure-ssis-ir"></a>Stav (celková Azure-SSIS IR)

Následující tabulka uvádí možné celkové stavy Azure-SSIS IR. Celkový stav naopak závisí na kombinovaných stavech všech uzlů, které patří do Azure-SSIS IR. 

| Celkový stav | Popis | 
| -------------- | ----------- | 
| Počáteční        | Uzly vaší Azure-SSIS IR nebyly přiděleny nebo připraveny. | 
| Spouštění       | Uzly vaší Azure-SSIS IR jsou přiděleny/připraveny a bylo zahájeno fakturaci. |
| Zahájeno        | Uzly vaší Azure-SSIS IR byly přiděleny nebo připraveny a jsou připraveny k nasazení nebo spuštění balíčků SSIS. |
| Zastavování       | Probíhá vydávání uzlů Azure-SSIS IR. |
| Zastaveno        | Byly vydány uzly Azure-SSIS IR a fakturace se zastavila. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Monitorování prostředí Azure-SSIS Integration runtime v Azure Portal

Pokud chcete monitorovat Azure-SSIS IR v Azure Portal, přejděte na stránku **prostředí Integration runtime** v centru **monitorování** v uživatelském rozhraní ADF, kde vidíte všechny prostředí Integration runtime.

![Monitorovat všechny prostředí Integration runtime](media/monitor-integration-runtime/monitor-integration-runtimes.png)

V dalším kroku vyberte název Azure-SSIS IR a otevřete jeho stránku monitorování, kde můžete zobrazit jeho celkové vlastnosti a stavy specifické pro uzel. Na této stránce najdete v závislosti na tom, jak nakonfigurujete obecné, nasazování a pokročilé nastavení svého Azure-SSIS IR, najdete různé informativní a funkční dlaždice.  Informační dlaždice **typ** a **oblast** zobrazují typ a oblast Azure-SSIS IR, v uvedeném pořadí. Informační dlaždice **Velikost uzlu** zobrazuje skladovou položku (SSIS edition_VM tier_VM Series), počet jader procesoru a velikost paměti RAM na uzel pro vaši Azure-SSIS IR. **Běžící/požadované uzly** porovnává počet aktuálně spuštěných uzlů na celkový počet uzlů, které byly pro vaši Azure-SSIS IR dříve požadovány. Funkční dlaždice jsou podrobněji popsané v následujících podrobnostech.

![Monitorování Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>Dlaždice stavu

Na dlaždici **stav** na stránce monitorování Azure-SSIS IR můžete zobrazit celkový stav, například **spuštění** nebo **zastavení**. Když vyberete stav **spuštění** , zobrazí se okno s tlačítkem **zastavit** v reálném čase, které zastaví vaše Azure-SSIS IR. Když vyberete stav **Zastaveno** , zobrazí se okno s živým **startem** , ve kterém se spustí Azure-SSIS IR. Automaticky otevírané okno má také tlačítko **spustit balíček SSIS** k automatickému vygenerování kanálu ADF s aktivitou spuštění balíčku SSIS, která běží na vašem Azure-SSIS IR (viz [spouštění balíčků SSIS jako spouštění aktivit balíčku SSIS v kanálech ADF](./how-to-invoke-ssis-package-ssis-activity.md)) a v textovém poli **ID prostředku** , ze kterého můžete zkopírovat ID prostředku Azure-SSIS IR ( `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` ). Přípona ID Azure-SSIS IR prostředku, která obsahuje vaše identifikátory ADF a Azure-SSIS IR, vytvoří ID clusteru, které se dá použít k nákupu dalších komponent SSIS úrovně Premium/licencované od nezávislých dodavatelů softwaru (ISV) a jejich navázání na Azure-SSIS IR (Další informace najdete [v tématu Instalace prémiových a licencovaných komponent na Azure-SSIS IR](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![Monitorovat dlaždici Azure-SSIS IR – stav](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Dlaždice KONCOVÉho bodu serveru SSISDB

Pokud používáte model nasazení projektu, kde jsou balíčky uložené v SSISDB hostovaném serverem Azure SQL Database nebo spravované instance, zobrazí se na stránce monitorování Azure-SSIS IR dlaždice **koncového bodu serveru SSISDB** (viz téma [konfigurace nastavení nasazení Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Na této dlaždici můžete vybrat odkaz s označením serveru Azure SQL Database nebo spravované instance, který bude zobrazovat okno, kde můžete zkopírovat koncový bod serveru z textového pole a použít ho při připojování z SSMS k nasazení, konfiguraci, spuštění a správě balíčků. V automaticky otevíraném okně můžete také vybrat odkaz **zobrazit Azure SQL Database nebo nastavení spravované instance** a znovu nakonfigurovat nebo změnit velikost SSISDB v Azure Portal.

![Monitorovat dlaždici Azure-SSIS IR – SSISDB](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Dlaždice PROXY/přípravy

Pokud si stáhnete, nainstalujete a nakonfigurujete Self-Hosted IR (SHIR) jako proxy pro Azure-SSIS IR k přístupu k datům v místním prostředí, na stránce monitorování Azure-SSIS IR se zobrazí dlaždice **proxy/fázování** (viz téma [Konfigurace SHIR jako proxy serveru pro váš Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)). Na této dlaždici můžete vybrat odkaz, který určí SHIR, aby se otevřela jeho stránka monitorování. Můžete také vybrat jiný odkaz určením Blob Storage služby Azure pro přípravu a znovu nakonfigurovat její propojenou službu.

#### <a name="validate-vnet--subnet-tile"></a>Dlaždice ověřit virtuální síť/podsíť

Pokud připojíte Azure-SSIS IR k virtuální síti, zobrazí se na stránce monitorování Azure-SSIS IR dlaždice **ověřit virtuální síť nebo podsíť** (podívejte se do tématu [připojení k vaší Azure-SSIS IR do virtuální](./join-azure-ssis-integration-runtime-virtual-network.md)sítě). Na této dlaždici můžete vybrat odkaz, který určí vaši virtuální síť a podsíť pro otevření okna, kde můžete zkopírovat ID prostředku virtuální `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` sítě () a název podsítě z textových polí a také ověřit konfiguraci virtuálních sítí a podsítí, abyste zajistili, že požadované příchozí a odchozí síťové přenosy a správu Azure-SSIS IR nejsou překážky.

![Monitorovat dlaždici Azure-SSIS IR – ověřit](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>Dlaždice pro DIAGNOSTIKu připojení

Na dlaždici **Diagnostika připojení** na stránce monitorování Azure-SSIS IR můžete vybrat odkaz **Test připojení** , který se zobrazí v okně, kde můžete zkontrolovat připojení mezi Azure-SSIS IR a relevantními údaji balíčku/konfigurace/úložiště dat, stejně jako služby pro správu, prostřednictvím jejich plně kvalifikovaného názvu domény (FQDN)/IP a určeného portu (viz [Testování připojení z vašeho Azure-SSIS IR](./ssis-integration-runtime-diagnose-connectivity-faq.md)).

![Snímek obrazovky, který ukazuje, kde můžete testovat připojení mezi Azure-SSIS IR a relevantními balíčky, konfigurace a úložišti dat.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Dlaždice STATICKÝch veřejných IP adres

Pokud přenesete vlastní statické veřejné IP adresy pro Azure-SSIS IR, zobrazí se na stránce monitorování Azure-SSIS IR dlaždice **statické veřejné** IP adresy (Další informace najdete v tématu [zavedení vlastních statických veřejných ip adres pro Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)). Na této dlaždici můžete vybrat odkazy, které určí vaše první/druhé statické veřejné IP adresy pro Azure-SSIS IR pro otevření okna, kde můžete zkopírovat ID prostředku ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` ) z textového pole. V automaticky otevíraném okně můžete také vybrat odkaz **Zobrazit nastavení statické veřejné IP adresy** , abyste mohli spravovat svoji první/druhou statickou IP adresu v Azure Portal.

![Snímek obrazovky, který ukazuje, kde můžete určit své první/druhé statické veřejné IP adresy.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Dlaždice úložiště balíčků

Pokud používáte model nasazení balíčku, ve kterém jsou balíčky uložené v systému souborů/Azure soubory/SQL Server databázi (MSDB) hostované vaší spravovanou instancí Azure SQL a spravujete prostřednictvím Azure-SSIS IRch úložišť balíčků, uvidíte na stránce monitorování Azure-SSIS IR dlaždici **úložiště balíčků** (viz [konfigurace nastavení nasazení Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Na této dlaždici můžete vybrat odkaz, který určí počet úložišť balíčků připojených k vašemu Azure-SSIS IR. otevře se okno, kde můžete změnit konfiguraci relevantních propojených služeb pro vaše úložiště balíčků Azure-SSIS IR nad systémem souborů/soubory Azure/MSDB hostované vaší spravovanou instancí Azure SQL.

![Monitorovat dlaždici Azure-SSIS IR – balíček](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>Chyba (y) dlaždice

Pokud dojde k problémům s zahájením/zastavením/údržbou nebo upgradem Azure-SSIS IR, zobrazí se na stránce monitorování Azure-SSIS IR další **chybová zpráva (y)** . Na této dlaždici můžete vybrat odkaz, který určí počet chyb generovaných vaším Azure-SSIS IR, aby se zobrazilo okno, kde můžete zobrazit tyto chyby podrobněji a zkopírovat je, abyste našli doporučená řešení v našem průvodci odstraňováním potíží (viz [Poradce při potížích s Azure-SSIS IR](./ssis-integration-runtime-management-troubleshoot.md)).

![Monitorování Azure-SSIS IR – dlaždice DIAGNOSTIKy](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Monitorování prostředí Azure-SSIS Integration runtime pomocí Azure Monitor

Pokud chcete monitorovat Azure-SSIS IR pomocí Azure Monitor, přečtěte si téma [monitorování operací SSIS pomocí Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Další informace o prostředí Azure-SSIS Integration runtime

Další informace o prostředí Azure-SSIS Integration runtime najdete v následujících článcích:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o obecných modulech Integration runtime, včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](./tutorial-deploy-ssis-packages-azure.md) Tento článek poskytuje podrobné pokyny k vytvoření Azure-SSIS IR a použití Azure SQL Database k hostování katalogu SSIS (SSISDB). 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek se rozbalí v tomto kurzu a poskytne pokyny k použití spravované instance Azure SQL k hostování SSISDB. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak spustit, zastavit nebo odstranit Azure-SSIS IR. Ukazuje také způsob horizontálního navýšení kapacity přidáním dalších uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek poskytuje pokyny k připojení Azure-SSIS IR k virtuální síti.

## <a name="next-steps"></a>Další kroky
Projděte si následující články pro monitorování kanálů různými způsoby: 

- [Rychlý Start: vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Monitorování kanálů Data Factory pomocí Azure Monitor](monitor-using-azure-monitor.md)
