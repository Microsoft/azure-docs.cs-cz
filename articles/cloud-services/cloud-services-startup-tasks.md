---
title: Spustit spuštění úlohy v cloudových služeb Azure | Microsoft Docs
description: Spuštění úlohy pomůže připravit vaše prostředí cloudové služby pro vaši aplikaci. To se naučíte, jak fungují spuštění úlohy a postupy, aby byly
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 1c1b3aa86dc8211de0c07c9fb68da5685c86f551
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843676"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak nakonfigurovat a spustit úlohy spuštění pro cloudové služby
Spuštění úlohy můžete použít k provádění operací před zahájením roli. Operace, které můžete chtít provést zahrnovat instalaci komponenty, registraci komponenty modelu COM, nastavení klíče registru nebo dlouhotrvající proces.

> [!NOTE]
> Spuštění úlohy se nedají použít k virtuálním počítačům, pouze pro webové služby Cloud a rolí pracovního procesu.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak fungují spuštění úlohy
Spuštění úlohy se akcí, které před zahájením role a jsou definovány v [ServiceDefinition.csdef] soubor pomocí [úloh] v rámci [spuštění] element. Často spuštění úlohy jsou dávkové soubory, ale lze je také konzolové aplikace nebo dávkové soubory, které se spouštějí skripty prostředí PowerShell.

Proměnné prostředí předání informací do úloha spuštění a místní úložiště slouží k předávání informací mimo spuštění úloh. Například proměnná prostředí můžete zadat cestu k programu, který chcete nainstalovat, a lze zapisovat soubory do místního úložiště, které lze poté číst později vaše role.

Spuštění úkolu můžete informace a protokolu chyb do adresáře určeného **TEMP** proměnné prostředí. Při spuštění úlohy **TEMP** proměnnou prostředí přeloží na *C:\\prostředky\\temp\\[identifikátor guid]. [Rolename]\\RoleTemp* directory při spuštění v cloudu.

Spuštění úlohy lze také spustit několikrát mezi jednotlivými restartováními. Například úloha spuštění spustí pokaždé, když recykluje roli a roli recykluje nemusí vždy zahrnovat restartování. Spuštění úlohy budou zasílány způsobem, který umožňuje, aby se spouštěly několikrát bez problémů.

Spuštění úlohy musí končit **errorlevel** (nebo ukončovací kód) nula na dokončení procesu spuštění. Pokud úloha spuštění končí nenulovou **errorlevel**, role se nespustí.

## <a name="role-startup-order"></a>Pořadí spouštění role
Postup spuštění role v Azure jsou následující:

1. Instance je označena jako **počáteční** a nepřijímá provoz.
2. Všechny úlohy spuštění jsou spouštěny podle jejich **taskType** atribut.
   
   * **Jednoduché** synchronně, provedení úloh po jednom.
   * **Pozadí** a **popředí** úkoly spouštějí asynchronně, paralelní spuštění úlohy.  
     
     > [!WARNING]
     > Služba IIS nemusí být plně nakonfigurované během fáze spuštění úloh v procesu spuštění proto role specifických dat pravděpodobně není k dispozici. Spuštění úlohy, které vyžadují specifickou rolí dat využít [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. Spuštění procesu role hostitele a vytvoření webu ve službě IIS.
4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metoda je volána.
5. Instance je označena jako **připraven** a provoz se směruje na instanci.
6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda je volána.

## <a name="example-of-a-startup-task"></a>Příklad spuštění úlohy
Spuštění úlohy jsou definovány v [ServiceDefinition.csdef] v souboru **úloh** elementu. **CommandLine** atribut určuje název a parametry spuštění dávkového souboru nebo konzoly příkazu **executionContext** atribut určuje úroveň oprávnění při spuštění úlohy a **taskType** atribut určuje, jak se úlohu spustit.

V tomto příkladu se proměnná prostředí **MyVersionNumber**, se vytvoří pro spuštění úlohy a nastaven na hodnotu "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

V následujícím příkladu **Startup.cmd** dávkový soubor zapíše řádek "aktuální verze je 1.0.0.0" do souboru StartupLog.txt v adresáři zadaném proměnnou prostředí TEMP. `EXIT /B 0` Řádku zajišťuje, že úloha spuštění končí **errorlevel** nula.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> V sadě Visual Studio **kopírovat do výstupního adresáře** vlastnost pro váš dávkový soubor spuštění musí být nastavená na **kopie vždy** a zajistit, aby byl váš dávkový soubor spuštění správně nasazen do projektu v Azure (**approot\\bin** pro webové role a **approot** pro role pracovního procesu).
> 
> 

## <a name="description-of-task-attributes"></a>Popis úloh atributů
Následující text popisuje atributy **úloh** element v [ServiceDefinition.csdef] souboru:

**commandLine** -určuje příkazový řádek pro spuštění úlohy:

* Příkaz, s parametry volitelné příkazového řádku, které zahájí spuštění úloh.
* Často to je název souboru dávkový soubor cmd nebo BAT.
* Tato úloha je relativní vzhledem k AppRoot\\složky Koš služby pro nasazení. Při určení cesty a souboru úlohy nejsou rozbalit proměnné prostředí. Pokud rozšíření prostředí je potřeba, můžete vytvořit malé .cmd skript, který volá spuštění úkolu.
* Může být konzolovou aplikaci nebo dávkového souboru, který začíná [skript prostředí PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** -určuje úroveň oprávnění pro spuštění úlohy. Úroveň oprávnění může být omezené nebo se zvýšenými oprávněními:

* **omezená**  
  Úloha spuštění běží se stejnými oprávněními jako roli. Při **executionContext** atribut pro [Runtime] element je také **omezené**, jsou použita uživatelská oprávnění.
* **zvýšené**  
  Spuštění úlohy se spustí s oprávněními správce. To umožňuje spouštění úloh k instalaci programy, udělat změny konfigurace služby IIS, provedení změn v registru a další úkoly úrovni správce bez zvýšení úrovně oprávnění role sám sebe.  

> [!NOTE]
> Úroveň oprávnění při spuštění úlohy, která nemusí být stejný jako roli sám sebe.
> 
> 

**taskType** -určuje způsob, jakým se spustí úloha spuštění.

* **jednoduché**  
  Úlohy jsou spouštěny synchronně, jeden po druhém, v pořadí zadaném v [ServiceDefinition.csdef] souboru. Pokud jeden **jednoduché** končí úloha spuštění **errorlevel** nulové hodnoty, další **jednoduché** spuštění úlohy je spuštěn. Pokud neexistují žádné další **jednoduché** spuštění úloh provést, pak bude spuštěn roli sám sebe.   
  
  > [!NOTE]
  > Pokud **jednoduché** úloh končí nenulovou **errorlevel**, instance se zablokuje. Následné **jednoduché** spuštění úlohy a role samostatně, se nespustí.
  > 
  > 
  
    K zajištění, že váš dávkový soubor končí **errorlevel** nulové hodnoty, spusťte příkaz `EXIT /B 0` na konci souboru dávkové zpracování.
* **pozadí**  
  Úlohy se spustí asynchronně, paralelně s spuštění role.
* **popředí**  
  Úlohy se spustí asynchronně, paralelně s spuštění role. Klíčovým rozdílem mezi **popředí** a **pozadí** úloh je, že **popředí** úloh brání roli z recyklace nebo dokud nebude úloha skončila vypnutí. **Pozadí** úlohy nemají toto omezení.

## <a name="environment-variables"></a>Proměnné prostředí
Proměnné prostředí jsou způsob, jak předat informace o spuštění úloh. Například můžete vkládat cestu k objektu blob, který obsahuje program pro instalaci, nebo čísla portů, který bude používat vaše role nebo nastavení k řízení funkcí spuštění úkolu.

Existují dva typy proměnných prostředí pro spuštění úlohy; statické proměnné prostředí, proměnné prostředí založené na členy [ RoleEnvironment] třídy. Jsou obě [prostředí] části [ServiceDefinition.csdef] souboru a obě použití [proměnné] elementu a **název** atribut.

Proměnné prostředí statické používá **hodnotu** atribut [proměnné] elementu. Tento příklad vytvoří proměnnou prostředí **MyVersionNumber** jehož statickou hodnotu "**1.0.0.0**". Dalším příkladem může být k vytvoření **StagingOrProduction** proměnné prostředí, kterou můžete ručně nastavit na hodnoty "**pracovní**"nebo"**produkční**" k provedení na základě hodnoty na základě různých spouštěcích akce **StagingOrProduction** proměnné prostředí.

Nepoužívejte proměnné prostředí založené na členy třídy RoleEnvironment **hodnotu** atribut [proměnné] elementu. Místo toho [RoleInstanceValue] podřízený element s příslušnou **XPath** hodnota atributu, se používají k vytvoření proměnné prostředí založené na konkrétní členem [ RoleEnvironment] třídy. Hodnoty **XPath** atribut pro přístup k různé [ RoleEnvironment] hodnoty jsou uvedeny [zde](cloud-services-role-config-xpath.md).

Chcete-li například vytvořit proměnnou prostředí, která je "**true**" když je spuštěn v emulátoru služby výpočty v, a "**false**" při spuštění v cloudu, použijte následující [proměnné] a [RoleInstanceValue] prvky:

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

## <a name="next-steps"></a>Další kroky
Zjistěte, jak provést některé [běžné úlohy spuštění](cloud-services-startup-tasks-common.md) s Cloudovou službou.

[Balíček](cloud-services-model-and-package.md) cloudové služby.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[úloh]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[spuštění]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[proměnné]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[ RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
