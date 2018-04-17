---
title: Vývoj placené nebo licencovanou komponenty pro modul runtime integrace Azure SSIS | Microsoft Docs
description: Tento článek popisuje, jak můžete vyvíjet nezávislý dodavatel softwaru a nainstalujte placené nebo licencovaná vlastní komponenty pro modul runtime integrace Azure SSIS
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Vývoj placené nebo licencovanou vlastní komponenty pro modul runtime integrace Azure SSIS

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Problém - IR Azure SSIS vyžaduje jiný přístup

Povaha runtime integrace Azure SSIS uvede několik problémů, které typické licencování metody použité pro místní instalaci součástí vlastní nedostatečné.

-   Uzly IR Azure SSIS se dynamicky mění a mohou být přiděleny nebo vydané kdykoli. Můžete například spuštění nebo zastavení uzly ke správě náklady nebo škálovat nahoru a dolů pomocí různých velikostí uzlu. V důsledku toho vazba komponenty třetích stran licenci na konkrétním uzlu pomocí informace specifické pro počítač, třeba adresu MAC nebo ID procesoru již přijatelná.

-   IR Azure SSIS příchozí nebo odchozí, můžete škálovat také tak, aby počet uzlů můžete zmenšit nebo rozšířit kdykoli.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Řešení – proměnné prostředí systému Windows a systémové proměnné SSIS pro vazbu licence a ověření

V důsledku omezení tradiční licencování metody popsané v předchozí části IR Azure SSIS poskytuje proměnné prostředí systému Windows a systémové proměnné SSIS pro vazbu licence a ověření komponenty jiných výrobců. Nezávislí výrobci softwaru můžete použít tyto proměnné získat informace o jedinečný a trvalé IR Azure SSIS, jako je například ID clusteru a počet uzlů clusteru. Tyto informace o ISV možné svázat licence k jejich součástí s Reakcí Azure SSIS *jako cluster s podporou*, s ID, které se nezmění. když zákazníci spuštění nebo zastavení, vertikální navýšení kapacity nebo dolů, škálovat příchozí nebo odchozí nebo překonfigurujte IR Azure SSIS žádným způsobem.

Následující diagram znázorňuje typické instalace, aktivace a licencí vazby a tok ověření pro komponenty jiných výrobců, které používají tyto nové proměnné:

![Instalace licencovanou komponent](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Pokyny
1. Nezávislí výrobci softwaru můžete nabízet jejich licencovanou součásti v různých úrovních (například jeden uzel, až 5 uzlů, až 10 uzly a tak dále) nebo jednotky SKU. ISV poskytuje odpovídající kód Product Key, když zákazníci zakoupit produktu. ISV můžete zadat taky kontejneru objektů blob Azure Storage, který obsahuje ISV instalační skript a přidružené soubory. Zákazník zkopírujte tyto soubory do svých vlastních kontejner úložiště a je upravit pomocí vlastní kód Product Key (například spuštěním `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Zákazníci následně zřídit nebo překonfigurujte IR Azure SSIS s identifikátor URI SAS jejich kontejneru jako parametr. Další informace najdete v tématu [vlastní nastavení pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Když IR Azure SSIS je zřízený nebo překonfigurovat, ISV instalace se spustí na každém uzlu a dotazem zjišťovat proměnné prostředí systému Windows, `SSIS_CLUSTERID` a `SSIS_CLUSTERNODECOUNT`. Potom IR Azure SSIS odesílá ID jeho clusteru a kód Product Key pro licencovaného produktu k aktivaci serveru ISV generovat aktivační klíč.

3. Po přijetí aktivační klíč, instalační program ISV dokáže klíč místně na každém uzlu (například v registru).

4. Při spuštění balíčku, který používá ISV licencovanou součást v uzlu Azure SSIS IR zákazníkům balíček čte místně uložené aktivační klíč a ověří proti ID uzlu clusteru. Balíček můžete volitelně nahlásit aktivační server ISV počet uzlů clusteru.

    Tady je příklad kódu, který ověří aktivační klíč a hlásí počet uzlů clusteru:

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

## <a name="next-steps"></a>Další postup

-   [Vlastní instalace pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition modulu runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)