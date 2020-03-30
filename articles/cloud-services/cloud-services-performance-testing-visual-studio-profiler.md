---
title: Místní profilování cloudové služby v emulátoru výpočetního prostředí | Dokumenty společnosti Microsoft
services: cloud-services
description: Prozkoumejte problémy s výkonem v cloudových službách pomocí profileru Visual Studia
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 21270d3c7143ce063ffe30d939368b9813e9072e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70094109"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testování výkonu cloudové služby místně v emulátoru výpočetního prostředí Azure pomocí profileru Visual Studio
Pro testování výkonu cloudových služeb jsou k dispozici různé nástroje a techniky.
Když publikujete cloudovou službu do Azure, můžete nechat Visual Studio shromažďovat data profilování a pak je analyzovat místně, jak je popsáno v [profilování aplikace Azure][1].
Diagnostiku můžete také použít ke sledování různých čítačů výkonu, jak je popsáno v [části Použití čítačů výkonu v Azure][2].
Můžete také chtít profil ovat aplikaci místně v emulátoru výpočetního prostředí před nasazením do cloudu.

Tento článek popisuje metodu vzorkování procesoru profilování, které lze provést místně v emulátoru. Vzorkování procesoru je metoda profilování, která není příliš rušivá. V určeném intervalu vzorkování profileru pořídí snímek zásobníku volání. Data jsou shromažďována po určitou dobu a zobrazena v sestavě. Tato metoda profilování inklinuje k označení, kde ve výpočtově náročné aplikace většina práce procesoru se provádí.  To vám dává možnost zaměřit se na "horké cesty", kde vaše aplikace tráví nejvíce času.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurace sady Visual Studio pro profilování
Nejprve existuje několik možností konfigurace sady Visual Studio, které mohou být užitečné při profilování. Chcete-li pochopit profilování sestavy, budete potřebovat symboly (.pdb soubory) pro vaši aplikaci a také symboly pro systémové knihovny. Budete chtít, aby se ujistil, že odkaz na dostupné symbol servery. Chcete-li to provést, v nabídce **Nástroje** v sadě Visual Studio zvolte **Možnosti**, pak zvolte **Ladění**a potom **Symboly**. Zkontrolujte, zda je seznam serverů Microsoft Symbol Servers v seznamu **Umístění souboru symbolů (.pdb).**  Můžete také https://referencesource.microsoft.com/symbolsodkazovat , které mohou mít další soubory symbolů.

![Volby symbolu][4]

V případě potřeby můžete zjednodušit sestavy, které generuje profiler nastavením pouze můj kód. Pokud je povolen pouze můj kód, jsou zásobníky volání funkcí zjednodušeny tak, aby volání zcela interní knihovny a rozhraní .NET Framework jsou skryty ze sestav. V nabídce **Nástroje** zvolte **Možnosti**. Potom rozbalte uzel **Nástroje výkonu** a zvolte **Obecné**. Zaškrtněte políčko **Povolit pouze můj kód pro sestavy profileru**.

![Možnosti pouze můj kód][17]

Tyto pokyny můžete použít s existujícím projektem nebo s novým projektem.  Pokud vytvoříte nový projekt vyzkoušet níže popsané techniky, zvolte c# **Azure Cloud Service** projektu a vyberte **webovou roli** a roli **pracovního procesu**.

![Role projektu Cloudové služby Azure][5]

Například pro účely, přidejte nějaký kód do projektu, který trvá hodně času a ukazuje některé zjevné problémy s výkonem. Do projektu role pracovníka například přidejte následující kód:

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

Volání tohoto kódu z metody RunAsync v třídě odvozené od odvozenou role role pracovního procesu. (Ignorujte upozornění na metodu spuštěnou synchronně.)

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

Vytvořte a spusťte cloudovou službu místně bez ladění (Ctrl+F5) s konfigurací řešení nastavenou na **release**. Tím je zajištěno, že všechny soubory a složky jsou vytvořeny pro spuštění aplikace místně a zajišťuje, že jsou spuštěny všechny emulátory. Spusťte rozhraní emulátoru výpočetního prostředí z hlavního panelu a ověřte, že role pracovního procesu je spuštěná.

## <a name="2-attach-to-a-process"></a>2: Připojení k procesu
Namísto profilování aplikace spuštěním z ide sady Visual Studio 2010 je nutné připojit profiler ke spuštěnému procesu. 

Chcete-li připojit profiler k procesu, v nabídce **Analyzovat** zvolte **Profiler** a **Připojit/Odpojit**.

![Připojit profil, volba][6]

Pro roli pracovního procesu vyhledejte proces WaWorkerHost.exe.

![Proces WaWorkerHost][7]

Pokud je složka projektu na síťové jednotce, profiler vás požádá o poskytnutí jiného umístění pro uložení sestav profilování.

 Můžete také připojit k webové roli připojením k WaIISHost.exe.
Pokud existuje více procesů role pracovní ho v aplikaci, je třeba použít processID k jejich rozlišení. Můžete dotaz processID programově přístup Process objektu. Například pokud přidáte tento kód run metoda RoleEntryPoint odvozené třídy v roli, můžete se podívat na protokol v umělá součást imupátoru výpočetního prostředí vědět, jaký proces se připojit k.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Chcete-li zobrazit protokol, spusťte ui compute emulátor.

![Spuštění hlavního nastavení výpočetního emulátoru][8]

Open the worker role log console window in the Compute Emulator UI by clicking on the console window's title bar. ID procesu se zobrazí v protokolu.

![Zobrazení ID procesu][9]

Jeden, který jste připojili, proveďte kroky v ui aplikace (v případě potřeby) k reprodukci scénáře.

Chcete-li profilování ukončit, zvolte odkaz **Zastavit profilování.**

![Zastavit profilování, volba][10]

## <a name="3-view-performance-reports"></a>3: Zobrazení zpráv o výkonu
Zobrazí se zpráva o výkonu aplikace.

V tomto okamžiku profiler zastaví provádění, uloží data do souboru .vsp a zobrazí sestavu, která zobrazuje analýzu těchto dat.

![Sestava profileru][11]

Pokud se v aktivní cestě zobrazí soubor String.wstrcpy, kliknutím na možnost Jen můj kód změňte zobrazení tak, aby zobrazovalo pouze uživatelský kód.  Pokud vidíte String.Concat, zkuste stisknout tlačítko Zobrazit vše kód.

Měli byste vidět Concatenate metoda a String.Concat zabírají velkou část doby provádění.

![Analýza zprávy][12]

Pokud jste v tomto článku přidali kód zřetězení řetězců, mělo by se zobrazit upozornění v seznamu úkolů. Může se také zobrazit upozornění, že existuje nadměrné množství uvolňování paměti, což je způsobeno počtem řetězců, které jsou vytvořeny a uvolněny.

![Upozornění výkonu][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Provádět změny a porovnávat výkon
Můžete také porovnat výkon před a po změně kódu.  Zastavte spuštěný proces a upravte kód, který nahradí operaci zřetězení řetězce pomocí StringBuilder:

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

Proveďte další spuštění výkonu a porovnejte výkon. Pokud jsou spuštěny v Průzkumníku výkonu ve stejné relaci, stačí vybrat obě sestavy, otevřít místní nabídku a zvolit **Porovnat sestavy výkonu**. Pokud chcete porovnat s běh v jiné relaci výkonu, otevřete nabídku **Analyzovat** a zvolte **Porovnat zprávy o výkonu**. Určete oba soubory v zobrazeném dialogovém okně.

![Možnost Porovnat sestavy výkonu][15]

Zprávy zvýrazňují rozdíly mezi těmito dvěma spuštěními.

![Srovnávací sestava][16]

Blahopřejeme! Začal jsi s profilerem.

## <a name="troubleshooting"></a>Řešení potíží
* Ujistěte se, že profilování sestavení verze a začít bez ladění.
* Pokud není v nabídce Profiler povolena možnost Připojit nebo odpojit, spusťte Průvodce výkonem.
* Pomocí uhlavního nastavení výpočetního emulátoru zobrazte stav aplikace. 
* Pokud máte problémy se spouštěním aplikací v emulátoru nebo připojením profileru, vypněte výpočetní emulátor a restartujte jej. Pokud to problém nevyřeší, zkuste restartovat počítač. K tomuto problému může dojít, pokud používáte výpočetní emulátor pozastavit a odebrat spuštěná nasazení.
* Pokud jste použili některý z příkazů profilování z příkazového řádku, zejména globální nastavení, ujistěte se, že VSPerfClrEnv /globaloff byla volána a že VsPerfMon.exe byla ukončena.
* Pokud při vzorkování, zobrazí se zpráva "PRF0025: Nebyla shromážděna žádná data", zkontrolujte, zda proces, který jste připojili k má aktivitu procesoru. Aplikace, které neprovádějí žádnou výpočetní práci, nemusí vytvářet žádná data vzorkování.  Je také možné, že proces byl ukončen před odběrvzorků bylo provedeno. Zkontrolujte, zda metoda Run pro roli, kterou profilujete, nekončí.

## <a name="next-steps"></a>Další kroky
Instrumentace binárních souborů Azure v emulátoru není v profileru Sady Visual Studio podporována, ale pokud chcete otestovat přidělení paměti, můžete tuto možnost zvolit při profilování. Můžete také zvolit profilování souběžnosti, které vám pomůže určit, zda vlákna plýtvají časem konkurenčními o zámky, nebo profilování interakce vrstvy, což vám pomůže sledovat problémy s výkonem při interakci mezi vrstvami aplikace, většina často mezi datovou vrstvou a rolí pracovního procesu.  Můžete zobrazit databázové dotazy, které vaše aplikace generuje, a použít data profilování ke zlepšení vašeho používání databáze. Informace o profilování interakce na úrovni najdete v návodu k příspěvku [blogu: Pomocí profileru interakce úrovně v systému Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
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
