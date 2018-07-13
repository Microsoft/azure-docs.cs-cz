---
title: Spouštění úloh po spuštění ve službě Azure Cloud Services | Dokumentace Microsoftu
description: Úlohy po spuštění pomůže připravit vaše prostředí cloudové služby pro vaši aplikaci. To vás naučí, jak fungují úlohy po spuštění a jak je
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 6601eba90f3c3644d418ddd0a74746e1a12bcbd3
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007775"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak nakonfigurovat a spustit úlohy po spuštění pro cloudovou službu
Úlohy po spuštění můžete použít k provádění operací před zahájením roli. Operace, které můžete chtít provést zahrnout instalaci součásti, registrace komponent COM, nastavení klíče registru nebo spouští se dlouho běžící proces.

> [!NOTE]
> Úlohy po spuštění se nedají použít k virtuálním počítačům, jenom pro cloudové služby Web a role pracovního procesu.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak fungují úlohy po spuštění
Úlohy po spuštění jsou akce prováděné před spuštěním vašich rolí a jsou definovány v [ServiceDefinition.csdef] souboru s použitím [Úkol] element v rámci [Po spuštění] element. Často úlohy po spuštění jsou dávkové soubory ale mohou být také konzolové aplikace nebo dávkové soubory, které spouští skripty prostředí PowerShell.

Proměnné prostředí předávání informací do úlohy po spuštění a místní úložiště slouží k předávání informací z úlohy po spuštění. Například proměnnou prostředí lze zadat cestu k programu, který chcete nainstalovat, a soubory se dají zapisovat do místního úložiště, který pak může číst později podle své role.

Vaše úloha po spuštění můžete protokolovat informace a chyby do adresáře určeného **TEMP** proměnné prostředí. Během úlohy po spuštění **TEMP** proměnné prostředí se překládá na *C:\\prostředky\\temp\\[identifikátor guid]. [ název_role]\\RoleTemp* adresář při spuštění v cloudu.

Úlohy po spuštění je také možné provádět několikrát mezi restartováními. Úlohu po spuštění je například možné spustit při každé recyklaci role a ty nemusí vždy zahrnovat restartování. Úlohy po spuštění by měly být napsány tak, že umožňuje, aby běžela bez problémů několikrát.

Úlohy po spuštění musí končit **errorlevel** (nebo ukončovací kód) 0 pro proces spuštění dokončil. Pokud úloha po spuštění ukončí s nenulové **errorlevel**, role se nespustí.

## <a name="role-startup-order"></a>Pořadí spouštění role
Postup spuštění role v Azure jsou následující:

1. Instance je označen jako **počáteční** a nepřijímá provoz.
2. Všechny úlohy po spuštění jsou spouštěny podle jejich **taskType** atribut.
   
   * **Jednoduché** úkoly provádějí synchronně, postupně po jednom.
   * **Pozadí** a **popředí** úlohy se spouští asynchronně, paralelní do počáteční úlohy.  
     
     > [!WARNING]
     > Služba IIS nemusí být plně nakonfigurované během fáze spouštění úloh v procesu spouštění tak konkrétní role dat nemusí být k dispozici. Úlohy po spuštění, které vyžadují konkrétní role dat používejte [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. Spustit hostitelský proces role a vytvoření webu ve službě IIS.
4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metoda je volána.
5. Instance je označen jako **připravené** a provoz se směruje na instanci.
6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda je volána.

## <a name="example-of-a-startup-task"></a>Příklad úlohy po spuštění
Úlohy po spuštění jsou definovány v [ServiceDefinition.csdef] souboru **úloh** elementu. **CommandLine** atribut určuje název a parametry spuštění služby batch do souboru nebo konzoly příkazu **kontextu executionContext** atribut určuje úroveň oprávnění úloha po spuštění a **taskType** atribut určuje, jak se úloha spustí.

V tomto příkladu proměnná prostředí **MyVersionNumber**, je vytvořen pro úlohy po spuštění a nastaven na hodnotu "**1.0.0.0**".

**Soubor ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

V následujícím příkladu **Startup.cmd** dávkového souboru zapíše řádek "aktuální verze je 1.0.0.0" StartupLog.txt souboru v adresáři určeném argumentem proměnná prostředí TEMP. `EXIT /B 0` Řádku zajistí, že úloha po spuštění ukončí s **errorlevel** nula.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> V sadě Visual Studio **kopírovat do výstupního adresáře** vlastnost k nahrání souboru při spuštění služby batch musí být nastavena na **vždy Kopírovat** ujistit se, že váš dávkový soubor při spuštění správně nasazuje se do projektu v Azure (**approot\\bin** pro webové role a **approot** rolí pracovních procesů).
> 
> 

## <a name="description-of-task-attributes"></a>Popis atributů úloh
Následující text popisuje atributy **úloh** prvek [ServiceDefinition.csdef] souboru:

**commandLine** -určuje příkazový řádek pro spuštění úlohy:

* Příkaz, s parametry volitelné příkazového řádku, který začíná úloha po spuštění.
* Často je to název dávkového souboru .cmd nebo BAT.
* Úloha je relativní vzhledem k AppRoot\\složky Bin pro nasazení. Nejsou proměnné prostředí rozbaleny určit cestu a název souboru úkolu. Pokud rozšíření prostředí je potřeba, můžete vytvořit malé .cmd skript, který volá vaše úloha po spuštění.
* Může být aplikace konzoly nebo dávkový soubor, který se spustí [skript prostředí PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**kontextu executionContext** -určuje úroveň oprávnění v úloze po spuštění. Úroveň oprávnění můžete omezené nebo se zvýšenými oprávněními:

* **Limited**  
  Úlohy po spuštění běží se stejnými oprávněními jako role. Když **kontextu executionContext** atribut pro [Modul runtime] prvek je také **omezené**, pak použijí uživatelská oprávnění.
* **se zvýšenými oprávněními**  
  Spouštěcí úkol se spustí s oprávněními správce. To umožňuje úlohy po spuštění instalace programy, udělat změny konfigurace služby IIS, provést změny v registru a další úlohy úrovni správce bez zvýšení úrovně oprávnění role samotný.  

> [!NOTE]
> Úroveň oprávnění úlohy po spuštění nemusí být stejné jako roli, samotného.
> 
> 

**taskType** – určuje způsob, jak provést úlohy po spuštění.

* **Jednoduché**  
  Úkoly se provádí asynchronně, jednotlivě, v pořadí zadaném v [ServiceDefinition.csdef] souboru. Pokud jeden **jednoduché** úloha po spuštění ukončí s **errorlevel** nula, další **jednoduché** provádí se úloha po spuštění. Pokud neexistují žádné další **jednoduché** úlohy po spuštění k provedení, pak spustí se vlastní role.   
  
  > [!NOTE]
  > Pokud **jednoduché** úloh končí nenulové **errorlevel**, instance se zablokuje. Následné **jednoduché** po spuštění úlohy a role, se nespustí.
  > 
  > 
  
    K zajištění, že dávkový soubor končí **errorlevel** nula, spusťte příkaz `EXIT /B 0` na konci procesu dávkového souboru.
* **Na pozadí**  
  Úkoly jsou spouštěny asynchronně, souběžně s spouštěcí roli.
* **Popředí**  
  Úkoly jsou spouštěny asynchronně, souběžně s spouštěcí roli. Klíčovým rozdílem mezi **popředí** a **pozadí** úkolu je, že **popředí** úloh brání roli z recyklaci nebo ukončení, dokud úloha byla ukončena. **Pozadí** úlohy nemají toto omezení.

## <a name="environment-variables"></a>Proměnné prostředí
Proměnné prostředí jsou tak k předávání informací do počáteční úlohy. Například můžete vložit cestu k objektu blob, který obsahuje program pro instalaci, nebo čísla portů, které budou používat vaši roli nebo nastavení k řízení funkcí vaše úloha po spuštění.

Existují dva typy proměnných prostředí pro úlohy po spuštění; statické proměnné a proměnné prostředí založené na členy [RoleEnvironment] třídy. Obě jsou v [prostředí] část [ServiceDefinition.csdef] souboru a oba použití [Proměnná] elementu a **název** atribut.

Statické proměnné používá **hodnotu** atribut [Proměnná] elementu. Tento příklad vytvoří proměnné prostředí **MyVersionNumber** obsahující statickou hodnotu "**1.0.0.0**". Dalším příkladem může být k vytvoření **StagingOrProduction** proměnné prostředí, které můžete ručně nastavit na hodnotu "**pracovní**"nebo"**produkční**" k provedení různé spouštěcí akce podle hodnoty **StagingOrProduction** proměnné prostředí.

Nepoužívejte proměnné prostředí založené na členech třídy RoleEnvironment **hodnotu** atribut [Proměnná] elementu. Místo toho [RoleInstanceValue] podřízený element s odpovídající **XPath** hodnotu atributu, se použijí k vytvoření proměnné prostředí založené na konkrétním členem [RoleEnvironment] třídy. Hodnoty **XPath** atribut pro přístup k různým [RoleEnvironment] najdete hodnoty [tady](cloud-services-role-config-xpath.md).

Například pro vytvoření proměnné prostředí, který je "**true**" když je spuštěn v emulátoru služby compute, a "**false**" při spuštění v cloudu, použijte následující [Proměnná] a [RoleInstanceValue] prvky:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Další postup
Zjistěte, jak provádět některé [běžné úlohy po spuštění](cloud-services-startup-tasks-common.md) s Cloudovou službou.

[Balíček](cloud-services-model-and-package.md) cloudové služby.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Úkol]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Po spuštění]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Modul runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Proměnná]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
