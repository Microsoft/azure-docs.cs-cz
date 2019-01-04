---
title: Nainstalujte licencovaných komponent pro prostředí Azure-SSIS integration runtime | Dokumentace Microsoftu
description: Zjistěte, jak můžete vyvíjet nezávislý dodavatel softwaru a instalace placených nebo licenci vlastní komponenty prostředí Azure-SSIS integration runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022321"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Nainstalujte placené nebo s licencí vlastní komponenty prostředí Azure-SSIS integration runtime

Tento článek popisuje, jak nezávislým výrobcem softwaru můžete vyvíjet a nainstalujte vlastní komponenty placené nebo s licencí pro SQL Server Integration Services (SSIS) balíčky, které běží v prostředí Azure-SSIS integration runtime v Azure.

## <a name="the-problem"></a>Problém

Povaha prostředí Azure-SSIS integration runtime představuje několik výzev, umožňují typické licencování metod používaných pro místní instalaci vlastních součástech nedostatečné. V důsledku toho Azure-SSIS IR vyžaduje jiný přístup.

-   Uzly Azure-SSIS IR jsou nestálá a můžete přidělené nebo vydané v každém okamžiku. Můžete například spustit nebo zastavit uzly pod kontrolou související náklady, nebo prostřednictvím různých velikostí uzlu škálovat nahoru a dolů. V důsledku toho vazba licenci komponenty třetích stran na jednotlivé uzly pomocí informace specifické pro počítač, třeba adresu MAC nebo ID procesoru už není možné.

-   Azure-SSIS IR snížení nebo navýšení kapacity, můžete škálovat také tak, aby z počtu uzlů můžete zmenšit nebo zvětšit kdykoli.

## <a name="the-solution"></a>Řešení

V důsledku omezení tradiční licencování metod popsaných v předchozí části poskytuje Azure-SSIS IR nové řešení. Toto řešení používá proměnné prostředí Windows a systémové proměnné prostředí SSIS vazba licence a ověřování komponenty třetích stran. Nezávislí výrobci softwaru tyto proměnné můžete použít k získání informací o jedinečný a trvalý pro Azure-SSIS IR, jako je například ID clusteru a počet uzlů clusteru. Pomocí těchto informací, nezávislé výrobce softwaru vazbu pak lze vytvořit licence na jejich součásti do prostředí Azure-SSIS IR *jako cluster*. Tato vazba používá ID, která se nemění, když zákazníci spustit nebo zastavit, vertikálně navýšit nebo snížit kapacitu škálovací snížení nebo navýšení kapacity nebo změnit konfiguraci prostředí Azure-SSIS IR žádným způsobem.

Následující diagram ukazuje typické instalace, aktivace a vazba licence a ověření toků pro komponenty třetích stran, které používají tyto nové proměnné:

![Instalace licencovaného komponent](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Pokyny
1. Nezávislí výrobci softwaru můžou nabízet svoje licencovaných součástí v různých úrovních (například jeden uzel, až 5 uzlů, až 10 uzlů a tak dále) nebo jednotky SKU. Výrobci poskytuje odpovídající kód Product Key, když zákazníci zakoupit produkt. Nezávislé výrobce softwaru můžete také zadat kontejner objektů blob Azure Storage, který obsahuje nezávislých výrobců softwaru instalační skript a přidružené soubory. Zákazníky můžete zkopírovat tyto soubory do své vlastní kontejner úložiště a upravit pomocí vlastní kód Product Key (například spuštěním `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Zákazníci můžou pak zřizovat nebo změnit konfiguraci prostředí Azure-SSIS IR s identifikátorem URI SAS jejich kontejneru jako parametr. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Pokud prostředí Azure-SSIS IR je zřízený nebo překonfigurovat, spustí instalační program nezávislých výrobců softwaru v každém uzlu a dotazem zjišťovat proměnné prostředí Windows, `SSIS_CLUSTERID` a `SSIS_CLUSTERNODECOUNT`. Prostředí Azure-SSIS IR poté odešle jeho ID clusteru a kód Product Key pro licencovaného produktu k aktivaci serveru nezávislých výrobců softwaru generovat aktivační kód.

3. Po přijetí aktivační kód, můžete místně uložený klíč na každém uzlu (například v registru) instalační program pro nezávislé výrobce softwaru.

4. Když zákazníci spouštět balíčku, který používá licencované komponenty nezávislé výrobce softwaru na uzlu Azure-SSIS IR, balíček přečte místně uložené aktivační kód a ověří proti ID uzlu clusteru. Balíček můžete také v případě potřeby nahlásit aktivační server ISV počet uzlů clusteru.

    Tady je příklad kódu, který ověřuje aktivační kód a hlásí počet uzlů clusteru:

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

Můžete najít seznam partnerů nezávislých výrobců softwaru, kteří mají upravit jejich součásti a rozšíření pro Azure-SSIS IR na konci tento příspěvek na blogu - [Enterprise Edition, vlastní nastavení a 3. stran rozšíření pro službu SSIS ve službě ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Další postup

-   [Vlastní nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
