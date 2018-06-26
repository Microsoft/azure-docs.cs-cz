---
title: Dotazování Avro dat pomocí Azure Data Lake Analytics | Microsoft Docs
description: Pomocí vlastnosti text zprávy route zařízení telemetrie do úložiště objektů Blob a zadávat dotazy na data formát Avro, které jsou zapsány do úložiště objektů Blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 08aed809184cbb65d632e1fb6f4b9bd25747e349
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751070"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Avro dotaz na data pomocí Azure Data Lake Analytics

Tento článek popisuje, jak dotazovat data Avro efektivně směrovat zprávy ze služby Azure IoT Hub ke službám Azure. Jak jsme oznámili ve v příspěvku blogu [Směrování zprávy v Azure IoT Hub: nyní se směrováním na text zprávy], podporuje IoT Hub směrování na vlastnosti nebo textu zprávy. Další informace najdete v tématu [směrování na obsah zpráv][Routing on message bodies]. 

Na výzvu, při Azure IoT Hub směrování zpráv do úložiště objektů Azure Blob, už IoT Hub zapíše obsah ve formátu Avro, který má vlastnost tělo zprávy i vlastnost zprávy. IoT Hub podporuje pouze ve formátu data Avro zápisu dat do úložiště objektů Blob a tento formát se nepoužije pro všechny ostatní koncové body. Další informace najdete v tématu [při použití Azure Storage kontejnery][When using Azure storage containers]. Formát Avro je skvělá pro zachování dat a zprávy, sice může být obtížné ji použít k dotazování na data. Porovnání je mnohem snazší pro dotazování na data ve formátu JSON nebo CSV.

Na adrese nerelační potřebám velkých objemů dat a formáty a tento problém vyřešili, můžete použít řadu vzory velkých objemů dat pro transformaci i škálování dat. Jedním ze vzorů, "platba za dotaz," je Azure Data Lake Analytics, která je hlavním cílem tohoto článku. I když můžete snadno provést dotaz v Hadoop nebo jiných řešeních, Data Lake Analytics je často vhodnější pro tento přístup "platba za dotaz". 

Je "Extraktor" pro Avro v U-SQL. Další informace najdete v tématu [Příklad Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Dotaz a exportu dat Avro do souboru CSV
V této části dotaz na Avro data a exportovat je do souboru CSV v úložišti objektů Blob v Azure, i když může snadno umístění dat v jiných úložišť úložiště nebo data.

1. Nastavení Azure IoT Hub pro data trasy pro koncový bod úložiště objektů Blob v Azure pomocí vlastnosti v textu zprávy zvolit zprávy.

    ![V části "Vlastní koncové body"][img-query-avro-data-1a]

    ![Příkaz trasy][img-query-avro-data-1b]

2. Zajistěte, aby vaše zařízení kódování, typu obsahu a požadovaná data ve vlastnostech nebo text zprávy, jak je uvedeno v dokumentaci k produktu. Při zobrazení těchto atributů v Průzkumníku zařízení, jak je vidět Tady můžete ověřit, že jsou správně nastaveny.

    ![V podokně Data události rozbočovače][img-query-avro-data-2]

3. Nastavte instanci Azure Data Lake Store a instanci Data Lake Analytics. Azure IoT Hub není směrovat do Data Lake Store instanci, ale instance Data Lake Analytics vyžaduje jednu.

    ![Data Lake Store a Data Lake Analytics instancí][img-query-avro-data-3]

4. V Data Lake Analytics nakonfigurujte jako další úložiště, stejné úložiště objektů Blob, který směruje Azure IoT Hub dat do úložiště objektů Blob v Azure.

    ![V podokně "Zdroje dat"][img-query-avro-data-4]
 
5. Jak je popsáno v [Příklad Avro U-SQL], budete potřebovat čtyři knihovny DLL. Nahrajte tyto soubory do umístění v instanci Data Lake Store.

    ![Čtyři nahrané soubory knihoven DLL][img-query-avro-data-5] 

6. V sadě Visual Studio vytvořte projekt U-SQL.
 
    ![Vytvoření projektu U-SQL][img-query-avro-data-6]

7. Umožňuje vložte obsah následujícího skriptu do nově vytvořeného souboru. Upravit třech částech vyznačených: účtu Data Lake Analytics, přidružené cesty k souborům DLL a správnou cestu k účtu úložiště.
    
    ![Tři části má být změněn][img-query-avro-data-7a]

    Skutečné skript U-SQL pro jednoduché výstup do souboru CSV:
    
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
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Trvalo Data Lake Analytics spusťte následující skript, která byla omezena na 10 analytické jednotky a zpracovat 177 soubory pět minut. Výsledek je zobrazené ve výstupu souboru CSV, který se zobrazí na následujícím obrázku:
    
    ![Výsledky výstup do souboru CSV][img-query-avro-data-7b]

    ![Výstup převeden do souboru CSV][img-query-avro-data-7c]

    Analyzovat ve formátu JSON, přejděte ke kroku 8.
    
8. Většina IoT zprávy jsou ve formátu JSON. Přidáním následující řádky můžete analyzovat zprávy do souboru JSON, která vám umožní přidat klauzulích WHERE a výstup pouze požadovaná data.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
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

    Výstup zobrazuje sloupec pro každou položku v `SELECT` příkaz. 
    
    ![Výstup zobrazuje sloupec pro každou položku][img-query-avro-data-8]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak k dotazování dat Avro efektivně směrovat zprávy ze služby Azure IoT Hub ke službám Azure.

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub naleznete v tématu [akcelerátoru řešení Azure IoT vzdálené monitorování][lnk-iot-sa-land].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

Další informace o směrování zpráv do služby IoT Hub, najdete v části [odesílat a přijímat zprávy službou IoT Hub][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Směrování zprávy v Azure IoT Hub: nyní se směrováním na text zprávy]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[Příklad Avro U-SQL]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
