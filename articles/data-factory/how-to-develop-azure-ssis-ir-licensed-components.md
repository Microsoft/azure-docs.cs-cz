---
title: Instalace licencovaných komponent pro runtime integrace Azure-SSIS
description: Zjistěte, jak může isv vyvíjet a instalovat placené nebo licencované vlastní komponenty pro runtime integrace Azure-SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: b7079262dc7db4f4a00a9dc79193da1574c7153a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605854"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalace placených nebo licencovaných vlastních komponent pro prostředí Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak může isv vyvíjet a instalovat placené nebo licencované vlastní součásti pro balíčky SQL Server Integration Services (SSIS), které běží v Azure v modulu runtime integrace Azure-SSIS.

## <a name="the-problem"></a>Problém

Povaha runtime integrace Azure-SSIS představuje několik výzev, které činí typické licenční metody používané pro místní instalaci vlastních součástí nedostatečné. V důsledku toho Azure-SSIS IR vyžaduje jiný přístup.

-   Uzly Azure-SSIS IR jsou nestálé a lze je přidělit nebo uvolnit kdykoli. Můžete například spustit nebo zastavit uzly pro správu nákladů nebo vertikálně navýšit a snížit kapacitu prostřednictvím různých velikostí uzlů. V důsledku toho již není možné vazby licence komponenty třetí strany na konkrétní uzel pomocí informací specifických pro počítač, jako je adresa MAC nebo ID procesoru.

-   Můžete také škálovat Azure-SSIS IR dovnitř nebo ven, takže počet uzlů můžete zmenšit nebo rozbalit kdykoli.

## <a name="the-solution"></a>Řešení

V důsledku omezení tradičních metod licencování popsaných v předchozí části poskytuje zařízení Azure-SSIS IR nové řešení. Toto řešení používá proměnné prostředí systému Windows a systémové proměnné SSIS pro licenční vazbu a ověření součástí třetích stran. Nevlastní nesežej-li tyto proměnné můžete použít k získání jedinečných a trvalých informací pro Azure-SSIS Ir, jako je například ID clusteru a počet uzlů clusteru. Pomocí těchto informací pak mohou nevlastnící výrobci a nezávazná vazba licence pro svou komponentu s infračerveným *serverem*Azure-SSIS jako cluster . Tato vazba používá ID, které se nemění, když zákazníci spouštějí nebo zastavují, vertikálně navýšili nebo snížili kapacitu, škálovali nebo vyřizovali infračervený přenos Azure-SSIS žádným způsobem.

Následující diagram znázorňuje typické instalace, aktivace a licence vazby a validation toky pro součásti jiných výrobců, které používají tyto nové proměnné:

![Instalace licencovaných komponent](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Pokyny
1. Nesamostatní nesamostatní nemítnamohou nabízet své licencované součásti v různých virtuálních zařízení nebo úrovních (například jeden uzel, až 5 uzlů, až 10 uzlů atd.). Neobsahuje odpovídající kód Product Key, když si zákazníci zakoupí produkt. Nerenovuje prostor pro řešení neposkytované také kontejner objektů blob úložiště Azure, který obsahuje skript nastavení isv a přidružené soubory. Zákazníci mohou tyto soubory zkopírovat do vlastního kontejneru úložiště a upravit `IsvSetup.exe -pid xxxx-xxxx-xxxx`je pomocí vlastního kódu Product Key (například spuštěním). Zákazníci pak mohou zřídit nebo překonfigurovat Azure-SSIS Ir s Identifikátor URI SAS jejich kontejneru jako parametr. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Při zřízení nebo překonfigurování infračerveného zařízení Azure-SSIS se instalační program isv `SSIS_CLUSTERID` `SSIS_CLUSTERNODECOUNT`spustí na každém uzlu, aby se dotazoval na proměnné prostředí Windows, a . Ind žel na server pro aktivaci softwaru a služeb na internetu pak odešle své ID clusteru a kód Product Key pro licencovaný produkt na aktivační server isv a vygeneruje aktivační klíč.

3. Po obdržení aktivačního klíče může instalační program ISV uložit klíč místně v každém uzlu (například v registru).

4. Když zákazníci spustí balíček, který používá licencované součásti isv na uzlu Azure-SSIS IR, balíček přečte místně uložený aktivační klíč a ověří jej proti ID clusteru uzlu. Balíček může také volitelně hlásit počet uzlů clusteru aktivačnímu serveru isv.

    Zde je příklad kódu, který ověřuje aktivační klíč a hlásí počet uzlů clusteru:

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

## <a name="isv-partners"></a>Partneři isv

Seznam partnerů pro vlastní potřebu, kteří přizpůsobili své součásti a rozšíření pro službu Azure-SSIS IR, najdete na konci tohoto příspěvku blogu – [Enterprise Edition, Vlastní nastavení a Rozšiřitelnost třetí strany pro SSIS v adf](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Další kroky

-   [Vlastní nastavení pro runtime integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition integračního běhu Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
