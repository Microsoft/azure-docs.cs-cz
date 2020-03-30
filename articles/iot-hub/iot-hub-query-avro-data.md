---
title: Dotaz na data Avro pomocí Azure Data Lake Analytics | Dokumenty společnosti Microsoft
description: Pomocí vlastností textu zprávy můžete směrovat telemetrii zařízení do úložiště objektů Blob a dotazovat se na data formátu Avro, která se zapisují do úložiště objektů Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605622"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Dotaz na data Avro pomocí Azure Data Lake Analytics

Tento článek popisuje, jak se dotazovat na data Avro efektivně směrovat zprávy z Azure IoT Hub do služeb Azure. [Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje filtrovat data pomocí rozšířených dotazů na základě vlastností zprávy, textu zprávy, značek dvojčete zařízení a vlastností dvojčete zařízení. Další informace o možnostech dotazování ve službě Směrování zpráv naleznete v článku o [syntaxi směrovacího dotazu zpráv](iot-hub-devguide-routing-query-syntax.md).

Výzvou bylo, že když Azure IoT Hub směruje zprávy do úložiště objektů Blob Azure, ve výchozím nastavení IoT Hub zapíše obsah ve formátu Avro, který má vlastnost tělo zprávy a vlastnost zprávy. Formát Avro se nepoužívá pro žádné jiné koncové body. Přestože formát Avro je skvělý pro uchovávání dat a zpráv, je výzvou jej použít k dotazování dat. Ve srovnání s formátem JSON nebo CSV je mnohem jednodušší pro dotazování dat. IoT Hub teď podporuje zápis dat do úložiště objektů Blob v JSON i AVRO.

Další informace najdete [v tématu použití Služby Azure Storage jako koncového bodu směrování](iot-hub-devguide-messages-d2c.md#azure-storage).

Chcete-li řešit nerelační potřeby a formáty velkých objemů dat a překonat tuto výzvu, můžete použít mnoho vzorů velkých objemů dat pro transformaci i škálování dat. Jeden ze vzorů ,pay za dotaz", je Azure Data Lake Analytics, který je v centru tohoto článku. I když můžete snadno spustit dotaz v Hadoop nebo jiných řešeních, Data Lake Analytics je často vhodnější pro tento přístup "platit za dotaz".

K dispozici je "extraktor" pro Avro v U-SQL. Další informace naleznete [v příkladu U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Dotaz a export dat Avro do souboru CSV

V této části dotazujete data Avro a exportujete je do souboru CSV v úložišti objektů Blob Azure, i když můžete snadno umístit data do jiných úložišť nebo úložišť dat.

1. Nastavte Azure IoT Hub pro směrování dat do koncového bodu úložiště objektů Blob Azure pomocí vlastnosti v textu zprávy k výběru zpráv.

   ![Část "Vlastní koncové body"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Pravidla směrování](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Další informace o nastavení tras a vlastních koncových bodů najdete v [tématu Směrování zpráv pro centrum IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Ujistěte se, že vaše zařízení má kódování, typ obsahu a potřebná data ve vlastnostech nebo v textu zprávy, jak je uvedeno v dokumentaci k produktu. Při zobrazení těchto atributů v Průzkumníkovi zařízení, jak je znázorněno zde, můžete ověřit, že jsou nastaveny správně.

   ![Podokno Data centra událostí](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Nastavte instanci Azure Data Lake Store a instanci Data Lake Analytics. Azure IoT Hub nesměruje na instanci úložiště datového jezera, ale instance Data Lake Analytics ji vyžaduje.

   ![Instance Úložiště datových jezer a Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. V analýza datového jezera nakonfigurujte úložiště objektů blob Azure jako další úložiště, stejné úložiště objektů blob, do kterého azure IoT Hub směruje data.

   ![Podokno Zdroje dat](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Jak je popsáno v [příkladu U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), budete potřebovat čtyři soubory DLL. Nahrajte tyto soubory do umístění v instanci Úložiště datového jezera.

   ![Čtyři nahrané soubory DLL](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. V sadě Visual Studio vytvořte projekt U-SQL.

   ! Vytvoření projektu U-SQL](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Vložte obsah následujícího skriptu do nově vytvořeného souboru. Upravte tři zvýrazněné části: účet Data Lake Analytics, přidružené cesty k souborům DLL a správnou cestu pro váš účet úložiště.

   ![Tři oddíly, které mají být upraveny](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Skutečný u-SQL skript pro jednoduchý výstup do souboru CSV:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Data Lake Analytics trvalo pět minut, než spouštěla následující skript, který byl omezen na 10 analytických jednotek a zpracoval 177 souborů. Výsledek je zobrazen ve výstupu souboru CSV, který je zobrazen na následujícím obrázku:

    ![Výsledky výstupu do souboru CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Výstup převedený do souboru CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Chcete-li analyzovat JSON, pokračujte krokem 8.

8. Většina zpráv IoT je ve formátu souboru JSON. Přidáním následujícířádky, můžete analyzovat zprávu do souboru JSON, který umožňuje přidat WHERE klauzule a výstup pouze potřebná data.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    Výstup zobrazí sloupec pro každou `SELECT` položku v příkazu.

    ![Výstup zobrazující sloupec pro každou položku](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak se dotazovat na data Avro efektivně směrovat zprávy z Azure IoT Hub do služeb Azure.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v [dokumentaci k akcelerátorům řešení Azure IoT](/azure/iot-accelerators).

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).

Další informace o směrování zpráv v centru IoT Hub najdete v tématu [Odesílání a přijímání zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).
