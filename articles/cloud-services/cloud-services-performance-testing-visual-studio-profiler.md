---
title: Profilace cloudové služby místně v emulátoru služby Compute | Dokumentace Microsoftu
services: cloud-services
description: Prozkoumat problémy s výkonem v cloudových službách pomocí profileru sady Visual Studio
documentationcenter: ''
author: mikejo
manager: douge
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: ea46039583681bd89e254d153997e3a300041d4e
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341350"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testování výkonu cloudové služby místně v emulátoru Azure Compute pomocí sady Visual Studio Profiler
Různé nástroje a techniky jsou k dispozici pro testování výkonu cloudové služby.
Při publikování v cloudové službě Azure, můžete použít Visual Studio shromáždění profilačních dat a analyzovat místně, jak je popsáno v [profilace aplikace Azure][1].
Můžete také použít diagnostiky ke sledování různých čítačů výkonu, jak je popsáno v [pomocí čítačů výkonu v Azure][2].
Můžete také chtít profilování aplikace místně v emulátoru služby compute před nasazením do cloudu.

Tento článek se věnuje metodě profilování pomocí vzorkování procesoru, která se dá dělat místně v emulátoru. Vzorkování procesoru je metoda profilování, která není velmi nežádoucí. V intervalech určené vzorkování profileru pořídí snímek zásobníku volání. Data se shromažďují po určitou dobu a zobrazí v sestavě. Tato metoda profilování ukazuje kde ve výpočetně náročné aplikaci většina práce procesoru se provádí.  Získáte tak možnost se zaměřit na "kritické cestě" kde je vaše aplikace spotřebuje většinu času.

## <a name="1-configure-visual-studio-for-profiling"></a>1: konfigurace pro profilaci sady Visual Studio
Nejprve se pár možností konfigurace sady Visual Studio, které můžou být užitečné při profilování. Dávat smysl profilování sestavy, budete potřebovat symboly (soubory PDB) pro aplikace a také symboly pro systémové knihovny. Bude potřeba Ujistěte se, že odkazují na servery symbolů k dispozici. K tomu, na **nástroje** nabídku v sadě Visual Studio, zvolte **možnosti**, klikněte na tlačítko **ladění**, pak **symboly**. Ujistěte se, že servery symbolů společnosti Microsoft je uveden v části **Symbol umístění souborů (.pdb)**.  Je také možné odkazovat http://referencesource.microsoft.com/symbols, který může mít soubory dalších symbolů.

![Možnosti symbolů][4]

V případě potřeby můžete zjednodušit sestavy, které generuje profiler nastavením pouze můj kód. S povoleným kódem Just My jsou zjednodušené zásobníky volání funkce tak, aby volání zcela interní knihovny a rozhraní .NET Framework jsou skryté ze sestav. Na **nástroje** nabídce zvolte **možnosti**. Pak rozbalte **nástroje pro měření výkonu** uzel a zvolte **Obecné**. Zaškrtněte políčko pro **povolit volbu pouze vlastní kód pro sestavy profileru**.

![Pouze můj kód – možnosti][17]

Můžete použít tyto pokyny s existující projekt nebo s novým projektem.  Pokud vytvoříte nový projekt vyzkoušet postupy popsané níže, zvolte možnost jazyka C# **Azure Cloud Service** projektu a vyberte **webovou roli** a **Role pracovního procesu**.

![Role Azure projekt cloudové služby][5]

Účely, třeba přidat nějaký kód do projektu, který vyžaduje spoustu času a ukazuje některé zřejmé výkonu problém. Například následující kód přidejte projektu role pracovního procesu:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Volání tento kód v metodě RunAsync v roli pracovního procesu RoleEntryPoint odvozených tříd. (Ignorovat varování o metodu běží synchronně.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Sestavení a spuštění cloudové služby místně bez ladění (Ctrl + F5), se konfigurace řešení nastavená na **vydání**. Tím se zajistí, že všechny soubory a složky jsou vytvořeny pro spuštění aplikace místně a zajistí, že jsou spuštěné všechny emulátorech. Začněte Uživatelském prostředí emulátoru výpočtů z hlavního panelu ověřte, zda je spuštěna vaše role pracovního procesu.

## <a name="2-attach-to-a-process"></a>2: připojit k procesu
Místo profilování aplikace jeho zahájením z integrovaného vývojového prostředí Visual Studio 2010, je nutné připojit profiler ke spuštěnému procesu. 

Připojit profiler k procesu, na **analyzovat** nabídce zvolte **Profiler** a **Attach/Detach**.

![Připojit profile – možnost][6]

Pro roli pracovního procesu vyhledejte proces WaWorkerHost.exe.

![WaWorkerHost procesu][7]

Pokud složky vašeho projektu je na síťové jednotce, profiler vás vyzve k zadejte jiné umístění pro uložení sestavy profilování.

 Můžete také připojit k webové roli připojením k WaIISHost.exe.
Pokud existuje více procesů role pracovního procesu v aplikaci, budete muset použít processID abychom je odlišili. Můžete zadávat dotazy processID prostřednictvím kódu programu pomocí přístupu k objektu Process. Například pokud přidáte tento kód do metody Run RoleEntryPoint odvozené třídy v roli, najdete v protokolu v uživatelském prostředí emulátoru Compute vědět, jaký proces pro připojení k.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Chcete-li zobrazit v protokolu, spusťte Uživatelském prostředí emulátoru výpočtů.

![Spustit emulátor služby výpočty uživatelského rozhraní][8]

Otevřete okno konzoly protokol role pracovního procesu v uživatelském prostředí emulátoru Compute po kliknutí na záhlaví okna konzoly. Zobrazí se ID procesu v protokolu.

![ID procesu zobrazení][9]

Jeden, který jste připojili, proveďte kroky v uživatelském rozhraní aplikace (v případě potřeby) pro reprodukci scénář.

Pokud chcete profilaci zastavit, vyberte **zastavit profilaci** odkaz.

![Zastavit profilování možnost][10]

## <a name="3-view-performance-reports"></a>3: zobrazení sestav výkonu
Zobrazí se sestava výkonu pro vaši aplikaci.

V tomto okamžiku profiler zastaví provádění, uloží data do souboru .vsp a zobrazí zprávu, která uvádí analýzu těchto dat.

![Profiler sestavy][11]

Pokud se zobrazí String.wstrcpy v horké cesty, klikněte na volbu pouze vlastní kód změnit zobrazení na Zobrazit pouze kód uživatele.  Pokud se zobrazí String.Concat, zkuste stisknutím tlačítka Zobrazit veškerý kód.

Byste měli vidět Concatenate metoda a String.Concat zabírá tak velkou část času spuštění.

![Analýza sestavy][12]

Pokud jste přidali kód pro zřetězení řetězců v tomto článku, měli byste vidět upozornění v seznamu úkolů pro tuto. Může také zobrazit upozornění, že existuje nadměrné množství uvolněné paměti, což je vzhledem k počtu řetězce, které jsou vytvořeny a odstraněn.

![Upozornění výkonu][14]

## <a name="4-make-changes-and-compare-performance"></a>4: proveďte změny a porovnání výkonu
Můžete také porovnat výkon před a po změně kódu.  Zastavení spuštěného procesu a upravit kód nahraďte operace zřetězení řetězců pomocí StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Provedení jiný výkon a pak porovnat výkon. V prohlížeči výkonu, pokud spuštění jsou ve stejné relaci, vám stačí vyberte obě sestavy, otevřete místní nabídku a zvolte **porovnat sestavy výkonu**. Pokud chcete k porovnání s spuštěný v jiné relaci výkonu, otevřete **analyzovat** nabídky a zvolte **porovnat sestavy výkonu**. V dialogovém okně, které se zobrazí, zadejte oba soubory.

![Porovnat sestavy výkonu – možnost][15]

Sestavy popisují rozdíly mezi dvěma běhy.

![Sestava porovnání][16]

Blahopřejeme! Začnete s profilerem.

## <a name="troubleshooting"></a>Řešení potíží
* Ujistěte se, že provádíte profilaci sestavení pro vydání a spustit bez ladění.
* Pokud v nabídce Profiler není povolena možnost Attach/Detach, spusťte Průvodce výkonem.
* Chcete-li zobrazit stav vaší aplikace pomocí Uživatelském prostředí emulátoru výpočtů. 
* Pokud máte potíže se spuštěním aplikace v emulátoru nebo připojuje se profiler vypnout emulátor služby výpočty dolů a restartujte ji. Pokud se problém nevyřeší, zkuste restartovat. Tomuto problému může dojít, pokud pomocí emulátoru Compute můžete pozastavit a odebrat spuštěné nasazení.
* Pokud jste použili některý z příkazů profilace z příkazového řádku, zejména globální nastavení, ujistěte se, že byla volána VSPerfClrEnv /globaloff a že VsPerfMon.exe byl vypnut.
* Pokud při vzorkování, zobrazí se zpráva "PRF0025: Nebyla shromážděna žádná data," Zkontrolujte, jestli jste se připojili k procesu aktivity procesoru. Aplikace, které nejsou provádějící všechny výpočetní práce nemusí vracet žádné data vzorkování.  Je také možné, že proces byl ukončen před provedením jakékoli vzorkování. Zkontrolujte, že spuštění metody pro roli, která profilujete neukončí.

## <a name="next-steps"></a>Další kroky
Instrumentace Azure binárních souborů se spustila v emulátoru není podporován v profileru sady Visual Studio, ale pokud chcete otestovat přidělení paměti, můžete tuto možnost zvolte, pokud profilace. Můžete také zvolit profilace souběžnosti, který vám pomůže určit, zda jsou vlákna plýtvání čas soutěží o ceny zámky, nebo profilaci interakce vrstev pomáhá sledovat problémy s výkonem při interakci mezi vrstvami aplikace, které nejvíce často mezi datovou vrstvou a role pracovního procesu.  Můžete zobrazit databázové dotazy, které vaše aplikace generuje a profilování údaje použít ke zkvalitnění používání databáze. Informace o profilování interakce vrstev, najdete v příspěvku blogu [návod: použití Profiler interakce vrstvy v aplikaci Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
