---
title: Dotazování na data Avro pomocí Azure Data Lake Analytics | Microsoft Docs
description: Vlastnosti textu zprávy slouží ke směrování telemetrie zařízení do úložiště objektů BLOB a dotazování na data formátu Avro, která se zapisují do úložiště objektů BLOB.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: f01cd6cd8c5f89206888f5123f7b14a524c9f40f
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94748471"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Dotazování na data Avro pomocí Azure Data Lake Analytics

Tento článek popisuje, jak pomocí dotazu na Avro data efektivně směrovat zprávy z Azure IoT Hub do služeb Azure. [Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje filtrovat data pomocí bohatých dotazů na základě vlastností zpráv, textu zprávy, značek a dvojitých vlastností zařízení. Další informace o možnostech dotazování ve směrování zpráv najdete v článku o [syntaxi dotazu směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

Je to proto, že když Azure IoT Hub směruje zprávy do služby Azure Blob Storage, ve výchozím nastavení IoT Hub zapisuje obsah ve formátu Avro, který má vlastnost tělo zprávy i vlastnost Message. Formát Avro se nepoužívá pro žádné jiné koncové body. I když je formát Avro skvělý pro zachování dat a zpráv, je to výzva k jeho použití k dotazování na data. Ve srovnání se formátem JSON nebo CSV je mnohem snazší dotazování na data. IoT Hub nyní podporuje zápis dat do úložiště objektů BLOB ve formátu JSON a také AVRO.

Další informace najdete v tématu [použití Azure Storage jako koncového bodu směrování](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

Chcete-li vyřešit nerelační potřeby velkých objemů dat a formáty a překonat tuto výzvu, můžete použít mnoho vzorů velkých objemů dat pro transformaci a škálování dat. Jednou ze vzorů je "platí pro dotaz", je Azure Data Lake Analytics, což je zaměření tohoto článku. I když můžete snadno spustit dotaz v Hadoop nebo jiných řešeních, Data Lake Analytics je často vhodnější pro tento přístup typu "Plaťte na dotaz".

Pro Avro v U-SQL je k dispozici "extraktor". Další informace najdete v tématu [Avro příklad u-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Dotazování a export Avro dat do souboru CSV

V této části se dotazuje na data Avro a exportují je do souboru CSV v úložišti objektů BLOB v Azure, i když můžete data snadno umístit do jiných úložišť nebo úložišť dat.

1. Nastavte Azure IoT Hub pro směrování dat do koncového bodu služby Azure Blob Storage pomocí vlastnosti v těle zprávy pro výběr zpráv.

   ![Oddíl Custom Endpoints](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Pravidla směrování](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Další informace o nastavení tras a vlastních koncových bodech najdete v tématu [směrování zpráv pro službu IoT Hub](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Ujistěte se, že vaše zařízení má kódování, typ obsahu a požadovaná data v rámci vlastností nebo textu zprávy, jak je odkazováno v dokumentaci k produktu. Při zobrazení těchto atributů v Device Explorer, jak je znázorněno zde, můžete ověřit, že jsou správně nastaveny.

   ![Podokno data centra událostí](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Nastavte instanci Azure Data Lake Store a instanci Data Lake Analytics. Azure IoT Hub nesměruje na instanci Data Lake Store, ale instance Data Lake Analytics vyžaduje jednu instanci.

   ![Instance Data Lake Store a Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. V Data Lake Analytics nakonfigurujte úložiště objektů BLOB v Azure jako další úložiště, stejné úložiště objektů blob, na které Azure IoT Hub směruje data.

   ![Podokno zdroje dat](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Jak je popsáno v [příkladu Avro U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), budete potřebovat čtyři soubory DLL. Nahrajte tyto soubory do umístění ve vaší instanci Data Lake Store.

   ![Čtyři nahrané soubory DLL](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. V aplikaci Visual Studio vytvořte projekt U-SQL.

   ![Vytvoření projektu U-SQL](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Vložte obsah následujícího skriptu do nově vytvořeného souboru. Upravte tři zvýrazněné oddíly: účet Data Lake Analytics, přidružené cesty k souborům DLL a správnou cestu k vašemu účtu úložiště.

   ![Tři oddíly, které mají být upraveny](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Skutečný skript U-SQL pro jednoduchý výstup do souboru CSV:

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

    Spuštění následujícího skriptu trvalo Data Lake Analytics pět minut, což bylo omezeno na 10 analytických jednotek a zpracovaných souborů 177. Výsledek se zobrazí ve výstupu souboru CSV, který je zobrazený na následujícím obrázku:

    ![Výsledky výstupu do souboru CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Výstup převedený na soubor CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Chcete-li kód JSON analyzovat, pokračujte krokem 8.

8. Většina zpráv IoT je ve formátu JSON. Přidáním následujících řádků můžete analyzovat zprávu do souboru JSON, který umožňuje přidat klauzule WHERE a výstup pouze potřebná data.

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

    Výstup zobrazí sloupec pro každou položku v `SELECT` příkazu.

    ![Výstup zobrazující sloupec pro každou položku](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí dotazu na Avro data efektivně směrovat zprávy z Azure IoT Hub do služeb Azure.

Příklady kompletních řešení, která používají IoT Hub, najdete v [dokumentaci k akcelerátorům řešení Azure IoT](../iot-accelerators/index.yml).

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).

Další informace o směrování zpráv v IoT Hub najdete v tématu [posílání a přijímání zpráv pomocí IoT Hub](iot-hub-devguide-messaging.md).
