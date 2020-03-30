---
title: Vývoj funkcí .NET Standard pro úlohy Azure Stream Analytics (preview)
description: Přečtěte si, jak psát uživatelem definované funkce jazyka c# pro úlohy Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426319"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Vývoj uživatelem definovaných funkcí .NET Standard pro úlohy Azure Stream Analytics (preview)

Azure Stream Analytics nabízí dotazovací jazyk podobný SQL pro provádění transformací a výpočtů přes datové proudy dat událostí. Existuje mnoho vestavěných funkcí, ale některé složité scénáře vyžadují další flexibilitu. Pomocí uživatelem definovaných funkcí .NET Standard (UDF) můžete vyvolat vlastní funkce napsané v libovolném standardním jazyce .NET (C#, F#, atd.) a rozšířit tak dotazovací jazyk Stream Analytics. UDF umožňují provádět složité matematické výpočty, importovat vlastní modely ML pomocí ML.NET a používat vlastní logiku imputace pro chybějící data. Funkce UDF pro úlohy Stream Analytics je aktuálně ve verzi preview a neměla by se používat v produkčních úlohách.

Uživatelem definovaná funkce .NET pro cloudové úlohy je k dispozici v:
* USA – středozápad
* Severní Evropa
* USA – východ
* USA – západ
* USA – východ 2
* Západní Evropa

Máte-li zájem o použití této funkce v jiné oblasti, můžete [požádat o přístup](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Přehled
Nástroje Visual Studia pro Azure Stream Analytics usnadňují psaní uf, testování úloh místně (i offline) a publikování úlohy Stream Analytics do Azure. Po publikování do Azure můžete nasadit úlohu do zařízení IoT pomocí služby IoT Hub.

Existují tři způsoby implementace udf:

* CodeBehind soubory v projektu ASA
* UDF z místního projektu
* Existující balíček z účtu úložiště Azure

## <a name="package-path"></a>Cesta k balíčku

Formát libovolného balíčku UDF `/UserCustomCode/CLR/*`má cestu . Knihovny dynamických odkazů (DLL) a prostředky `/UserCustomCode/CLR/*` jsou zkopírovány do složky, což pomáhá izolovat knihovny DLL uživatelů od knihovny DLL systému a Azure Stream Analytics. Tato cesta balíčku se používá pro všechny funkce bez ohledu na metodu použitou k jejich použití.

## <a name="supported-types-and-mapping"></a>Podporované typy a mapování

|**Typ UDF (C#)**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|řetězec  |  Nvarchar(max)   |
|data a času.  |  data a času.   |
|struct   |  IZáznam   |
|objekt  |  IZáznam   |
|>\<objektu pole  |  IArray   |
|slovník<řetězec,> objektu  |  IZáznam   |

## <a name="codebehind"></a>Codebehind
Uživatelem definované funkce můžete zapisovat do codebehind **skriptu.asql.** Nástroje sady Visual Studio automaticky zkompilují soubor CodeBehind do souboru sestavení. Sestavení jsou zabaleny jako soubor zip a odeslány do vašeho účtu úložiště při odeslání úlohy do Azure. Můžete se dozvědět, jak napsat C# UDF pomocí CodeBehind podle [následujících C# UDF pro streamování Analytics Edge úlohy](stream-analytics-edge-csharp-udf.md) kurzu. 

## <a name="local-project"></a>Místní projekt
Uživatelem definované funkce lze zapsat v sestavení, které je později odkazováno v dotazu Azure Stream Analytics. Toto je doporučená možnost pro složité funkce, které vyžadují plný výkon jazyka .NET Standard nad rámec jazyka výrazu, jako je například procedurální logika nebo rekurze. UDFz místního projektu se taky může použít, když potřebujete sdílet logiku funkce napříč několika dotazy Azure Stream Analytics. Přidání UDFs do místního projektu umožňuje ladit a testovat funkce místně z Visual Studio.

Odkaz na místní projekt:

1. Vytvořte novou knihovnu tříd ve vašem řešení.
2. Napište kód do třídy. Mějte na paměti, že třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*. 
3. Sestavte projekt. Nástroje zabalí všechny artefakty ve složce bin do souboru ZIP a nahrají soubor ZIP do účtu úložiště. Pro externí odkazy použijte odkaz na sestavení namísto balíčku NuGet.
4. Odkazujte na novou třídu v projektu Azure Stream Analytics.
5. Přidejte novou funkci v projektu Azure Stream Analytics.
6. Nakonfigurujte cestu sestavení `JobConfig.json`v konfiguračním souboru úlohy . Nastavte cestu sestavení na **odkaz na místní projekt nebo codebehind**.
7. Znovu sestavte projekt funkce i projekt Azure Stream Analytics.  

### <a name="example"></a>Příklad

V tomto příkladu **UDFTest** je c# projekt knihovny třídy a **ASAUDFDemo** je projekt Azure Stream Analytics, který bude odkazovat **UDFTest**.

![Projekt Azure Stream Analytics IoT Edge ve Visual Studiu](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Vytvořte svůj projekt C#, který vám umožní přidat odkaz na váš C# UDF z dotazu Azure Stream Analytics.
    
   ![Vytvoření projektu Azure Stream Analytics IoT Edge ve Visual Studiu](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Přidejte odkaz na projekt Jazyka C# v projektu ASA. Klikněte pravým tlačítkem myši na uzel Odkazy a zvolte Přidat odkaz.

   ![Přidání odkazu na projekt jazyka C# v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. V seznamu zvolte název projektu Jazyka C#. 
    
   ![Zvolte název projektu jazyka C# ze seznamu odkazů.](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Měl by se zobrazit **UDFTest** uvedený v části **Odkazy** v **Průzkumníku řešení**.

   ![Zobrazení uživatelem definované holce v průzkumníku řešení](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klikněte pravým tlačítkem myši na složku **Funkce** a zvolte **Nová položka**.

   ![Přidání nové položky do funkcí v řešení Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Přidejte funkci C# **SquareFunction.json** do projektu Azure Stream Analytics.

   ![Výběr funkce CSharp z položek Stream Analytics Edge v Visual Studiu](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Poklepáním na funkci v **Průzkumníku řešení** otevřete dialogové okno konfigurace.

   ![C ostré funkce konfigurace v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. V konfiguraci funkce C# zvolte **Načíst z odkazu projektu ASA** a souvisejících názvů sestavení, třída a metody z rozevíracího seznamu. Chcete-li odkazovat na metody, typy a funkce v dotazu Stream Analytics, třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*.

   ![Konfigurace ostré funkce Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Existující balíčky

Standardní udf .NET můžete vytvářet v libovolném rozhraní IDE podle vašeho výběru a vyvolat je z vašeho dotazu Azure Stream Analytics. Nejprve zkompilujte kód a zabalte všechny knihovny DLL. Formát balíčku má cestu `/UserCustomCode/CLR/*`. Potom nahrajte `UserCustomCode.zip` do kořenového adresáře kontejneru ve vašem účtu úložiště Azure.

Po nahrání balíčků zip sestavení do vašeho účtu úložiště Azure můžete použít funkce v dotazech Azure Stream Analytics. Jediné, co musíte udělat, je zahrnout informace o úložišti do konfigurace úlohY Stream Analytics. Tuto možnost nelze testovat místně pomocí funkce, protože nástroje sady Visual Studio nebudou stahovat balíček. Cesta balíčku je analyzována přímo ke službě. 

Chcete-li nakonfigurovat cestu sestavení `JobConfig.json`v konfiguračním souboru úlohy, :

Rozbalte část **Konfigurace kódu definovaného uživatelem** a vyplňte do konfigurace následující navrhované hodnoty:

   |**Nastavení**|**Navrhovaná hodnota**|
   |-------|---------------|
   |Prostředek globálního nastavení úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálního nastavení úložiště| < > předplatného|
   |Účet úložiště globálního nastavení úložiště| < > účtu úložiště|
   |Prostředek nastavení vlastního úložiště kódu|Zvolit zdroj dat z aktuálního účtu|
   |Účet úložiště vlastního úložiště kódu|< > účtu úložiště|
   |Kontejner nastavení úložiště vlastního kódu|< > skladovacího kontejneru|
   |Zdroj sestavení vlastního kódu|Existující balíčky sestavení z cloudu|
   |Zdroj sestavení vlastního kódu|UserCustomCode.zip|

## <a name="limitations"></a>Omezení
Náhled UDF má aktuálně následující omezení:

* Standardní u-fs .NET lze vytvořit jenom v sadě Visual Studio a publikovat do Azure. Verze rozhraní UD Standard jen pro čtení lze zobrazit v části **Funkce** na webu Azure Portal. Vytváření funkcí .NET Standard není na webu Azure Portal podporováno.

* Editor dotazů na webu Azure Portal zobrazuje chybu při použití udf rozhraní .NET Standard na portálu. 

* Vzhledem k tomu, že vlastní kód sdílí kontext s motorem Azure Stream Analytics, vlastní kód nemůže odkazovat na nic, co má konfliktní obor názvů nebo dll_name s kódem Azure Stream Analytics. Například, nemůžete odkazovat *Newtonsoft Json*.

## <a name="next-steps"></a>Další kroky

* [Kurz: Napište uživatelem definovanou funkci Jazyka C# pro úlohu Azure Stream Analytics (preview)](stream-analytics-edge-csharp-udf.md)
* [Kurz: Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Zobrazení úloh Azure Stream Analytics pomocí Visual Studia](stream-analytics-vs-tools.md)
