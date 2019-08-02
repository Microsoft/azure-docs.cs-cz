---
title: Instalace licencovaných komponent pro prostředí Azure-SSIS Integration runtime | Microsoft Docs
description: Přečtěte si, jak může ISV vyvíjet a instalovat placené nebo licencované vlastní komponenty pro prostředí Azure-SSIS Integration runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 1c574578e6ed6ee032be01718eb3e8afd27fdf6f
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708014"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalace placených nebo licencovaných vlastních komponent pro prostředí Azure-SSIS Integration runtime

Tento článek popisuje, jak může ISV vyvíjet a instalovat placené nebo licencované vlastní součásti pro balíčky služba SSIS (SQL Server Integration Services) (SSIS), které běží v Azure v prostředí Azure-SSIS Integration runtime.

## <a name="the-problem"></a>Problém

Povaha prostředí Azure-SSIS Integration runtime prezentuje několik výzev, které usnadňují používání typických metod licencování pro místní instalaci vlastních komponent. Výsledkem je, že Azure-SSIS IR vyžaduje jiný přístup.

-   Uzly prostředí Azure-SSIS IR jsou nestálé a dají se přidělit nebo uvolnit kdykoli. Můžete například spouštět nebo zastavovat uzly pro správu nákladů nebo škálovat nahoru a dolů pomocí různých velikostí uzlů. V důsledku toho není možné vytvořit vazbu licence na součást třetí strany na konkrétní uzel pomocí informací specifických pro konkrétní počítač, jako je například adresa MAC nebo ID procesoru, již není životaschopná.

-   Můžete také škálovat Azure-SSIS IR, aby se počet uzlů mohl kdykoli zmenšit nebo rozbalit.

## <a name="the-solution"></a>Řešení

V důsledku omezení tradičních licenčních metod popsaných v předchozí části poskytuje Azure-SSIS IR nové řešení. Toto řešení používá proměnné prostředí systému Windows a systémové proměnné SSIS pro vázání licencí a ověřování komponent jiných výrobců. Nezávislý výrobce softwaru může tyto proměnné použít k získání jedinečných a trvalých informací pro Azure-SSIS IR, jako je ID clusteru a počet uzlů clusteru. Pomocí těchto informací může nezávislý výrobce softwaru potom připojit licenci pro svou komponentu k Azure-SSIS IR *jako cluster*. Tato vazba používá ID, které se nemění, když zákazníci začnou nebo zastavují horizontální navýšení nebo snížení kapacity, horizontální škálování nebo překonfigurování Azure-SSIS IR jakýmkoli způsobem.

Následující diagram znázorňuje typickou instalaci, aktivaci a vázání licencí a toky ověřování pro komponenty třetích stran, které používají tyto nové proměnné:

![Instalace licencovaných komponent](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrukce
1. Nezávislý výrobce softwaru může nabízet své licencované komponenty v různých SKU nebo vrstvách (například jeden uzel, až 5 uzlů, až 10 uzlů a tak dále). ISV poskytuje odpovídající kód Product Key, když si zákazníci nakupují produkt. ISV může také poskytovat Azure Storage kontejner objektů blob, který obsahuje skript instalačního programu ISV a přidružené soubory. Zákazníci mohou tyto soubory zkopírovat do vlastního kontejneru úložiště a upravit je pomocí vlastního kódu Product Key (například spuštěním `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Zákazníci pak můžou zřídit a znovu nakonfigurovat Azure-SSIS IR s identifikátorem URI SAS svého kontejneru jako s parametrem. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Když je Azure-SSIS IR zřízená nebo překonfigurovaná, na každém uzlu se spustí instalační program ISV `SSIS_CLUSTERID` a provede dotaz na proměnné prostředí systému Windows a. `SSIS_CLUSTERNODECOUNT` Pak Azure-SSIS IR odešle své ID clusteru a kód Product Key pro licencovaný produkt pro aktivační server ISV, aby vygeneroval aktivační klíč.

3. Po přijetí aktivačního klíče může instalační program ISV Uložit klíč místně na každém uzlu (například v registru).

4. Když zákazníci spustí balíček, který používá licencovanou komponentu ISV v uzlu Azure-SSIS IR, načte místně uložený aktivační klíč a ověří ho s ID clusteru uzlu. Balíček může také volitelně ohlásit počet uzlů clusteru pro aktivační server ISV.

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

Seznam partnerů ISV, kteří si na konci tohoto blogového příspěvku přistavili své komponenty a rozšíření pro Azure-SSIS IR, najdete na konci tohoto příspěvku na blogu – [Enterprise Edition, Custom Setup a rozšíření třetích stran pro SSIS v ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Další kroky

-   [Vlastní nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Edice Enterprise systému Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
