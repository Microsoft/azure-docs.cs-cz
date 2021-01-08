---
title: Vývoj funkcí .NET Standard pro úlohy Azure Stream Analytics (Preview)
description: Naučte se psát uživatelsky definované funkce v jazyce C# pro úlohy Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/10/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 52fa6f05db5452a2e7b8ec4f93d65525873c8c7e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020566"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics (Preview)

Azure Stream Analytics nabízí dotazovací jazyk podobný SQL pro provádění transformací a výpočtů přes streamy dat událostí. Existuje mnoho vestavěných funkcí, ale některé složité scénáře vyžadují větší flexibilitu. S .NET Standard uživatelsky definovaných funkcí (UDF) můžete vyvolat vlastní funkce napsané v jakémkoli jazyce .NET Standard (C#, F # atd.) a rozšíříte tak Stream Analytics dotazovací jazyk. UDF vám umožní provádět komplexní matematické výpočty, importovat vlastní ML modely pomocí ML.NET a použít vlastní logiku imputace pro chybějící data. Funkce UDF pro úlohy Stream Analytics je momentálně ve verzi Preview a neměla by se používat v produkčních úlohách.

Uživatelem definovaná funkce .NET pro cloudové úlohy je dostupná v:
* USA – středozápad
* Severní Evropa
* East US
* USA – západ
* USA – východ 2
* West Europe

Pokud vás zajímá použití této funkce v jakékoli jiné oblasti, můžete [požádat o přístup](https://aka.ms/ccodereqregion).

## <a name="package-path"></a>Cesta k balíčku

Formát libovolného balíčku UDF má cestu `/UserCustomCode/CLR/*` . Knihovny DLL (Dynamic Link Library) a prostředky se zkopírují do `/UserCustomCode/CLR/*` složky, která pomáhá izolovat knihovny DLL uživatelů ze systémových a Azure Stream Analytics knihoven DLL. Tato cesta k balíčku se používá pro všechny funkce bez ohledu na metodu použitou k jejich využití.

## <a name="supported-types-and-mapping"></a>Podporované typy a mapování

Pro Azure Stream Analytics hodnoty, které se mají použít v jazyce C#, je nutné je zařadit z jednoho prostředí do druhého. Zařazování se stane pro všechny vstupní parametry UDF. Každý typ Azure Stream Analytics má odpovídající typ v jazyce C# zobrazený v následující tabulce:

|**Typ Azure Stream Analytics** |**Typ C#** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar (max) | řetězec |
|datetime | DateTime |
|Záznam | Slovník\<string, object> |
|Pole | Objekt [] |

Totéž platí i v případě, že je potřeba zařadit data z C# do Azure Stream Analytics, což se stane výstupní hodnotou UDF. Následující tabulka uvádí podporované typy:

|**Typ C#**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|řetězec  |  nvarchar (max)   |
|DateTime  |  data a času.   |
|struct  |  Záznam   |
|object  |  Záznam   |
|Objekt []  |  Pole   |
|Slovník\<string, object>  |  Záznam   |

## <a name="develop-a-udf-in-visual-studio-code"></a>Vývoj systému souborů UDF v Visual Studio Code

[Visual Studio Code nástroje pro Azure Stream Analytics](quick-create-visual-studio-code.md) usnadňují psaní UDF, testování vašich úloh místně (dokonce i offline) a publikování Stream Analytics úlohy do Azure.

Existují dva způsoby, jak implementovat .NET Standard UDF v nástrojích Visual Studio Code.

* Systém souborů UDF z místních knihoven DLL
* Systém souborů UDF z místního projektu

### <a name="local-project"></a>Místní projekt

Uživatelsky definované funkce lze zapsat do sestavení, které je později odkazováno v Azure Stream Analytics dotazu. Toto je doporučená možnost pro komplexní funkce, které vyžadují celou sílu .NET Standardho jazyka nad rámec jeho jazyka, jako je například Procedurální logika nebo rekurze. UDF z místního projektu se může používat i v případě, že potřebujete sdílet logiku funkce v několika Azure Stream Analyticsch dotazech. Přidáním UDF do svého místního projektu získáte možnost ladit a testovat funkce místně.

Postup při odkazování na místní projekt:

1. Na místním počítači vytvořte novou knihovnu tříd .NET Standard.
2. Napište kód do vaší třídy. Pamatujte, že třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*.
3. Přidejte nový konfigurační soubor funkce CSharp do projektu Azure Stream Analytics a odkazujte na projekt knihovny tříd CSharp.
4. Nakonfigurujte cestu sestavení v konfiguračním souboru úlohy, v `JobConfig.json` oddílu **CustomCodeStorage** . Tento krok není nutný pro místní testování.

### <a name="local-dlls"></a>Místní knihovny DLL

Můžete také odkazovat na místní knihovny DLL, které obsahují funkce definované uživatelem.

### <a name="example"></a>Příklad

V tomto příkladu je **CSharpUDFProject** projekt knihovny tříd jazyka C# a **ASAUDFDemo** je projekt Azure Stream Analytics, který bude odkazovat **CSharpUDFProject**.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-demo.png" alt-text="Azure Stream Analytics projektu v Visual Studio Code":::

Následující UDF má funkci, která vynásobí celé číslo sám sebou, aby vygenerovala čtverec z celého čísla. Třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*.

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

Následující kroky ukazují, jak přidat funkci C# UDF do projektu Stream Analytics.

1. Klikněte pravým tlačítkem na složku **Functions** a vyberte **Přidat položku**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function.png" alt-text="Přidat novou funkci v Azure Stream Analytics projektu":::

2. Přidejte funkci jazyka C# **SquareFunction** do projektu Azure Stream Analytics.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-2.png" alt-text="Vyberte funkci CSharp z Stream Analytics projektu v VS Code":::

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-name.png" alt-text="Do VS Code zadejte název funkce CSharp":::

3. V konfiguraci funkce jazyka C# vyberte možnost **zvolit cestu projektu knihovny** a zvolte projekt C# v rozevíracím seznamu a vyberte **sestavit projekt** pro sestavení projektu. Pak zvolte **Vybrat třídu** a **Vybrat metodu** a vyberte v rozevíracím seznamu související třídu a název metody. Chcete-li odkazovat na metody, typy a funkce v dotazu Stream Analytics, třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-project.png" alt-text="Stream Analytics konfigurace funkce jazyka C pro ostrý VS Code":::

    Pokud chcete použít jazyk C# UDF z knihovny DLL, vyberte možnost **zvolit cestu knihoven DLL knihovny** a zvolte knihovnu DLL. Pak zvolte **Vybrat třídu** a **Vybrat metodu** a vyberte v rozevíracím seznamu související třídu a název metody.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-dll.png" alt-text="Stream Analytics konfigurace ostře funkcí jazyka C":::

4. Ve svém dotazu Azure Stream Analytics vyvolat UDF.

   ```sql
    SELECT price, udf.SquareFunction(price)
    INTO Output
    FROM Input 
   ```

5. Před odesláním úlohy do Azure nakonfigurujte cestu k balíčku v konfiguračním souboru úlohy, v `JobConfig.json` oddílu **CustomCodeStorage** . Pomocí **volby vybrat z předplatného** v CodeLens zvolte předplatné a v rozevíracím seznamu zvolte účet úložiště a název kontejneru. Ponechte **cestu** jako výchozí. Tento krok není nutný pro místní testování.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-configure-storage-account.png" alt-text="Zvolit cestu k knihovně":::

## <a name="develop-a-udf-in-visual-studio"></a>Vývoj systému souborů UDF v aplikaci Visual Studio

Existují tři způsoby, jak implementovat UDF v nástrojích sady Visual Studio.

* Soubory CodeBehind v projektu ASA
* Systém souborů UDF z místního projektu
* Existující balíček z účtu služby Azure Storage

### <a name="codebehind"></a>CodeBehind

Ve **skriptu. aSQL** CodeBehind můžete napsat uživatelsky definované funkce. Nástroje Visual Studio budou automaticky kompilovat soubor CodeBehind do souboru sestavení. Sestavení jsou zabalena jako soubor zip a při odeslání vaší úlohy do Azure se nahrají do svého účtu úložiště. Informace o tom, jak psát C# UDF pomocí CodeBehind, najdete v kurzu [pro úlohy v jazyce C# UDF pro Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) . 

### <a name="local-project"></a>Místní projekt

Odkazování na místní projekt v aplikaci Visual Studio:

1. Vytvoří novou knihovnu tříd .NET Standard ve vašem řešení.
2. Napište kód do vaší třídy. Pamatujte, že třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*. 
3. Sestavte projekt. Nástroje zabalí všechny artefakty ve složce bin do souboru zip a nahrajte soubor zip do účtu úložiště. Pro externí odkazy použijte místo balíčku NuGet odkaz na sestavení.
4. Odkaz na novou třídu ve vašem projektu Azure Stream Analytics.
5. Přidejte do projektu Azure Stream Analytics novou funkci.
6. Nakonfigurujte cestu sestavení v konfiguračním souboru úlohy, `JobConfig.json` . Nastavte cestu sestavení na **odkaz místní projekt nebo CodeBehind**.
7. Znovu sestavte projekt funkce i projekt Azure Stream Analytics.  

### <a name="example"></a>Příklad

V tomto příkladu je **UDFTest** projekt knihovny tříd jazyka C# a **ASAUDFDemo** je projekt Azure Stream Analytics, který bude odkazovat **UDFTest**.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png" alt-text="Projekt Azure Stream Analytics IoT Edge v aplikaci Visual Studio":::

1. Sestavte projekt C#, který vám umožní přidat odkaz na systém souborů UDF v C# z Azure Stream Analytics dotazu.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png" alt-text="Sestavení projektu Azure Stream Analytics IoT Edge v aplikaci Visual Studio":::

2. Přidejte odkaz na projekt C# v projektu ASA. Klikněte pravým tlačítkem myši na uzel odkazy a vyberte možnost Přidat odkaz.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png" alt-text="Přidání odkazu na projekt C# v aplikaci Visual Studio":::

3. V seznamu vyberte název projektu C#.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png" alt-text="Vyberte název projektu C# ze seznamu odkazů.":::

4. **UDFTest** by se měl zobrazit v části **odkazy** v **Průzkumník řešení**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png" alt-text="Zobrazit odkaz na funkci definované uživatelem v Průzkumníkovi řešení":::

5. Klikněte pravým tlačítkem na složku **Functions** a vyberte možnost **Nová položka**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png" alt-text="Přidání nové položky do funkcí v řešení Azure Stream Analytics Edge":::

6. Přidejte funkci jazyka C# **SquareFunction.js** do projektu Azure Stream Analytics.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png" alt-text="Výběr funkce CSharp z položek hran Stream Analytics v aplikaci Visual Studio":::

7. Dvojím kliknutím na funkci v **Průzkumník řešení** otevřete dialogové okno konfigurace.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png" alt-text="Konfigurace ostrých funkcí jazyka C v aplikaci Visual Studio":::

8. V konfiguraci funkce jazyka C# vyberte z rozevíracího seznamu možnost **načíst z aplikace ASA odkaz na projekt** a související názvy sestavení, třídy a metody. Chcete-li odkazovat na metody, typy a funkce v dotazu Stream Analytics, třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png" alt-text="Stream Analytics C – konfigurace ostrých funkcí v aplikaci Visual Studio":::

## <a name="existing-packages"></a>Existující balíčky

Můžete vytvořit .NET Standard UDF v jakémkoli integrovaném vývojovém prostředí a vyvolat je z dotazu Azure Stream Analytics. Nejprve zkompilujte kód a zabalíte všechny knihovny DLL. Formát balíčku má cestu `/UserCustomCode/CLR/*` . Pak nahrajte `UserCustomCode.zip` do kořenového adresáře kontejneru v účtu úložiště Azure.

Po nahrání balíčků zip pro sestavení do vašeho účtu úložiště Azure můžete použít funkce v Azure Stream Analytics dotazy. Vše, co potřebujete udělat, je zahrnout informace o úložišti v konfiguraci úlohy Stream Analytics. Tuto funkci nemůžete místně otestovat pomocí této možnosti, protože Visual Studio Tools nebude balíček stahovat. Cesta k balíčku se analyzuje přímo na službu. 

Chcete-li konfigurovat cestu sestavení v konfiguračním souboru úlohy, `JobConfig.json` postupujte takto:

Rozbalte část **Konfigurace kódu definovaného uživatelem** a vyplňte do konfigurace následující navrhované hodnoty:

   |**Nastavení**|**Navrhovaná hodnota**|
   |-------|---------------|
   |Prostředek nastavení globálního úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálních nastavení úložiště| < vaše předplatné >|
   |Účet úložiště nastavení globálního úložiště| < svého účtu úložiště >|
   |Prostředek nastavení úložiště vlastního kódu|Zvolit zdroj dat z aktuálního účtu|
   |Vlastní nastavení úložiště kódu účet úložiště|< svého účtu úložiště >|
   |Kontejner nastavení vlastního úložiště kódu|< kontejneru úložiště >|
   |Zdroj sestavení vlastního kódu|Existující balíčky sestavení z cloudu|
   |Zdroj sestavení vlastního kódu|UserCustomCode.zip|

## <a name="user-logging"></a>Protokolování uživatele

Mechanismus protokolování umožňuje zachytit vlastní informace, když je úloha spuštěná. Data protokolu můžete použít k ladění nebo vyhodnocení správnosti vlastního kódu v reálném čase.

`StreamingContext`Třída umožňuje publikovat diagnostické informace pomocí `StreamingDiagnostics.WriteError` funkce. Následující kód ukazuje rozhraní vystavené Azure Stream Analytics.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` se předává jako vstupní parametr metodě UDF a dá se použít v rámci systému souborů UDF k publikování informací o vlastním protokolu. V následujícím příkladu `MyUdfMethod` definuje **datový** vstup, který je poskytován dotazem, a vstupní **kontext** jako modul, který je `StreamingContext` poskytován modulem runtime. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

`StreamingContext`Hodnotu není nutné předat dotazem SQL. Azure Stream Analytics poskytuje kontextový objekt automaticky, pokud je přítomný vstupní parametr. Použití se `MyUdfMethod` nemění, jak je znázorněno v následujícím dotazu:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

Ke zprávám protokolu můžete přistupovat prostřednictvím [diagnostických protokolů](data-errors.md).

## <a name="limitations"></a>Omezení

Systém UDF Preview má nyní následující omezení:

* .NET Standard UDF se dá vytvořit jenom v Visual Studio Code nebo Visual Studiu a publikovat do Azure. Verze .NET Standard UDF jen pro čtení se dají zobrazit v Azure Portalch **funkcích** . Vytváření .NET Standardch funkcí není v Azure Portal podporováno.

* V editoru dotazů Azure Portal se při použití .NET Standard UDF na portálu zobrazí chyba. 

* Vzhledem k tomu, že vlastní sdílené složky mají kontext s Azure Stream Analytics Engine, vlastní kód nemůže odkazovat na cokoli s konfliktním oborem názvů/dll_name s Azure Stream Analytics kódem. Například nemůžete odkazovat na *Newtonsoft JSON*.

* Podpůrné soubory zahrnuté v projektu se zkopírují do souboru ZIP vlastního kódu uživatele, který se používá při publikování úlohy do cloudu. Všechny soubory v podsložkách se zkopírují přímo do kořenové složky vlastního kódu uživatele v cloudu, když je to unzip. Při dekomprimaci je soubor zip "plochý".

* Vlastní kód uživatele nepodporuje prázdné složky. Nepřidávejte prázdné složky do podpůrných souborů v projektu.

## <a name="next-steps"></a>Další kroky

* [Kurz: zápis uživatelsky definované funkce v jazyce C# pro úlohu Azure Stream Analytics (Preview)](stream-analytics-edge-csharp-udf.md)
* [Kurz: Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)