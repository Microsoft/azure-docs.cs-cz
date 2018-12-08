---
title: Vývoj .NET Standard funkcí pro úlohy Azure Stream Analytics Edge (Preview)
description: Další informace o zápisu jazyka c# uživatelsky definovaných funkcí pro úlohy Stream Analytics Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5df4c9dfe18b02ade3a37717da9c68acbfcf1853
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106596"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics Edge (Preview)

Azure Stream Analytics nabízí SQL jako dotazovací jazyk pro provádění transformací a výpočtů prostřednictvím datových proudů dat událostí. K dispozici je mnoho integrovaných funkcí, ale některé složité scénáře vyžadují větší flexibilitu. Pomocí .NET Standard uživatelem definované funkce (UDF), můžete vyvolat vašich vlastních funkcích napsané v libovolném jazyce .NET standard (C#, F#atd) pro rozšíření dotazovací jazyk Stream Analytics. Uživatelem definovanými funkcemi umožňují provádět složité matematické výpočty, vlastní modely ML s využitím ML.NET importovat a používat vlastní imputace logiku pro chybějící data. Funkce uživatelem definovaných funkcí pro úlohy Stream Analytics Edge je v současné době ve verzi a neměla by se používat v produkčních úlohách.

## <a name="overview"></a>Přehled
Visual Studio tools pro Azure Stream Analytics usnadňuje psaní uživatelem definovanými funkcemi, testování vašich úloh místně (i offline) a publikování vaší úlohy Stream Analytics v Azure. Po publikování do Azure, můžete nasadit úlohy na zařízení IoT pomocí služby IoT Hub.

Existují tři způsoby, jak implementovat uživatelem definovanými funkcemi:

* CodeBehind soubory v projektu Azure Stream Analytics
* UDF z místní projekt
* Stávající balíček z účtu služby Azure storage

## <a name="package-path"></a>Cesta k balíčku

Formát libovolný balíček UDF má cestu `/UserCustomCode/CLR/*`. Dynamické knihovny (DLL) a prostředky jsou zkopírovány v části `/UserCustomCode/CLR/*` složku, která pomáhá izolovat uživatele knihoven DLL ze systému a knihovny DLL Azure Stream Analytics. Tato cesta balíčku se používá pro všechny funkce bez ohledu na to metoda použitá jim využívat.

## <a name="supported-types-and-mapping"></a>Podporované typy a mapování

|**Typ systému souborů UDF (C#)**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|Long  |  bigint   |
|double  |  double   |
|řetězec  |  nvarchar(max)   |
|Datum a čas  |  Datum a čas   |
|– Struktura  |  IRecord   |
|objekt  |  IRecord   |
|Pole<object>  |  IArray   |
|Dictionary < string, object >  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Můžete napsat uživatelsky definovaných funkcí v **Script.asql** CodeBehind. Nástroje sady Visual Studio bude automaticky kompilaci CodeBehind do souboru sestavení. Sestavení se lze zabalit jako soubor zip a nahráli do svého účtu úložiště, když odešlete úlohu do Azure. Zjistěte, jak psát UDF C# CodeBehind pomocí následujících [UDF C# pro úlohy Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) kurzu. 

## <a name="local-project"></a>Místní projekt
Uživatelem definované funkce je možné psát v sestavení, která je popsána dále v dotazu Azure Stream Analytics. Tato možnost se doporučuje pro složité funkce, které vyžadují plný výkon rozhraní .NET Standard jazyka nad rámec jeho jazyk výrazů, jako je například procesní logiky nebo rekurzi. UDF z místní projektu mohou být také použity, když budete chtít sdílet logiky funkce mezi několik dotazů Azure Stream Analytics. Přidání funkcí UDF místní projekt poskytuje možnost ladit a testovat vaše funkce místně v sadě Visual Studio.

Chcete-li odkazovat místní projekt:

1. Vytvořte novou knihovnu tříd ve vašem řešení.
2. Psát kód ve své třídě. Mějte na paměti, že třídy musí být definován jako *veřejné* a objekty musí být definován jako *statické veřejné*. 
3. Sestavte projekt. Nástroje se balíček všechny artefakty do složky bin do souboru zip a nahrajte soubor zip do účtu úložiště. Pro externí odkazy použijte odkaz na sestavení namísto balíčku NuGet.
4. Odkaz na novou třídu v projektu Azure Stream Analytics.
5. Přidáte nové funkce v projektu Azure Stream Analytics.
6. Konfigurovat cestu sestavení v konfiguračním souboru úlohy `JobConfig.json`. Nastavte cestu sestavení **odkazu na místní projekt nebo CodeBehind**.
7. Znovu sestavte projekt functions a Azure Stream Analytics projektu.  

### <a name="example"></a>Příklad:

V tomto příkladu **UDFTest** je projekt knihovny tříd jazyka C# a **ASAEdgeUDFDemo** je projektu Azure Stream Analytics Edge, který bude odkazovat na **UDFTest**.

![Azure Stream Analytics IoT Edge projektu v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Sestavení projektu C#, která vám umožní přidat odkaz na UDF C# z dotazu Azure Stream Analytics.
    
   ![Vytvoření projektu aplikace Azure Stream Analytics IoT Edge v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Přidání odkazu do projektu C# v projektu Azure Stream Analytics Edge. Klikněte pravým tlačítkem na uzel odkazy a zvolte možnost Přidat odkaz.

   ![Přidání odkazu do projektu C# v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Ze seznamu vyberte název projektu C#. 
    
   ![Zvolte ze seznamu odkaz na název projektu C#](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Měli byste vidět **UDFTest** uvedené v části **odkazy** v **Průzkumníku řešení**.

   ![Zobrazit uživateli definované odkaz funkce v Průzkumníku řešení](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klikněte pravým tlačítkem na **funkce** složky a vyberte **nová položka**.

   ![Přidat novou položku do funkce v Azure Stream Analytics Edge řešení](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Přidat funkci v jazyce C# **SquareFunction.json** do projektu Azure Stream Analytics.

   ![Vyberte funkci CSharp ze Stream Analytics Edge položky v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Poklikejte na funkci v **Průzkumníka řešení** otevřete dialogové okno konfigurace.

   ![Konfigurace jazyka C sharp funkce v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. V konfiguraci funkce C#, zvolte **zatížení z odkazu na projekt Azure Stream Analytics** a související sestavení, třídy a metody názvy z rozevíracího seznamu. K odkazování na metody, typy a funkce v dotazu Stream Analytics Edge, musí se definovat třídy *veřejné* a objekty musí být definován jako *statické veřejné*.

   ![Konfigurace Stream Analytics C sharp – funkce](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Existující balíčky

Můžete vytvářet .NET Standard uživatelem definovanými funkcemi v jakémkoli rozhraní IDE podle svého výběru a volat z vašeho dotazu Azure Stream Analytics. Nejprve ji zkompilujte vašeho kódu a balíček všechny knihovny DLL. Formát balíčku má cestu `/UserCustomCode/CLR/*`. Potom obsah nahrajete `UserCustomCode.zip` do kořenového adresáře kontejneru v účtu úložiště Azure.

Po sestavení balíčků zip nahrané do účtu úložiště Azure, můžete použít funkce v Azure Stream Analytics dotazů. Všechno, co musíte udělat, je zahrnout informace o úložiště konfigurace úlohy Stream Analytics Edge. Funkci místně s touto možností nelze otestovat, protože balíček nebude stahovat nástrojů sady Visual Studio. Cesta k balíčku je analyzován přímo ke službě. 

Abyste nakonfigurovali cestu sestavení v konfiguračním souboru úlohy `JobConfig.json`:

Rozbalte část **Konfigurace kódu definovaného uživatelem** a vyplňte do konfigurace následující navrhované hodnoty:

 |**Nastavení**  |**Navrhovaná hodnota**  |
 |---------|---------|
 |Zdroj sestavení  | Existující balíčky sestavení z cloudu    |
 |Prostředek  |  Zvolit data z aktuálního účtu   |
 |Předplatné  |  Zvolte vaše předplatné.   |
 |Účet úložiště  |  Vyberte svůj účet úložiště   |
 |Kontejner  |  Zvolte kontejner, který jste vytvořili ve svém účtu úložiště.   |

![Konfigurace úlohy Azure Stream Analytics Edge v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Omezení
Ve verzi preview UDF aktuálně má následující omezení:

* Jazyky .NET standard jde použít jenom pro Azure Stream Analytics na hraničních zařízeních IoT. Pro cloudové úlohy můžete psát uživatelem definované funkce jazyka JavaScript. Další informace najdete [Azure Stream Analytics JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) kurzu.

* Standardní UDF .NET můžete pouze vytvořené v sadě Visual Studio a publikovat do Azure. V části lze zobrazit jen pro čtení verzích rozhraní .NET Standard UDF **funkce** na webu Azure Portal. Vytváření funkcí .NET Standard není podporován na webu Azure Portal.

* Editor dotazů Azure portal zobrazuje chybu, když pomocí .NET Standard UDF na portálu. 

* Protože vlastní kód sdílí kontext pomocí modulu Azure Stream Analytics, vlastní kód nemůže odkazovat na cokoli, co má konfliktní obor názvů/název_dll kódem Azure Stream Analytics. Například nelze odkazovat *Newtonsoft Json*.

## <a name="next-steps"></a>Další postup

* [Kurz: Psaní C# uživatelem definované funkce pro úlohu Azure Stream Analytics Edge (Preview)](stream-analytics-edge-csharp-udf.md)
* [Kurzu: Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)
