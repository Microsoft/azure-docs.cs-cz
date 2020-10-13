---
title: Místní profilování cloudové služby v emulátoru služby COMPUTE | Microsoft Docs
services: cloud-services
description: Vyšetřete problémy s výkonem v cloudových službách pomocí profileru sady Visual Studio
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
ms.openlocfilehash: 503b1ca8c8f1ff06bbdd54316611d886fb1e9d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462305"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Místní testování výkonu cloudové služby v emulátoru služby COMPUTE Azure pomocí profileru sady Visual Studio
K dispozici je celá řada nástrojů a technik pro testování výkonu Cloud Services.
Když publikujete cloudovou službu do Azure, můžete mít Visual Studio shromažďovat data profilace a pak je analyzovat místně, jak je popsáno v [profilování aplikace Azure][1].
Pomocí diagnostiky můžete také sledovat nejrůznější čítače výkonu, jak je popsáno v tématu [použití čítačů výkonu v Azure][2].
Můžete také chtít profilovat aplikaci místně v emulátoru služby COMPUTE, než ji nasadíte do cloudu.

Tento článek se věnuje metodě profilování vzorkování procesoru, která se dá provést místně v emulátoru. Vzorkování procesoru je metoda profilování, která není velmi rušivá. V rámci určeného intervalu vzorkování vybere Profiler snímek zásobníku volání. Data se shromažďují v časovém intervalu a zobrazují se v sestavě. Tato metoda profilace představuje v úmyslu označovat, kde je většina práce s PROCESORem náročná na výpočetní výkon.  Díky tomu se můžete soustředit na "horkou cestu", kde je vaše aplikace nejvíc vytráví.

## <a name="1-configure-visual-studio-for-profiling"></a>1: konfigurace sady Visual Studio pro profilaci
Nejdřív je několik možností konfigurace sady Visual Studio, které mohou být užitečné při profilaci. Chcete-li mít smysl sestav profilace, budete potřebovat symboly (soubory PDB) pro vaši aplikaci a také symboly pro systémové knihovny. Budete chtít, abyste se ujistili, že budete odkazovat na dostupné servery symbolů. To provedete tak, že v nabídce **nástroje** v aplikaci Visual Studio kliknete na možnost **Možnosti**a pak na položku **ladění**a **symboly**. Ujistěte se, že jsou servery symbolů společnosti Microsoft uvedeny v části **umístění souborů symbolů (. pdb)**.  Můžete také odkazovat https://referencesource.microsoft.com/symbols na, které mohou mít další soubory symbolů.

![Možnosti symbolu][4]

V případě potřeby můžete zjednodušit sestavy vygenerované profilerem nastavením Pouze můj kód. Když je povoleno Pouze můj kód, zásobníky volání funkce jsou zjednodušeny, aby se v sestavách zavolaly výhradně interní knihovny a .NET Framework jsou skryté. V nabídce **nástroje** klikněte na příkaz **Možnosti**. Pak rozbalte uzel **nástroje výkonu** a zvolte **Obecné**. Zaškrtněte políčko **povolit pouze můj kód pro sestavy profileru**.

![Možnosti Pouze můj kód][17]

Tyto pokyny můžete použít spolu s existujícím projektem nebo s novým projektem.  Pokud vytvoříte nový projekt k vyzkoušení níže popsaných postupů, zvolte projekt **cloudové služby Azure** v C# a vyberte **webovou roli** a **roli pracovního procesu**.

![Role projektu cloudové služby Azure][5]

Pro účely tohoto příkladu přidejte do projektu nějaký kód, který vybere spoustu času a demonstruje nějaký zjevné problémy s výkonem. Přidejte například následující kód do projektu role pracovního procesu:

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

Tento kód zavolejte z metody RunAsync v odvozené třídě role pracovního procesu RoleEntryPoint. (Ignorovat upozornění na metodu spuštěnou synchronně.)

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

Sestavte a spusťte cloudovou službu místně bez ladění (CTRL + F5) s konfigurací řešení nastavenou na **release**. Tím se zajistí, že se všechny soubory a složky vytvoří pro místní spuštění aplikace, a zajistí se, že se spustí všechny emulátory. Spusťte z hlavního panelu uživatelské rozhraní emulátoru výpočtů a ověřte, jestli je spuštěná vaše role pracovního procesu.

## <a name="2-attach-to-a-process"></a>2: připojení k procesu
Namísto profilování aplikace spuštěním z rozhraní IDE sady Visual Studio 2010 je nutné připojit profiler ke spuštěnému procesu. 

K připojení profileru k procesu klikněte v nabídce **analyzovat** na možnost **Profiler** a **připojit nebo odpojit**.

![Možnost připojení profilu][6]

V případě role pracovního procesu Najděte WaWorkerHost.exe procesu.

![WaWorkerHost proces][7]

Pokud je složka projektu na síťové jednotce, Profiler vás vyzve k zadání jiného umístění pro uložení sestav profilace.

 K webové roli se můžete připojit taky tak, že se připojíte k WaIISHost.exe.
Pokud je v aplikaci více procesů role pracovního procesu, je nutné použít rozhraní processID k jejich rozlišení. Můžete zadat dotaz na processID programově pomocí přístupu k objektu procesu. Například pokud přidáte tento kód do metody Run třídy odvozené od RoleEntryPoint v roli, můžete si prohlédnout protokol v uživatelském rozhraní emulátoru COMPUTE a zjistit, ke kterému procesu se chcete připojit.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Chcete-li zobrazit protokol, spusťte uživatelské rozhraní emulátoru výpočtů.

![Spuštění uživatelského rozhraní emulátoru COMPUTE][8]

Kliknutím na záhlaví okna konzoly v uživatelském rozhraní emulátoru výpočtů otevřete okno konzoly protokolu role pracovního procesu. V protokolu můžete zobrazit ID procesu.

![Zobrazit ID procesu][9]

K tomu, abyste mohli scénář reprodukováni, proveďte kroky v uživatelském rozhraní vaší aplikace (v případě potřeby).

Pokud chcete profilaci zastavit, klikněte na odkaz **zastavit profilaci** .

![Zastavit možnost profilace][10]

## <a name="3-view-performance-reports"></a>3: zobrazení sestav výkonu
Zobrazí se sestava výkonu aplikace.

V tomto okamžiku Profiler zastaví provádění, uloží data do souboru. vsp a zobrazí sestavu, která zobrazuje analýzu těchto dat.

![Sestava profileru][11]

Pokud se zobrazí řetězec. wstrcpy v kritické cestě, klikněte na Pouze můj kód a změňte zobrazení tak, aby se zobrazilo pouze kód uživatele.  Pokud se zobrazí řetězec. Concat, zkuste stisknout tlačítko Zobrazit veškerý kód.

Měla by se zobrazit metoda zřetězení a řetězec. Concat zabírá velkou část doby spuštění.

![Analýza sestavy][12]

Pokud jste přidali kód pro zřetězení řetězců v tomto článku, mělo by se zobrazit upozornění v Seznam úkolů. Může se zobrazit také upozornění, že existuje nadměrné množství uvolňování paměti, které je způsobeno počtem vytvořených a uvolněných řetězců.

![Upozornění výkonu][14]

## <a name="4-make-changes-and-compare-performance"></a>4: provedení změn a porovnání výkonu
Můžete také porovnat výkon před a po změně kódu.  Zastavte běžící proces a upravte kód tak, aby nahradil operaci zřetězení řetězce s použitím StringBuilder:

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

Spusťte jiný výkon a pak porovnejte výkon. V Prohlížeč výkonu, pokud jsou běhy ve stejné relaci, stačí vybrat obě sestavy, otevřít místní nabídku a zvolit **Porovnat sestavy výkonu**. Pokud chcete porovnat s běhm v jiné relaci výkonu, otevřete nabídku **analyzovat** a vyberte možnost **Porovnat sestavy výkonu**. V dialogovém okně, které se zobrazí, zadejte oba soubory.

![Compare – možnost sestav výkonu][15]

Sestavy zvýrazňují rozdíly mezi dvěma spuštěními.

![Sestava porovnání][16]

Gratulujeme! V profileru jste začali pracovat.

## <a name="troubleshooting"></a>Řešení potíží
* Nezapomeňte profilovat sestavení pro vydání a spustit bez ladění.
* Pokud v nabídce profileru není povolená možnost připojit/odpojit, spusťte Průvodce výkonem.
* K zobrazení stavu aplikace použijte uživatelské rozhraní emulátoru Compute. 
* Pokud máte problémy se spouštěním aplikací v emulátoru nebo připojením profileru, vypněte emulátor COMPUTE a restartujte ho. Pokud se tím problém nevyřeší, zkuste restartovat. K tomuto problému může dojít, pokud použijete emulátor služby COMPUTE k pozastavení a odebrání spuštěných nasazení.
* Pokud jste použili některý z příkazů profilace z příkazového řádku, zejména globální nastavení, zajistěte, aby byla zavolána metoda VSPerfClrEnv/globaloff a VsPerfMon.exe byla ukončena.
* Pokud se při vzorkování zobrazí zpráva "PRF0025: nebyla shromážděna žádná data", zkontrolujte, že proces, ke kterému jste připojeni, má aktivitu CPU. Aplikace, které nemají žádnou výpočetní práci, nemusí vydávat žádná data vzorkování.  Je také možné, že byl proces ukončen před dokončením vzorkování. Zkontrolujte, že metoda Run pro roli, kterou vytváříte, nekončí.

## <a name="next-steps"></a>Další kroky
Instrumentace binárních souborů Azure v emulátoru není v profileru sady Visual Studio podporována, ale pokud chcete otestovat přidělení paměti, můžete zvolit tuto možnost při profilaci. Můžete také zvolit profilaci souběžného zpracování, která vám pomůže určit, zda vlákna mají ztrácet čas nebo profilaci interakce vrstev, což vám pomůže sledovat problémy s výkonem při interakci mezi úrovněmi aplikace, nejčastěji mezi datovou vrstvou a rolí pracovního procesu.  Můžete zobrazit databázové dotazy, které vaše aplikace vygeneruje, a použít data profilace ke zlepšení využití databáze. Informace o profilování interakce vrstev najdete v návodu na blogovém příspěvku [: použití profileru interakce vrstev v aplikaci Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://docs.microsoft.com/archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
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
