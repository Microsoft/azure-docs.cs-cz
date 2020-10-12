---
title: Spouštění úloh po spuštění v Azure Cloud Services | Microsoft Docs
description: Úlohy po spuštění vám pomůžou připravit prostředí cloudové služby pro vaši aplikaci. Naučíte se, jak fungují úlohy při spouštění a jak je udělat
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75360306"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak nakonfigurovat a spustit úlohy po spuštění pro cloudovou službu
Úlohy po spuštění můžete použít k provádění operací před spuštěním role. Operace, které můžete chtít provést, zahrnují instalaci komponenty, registraci komponent modelu COM, nastavení klíčů registru nebo spuštění dlouhotrvajícího procesu.

> [!NOTE]
> Úlohy po spuštění se nevztahují na Virtual Machines, a to pouze na webové role a role pracovních procesů cloudové služby.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak fungují úlohy při spuštění
Úlohy po spuštění jsou akce, které jsou provedeny před začátkem rolí a jsou definovány v souboru [ServiceDefinition. csdef] pomocí elementu [Task] v elementu [Startup] . Často spouštěné úlohy jsou dávkové soubory, ale mohou být také konzolové aplikace nebo dávkové soubory, které spouštějí skripty prostředí PowerShell.

Proměnné prostředí předávají informace do spouštěcí úlohy a pomocí místního úložiště je možné předávat informace z úlohy po spuštění. Například proměnná prostředí může určovat cestu k programu, který chcete nainstalovat, a do místního úložiště je možné zapisovat soubory, které pak můžou později číst vaše role.

Úloha po spuštění může protokolovat informace a chyby do adresáře určeného proměnnou prostředí **TEMP** . V průběhu úlohy po spuštění se proměnná prostředí **TEMP** přeloží na *C: \\ Resources \\ TEMP \\ [GUID]. [ roleName] \\ RoleTemp* adresář při spuštění v cloudu.

Úlohy po spuštění je také možné provádět několikrát mezi restartováními. Úlohu po spuštění je například možné spustit při každé recyklaci role a ty nemusí vždy zahrnovat restartování. Úlohy po spuštění by měly být zapsány způsobem, který jim umožní běžet několikrát bez problémů.

Spouštěcí úlohy musí končit znakem **errorlevel** (nebo ukončovacím kódem) nula, aby bylo možné proces spuštění dokončit. Pokud úloha po spuštění skončí s nenulovou hodnotou **errorlevel**, role se nespustí.

## <a name="role-startup-order"></a>Pořadí spouštění role
Následuje seznam spouštěcí procedury role v Azure:

1. Instance je označena jako **spuštěná** a nepřijímá provoz.
2. Všechny úlohy po spuštění jsou spouštěny podle jejich atributu **taskType** .
   
   * **Jednoduché** úlohy jsou spouštěny synchronně, jeden po druhém.
   * Úlohy na **pozadí** a na **popředí** jsou spouštěny asynchronně, paralelně s úlohou po spuštění.  
     
     > [!WARNING]
     > Služba IIS nemusí být během procesu spuštění plně nakonfigurovaná během fáze úlohy po spuštění, takže data specifická pro role možná nebudou k dispozici. Úlohy po spuštění, které vyžadují data specifická pro danou roli, by měly používat [Microsoft. windowsazure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Proces hostitele role je spuštěn a lokalita je vytvořena ve službě IIS.
4. Je volána metoda [Microsoft. windowsazure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .
5. Instance je označena jako **připravená** a provoz se směruje do instance.
6. Je volána metoda [Microsoft. windowsazure. ServiceRuntime. RoleEntryPoint. Run](/previous-versions/azure/reference/ee772746(v=azure.100)) .

## <a name="example-of-a-startup-task"></a>Příklad úlohy po spuštění
Úlohy po spuštění jsou definovány v souboru [ServiceDefinition. csdef] v elementu **Task** . Atribut **CommandLine** Určuje název a parametry spouštěcího dávkového souboru nebo konzoly příkazového řádku, atribut **ExecutionContext** určuje úroveň oprávnění úlohy po spuštění a atribut **taskType** určuje, jak bude úkol proveden.

V tomto příkladu je pro úlohu po spuštění vytvořena proměnná prostředí **MyVersionNumber**a je nastavena na hodnotu**1.0.0.0**.

**ServiceDefinition. csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

V následujícím příkladu vytvoří dávkový soubor **Startup. cmd** řádek "aktuální verze je 1.0.0.0" do souboru StartupLog.txt v adresáři určeném PROMĚNNOU prostředí TEMP. Na `EXIT /B 0` řádku se zaručí, že úloha po spuštění skončí s hodnotou **errorlevel** nula.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> V sadě Visual Studio by měla být vlastnost **Kopírovat do výstupního adresáře** pro spouštěcí dávkový soubor nastavená na hodnotu **vždy kopírovat** , aby bylo zajištěno, že váš spouštěcí dávkový soubor bude správně nasazen do projektu v Azure (**AppRoot \\ bin** pro webové role a **AppRoot** pro role pracovního procesu).
> 
> 

## <a name="description-of-task-attributes"></a>Popis atributů úloh
Následující popis popisuje atributy elementu **Task** v souboru [ServiceDefinition. csdef] :

**CommandLine** – Určuje příkazový řádek pro úlohu po spuštění:

* Příkaz s nepovinnými parametry příkazového řádku, které začínají úlohu po spuštění.
* Často se jedná o název souboru dávkového souboru. cmd nebo. bat.
* Úkol je relativní vzhledem ke složce AppRoot \\ bin pro nasazení. Proměnné prostředí nejsou roztažené při určování cesty a souboru úlohy. Pokud je nutné rozšíření prostředí, můžete vytvořit malý skript. cmd, který bude volat úlohu po spuštění.
* Může se jednat o konzolovou aplikaci nebo dávkový soubor, který spouští [powershellový skript](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**ExecutionContext** – určuje úroveň oprávnění pro úlohu po spuštění. Úroveň oprávnění může být omezená nebo zvýšená:

* **limitovan**  
  Úloha po spuštění se spouští se stejnými oprávněními jako role. Je-li atribut **ExecutionContext** pro element [runtime] také **omezen**, jsou použita oprávnění uživatele.
* **úrovně**  
  Úloha po spuštění se spouští s oprávněními správce. To umožňuje úlohám po spuštění instalovat programy, provádět změny konfigurace služby IIS, provádět změny v registru a další úlohy na úrovni správce bez zvýšení úrovně oprávnění samotné role.  

> [!NOTE]
> Úroveň oprávnění úlohy po spuštění nemusí být stejná jako samotná role.
> 
> 

**taskType** – určuje způsob spuštění spouštěného úkolu.

* **pouh**  
  Úlohy jsou spouštěny synchronně, po jednom v pořadí určeném v souboru [ServiceDefinition. csdef] . Když jedna **Jednoduchá** úloha po spuštění skončí s **errorlevel** hodnotou nula, spustí se další **jednoduchý** úkol po spuštění. Pokud neexistují žádné **jednoduché** úlohy po spuštění, spustí se tato role.   
  
  > [!NOTE]
  > Pokud je **jednoduchý** úkol ukončen s nenulovou hodnotou **errorlevel**, instance bude zablokována. Následné **jednoduché** úvodní úlohy a samotné role se nespustí.
  > 
  > 
  
    Aby se zajistilo, že dávkový soubor skončí **errorlevel** s hodnotou nula, spusťte příkaz `EXIT /B 0` na konci procesu dávkového souboru.
* **background**  
  Úlohy jsou spouštěny asynchronně, paralelně s spuštěním role.
* **zachovat**  
  Úlohy jsou spouštěny asynchronně, paralelně s spuštěním role. Klíčovým rozdílem mezi **popředí** a úlohou na **pozadí** je, že úloha na **popředí** brání roli v recyklaci nebo vypnutí, dokud se úloha neukončí. Úlohy na **pozadí** nemají toto omezení.

## <a name="environment-variables"></a>Proměnné prostředí
Proměnné prostředí představují způsob, jak předat informace spouštěcí úloze. Můžete například umístit cestu k objektu blob, který obsahuje program k instalaci, nebo čísla portů, která vaše role bude používat, nebo nastavení pro řízení funkcí úlohy po spuštění.

Existují dva druhy proměnných prostředí pro úlohy po spuštění; statické proměnné prostředí a proměnné prostředí založené na členech třídy [RoleEnvironment] . Obě jsou v oddílu [prostředí] souboru [ServiceDefinition. csdef] a oba používají atribut [Variable] element a **Name** .

Statické proměnné prostředí používají atribut **Value** elementu [Variable] . Výše uvedený příklad vytvoří proměnnou prostředí **MyVersionNumber** , která má statickou hodnotu "**1.0.0.0**". Dalším příkladem je vytvoření proměnné prostředí **StagingOrProduction** , kterou můžete ručně nastavit na hodnoty "**fázování**" nebo "**produkční**", aby se na základě hodnoty proměnné prostředí **StagingOrProduction** prováděly různé akce při spuštění.

Proměnné prostředí založené na členech třídy RoleEnvironment nepoužívají atribut **Value** elementu [Variable] . Namísto toho je podřízený prvek [RoleInstanceValue] s odpovídající hodnotou atributu **XPath** použit k vytvoření proměnné prostředí na základě konkrétního člena třídy [RoleEnvironment] . Hodnoty pro atribut **XPath** pro přístup k různým hodnotám [RoleEnvironment] lze nalézt [zde](cloud-services-role-config-xpath.md).

Pokud například chcete vytvořit proměnnou prostředí, která je "**true**", když je instance spuštěna v emulátoru služby COMPUTE a "**NEPRAVDA**" při spuštění v cloudu, použijte následující [proměnnou] a [RoleInstanceValue] prvky:

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
Naučte se provádět některé [běžné úlohy po spuštění](cloud-services-startup-tasks-common.md) s vaší cloudovou službou.

[Zabalit](cloud-services-model-and-package.md) cloudovou službu.  

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Úloha]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Spuštění]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Modul runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Proměnná]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



