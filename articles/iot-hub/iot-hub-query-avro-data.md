---
title: Dotazování na Avro data pomocí Azure Data Lake Analytics | Dokumentace Microsoftu
description: Pomocí vlastnosti text zprávy směrovat do úložiště objektů Blob v telemetrii zařízení a zadávat dotazy na data formátu Avro, která jsou zapsána do úložiště objektů Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: 69c890cfc3db04fe625ed7ad008f545c01844834
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993480"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Dotazování na Avro data pomocí Azure Data Lake Analytics

Tento článek popisuje, jak dotazovat data Avro můžete efektivně směrovat zprávy ze služby Azure IoT Hub ke službám Azure. [Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje filtrovat data s využitím bohaté dotazy na základě vlastnosti zprávy, text zprávy, značky dvojčat zařízení a vlastnosti dvojčete zařízení. Další informace o dotazování funkce směrování zpráv, najdete v článku o syntaxi dotazů směrování zpráv. 
<!--[Message Routing Query Syntax](iot-hub-devguide-routing-query-syntax.md). I don't have this article yet. -->

Před obrovskou výzvou – dochází po, který Azure IoT Hub provádí směrování zpráv do služby Azure Blob storage, služby IoT Hub zapíše obsah ve formátu Avro, který má do zprávy vlastnost text i vlastnost zprávy. IoT Hub pouze v datovém formátu Avro podporuje zápis dat do úložiště objektů Blob a tento formát se používá pro všechny ostatní koncové body. Další informace najdete v článku o používání kontejnerů Azure Storage. Ačkoli formát Avro se skvěle hodí pro zachování dat a zpráva, představuje výzvu ji používat k dotazování na data. Porovnání je mnohem jednodušší pro dotazování na data ve formátu JSON nebo CSV.

<!-- https://review.docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c?branch=pr-en-us-51566#azure-blob-storage  NEW LINK FOR 'WHEN USING STORAGE CONTAINERS' -->

Adres nerelačních potřebám velkých objemů dat a formátů a tento problém vyřešili, můžete použít mnoho vzorků velkých objemů dat pro transformaci a škálování data. Jednomu ze vzorů, "platba za dotazu", Azure Data Lake Analytics, který je hlavním cílem tohoto článku. I když v Hadoop nebo jiná řešení můžete snadno spustit dotaz, Data Lake Analytics je často vhodnější pro tento přístup "platba za dotaz". 

Není k dispozici "Extraktor" pro Avro v U-SQL. Další informace najdete v tématu [U-SQL Avro příklad](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Dotazování a Avro data exportovat do souboru CSV
V této části dotazování na Avro data a exportujte ho do souboru CSV v úložišti objektů Blob v Azure, i když může snadno umístit data v jiných úložištích nebo dat úložiště.

1. Nastavení služby Azure IoT Hub pro data trasy pro koncový bod úložiště objektů Blob v Azure pomocí vlastnosti v textu zprávy k výběru zprávy.

   ![V části "Vlastní koncové body"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Pravidla směrování](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Další informace o nastavení trasy a vlastní koncové body, naleznete v tématu [směrování zpráv pro službu IoT hub](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Ujistěte se, že vaše zařízení má kódování, typu obsahu a potřebná data ve vlastnosti nebo zprávy, jak je uvedeno v dokumentaci k produktu. Při zobrazení těchto atributů v Device Explorer, jak je znázorněno zde můžete ověřit, že jsou nastavené správně.

   ![V podokně Data centra událostí](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Nastavte instanci Azure Data Lake Store a Data Lake Analytics instance. Azure IoT Hub nesměruje do Data Lake Store instance, ale instance Data Lake Analytics vyžaduje.

   ![Data Lake Store a Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. V Data Lake Analytics nakonfigurujte jako další úložiště, stejné úložiště objektů Blob, který směruje Azure IoT Hub dat do úložiště objektů Blob v Azure.

   ![V podokně "Zdroje dat"](./media/iot-hub-query-avro-data/query-avro-data-4.png)
 
5. Jak je popsáno v [U-SQL Avro příklad](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), budete potřebovat čtyři knihovny DLL. Tyto soubory nahrajte do umístění ve vaší instanci Data Lake Store.

   ![Čtyři nahraných souborů knihovny DLL](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. V sadě Visual Studio vytvořte projekt U-SQL.
 
   ! Vytvoření project](./media/iot-hub-query-avro-data/query-avro-data-6.png) U-SQL

7. Vložte obsah následujícího skriptu do nově vytvořený soubor. Upravit tři zvýrazněné sekce: účtu Data Lake Analytics, cesty k souborům přidružené knihovny DLL a správnou cestu k účtu úložiště.
    
   ![Tři části má být upraven](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

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

    Data Lake Analytics, kterou trvalo 5 minut, spusťte následující skript, která byla omezena na 10 jednotek analýzy a zpracování 177 soubory. Výsledek se zobrazí ve výstupu souboru CSV, který se zobrazí na následujícím obrázku:
    
    ![Výsledky výstupu do souboru CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Výstup převodu do souboru CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Parsovat JSON, pokračujte krokem 8.
    
8. Většina IoT zprávy jsou ve formátu JSON. Přidejte následující řádky, je možné analyzovat zprávu do souboru JSON, která vám umožní přidat klauzule WHERE a výstup pouze potřebná data.

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

    Výstup zobrazuje sloupec pro každou položku v `SELECT` příkazu. 
    
    ![Výstup zobrazuje sloupec pro každou položku](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak zadávat dotazy na data Avro můžete efektivně směrovat zprávy ze služby Azure IoT Hub ke službám Azure.

Příklady kompletní řešení začátku do konce, které používají služby IoT Hub, najdete v článku [dokumentace k Akcelerátorům řešení Azure IoT](/azure/iot-accelerators).

Další informace o vývoji řešení s využitím služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md).

Další informace o směrování zpráv ve službě IoT Hub, najdete v článku [odesílání a příjem zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).
