---
title: Kurz – vlastní deserializátory .NET pro cloudové úlohy Azure Stream Analytics
description: Tento kurz ukazuje, jak vytvořit vlastní deserializátor .NET pro cloudovou úlohu Azure Stream Analytics pomocí Sady Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75443695"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Kurz: Vlastní deserializátory .NET pro Azure Stream Analytics

Azure Stream Analytics má [integrovanou podporu pro tři datové formáty](stream-analytics-parsing-json.md): JSON, CSV a Avro. Pomocí vlastních deserializátorů .NET můžete číst data z jiných formátů, jako je [vyrovnávací paměť protokolu](https://developers.google.com/protocol-buffers/), [bond](https://github.com/Microsoft/bond) a další uživatelem definované formáty pro úlohy v cloudu i edge.

Tento kurz ukazuje, jak vytvořit vlastní deserializátor .NET pro cloudovou úlohu Azure Stream Analytics pomocí Sady Visual Studio. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte vlastní deserializátor pro vyrovnávací paměť protokolu.
> * Vytvořte úlohu Azure Stream Analytics ve Visual Studiu.
> * Nakonfigurujte úlohu Stream Analytics tak, aby používala vlastní deserializátor.
> * Spusťte úlohu Stream Analytics místně a otestujte vlastní deserializátor.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/downloads/) nebo [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/). Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Expresní edice není podporována.

* [Nainstalujte nástroje Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md) nebo aktualizujte na nejnovější verzi. Podporovány jsou následující verze sady Visual Studio:
   * Visual Studio 2015
   * Visual Studio 2017

* Otevřete **Cloud Explorer** ve Visual Studiu a přihlaste se k předplatnému Azure.

* Vytvořte si kontejner ve svém účtu úložiště Azure.
Kontejner, který vytvoříte, se použije k ukládání prostředků souvisejících s úlohou Stream Analytics. Pokud už máte účet úložiště s existujícími kontejnery, můžete je použít. Pokud ne, budete muset [vytvořit nový kontejner](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Vytvoření vlastního deserializátoru

1. Otevřete Visual Studio a vyberte **Soubor > Nový > Project**. Vyhledejte **Stream Analytics** a vyberte **Azure Stream Analytics Vlastní deserializer Project (.NET)**. Pojmenujte projekt, jako **je Protobuf Deserializer**.

   ![Vytvoření projektu knihovny standardní třídy Visual Studio dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **Protobuf Deserializer** a v nabídce vyberte **Spravovat balíčky NuGet.** Pak nainstalujte balíčky **Microsoft.Azure.StreamAnalytics** a **Google.Protobuf** NuGet.

3. Přidejte [třídu MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) a [třídu MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) do projektu.

4. Sestavte projekt **Protobuf Deserializer.**

## <a name="add-an-azure-stream-analytics-project"></a>Přidání projektu Azure Stream Analytics

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na řešení **Protobuf Deserializer** a vyberte **přidat > nový projekt**. V části **Azure Stream Analytics > Stream Analytics**zvolte **Azure Stream Analytics Application**. Pojmenujte jej **ProtobufCloudDeserializer** a vyberte **OK**. 

2. Klikněte pravým tlačítkem na **Reference** v rámci projektu **ProtobufCloudDeserializer** Azure Stream Analytics. V **části Projekty**přidejte **protobufdeserializátor**. Měl by být automaticky naplněn pro vás.

## <a name="configure-a-stream-analytics-job"></a>Konfigurace úlohy Stream Analytics

1. Poklepejte na soubor **JobConfig.json**. Použijte výchozí konfigurace, s výjimkou následujících nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Prostředek globálního nastavení úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálního nastavení úložiště| < > předplatného|
   |Účet úložiště globálního nastavení úložiště| < > účtu úložiště|
   |Prostředek nastavení vlastního úložiště kódu|Zvolit zdroj dat z aktuálního účtu|
   |Účet úložiště vlastního úložiště kódu|< > účtu úložiště|
   |Kontejner nastavení úložiště vlastního kódu|< > skladovacího kontejneru|

2. V části **Vstupy**poklepejte na **položku Input.json**. Použijte výchozí konfigurace, s výjimkou následujících nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Zdroj|Blob Storage|
   |Prostředek|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné|< > předplatného|
   |Účet úložiště|< > účtu úložiště|
   |Kontejner|< > skladovacího kontejneru|
   |Formát serializace událostí|Ostatní (Protobuf, XML, proprietární...)|
   |Prostředek|Načíst z odkazu projektu ASA nebo codeBehind|
   |Název sestavení CSharp|ProtobufDeserializer.dll|
   |Název třídy|MessageBodyProto.MessageBodyDeserializer|
   |Typ komprese událostí|Žádný|

3. Přidejte následující dotaz do souboru **Script.asaql.**

   ```sql
   SELECT * FROM Input
   ```

4. Stáhněte si [ukázkový vstupní soubor protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Ve složce **Vstupy** klepněte pravým tlačítkem myši na **input.json** a vyberte **Přidat místní vstup**. Potom poklepejte na **local_Input.json** a nakonfigurujte následující nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Alias pro vstup|Vstup|
   |Typ zdroje|Datový proud|
   |Formát serializace událostí|Ostatní (Protobuf, XML, proprietární...)|
   |Název sestavení CSharp|ProtobufDeserializer.dll|
   |Název třídy|MessageBodyProto.MessageBodyDeserializer|
   |Cesta k místnímu vstupnímu souboru|< cestu k souboru pro stažený ukázkový vstupní soubor protobuf>|

## <a name="execute-the-stream-analytics-job"></a>Provedení úlohy Stream Analytics

1. Otevřete **soubor Script.asaql** a vyberte **spustit místně**.

2. Výsledky sledujte ve **výsledcích místního spuštění služby Stream Analytics**.

Úspěšně jste implementovali vlastní deserializátor pro úlohu Stream Analytics! V tomto kurzu jste testovali vlastní deserializer místně. Pro vaše skutečná data byste správně nakonfigurovali vstup a výstup. Pak odešlete úlohu do Azure z Visual Studia a spusťte svou úlohu v cloudu pomocí vlastního deserializátoru, který jste právě implementovali.

## <a name="debug-your-deserializer"></a>Ladění deserializátoru

Deserializátor .NET můžete ladit místně stejným způsobem, jakým ladíte standardní kód .NET. 

1. Přidejte do funkce zarážky.

2. Stisknutím klávesy **F5** spusťte ladění. Program se podle očekávání bude zastavovat na zarážkách.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto kurzu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak implementovat vlastní deserializátor .NET pro serializaci vstupu vyrovnávací paměti protokolu. Další informace o vytváření vlastních deserializerů najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Vytvoření různých deserializátorů .NET pro úlohy Azure Stream Analytics](custom-deserializer-examples.md)
