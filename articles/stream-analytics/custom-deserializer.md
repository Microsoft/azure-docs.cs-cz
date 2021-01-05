---
title: Kurz – vlastní deserializace rozhraní .NET pro cloudové úlohy Azure Stream Analytics
description: Tento kurz ukazuje, jak vytvořit vlastní deserializátor .NET pro cloudovou úlohu Azure Stream Analytics pomocí sady Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 2353d15707fe215bfcab7912f2a9c598c4af7e49
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822008"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Kurz: vlastní deserializace rozhraní .NET pro Azure Stream Analytics

Azure Stream Analytics má [integrovanou podporu pro tři formáty dat](stream-analytics-parsing-json.md): JSON, CSV a Avro. Pomocí vlastních deserializátorů .NET můžete číst data z jiných formátů, jako je například [vyrovnávací paměť protokolu](https://developers.google.com/protocol-buffers/), [vázání](https://github.com/Microsoft/bond) a další uživatelsky definované formáty pro úlohy cloudu i Edge.

Tento kurz ukazuje, jak vytvořit vlastní deserializátor .NET pro cloudovou úlohu Azure Stream Analytics pomocí sady Visual Studio. Informace o tom, jak vytvořit deserializátory .NET v Visual Studio Code, najdete v tématu [Vytvoření deserializátorů .NET pro úlohy Azure Stream Analytics v Visual Studio Code](visual-studio-code-custom-deserializer.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte vlastní deserializaci pro vyrovnávací paměť protokolu.
> * Vytvořte úlohu Azure Stream Analytics v aplikaci Visual Studio.
> * Nakonfigurujte svou úlohu Stream Analytics, aby používala vlastní deserializaci.
> * Spustit úlohu Stream Analytics místně a otestovat a ladit vlastní deserializaci.


## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Nainstalujte [Visual studio 2019 (doporučeno)](https://www.visualstudio.com/downloads/) nebo [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/). Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express není podporovaná. 

* [Nainstalujte nástroje pro Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md) nebo aktualizujte na nejnovější verzi. 

* Otevřete **Průzkumníka cloudu** v aplikaci Visual Studio a přihlaste se ke svému předplatnému Azure.

* Vytvořte ve svém účtu Azure Storage kontejner.
Kontejner, který vytvoříte, se použije k uložení prostředků souvisejících s vaší úlohou Stream Analytics. Pokud už máte účet úložiště s existujícími kontejnery, můžete je použít. Pokud ne, budete muset [vytvořit nový kontejner](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Vytvoření vlastního odserializace

1. Otevřete Visual Studio a vyberte **soubor > nový > projekt**. Vyhledejte **Stream Analytics** a vyberte **Azure Stream Analytics vlastní projekt deserializace (.NET)**. Dejte projektu název, jako je **Protobuf deserializace**.

   ![Vytvořit projekt standardní knihovny tříd pro Visual Studio dotnet Standard](./media/custom-deserializer/create-dotnet-library-project.png)

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **deserializátor Protobuf** a v nabídce vyberte **Spravovat balíčky NuGet** . Pak nainstalujte balíčky NuGet **Microsoft. Azure. StreamAnalytics** a **Google. Protobuf** .

3. Do projektu přidejte [třídu MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) a [třídu MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) .

4. Sestavte projekt **deserializace Protobuf** .

## <a name="add-an-azure-stream-analytics-project"></a>Přidat Azure Stream Analytics projekt

1. V Průzkumník řešení klikněte pravým tlačítkem na řešení **deserializátor Protobuf** a vyberte **Přidat > nový projekt**. V části **Azure Stream Analytics > Stream Analytics** vyberte **Azure Stream Analytics Application (aplikace**). Pojmenujte ji **ProtobufCloudDeserializer** a vyberte **OK**. 

2. Klikněte pravým tlačítkem na **odkazy** v rámci projektu **ProtobufCloudDeserializer** Azure Stream Analytics. V části **projekty** přidejte **Protobuf deserializaci**. Mělo by se automaticky vyplnit.

## <a name="configure-a-stream-analytics-job"></a>Konfigurace úlohy Stream Analytics

1. Dvakrát klikněte na **JobConfig.jszapnuto**. Použijte výchozí konfigurace s výjimkou následujících nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Prostředek nastavení globálního úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálních nastavení úložiště| < vaše předplatné >|
   |Účet úložiště nastavení globálního úložiště| < svého účtu úložiště >|
   |Prostředek nastavení úložiště vlastního kódu|Zvolit zdroj dat z aktuálního účtu|
   |Vlastní nastavení úložiště kódu účet úložiště|< svého účtu úložiště >|
   |Kontejner nastavení vlastního úložiště kódu|< kontejneru úložiště >|

2. V části **vstupy** dvakrát klikněte **Input.jsna**. Použijte výchozí konfigurace s výjimkou následujících nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Zdroj|Blob Storage|
   |Prostředek|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné|< vaše předplatné >|
   |Účet úložiště|< svého účtu úložiště >|
   |Kontejner|< kontejneru úložiště >|
   |Formát serializace události|Jiné (Protobuf, XML, proprietární...)|
   |Prostředek|Zatížení z odkazu na projekt ASA nebo CodeBehind|
   |Název sestavení CSharp|ProtobufDeserializer.dll|
   |Název třídy|MessageBodyProto.MessageBodyDeserializer|
   |Typ komprese události|Žádné|

3. Do souboru **script. asaql** přidejte následující dotaz.

   ```sql
   SELECT * FROM Input
   ```

4. Stáhněte si [vzorový vstupní soubor protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Ve složce **vstupy** klikněte pravým tlačítkem na **Input.jsna** a vyberte **Přidat místní vstup**. Pak dvakrát klikněte **local_Input.jsna** a nakonfigurujte následující nastavení:

   |Nastavení|Navrhovaná hodnota|
   |-------|---------------|
   |Alias pro vstup|Vstup|
   |Typ zdroje|Datový proud|
   |Formát serializace události|Jiné (Protobuf, XML, proprietární...)|
   |Název sestavení CSharp|ProtobufDeserializer.dll|
   |Název třídy|MessageBodyProto.MessageBodyDeserializer|
   |Cesta k místnímu vstupnímu souboru|< cestu k souboru pro stažený ukázkový vstupní soubor protobuf>|

## <a name="execute-the-stream-analytics-job"></a>Spustit úlohu Stream Analytics

1. Otevřete **skript. asaql** a vyberte **spustit místně**.

2. Sledujte výsledky v **Stream Analytics výsledky místního spuštění**.

Úspěšně jste implementovali vlastní deserializaci pro vaši úlohu Stream Analytics! V tomto kurzu jste otestovali vlastní deserializace místně. Pro vaše skutečná data jste správně nakonfigurovali vstup a výstup. Pak odešlete úlohu do Azure ze sady Visual Studio, abyste mohli spustit úlohu v cloudu s použitím vlastního odsériového nástroje, který jste právě implementovali.

## <a name="debug-your-deserializer"></a>Ladění odserializátoru

Deserializaci rozhraní .NET můžete ladit místně stejným způsobem jako ladění standardního kódu .NET.

1. Klikněte pravým tlačítkem myši na název projektu **ProtobufCloudDeserializer** a nastavte jej jako spouštěný projekt.

2. Přidejte do své funkce zarážky.

3. Stisknutím klávesy **F5** spusťte ladění. Program se podle očekávání bude zastavovat na zarážkách.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto kurzu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak implementovat vlastní deserializátory .NET pro serializaci vstupu vyrovnávací paměti protokolu. Další informace o vytváření vlastních deserializátorů najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Vytvoření různých deserializátorů .NET pro úlohy Azure Stream Analytics](custom-deserializer-examples.md)