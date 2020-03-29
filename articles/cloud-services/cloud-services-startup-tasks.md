---
title: Spouštění úloh po spuštění ve cloudových službách Azure | Dokumenty společnosti Microsoft
description: Úlohy při spuštění pomáhají připravit prostředí cloudové služby pro vaši aplikaci. To vás naučí, jak úlohy spuštění fungují a jak je vytvořit
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360306"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Konfigurace a spuštění úloh při spuštění pro cloudovou službu
Úlohy spuštění můžete použít k provádění operací před spuštěním role. Mezi operace, které můžete chtít provést, patří instalace součásti, registrace součástí modelu COM, nastavení klíčů registru nebo spuštění dlouho běžícího procesu.

> [!NOTE]
> Úlohy při spuštění se nevztahují na virtuální počítače, ale pouze na webové role cloudových služeb a role pracovních dělníků.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak fungují úlohy při spuštění
Úlohy při spuštění jsou akce, které jsou prováděny před zahájením rolí a jsou definovány v souboru [ServiceDefinition.csdef] pomocí prvku [Task] v rámci prvku [Po spuštění.] Často spouštěcí úlohy jsou dávkové soubory, ale mohou to být také konzolové aplikace nebo dávkové soubory, které spouštějí skripty prostředí PowerShell.

Proměnné prostředí předávají informace do úlohy při spuštění a místní úložiště lze použít k předání informací z úlohy při spuštění. Proměnná prostředí může například určit cestu k programu, který chcete nainstalovat, a soubory mohou být zapsány do místního úložiště, které pak mohou být později přečteny vašimi rolemi.

Úloha při spuštění může protokolovat informace a chyby do adresáře určeného proměnnou prostředí **TEMP.** Během úlohy při spuštění se proměnná prostředí **TEMP** překládá na *dočasnou\\\\hodnotu C: Zdroje\\[guid].[ rolename]\\Adresář RoleTemp* při spuštění v cloudu.

Úlohy po spuštění je také možné provádět několikrát mezi restartováními. Úlohu po spuštění je například možné spustit při každé recyklaci role a ty nemusí vždy zahrnovat restartování. Úlohy při spuštění by měly být napsány způsobem, který jim umožňuje několikrát spustit bez problémů.

Úlohy při spuštění musí končit **úrovní chyby** (nebo ukončovacím kódem) nula, aby byl proces spuštění dokončen. Pokud úloha při spuštění končí chybovou **úrovní**bez nuly , role se nespustí.

## <a name="role-startup-order"></a>Pořadí spuštění role
V následujícím seznamu je uvedena procedura spuštění role v Azure:

1. Instance je označena jako **Spuštění** a nepřijímá přenosy.
2. Všechny úlohy při spuštění jsou prováděny podle jejich **taskType** atribut.
   
   * **Jednoduché** úkoly jsou prováděny synchronně, jeden po druhém.
   * Úlohy **na pozadí** a **popředí** jsou spouštěny asynchronně, paralelně s úlohou při spuštění.  
     
     > [!WARNING]
     > Služba IIS nemusí být během fáze úlohy spuštění v procesu spuštění plně nakonfigurována, takže data specifická pro roli nemusí být k dispozici. Úlohy při spuštění, které vyžadují data specifická pro konkrétní role, by měly používat [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Proces hostitele role je spuštěn a web je vytvořen ve službě IIS.
4. Nazývá se metoda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))
5. Instance je označena jako **Připravena** a provoz je směrován na instanci.
6. Nazývá se metoda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run.](/previous-versions/azure/reference/ee772746(v=azure.100))

## <a name="example-of-a-startup-task"></a>Příklad úlohy spuštění
Úlohy při spuštění jsou definovány v souboru [ServiceDefinition.csdef] v elementu **Task.** Atribut **commandLine** určuje název a parametry spouštěcího dávkového souboru nebo příkazu konzoly, atribut **executionContext** určuje úroveň oprávnění spouštěcí úlohy a atribut **taskType** určuje způsob provedení úlohy.

V tomto příkladu je pro úlohu při spuštění vytvořena proměnná prostředí **MyVersionNumber**a nastavena na hodnotu "**1.0.0.0**".

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

V následujícím příkladu dávkový soubor **Startup.cmd** zapíše řádek "Aktuální verze je 1.0.0.0" do souboru StartupLog.txt v adresáři určeném proměnnou prostředí TEMP. Řádek `EXIT /B 0` zajišťuje, že úloha při spuštění končí **chybovou úrovní** nula.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> V sadě Visual Studio by měla být vlastnost **Kopírovat do výstupního adresáře** pro spouštěcí dávkový soubor nastavena na **Kopírovat vždy,** abyste měli jistotu, že je spouštěcí dávkový soubor správně nasazen do vašeho projektu v Azure **(přihrádka aplikace\\** pro webové role a **kořenová složka aplikace** pro role pracovního procesu).
> 
> 

## <a name="description-of-task-attributes"></a>Popis atributů úloh
Následující text popisuje atributy prvku **Task** v souboru [ServiceDefinition.csdef:]

**příkazcommandLine** - Určuje příkazový řádek pro úlohu při spuštění:

* Příkaz s volitelnými parametry příkazového řádku, který začíná úlohu při spuštění.
* Často se jedná o název souboru cmd nebo .bat batch.
* Úloha je relativní vzhledem ke složce AppRoot\\Bin pro nasazení. Proměnné prostředí nejsou rozbaleny při určování cesty a souboru úlohy. Pokud je vyžadováno rozšíření prostředí, můžete vytvořit malý skript CMD, který volá úlohu při spuštění.
* Může se na pokládá být konzolová aplikace nebo dávkový soubor, který spouští [skript prostředí PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - Určuje úroveň oprávnění pro úlohu při spuštění. Úroveň oprávnění může být omezena nebo zvýšena:

* **Omezené**  
  Úloha při spuštění je spuštěna se stejnými oprávněními jako role. Pokud je také **omezený**atribut **executionContext** pro prvek [Runtime] , použijí se oprávnění uživatele.
* **Zvýšené**  
  Úloha při spuštění je spuštěna s oprávněními správce. To umožňuje úlohám při spuštění instalovat programy, provádět změny konfigurace služby IIS, provádět změny registru a další úlohy na úrovni správce, aniž by se zvýšila úroveň oprávnění samotné role.  

> [!NOTE]
> Úroveň oprávnění spouštěcí úlohy nemusí být stejná jako samotná role.
> 
> 

**taskType** - Určuje způsob spuštění úlohy.

* **Jednoduché**  
  Úkoly jsou prováděny synchronně, jeden po druhém, v pořadí určeném v souboru [ServiceDefinition.csdef.] Když jedna **jednoduchá** spouštěcí úloha skončí **chybovou úrovní** nula, provede se další **jednoduchá** spouštěcí úloha. Pokud nejsou k dispozici žádné další **jednoduché** úlohy spuštění spustit, pak role sama o sobě bude spuštěna.   
  
  > [!NOTE]
  > Pokud **jednoduchý** úkol končí nenulovou **chybovou úrovní**, instance bude blokována. Následné **jednoduché** úlohy při spuštění a samotná role se nespustí.
  > 
  > 
  
    Chcete-li zajistit, aby dávkový soubor skončil úrovní `EXIT /B 0` **chyby** nula, spusťte příkaz na konci procesu dávkového souboru.
* **Pozadí**  
  Úlohy jsou prováděny asynchronně, souběžně se spuštěním role.
* **Popředí**  
  Úlohy jsou prováděny asynchronně, souběžně se spuštěním role. Hlavní rozdíl mezi **úkolem na popředí** a **úlohou na pozadí** spočívá v tom, že úloha v **popředí** zabrání roli v recyklaci nebo vypnutí, dokud úkol neskončí. Úlohy **na pozadí** nemají toto omezení.

## <a name="environment-variables"></a>Proměnné prostředí
Proměnné prostředí jsou způsob, jak předat informace spouštěcí úlohy. Můžete například umístit cestu k objektu blob, který obsahuje program k instalaci, nebo čísla portů, která bude vaše role používat, nebo nastavení pro řízení funkcí úlohy při spuštění.

Existují dva druhy proměnných prostředí pro úlohy při spuštění; proměnné statického prostředí a proměnné prostředí založené na členech třídy [RoleEnvironment.] Oba jsou v části [Prostředí] souboru [ServiceDefinition.csdef] a oba používají element [Variable] a **atribut name.**

Proměnné statického prostředí používají atribut **hodnoty** prvku [Variable.] Výše uvedený příklad vytvoří proměnnou prostředí **MyVersionNumber,** která má statickou hodnotu "**1.0.0.0**". Dalším příkladem by bylo vytvoření proměnné prostředí **StagingOrProduction,** kterou můžete ručně nastavit na hodnoty "**staging**" nebo "**production**" k provádění různých spouštěcích akcí na základě hodnoty proměnné prostředí **StagingOrProduction.**

Proměnné prostředí založené na členech třídy RoleEnvironment nepoužívají atribut **value** elementu [Variable.] Místo toho [roleInstanceValue] podřízený prvek, s příslušnou hodnotou **atributu XPath,** se používají k vytvoření proměnné prostředí na základě konkrétního člena třídy [RoleEnvironment.] Hodnoty atributu **XPath** pro přístup k různým hodnotám [RoleEnvironment] naleznete [zde](cloud-services-role-config-xpath.md).

Chcete-li například vytvořit proměnnou prostředí, která je **"true**", když je instance spuštěna v emulátoru výpočetního prostředí, a "**false**" při spuštění v cloudu, použijte následující [elementy Variable] a [RoleInstanceValue:]

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
Přečtěte si, jak provádět některé [běžné úlohy při spuštění](cloud-services-startup-tasks-common.md) pomocí cloudové služby.

[Zabalte](cloud-services-model-and-package.md) si cloudovou službu.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Úkol]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Spuštění]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Modul runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Proměnná]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[Hodnota instance role]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[Prostředí role]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



