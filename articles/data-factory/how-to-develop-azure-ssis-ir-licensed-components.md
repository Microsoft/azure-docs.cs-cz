---
title: Instalace placené nebo licencovaná komponenty pro modul runtime integrace Azure SSIS | Microsoft Docs
description: Zjistěte, jak můžete vyvíjet nezávislý dodavatel softwaru a nainstalujte placené nebo licencovaná vlastní komponenty pro modul runtime integrace Azure SSIS
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e2e000df2933b8fa08bf98ef55b12f90de6a5e51
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830839"
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Vývoj placené nebo licencovanou vlastní komponenty pro modul runtime integrace Azure SSIS

Tento článek popisuje, jak vyvíjet a nainstalovat placené nebo licencovanou vlastní součásti pro integraci služby SSIS (SQL Server) balíčky, které spustit v Azure v modulu runtime integrace Azure SSIS ISV.

## <a name="the-problem"></a>Problém

Povaha runtime integrace Azure SSIS uvede několik problémů, které typické licencování metody použité pro místní instalaci součástí vlastní nedostatečné. V důsledku toho IR Azure SSIS vyžaduje jiný přístup.

-   Uzly IR Azure SSIS se dynamicky mění a mohou být přiděleny nebo vydané kdykoli. Můžete například spuštění nebo zastavení uzly ke správě náklady nebo škálovat nahoru a dolů pomocí různých velikostí uzlu. V důsledku toho vazba komponenty třetích stran licenci na konkrétním uzlu pomocí informace specifické pro počítač, třeba adresu MAC nebo ID procesoru již přijatelná.

-   IR Azure SSIS příchozí nebo odchozí, můžete škálovat také tak, aby počet uzlů můžete zmenšit nebo rozšířit kdykoli.

## <a name="the-solution"></a>Řešení

V důsledku omezení tradiční licencování metody popsané v předchozí části IR Azure SSIS poskytuje nové řešení. Toto řešení používá proměnné prostředí systému Windows a systémové proměnné SSIS pro vazbu licence a ověření komponenty jiných výrobců. Nezávislí výrobci softwaru můžete použít tyto proměnné získat informace o jedinečný a trvalé IR Azure SSIS, jako je například ID clusteru a počet uzlů clusteru. Pomocí těchto informací můžete vázat nezávislí výrobci softwaru pak licence k jejich součástí na Azure SSIS IR *jako cluster s podporou*. Tato vazba používá ID, které se nezmění. když zákazníci spustit nebo zastavit, škálovat nahoru nebo dolů škálování příchozí nebo odchozí nebo překonfigurujte IR Azure SSIS žádným způsobem.

Následující diagram znázorňuje typické instalace, aktivace a licencí vazby a tok ověření pro komponenty jiných výrobců, které používají tyto nové proměnné:

![Instalace licencovanou komponent](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Pokyny
1. Nezávislí výrobci softwaru můžete nabízet jejich licencovanou součásti v různých úrovních (například jeden uzel, až 5 uzlů, až 10 uzly a tak dále) nebo jednotky SKU. ISV poskytuje odpovídající kód Product Key, když zákazníci zakoupit produktu. ISV můžete zadat taky kontejneru objektů blob Azure Storage, který obsahuje ISV instalační skript a přidružené soubory. Zákazník zkopírujte tyto soubory do svých vlastních kontejner úložiště a je upravit pomocí vlastní kód Product Key (například spuštěním `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Zákazníci následně zřídit nebo překonfigurujte IR Azure SSIS s identifikátor URI SAS jejich kontejneru jako parametr. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

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

## <a name="isv-partners"></a>ISV partnery

Můžete najít seznam ISV partnerů, kteří mají přizpůsobit jejich součástí a rozšíření Azure SSIS Reakcí na konci tohoto příspěvku na blogu - [Enterprise Edition, vlastní instalace a 3. stran rozšiřitelnost SSIS v ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Další postup

-   [Vlastní instalace pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition modulu runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
