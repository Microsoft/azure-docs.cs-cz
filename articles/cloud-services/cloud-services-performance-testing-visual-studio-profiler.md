---
title: Profilace cloudové služby místně v emulátoru služby výpočty v | Microsoft Docs
services: cloud-services
description: Prozkoumat problémy s výkonem v cloudové služby Visual Studio profileru
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
ms.openlocfilehash: 8ff7b88a3086488ab669288687c274237ca30b47
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testování výkonu cloudové služby místně v emulátor výpočtů v Azure pomocí sady Visual Studio profileru
K dispozici pro testování výkonu cloudové služby jsou různé nástroje a techniky.
Při publikování Cloudová služba Azure, můžete v aplikaci Visual Studio shromažďovat data profilování a potom analyzovat místně, jak je popsáno v [profilace aplikace Azure][1].
Můžete také použít diagnostiky sledovat celou řadu čítače výkonu, jak je popsáno v [pomocí čítačů výkonu v Azure][2].
Také můžete chtít profil aplikace místně v emulátoru služby výpočty v ještě před nasazením do cloudu.

Tento článek se věnuje metodě profilování pomocí vzorkování procesoru, která se dá dělat místně v emulátoru. Vzorkování procesoru je, že metoda profilace, který není velmi nežádoucí. V intervalu určené vzorkování profileru pořídí snímek zásobníku volání. Data jsou shromažďovány prostřednictvím v časovém intervalu a zobrazí v sestavě. Tato metoda profilace se obvykle označuje, kde výpočetně náročné aplikace se právě provádí většinu práce procesoru.  To vám dává příležitost a zaměřit se na "aktivní cesta" kde vaše aplikace je výdaje nejvíce času.

## <a name="1-configure-visual-studio-for-profiling"></a>1: konfigurace Visual Studia pro profilaci
Existují nejprve pár možností konfigurace sady Visual Studio, které mohou být užitečné při vytváření profilu. Chcete-li smysl sestav profilování, budete potřebovat symboly (soubory PDB) pro vaše aplikace a také symboly pro knihovny systému. Budete chtít Ujistěte se, zda odkazujete servery k dispozici symbolů. To uděláte, na **nástroje** ve Visual Studiu zvolte v nabídce zvolte **možnosti**, zvolte **ladění**, pak **symboly**. Ujistěte se, že servery symbolů Microsoft je uveden v části **symbolů umístění souborů (.pdb)**.  Můžete taky odkazovat http://referencesource.microsoft.com/symbols, což může mít další symbol soubory.

![Možnosti – symbol][4]

V případě potřeby můžete zjednodušit sestavy, které generuje profileru nastavením pouze můj kód. S pouze můj kód povolena jsou zjednodušené zásobníky volání funkce tak, aby se ze sestav skrytá volání zcela interní knihovny a rozhraní .NET Framework. Na **nástroje** nabídky, zvolte **možnosti**. Potom rozbalte **nástroje pro sledování výkonu** uzel a zvolte **Obecné**. Zaškrtněte políčko **povolit volbu pouze vlastní kód pro sestav profileru**.

![Pouze můj kód možnosti][17]

Tyto pokyny můžete použít existující projekt nebo s nový projekt.  Pokud vytvoříte nový projekt můžete vyzkoušet níže popsané postupy, zvolte C# **Azure Cloud Service** projektu a vyberte **webovou roli** a **Role pracovního procesu**.

![Role projektu Azure Cloud Service][5]

Pro účely, například přidat nějaký kód do projektu, který přebírá mnoho času a ukazuje některé zřejmé výkonu problém. Například přidejte následující kód do projektu role pracovního procesu:

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

Tento kód volejte z metodě RunAsync v roli pracovního procesu RoleEntryPoint odvozené třídy. (Ignorovat upozornění o metodě synchronně spuštěná.)

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

Sestavení a spuštění cloudové služby místně bez ladění (Ctrl + F5), s konfigurací řešení nastavena na **verze**. To zajišťuje, že všechny soubory a složky jsou vytvořeny pro spuštění aplikace místně a zajistí, že jsou spuštěné všechny emulátorů. Ověřte, zda je spuštěna své role pracovního procesu začít uživatelské prostředí emulátoru výpočtů z hlavního panelu.

## <a name="2-attach-to-a-process"></a>2: připojení k procesu
Místo profilace aplikace pomocí spuštění z prostředí Visual Studio 2010 IDE, musí připojení profileru k spuštěných procesů. 

K připojení profileru k procesu, na **analyzovat** nabídce zvolte **profileru** a **Attach/Detach**.

![Připojte profile – možnost][6]

Pro roli pracovního procesu najít WaWorkerHost.exe proces.

![Proces WaWorkerHost][7]

Pokud složky projektu na síťové jednotce, požádá profileru zadat jiné umístění pro uložení sestav profilování.

 Můžete se také připojit k webové role připojením k WaIISHost.exe.
Pokud existují více procesů role v aplikaci, budete muset použít processID rozlište je. Prostřednictvím kódu programu můžete dotazovat processID díky přístupu k objekt procesu. Například tento kód vložte do metoda Run RoleEntryPoint odvozené třídy v roli, můžete zobrazit v protokolu v uživatelské prostředí emulátoru výpočtů vědět, co proces připojení k.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Chcete-li zobrazit protokol, spustíte uživatelské prostředí emulátoru výpočtů.

![Spustit emulátoru služby výpočty uživatelského rozhraní][8]

Otevřete v okně konzoly protokolu role pracovního procesu v uživatelské prostředí emulátoru výpočtů kliknutím na záhlaví okna konzoly. Zobrazí ID procesu v protokolu.

![ID procesu zobrazení][9]

Jeden, který jste připojili, proveďte kroky v uživatelském rozhraní aplikace (v případě potřeby) pro reprodukci scénáři.

Pokud chcete zastavit profilování, vyberte **zastavit profilování** odkaz.

![Zastavit profilování možnost][10]

## <a name="3-view-performance-reports"></a>3: Zobrazit sestavy pro zvýšení výkonu
Zobrazí se sestava výkonu pro vaši aplikaci.

V tomto okamžiku profileru zastaví provádění, uloží data v souboru .vsp a zobrazí sestavu, která obsahuje analýzu tato data.

![Sestav profileru][11]

Pokud se zobrazí String.wstrcpy v aktivní trase, klikněte na volbu pouze vlastní kód změníte zobrazení. Chcete-li zobrazit pouze uživatele kód.  Pokud se zobrazí String.Concat, zkuste kliknutím na tlačítko Zobrazit všechny kódu.

Měli byste vidět zřetězení metoda a String.Concat zabírají velkou část čas provádění.

![Analýzy zprávy][12]

Pokud jste přidali kód zřetězení řetězec v tomto článku, měli byste vidět upozornění v seznamu úkolů pro tuto. Může se také zobrazit upozornění, že je nadměrné množství uvolňování paměti, která je kvůli velkému počtu řetězce, které jsou vytvořeny a zlikvidován.

![Upozornění výkonu][14]

## <a name="4-make-changes-and-compare-performance"></a>4: proveďte změny a porovnání výkonu
Také můžete porovnat výkon před a po změně kódu.  Zastavení spuštěných procesů a upravit kód, který nahradí operace zřetězení řetězců pomocí StringBuilder:

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

Nezadávejte jiné výkonu spustit a potom porovnejte výkon. V Průzkumníku výkonu, pokud je spuštěn jsou ve stejné relaci, můžete právě vybrat obě tyto sestavy, otevřete místní nabídky a zvolte **porovnat sestavy pro zvýšení výkonu**. Pokud chcete porovnat s spuštěný v jiné relaci výkonu, otevřete **analyzovat** nabídce a zvolte **porovnat sestavy pro zvýšení výkonu**. Zadejte oba soubory v zobrazeném dialogu.

![Porovnání výkonu sestavy možnost][15]

Sestavy zvýrazněte rozdíly mezi dvěma spustí.

![Sestavy porovnání][16]

Blahopřejeme! Začnete profileru.

## <a name="troubleshooting"></a>Řešení potíží
* Zkontrolujte, zda jsou profilace sestavení pro vydání a spustit bez ladění.
* Pokud je možnost Attach/Detach není povoleno v nabídce profileru, spusťte Průvodce výkonu.
* Uživatelské prostředí emulátoru výpočtů slouží k zobrazení stavu aplikace. 
* Pokud máte potíže se spuštěním aplikace v emulátoru, nebo se připojení profileru, vypnout dolů emulátoru služby výpočty v a restartujte ji. Pokud se problém nevyřeší, zkuste restartování. Tomuto problému může dojít, pokud používáte emulátor výpočetní pozastavení a odebrat spuštěné nasazení.
* Pokud jste použili žádné příkazy profilování z příkazového řádku, zejména globální nastavení, ujistěte se, že byla volána /globaloff vsperfclrenv – a že VsPerfMon.exe byla vypnuta.
* Pokud při vzorkování, zobrazí se zpráva "PRF0025: Nebyla shromážděna žádná data," Zkontrolujte, jestli proces, který jste připojili k procesoru aktivitu. Aplikace, které nejsou provádění veškerou práci, výpočetní nemusí vracet žádné data vzorkování.  Je také možné, že proces byl ukončen před provedením jakékoli vzorkování. Zkontrolujte, že metoda Run pro roli, která jsou profilace neskončí.

## <a name="next-steps"></a>Další kroky
Instrumentace Azure binárních souborů v emulátoru není podporována v sadě Visual Studio profiler, ale pokud chcete testovat přidělení paměti, můžete tuto možnost, při vytváření profilu. Můžete také profilace souběžného zpracování, který vám pomůže určit, zda jsou vláken plýtvání časem neslučitelných pro zámky nebo profilace interakce vrstvy, které umožňuje sledovat problémy s výkonem při interakci mezi vrstvami aplikace nejčastěji mezi datovou vrstvou a roli pracovního procesu.  Můžete zobrazit dotazy databáze, které vaše aplikace generuje a využívat data profilování ke zlepšování používání databáze. Informace o vytváření profilů interakce vrstvy, naleznete v příspěvku blogu [návod: použití profileru interakce vrstvy v sadě Visual Studio Team System 2010][3].

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
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
