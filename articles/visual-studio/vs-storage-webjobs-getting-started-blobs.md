---
title: Začínáme s blob storage a Visual Studio připojené služby (webové úlohy projekty) | Dokumentace Microsoftu
description: Jak začít používat úložiště objektů Blob v projektu úlohy WebJob po připojení ke službě Azure storage pomocí sady Visual Studio připojené služby.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 798073d5510e50ead35ed118b03e981d133cd32a
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058660"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s Azure Blob storage a Visual Studio připojené služby (webové úlohy projektů)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje ukázky kódu C#, které ukazují, jak aktivovat proces při vytvoření nebo aktualizaci objektu blob Azure. Ukázky kódu použijte [sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verzi 1.x. Po přidání účtu úložiště do projektu úlohy WebJob pomocí sady Visual Studio **přidání připojené služby** dialogového okna, je nainstalovaná odpovídající balíček NuGet pro Azure Storage, do projektu, jsou přidány příslušné odkazy na rozhraní .NET a připojovací řetězce pro účet úložiště se aktualizují v souboru App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak aktivovat funkci, když se vytvoří nebo aktualizuje objekt blob
Tato část ukazuje způsob použití **BlobTrigger** atribut.

 **Poznámka:** sada WebJobs SDK kontroluje soubory protokolů, které chcete sledovat pro nové nebo změněné objekty BLOB. Tento proces je ze své podstaty pomalé. funkce nemusí se aktivují až několik minut nebo i déle po vytvoření objektu blob.  Pokud vaše aplikace potřebuje pro zpracování objektů BLOB okamžitě, doporučujeme k vytvoření zprávy fronty při vytvoření objektu blob a použít **QueueTrigger** atribut místo **BlobTrigger** atribut na funkci, která zpracovává objekt blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Jeden zástupný symbol pro název objektu blob s příponou
Zkopíruje text objekty BLOB, které se zobrazují v následujícím příkladu kódu *vstupní* kontejneru *výstup* kontejneru:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktor atributu má parametr řetězec, který určuje název kontejneru a zástupný symbol pro název objektu blob. V tomto příkladu, pokud objekt blob s názvem *Blob1.txt* se vytvoří v *vstupní* kontejneru, funkce vytvoří objekt blob s názvem *Blob1.txt* v *výstup* kontejneru.

Vzor názvu s zástupný název objektu blob, můžete určit, jak je znázorněno v následujícím příkladu kódu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Tento kód zkopíruje jenom objekty BLOB, které mají názvy začínají na "původní-". Například *původní Blob1.txt* v *vstupní* kontejneru je zkopírován do *kopírování Blob1.txt* v *výstup* kontejneru.

Pokud je třeba zadat vzor názvu pro názvy objektů blob, které mají v názvu složených závorek, dvakrát složených závorek. Například, pokud chcete najít objekty BLOB v *imagí* kontejner, který mají názvy takto:

        {20140101}-soundfile.mp3

používá se pro váš model:

        images/{{20140101}}-{name}

V tomto příkladu *název* hodnotu zástupného symbolu by *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Zástupné symboly název a příponu samostatných objektů blob
Následující vzorový kód změní přípona souboru jako zkopíruje objekty BLOB, které se zobrazují v *vstupní* kontejneru *výstup* kontejneru. Kód protokoluje rozšíření *vstupní* objektů blob a nastaví rozšíření *výstup* objektu blob do *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, které můžete vytvořit vazbu k objektům BLOB
Můžete použít **BlobTrigger** atribut na následující typy:

* **řetězec**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Jiné typy rekonstruovány [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Pokud budete chtít pracovat přímo s účtem úložiště Azure, můžete také přidat **CloudStorageAccount** parametr do podpisu metody.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Získání obsahu objektů blob text vazbou na řetězec
Pokud se očekává, objekty BLOB text, **BlobTrigger** lze použít u **řetězec** parametru. Následující vzorový kód vytvoří vazbu blob text, který má **řetězec** parametr s názvem **logMessage**. Funkce používá tento parametr zapsat obsah objektu blob do řídicího panelu WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Začínáme s použitím ICloudBlobStreamBinder serializovat obsah objektu blob
Následující vzorový kód používá třídu, která implementuje **ICloudBlobStreamBinder** povolit **BlobTrigger** atribut pro vytvoření vazby k objektu blob **WebImage** typu.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

**WebImage** vazební kód je k dispozici v **WebImageBinder** třídu odvozenou od **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Způsob zpracování nezpracovatelných objekty BLOB
Když **BlobTrigger** funkce selže, sady SDK volá znovu, v případě, chyba byla způsobená o přechodnou chybu. Pokud chyba je způsobená obsah objektu blob, funkce selže pokaždé, když se ho pokusí zpracovat objektu blob. Ve výchozím nastavení sady SDK volá funkci až 5krát pro daný objekt blob. Když pátého selže, sady SDK přidá zprávu do fronty s názvem *webjobs. blobtrigger poison*.

Maximální počet opakování je možné konfigurovat. Stejné **MaxDequeueCount** nastavení se používá pro zpracování nezpracovatelných objektů blob a zpracování fronty nezpracovatelných zpráv.

Pro objekty BLOB nezpracovatelná zpráva fronty je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu *{název úlohy WebJob}*. Funkce. *{Název funkce}*, například: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* Značka ETag (identifikátor verze objektů blob, například: "0x8D1DC6E70A277EF")

V následující ukázce kódu **CopyBlob** funkce má kód, který způsobí, že selhání pokaždé, když je volána. Po volání sady SDK pro maximální počet opakovaných pokusů, se vytvoří zprávu ve frontě nezpracovatelných objektů blob a tuto zprávu zpracuje **LogPoisonBlob** funkce.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Sada SDK automaticky deserializuje zprávy JSON. Tady je **PoisonBlobMessage** třídy:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Objekt blob algoritmu cyklického dotazování
Sada WebJobs SDK kontroluje všechny kontejnery, které jsou určené **BlobTrigger** atributy při spuštění aplikace. V účtu úložiště velké tato kontrola může nějakou dobu trvat, takže pravděpodobně bude nějakou dobu předtím, než jsou nalezeny nové objekty BLOB a **BlobTrigger** funkcí se spustí.

Po spuštění aplikace, detekovat nové nebo změněné objekty BLOB, sada SDK pravidelně čte z protokoly úložiště objektů blob. Protokoly objektů blob jsou ukládány do vyrovnávací paměti a jenom fyzicky zapisují se každých 10 minut, nebo tak, takže může být poměrně dlouhodobému výpadku po objekt blob se vytvoří nebo aktualizuje před odpovídajícími **BlobTrigger** funkce provede.

Dojde k výjimce pro objekty BLOB, které vytvoříte pomocí **Blob** atribut. Když se sadou WebJobs SDK vytvoří nový objekt blob, předá nový objekt blob okamžitě na žádné odpovídající **BlobTrigger** funkce. Proto pokud máte řetězec objektu blob vstupy a výstupy, sady SDK můžete zpracovat efektivně. Pokud chcete s nízkou latencí s funkcí pro zpracování objektů BLOB, které jsou vytvořeny nebo aktualizovány jiným způsobem objekt blob, doporučujeme používat, ale **QueueTrigger** spíše než **BlobTrigger**.

### <a name="blob-receipts"></a>Potvrzení objektu BLOB
Sada WebJobs SDK, zajišťuje, že žádné **BlobTrigger** funkce volána více než jednou pro stejný objekt blob nová nebo aktualizovaná. Dosahuje toho díky udržování *blob příjmy* aby bylo možné zjistit, pokud verze daného objektu blob byla zpracována.

Potvrzení objektu BLOB se ukládají v kontejneru nazvaném *azure – webjobs – hostitelé* v určeném AzureWebJobsStorage připojovací řetězec účtu úložiště Azure. Potvrzení objektu blob obsahuje následující informace:

* Funkce, která byla volána pro objekt blob ("*{název úlohy WebJob}*. Funkce. *{Název funkce}*", například:"WebJob1.Functions.CopyBlob")
* Název kontejneru
* Typ objektu blob ("BlockBlob" nebo "PageBlob")
* Název objektu blob
* Značka ETag (identifikátor verze objektů blob, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, můžete ručně odstranit potvrzení tohoto objektu blob z objektu blob *azure – webjobs – hostitelé* kontejneru.

## <a name="related-topics-covered-by-the-queues-article"></a>Související témata, které jsou uvedené v článku fronty
Informace o tom, jak zpracovat zpracování objektu blob aktivuje zpráv fronty, nebo sada WebJobs SDK scénáře netýkající se konkrétních objektů blob zpracování, projděte si téma [tom, jak pomocí Azure queue storage se sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Související témata v tomto článku zahrnují následující:

* Asynchronní funkce
* Více instancí
* Řádné vypnutí
* Použití sady WebJobs SDK atributů v těle funkce
* Nastavení sady SDK připojovacích řetězců v kódu.
* Nastavení hodnot pro WebJobs SDK parametry konstruktoru v kódu
* Konfigurace **MaxDequeueCount** pro zpracování nezpracovatelných objektů blob.
* Ruční aktivace funkce
* Zápis protokolů

## <a name="next-steps"></a>Další postup
Tento článek poskytuje ukázek kódu, které ukazují, jak zvládnout běžné scénáře pro práci s objekty BLOB Azure. Další informace o tom, jak používat Azure WebJobs a sada WebJobs SDK najdete v tématu [prostředků dokumentace Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

