---
title: Vytváření vlastních deserializátorů .NET pro Azure Stream Analytics cloudových úloh pomocí Visual Studio Code
description: Tento kurz ukazuje, jak vytvořit vlastní deserializátor .NET pro cloudovou úlohu Azure Stream Analytics pomocí Visual Studio Code.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 0f041910d1b02cc6d1fd48bfd773711c3361c0f5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822460"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Vytváření vlastních deserializátorů .NET pro Azure Stream Analytics v Visual Studio Code

Azure Stream Analytics má [integrovanou podporu pro tři formáty dat](stream-analytics-parsing-json.md): JSON, CSV a Avro. Pomocí vlastních deserializátorů .NET můžete číst data z jiných formátů, jako je například [vyrovnávací paměť protokolu](https://developers.google.com/protocol-buffers/), [vázání](https://github.com/Microsoft/bond) a další uživatelsky definované formáty pro cloudové úlohy.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Vlastní deserializace .NET v Visual Studio Code

Pomocí Visual Studio Code můžete vytvořit, otestovat a ladit vlastní odserializátory .NET pro cloudovou úlohu Azure Stream Analytics.

### <a name="prerequisites"></a>Požadavky

* Nainstalujte [sadu .NET Core SDK](https://dotnet.microsoft.com/download) a restartujte Visual Studio Code.

* V tomto [rychlém](quick-create-visual-studio-code.md) startu se dozvíte, jak pomocí Visual Studio Code vytvořit úlohu Stream Analytics.

### <a name="create-a-custom-deserializer"></a>Vytvoření vlastního odserializace

1. Otevřete terminál a spusťte následující příkaz pro vytvoření knihovny tříd .NET v Visual Studio Code pro vlastní deserializaci s názvem **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Otevřete adresář projektu **ProtobufDeserializer** a nainstalujte balíčky NuGet [Microsoft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) a [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/) .

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Do projektu přidejte [třídu MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) a [třídu MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) .

4. Sestavte projekt **ProtobufDeserializer** .

### <a name="add-an-azure-stream-analytics-project"></a>Přidat Azure Stream Analytics projekt

1. Otevřete Visual Studio Code a výběrem **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů. Pak zadejte ASA a vyberte **ASA: vytvořit nový projekt**. Pojmenujte ho **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Konfigurace úlohy Stream Analytics

1. Dvakrát klikněte na **JobConfig.jszapnuto**. Použijte výchozí konfigurace s výjimkou následujících nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Prostředek nastavení globálního úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálních nastavení úložiště| < vaše předplatné >|
   |Účet úložiště nastavení globálního úložiště| < svého účtu úložiště >|
   |CustomCodeStorage nastavení účtu úložiště|< svého účtu úložiště >|
   |Kontejner nastavení CustomCodeStorage|< kontejneru úložiště >|

2. V části **vstupy** složky otevřete **input.js**. Vyberte **Přidat živý vstup** a přidejte vstup z Azure Data Lake Storage Gen2 nebo BLOB Storage, zvolte **vybrat z předplatného Azure**. Použijte výchozí konfigurace s výjimkou následujících nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Název|Vstup|
   |Předplatné|< vaše předplatné >|
   |Účet úložiště|< svého účtu úložiště >|
   |Kontejner|< kontejneru úložiště >|
   |Typ serializace|Zvolit **vlastní**|
   |SerializationProjectPath|Vyberte možnost **zvolit cestu projektu knihovny** z CodeLens a vyberte projekt knihovny **ProtobufDeserializer** vytvořený v předchozím oddílu. Vyberte **sestavit projekt** a sestavte projekt.|
   |SerializationClassName|Pokud chcete automaticky vyplnit název třídy a cestu k DLL, vyberte **Vybrat třídu deserializace** z CodeLens.|
   |Název třídy|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Přidat vlastní vstup deserializace.":::

3. Do souboru **ProtobufCloudDeserializer. asaql** přidejte následující dotaz.

   ```sql
   SELECT * FROM Input
   ```

4. Stáhněte si [vzorový vstupní soubor protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Ve složce **vstupy** klikněte pravým tlačítkem na **input.jsna** a vyberte **Přidat místní vstup**. Pak dvakrát klikněte **local_input1.jsna** a použijte výchozí konfigurace, s výjimkou následujících nastavení.

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Vybrat cestu k místnímu souboru|Kliknutím na CodeLens vyberte < cestu k souboru pro stažený ukázkový protobuf vstupní soubor>|

### <a name="execute-the-stream-analytics-job"></a>Spustit úlohu Stream Analytics

1. Otevřete **ProtobufCloudDeserializer. asaql** a vyberte **spustit místně** z CodeLens a pak zvolte **použít místní vstup** z rozevíracího seznamu.

2. Sledujte výsledky na kartě **výsledky** v zobrazení diagramu úloh na pravé straně. Můžete také kliknout na kroky v diagramu úloh a zobrazit mezilehlé výsledky. Další podrobnosti najdete v tématu [ladění místně pomocí diagramu úloh](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Zkontroluje místní výsledek spuštění.":::

Úspěšně jste implementovali vlastní deserializaci pro vaši úlohu Stream Analytics! V tomto kurzu jste místně otestovali vlastní deserializace s místními vstupními daty. Můžete ho také otestovat [pomocí živého vstupu dat v cloudu](visual-studio-code-local-run-live-input.md). Pro spuštění úlohy v cloudu jste správně nakonfigurovali vstup a výstup. Pak odešlete úlohu do Azure z Visual Studio Code ke spuštění úlohy v cloudu s využitím vlastního odserializátoru, který jste právě implementovali.

### <a name="debug-your-deserializer"></a>Ladění odserializátoru

Deserializaci rozhraní .NET můžete ladit místně stejným způsobem jako ladění standardního kódu .NET. 

1. Přidání zarážek ve funkci .NET.

2. Klikněte na možnost **Spustit** z Visual Studio Code na řádku aktivity a vyberte možnost **vytvořit launch.jsv souboru**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Vytvořte spouštěcí soubor.":::

   Zvolte **ProtobufCloudDeserializer** a potom v rozevíracím seznamu **Azure Stream Analytics** .
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Vytvořte spouštěcí soubor 2.":::

   Upravte **launch.jsv** souboru tak, aby nahradil <ASAScript> . asaql pomocí ProtobufCloudDeserializer. asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Nakonfigurujte spouštěcí soubor.":::

3. Stisknutím klávesy **F5** spusťte ladění. Program se podle očekávání bude zastavovat na zarážkách. Tato funkce funguje pro místní vstup i pro živá vstupní data.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Ladit vlastní deserializaci.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto kurzu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak implementovat vlastní deserializátory .NET pro serializaci vstupu vyrovnávací paměti protokolu. Další informace o vytváření vlastních deserializátorů najdete v následujícím článku:

> [!div class="nextstepaction"]
> * [Vytvoření různých deserializátorů .NET pro úlohy Azure Stream Analytics](custom-deserializer-examples.md)
> * [Testování Azure Stream Analytics úloh místně pomocí živého vstupu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)