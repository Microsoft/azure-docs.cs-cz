---
title: Vývoj funkcí .NET Standard pro úlohy Azure Stream Analytics (Preview)
description: Naučte se psát uživatelsky definované funkce v jazyce c# pro úlohy Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 835eeaa0036d824a65918102ab7a00c3cf236931
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72988397"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics (Preview)

Azure Stream Analytics nabízí dotazovací jazyk podobný SQL pro provádění transformací a výpočtů přes streamy dat událostí. Existuje mnoho vestavěných funkcí, ale některé složité scénáře vyžadují větší flexibilitu. S .NET Standard uživatelsky definovaných funkcí (UDF) můžete vyvolat vlastní funkce napsané v jakémkoli jazyce .NET Standard (C#, F#atd.) pro rozšiřování Stream Analytics dotazovacího jazyka. UDF vám umožní provádět komplexní matematické výpočty, importovat vlastní ML modely pomocí ML.NET a použít vlastní logiku imputace pro chybějící data. Funkce UDF pro úlohy Stream Analytics je momentálně ve verzi Preview a neměla by se používat v produkčních úlohách.

## <a name="overview"></a>Přehled
Visual Studio Tools for Azure Stream Analytics usnadňuje psaní UDF, testování vašich úloh místně (i offline) a publikování Stream Analytics úlohy do Azure. Po publikování do Azure můžete svoji úlohu nasadit do zařízení IoT pomocí IoT Hub.

Existují tři způsoby, jak implementovat UDF:

* Soubory CodeBehind v projektu ASA
* Systém souborů UDF z místního projektu
* Existující balíček z účtu služby Azure Storage

## <a name="package-path"></a>Cesta k balíčku

Formát libovolného balíčku UDF má `/UserCustomCode/CLR/*`cesty. Knihovny DLL (Dynamic Link Library) a prostředky se zkopírují do složky `/UserCustomCode/CLR/*`, která pomáhá izolovat knihovny DLL uživatelů ze systémových a Azure Stream Analyticsch knihoven DLL. Tato cesta k balíčku se používá pro všechny funkce bez ohledu na metodu použitou k jejich využití.

## <a name="supported-types-and-mapping"></a>Podporované typy a mapování

|**Typ UDF (C#)**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|Dlouhou  |  bigint   |
|double  |  double   |
|string  |  nvarchar (max)   |
|Hodnotu  |  Hodnotu   |
|nemají  |  IRecord   |
|object  |  IRecord   |
|Objekt\<pole >  |  IArray   |
|< řetězec slovníku, > objektů  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Ve **skriptu. aSQL** CodeBehind můžete napsat uživatelsky definované funkce. Nástroje Visual Studio budou automaticky kompilovat soubor CodeBehind do souboru sestavení. Sestavení jsou zabalena jako soubor zip a při odeslání vaší úlohy do Azure se nahrají do svého účtu úložiště. Informace o tom, jak napsat C# UDF pomocí CodeBehind, najdete v [ C# kurzu pro úlohy systému souborů UDF pro Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) . 

## <a name="local-project"></a>Místní projekt
Uživatelsky definované funkce lze zapsat do sestavení, které je později odkazováno v Azure Stream Analytics dotazu. Toto je doporučená možnost pro komplexní funkce, které vyžadují celou sílu .NET Standardho jazyka nad rámec jeho jazyka, jako je například Procedurální logika nebo rekurze. UDF z místního projektu se může používat i v případě, že potřebujete sdílet logiku funkce v několika Azure Stream Analyticsch dotazech. Přidání UDF do místního projektu vám umožní ladit a testovat funkce místně ze sady Visual Studio.

Postup při odkazování na místní projekt:

1. Vytvořte ve svém řešení novou knihovnu tříd.
2. Napište kód do vaší třídy. Pamatujte, že třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*. 
3. Sestavte projekt. Nástroje zabalí všechny artefakty ve složce bin do souboru zip a nahrajte soubor zip do účtu úložiště. Pro externí odkazy použijte místo balíčku NuGet odkaz na sestavení.
4. Odkaz na novou třídu ve vašem projektu Azure Stream Analytics.
5. Přidejte do projektu Azure Stream Analytics novou funkci.
6. Nakonfigurujte cestu sestavení v konfiguračním souboru úlohy `JobConfig.json`. Nastavte cestu sestavení na **odkaz místní projekt nebo CodeBehind**.
7. Znovu sestavte projekt funkce i projekt Azure Stream Analytics.  

### <a name="example"></a>Příklad:

V tomto příkladu je **UDFTest** projekt knihovny C# tříd a **ASAUDFDemo** je projekt Azure Stream Analytics, který bude odkazovat **UDFTest**.

![Projekt Azure Stream Analytics IoT Edge v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Sestavte C# projekt, který vám umožní přidat odkaz na systém C# souborů UDF z Azure Stream Analyticsho dotazu.
    
   ![Sestavení projektu Azure Stream Analytics IoT Edge v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Přidejte odkaz na C# projekt v projektu ASA. Klikněte pravým tlačítkem myši na uzel odkazy a vyberte možnost Přidat odkaz.

   ![Přidání odkazu na C# projekt v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. V seznamu C# vyberte název projektu. 
    
   ![Vyberte název C# projektu ze seznamu odkazů.](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **UDFTest** by se měl zobrazit v části **odkazy** v **Průzkumník řešení**.

   ![Zobrazit odkaz na funkci definované uživatelem v Průzkumníkovi řešení](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klikněte pravým tlačítkem na složku **Functions** a vyberte možnost **Nová položka**.

   ![Přidání nové položky do funkcí v řešení Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Do projektu C# Azure Stream Analytics přidejte funkci **SquareFunction. JSON** .

   ![Výběr funkce CSharp z položek hran Stream Analytics v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dvojím kliknutím na funkci v **Průzkumník řešení** otevřete dialogové okno konfigurace.

   ![Konfigurace ostrých funkcí jazyka C v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. V konfiguraci C# funkce vyberte z rozevíracího seznamu možnost **načíst z aplikace ASA odkaz na projekt** a související názvy sestavení, třídy a metody. Chcete-li odkazovat na metody, typy a funkce v dotazu Stream Analytics, třídy musí být definovány jako *veřejné* a objekty musí být definovány jako *statické veřejné*.

   ![Stream Analytics konfigurace ostře funkcí jazyka C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Existující balíčky

Můžete vytvořit .NET Standard UDF v jakémkoli integrovaném vývojovém prostředí a vyvolat je z dotazu Azure Stream Analytics. Nejprve zkompilujte kód a zabalíte všechny knihovny DLL. Formát balíčku má `/UserCustomCode/CLR/*`cesty. Pak nahrajte `UserCustomCode.zip` do kořenového adresáře kontejneru v účtu úložiště Azure.

Po nahrání balíčků zip pro sestavení do vašeho účtu úložiště Azure můžete použít funkce v Azure Stream Analytics dotazy. Vše, co potřebujete udělat, je zahrnout informace o úložišti v konfiguraci úlohy Stream Analytics. Tuto funkci nemůžete místně otestovat pomocí této možnosti, protože Visual Studio Tools nebude balíček stahovat. Cesta k balíčku se analyzuje přímo na službu. 

Chcete-li konfigurovat cestu sestavení v konfiguračním souboru úlohy, `JobConfig.json`:

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
   |Zdroj sestavení vlastního kódu|UserCustomCode. zip|

## <a name="limitations"></a>Omezení
Systém UDF Preview má nyní následující omezení:

* .NET Standard UDF lze vytvořit pouze v aplikaci Visual Studio a publikovat do Azure. Verze .NET Standard UDF jen pro čtení se dají zobrazit v Azure Portalch **funkcích** . Vytváření .NET Standardch funkcí není v Azure Portal podporováno.

* V editoru dotazů Azure Portal se při použití .NET Standard UDF na portálu zobrazí chyba. 

* Vzhledem k tomu, že vlastní sdílené složky mají kontext s Azure Stream Analytics Engine, vlastní kód nemůže odkazovat na cokoli s konfliktním oborem názvů/DLL_name s kódem Azure Stream Analytics. Například nemůžete odkazovat na *Newtonsoft JSON*.

## <a name="next-steps"></a>Další kroky

* [Kurz: zápis C# uživatelsky definované funkce pro úlohu Azure Stream Analytics (Preview)](stream-analytics-edge-csharp-udf.md)
* [Kurz: Azure Stream Analytics uživatelsky definovaných funkcí jazyka JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
