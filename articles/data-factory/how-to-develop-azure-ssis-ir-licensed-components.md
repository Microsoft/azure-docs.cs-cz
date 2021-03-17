---
title: Instalace licencovaných komponent pro Azure-SSIS Integration runtime
description: Přečtěte si, jak může ISV vyvíjet a instalovat placené nebo licencované vlastní komponenty pro prostředí Azure-SSIS Integration runtime.
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386284"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalace placených nebo licencovaných vlastních komponent pro prostředí Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak může ISV vyvíjet a instalovat placené nebo licencované vlastní součásti pro balíčky služba SSIS (SQL Server Integration Services) (SSIS), které běží v Azure v prostředí Azure-SSIS Integration runtime.

## <a name="the-problem"></a>Problém

Povaha prostředí Azure-SSIS Integration runtime prezentuje několik výzev, které usnadňují používání typických metod licencování pro místní instalaci vlastních komponent. V důsledku toho Azure-SSIS IR vyžaduje jiný přístup.

-   Uzly Azure-SSIS IR jsou nestálé a dají se přidělit nebo uvolnit kdykoli. Můžete například spouštět nebo zastavovat uzly pro správu nákladů nebo škálovat nahoru a dolů pomocí různých velikostí uzlů. V důsledku toho není možné vytvořit vazbu licence na součást třetí strany na konkrétní uzel pomocí informací specifických pro konkrétní počítač, jako je například adresa MAC nebo ID procesoru, již není životaschopná.

-   Můžete také škálovat Azure-SSIS IR do nebo ven, aby se počet uzlů mohl kdykoli zmenšit nebo rozbalit.

## <a name="the-solution"></a>Řešení

V důsledku omezení tradičních licenčních metod popsaných v předchozí části Azure-SSIS IR poskytuje nové řešení. Toto řešení používá proměnné prostředí systému Windows a systémové proměnné SSIS pro vázání licencí a ověřování komponent jiných výrobců. Nezávislé výrobce softwaru může tyto proměnné použít k získání jedinečných a trvalých informací pro Azure-SSIS IR, jako je například ID clusteru a počet uzlů clusteru. Pomocí těchto informací může nezávislý výrobce softwaru potom pro svou komponentu navazovat licenci na Azure-SSIS IR *jako cluster*. Tato vazba používá ID, které se nemění, když zákazníci spouštějí nebo zastavují horizontální navýšení nebo snížení kapacity, nebo Azure-SSIS IR jakýmkoli způsobem znovu nakonfigurují.

Následující diagram znázorňuje typickou instalaci, aktivaci a vázání licencí a toky ověřování pro komponenty třetích stran, které používají tyto nové proměnné:

![Instalace licencovaných komponent](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Pokyny
1. Nezávislý výrobce softwaru může nabízet své licencované komponenty v různých SKU nebo vrstvách (například jeden uzel, až 5 uzlů, až 10 uzlů a tak dále). ISV poskytuje odpovídající kód Product Key, když si zákazníci nakupují produkt. ISV může také poskytovat Azure Storage kontejner objektů blob, který obsahuje skript instalačního programu ISV a přidružené soubory. Zákazníci mohou tyto soubory zkopírovat do vlastního kontejneru úložiště a upravit je pomocí vlastního kódu Product Key (například spuštěním `IsvSetup.exe -pid xxxx-xxxx-xxxx` ). Zákazníci pak můžou zřídit nebo znovu nakonfigurovat Azure-SSIS IR s identifikátorem URI SAS svého kontejneru jako s parametrem. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Při zřizování nebo změně konfigurace Azure-SSIS IR spustí instalační program ISV na každém uzlu dotaz na proměnné prostředí systému Windows `SSIS_CLUSTERID` a `SSIS_CLUSTERNODECOUNT` . Pak Azure-SSIS IR odešle své ID clusteru a kód Product Key pro licencovaný produkt pro aktivační server ISV, aby vygeneroval aktivační klíč.

3. Po přijetí aktivačního klíče může instalační program ISV Uložit klíč místně na každém uzlu (například v registru).

4. Když zákazníci spustí balíček, který používá licencovanou komponentu ISV v uzlu Azure-SSIS IR, načte místně uložený aktivační klíč a ověří ho proti ID clusteru uzlu. Balíček může také volitelně ohlásit počet uzlů clusteru pro aktivační server ISV.

    Tady je příklad kódu, který ověřuje aktivační klíč a oznamuje počet uzlů clusteru:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Partneři ISV

Seznam partnerů ISV, kteří si na konci tohoto blogového příspěvku přistavili své komponenty a rozšíření pro Azure-SSIS IR, najdete na konci tohoto příspěvku na blogu – [Enterprise Edition, vlastní instalace a rozšíření třetích stran pro SSIS v ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Další kroky

-   [Vlastní nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Edice Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
