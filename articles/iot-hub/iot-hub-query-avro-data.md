---
title: Dotaz Avro dat pomocí Azure Data Lake Analytics | Microsoft Docs
description: Pomocí vlastnosti text zprávy na zařízení telemetrie trasy úložiště objektů blob a zadávat dotazy na data formát Avro zapisovat do úložiště objektů blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34727590"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Avro dotaz na data pomocí Azure Data Lake Analytics

Tento článek je o tom, jak Avro dotaz na data pro efektivní směrování zpráv ze služby Azure IoT Hub ke službám Azure. Následující blogovém příspěvku –[Směrování zprávy v Azure IoT Hub: nyní se směrováním na text zprávy], podporuje IoT Hub směrování na vlastnosti nebo textu zprávy. Viz také [směrování na obsah zpráv][Routing on message bodies]. 

Na výzvu, při Azure IoT Hub směrování zpráv do úložiště objektů blob, už IoT Hub zapíše obsah ve formátu Avro, který má tělo zprávy a vlastnosti zprávy. Upozorňujeme, že Centrum IoT podporuje pouze zápis dat do úložiště objektů blob v formát Avro dat a tento formát se nepoužije pro všechny ostatní koncové body. V tématu [při použití Azure Storage kontejnery][When using Azure storage containers]. Formát Avro je skvělá pro uchovávání dat nebo zprávy, je obtížné pro dotazy na data. Porovnání je mnohem snazší pro dotazování na data ve formátu JSON nebo CSV.

Chcete-li tento problém vyřešit, můžete mnoho velkých objemů dat vzory pro převod a škálování dat řeší potřeby nerelační velkých objemů dat a formáty. Jedním ze vzorů, "platíte za dotazu" vzor, je Azure Data Lake Analytics (ADLA). Je fokus v tomto článku. Když dotaz mohl snadno spustit v Hadoop nebo jiných řešeních, ADLA je často vhodnější pro tento přístup "platba za dotaz". Je "Extraktor" pro Avro v U-SQL. V tématu [Příklad Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Dotaz a exportu dat Avro do souboru CSV
V části vás provede dotazování na Avro data a export do souboru CSV v úložišti objektů Blob Azure, i když může snadno umístění dat v jiných úložišť úložiště nebo data.

1. Nastavte Azure IoT Hub pro data trasy pro koncový bod Azure Blob Storage pomocí vlastnosti v textu zprávy zvolit zprávy.

    ![Snímek obrazovky pro krok 1a][img-query-avro-data-1a]

    ![Snímek obrazovky pro krok 1b][img-query-avro-data-1b]

2. Zkontrolujte, že vaše zařízení má kódování, typu obsahu a požadovaná data ve vlastnostech nebo textu zprávy, jak je uvedeno v dokumentaci k produktu. Při zobrazení v Průzkumníku zařízení (viz níže), můžete ověřit, jestli jsou správně nastavené těchto atributů.

    ![Snímek obrazovky v kroku 2][img-query-avro-data-2]

3. Nastavení služby Azure Data Lake Store (ADLS) a instanci Azure Data Lake Analytics. Zatímco Azure IoT Hub není směrovat do Azure Data Lake Store, ADLA vyžaduje jeden.

    ![Snímek obrazovky pro krok 3][img-query-avro-data-3]

4. V ADLA nakonfigurujte jako další úložiště, stejné úložiště objektů Blob, který směruje Azure IoT Hub dat do úložiště objektů Blob Azure.

    ![Snímek obrazovky kroku 4][img-query-avro-data-4]
 
5. Jak je popsáno v [Příklad Avro U-SQL], jsou 4 knihovny DLL, které jsou potřeba.  Nahrajte tyto soubory do umístění, do vaší ADLS.

    ![Snímek obrazovky v kroku 5][img-query-avro-data-5] 

6. V sadě Visual Studio vytvořte projekt U-SQL
 
    ![Snímek obrazovky kroku 6][img-query-avro-data-6]

7. Zkopírujte obsah následujícího skriptu a vložte jej do nově vytvořeného souboru. Úprava 3 zvýrazněné části: účtu ADLA cesty přidružené knihovny DLL a správnou cestu k účtu úložiště.
    
    ![Snímek obrazovky pro krok 7a][img-query-avro-data-7a]

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

    Spuštění skriptu vidíte níže, ADLA trvalo 5 minut po omezenou na 10 analytické jednotky a zpracovat 177 soubory, shrnutí výstup do souboru CSV.
    
    ![Snímek obrazovky pro krok 7b][img-query-avro-data-7b]

    Zobrazení výstupu, vidíte, že obsah Avro byl převeden do souboru CSV. Pokud chcete analyzovat ve formátu JSON, přejděte ke kroku 8.
    
    ![Snímek obrazovky pro krok 7c][img-query-avro-data-7c]

    
8. Většina IoT zprávy jsou ve formátu JSON.  Přidání následující řádky, můžete analyzovat zprávy do formátu JSON, abyste mohli přidat klauzulích WHERE a pouze výstup požadovaná data.

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

9. Zobrazení výstupu, uvidíte nyní sloupce pro každou položku v příkazu select. 
    
    ![Snímek obrazovky pro krok 8][img-query-avro-data-8]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak k dotazování dat Avro pro efektivní směrování zpráv ze služby Azure IoT Hub ke službám Azure.

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [akcelerátoru řešení Azure IoT vzdálené monitorování][lnk-iot-sa-land].

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
