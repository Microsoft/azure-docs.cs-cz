---
title: Začínáme s objektem blob storage a Visual Studio připojené služeb (webové úlohy projekty) | Microsoft Docs
description: Jak začít používat úložiště objektů Blob v projektu webové úlohy po připojení k Azure storage pomocí sady Visual Studio připojené služby.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 4800d6ad0482462b9eccb72de34de56e4f7c370c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s Azure Blob storage a Visual Studio připojené služeb (webové úlohy projekty)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje C# ukázek kódu, které ukazují, jak spustit proces, při vytvoření nebo aktualizaci objektu blob Azure. Kód – ukázky použití [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verze 1.x. Při přidávání účtu úložiště do projektu úlohy WebJob pomocí sady Visual Studio **přidat připojení služby** dialogu odpovídající balíček NuGet pro úložiště Azure je nainstalovaný, odkazy na příslušné .NET jsou přidány do projektu a připojovací řetězce pro účet úložiště se aktualizují v souboru App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak aktivovat funkci, když se vytvoří nebo aktualizuje objekt blob
Tato část ukazuje způsob použití **BlobTrigger** atribut.

 **Poznámka:** WebJobs SDK kontroluje soubory protokolů, které chcete sledovat pro nové nebo změněné objekty BLOB. Tento proces je ze své podstaty pomalé. funkce nemusí získat aktivuje až několik minut nebo déle po vytvoření objektu blob.  Pokud aplikace potřebuje ke zpracování objektů BLOB okamžitě, doporučuje se vytvořit zprávu fronty, při vytváření objektu blob a použít **QueueTrigger** atribut místo **BlobTrigger** atribut na funkce, která zpracovává objektu blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Jeden zástupný symbol pro název objektu blob s příponou
Následující ukázka kódu zkopíruje text objekty BLOB, které se zobrazují v *vstupní* kontejner, aby *výstup* kontejneru:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktoru atributu přijímá řetězcový parametr, který určuje název kontejneru a zástupný symbol pro název objektu blob. V tomto příkladu, pokud objekt blob s názvem *Blob1.txt* je vytvořen v *vstupní* kontejneru, funkce vytvoří objekt blob s názvem *Blob1.txt* v *výstup* kontejneru.

Vzor názvů s zástupný symbol název objektu blob, můžete určit, jak znázorňuje následující ukázka kódu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Tento kód zkopíruje pouze objekty BLOB, které mají názvy počínaje "původní-". Například *původní Blob1.txt* v *vstupní* kontejneru se zkopíruje do *kopie Blob1.txt* v *výstup* kontejneru.

Pokud budete muset zadat vzor názvu pro názvy objektů blob, které mají v názvu složené závorky, dvakrát složené závorky. Například, pokud chcete najít objektů BLOB v *bitové kopie* kontejner, který mají názvy takto:

        {20140101}-soundfile.mp3

To lze používejte pro vaše vzor:

        images/{{20140101}}-{name}

V příkladu *název* hodnotu zástupného symbolu by *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Zástupné symboly název a příponu samostatných objektů blob
Následující ukázka kódu změní přípona souboru, protože kopíruje objekty BLOB, které se zobrazují v *vstupní* kontejner, aby *výstup* kontejneru. Kód protokoly rozšíření *vstupní* objektů blob a nastaví rozšíření *výstup* objektu blob do *.txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, které můžete vázat na objekty BLOB
Můžete použít **BlobTrigger** atribut u následujících typů:

* **Řetězec**
* **TextReader**
* **Datový proud**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Jiné typy deserializoval [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Pokud chcete pracovat přímo s účtu úložiště Azure, můžete také přidat **CloudStorageAccount** parametru podpis metody.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Získávání obsahu objektu blob text vazby na řetězec
Pokud se očekává text objekty BLOB, **BlobTrigger** lze použít pro **řetězec** parametr. Následující ukázka kódu váže objekt blob text **řetězec** parametr s názvem **logMessage**. Funkce pomocí tohoto parametru zapsat obsah objektu blob na řídicím panelu WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Získávání serializovat obsah objektu blob pomocí ICloudBlobStreamBinder
Následující příklad kódu používá třídu, která implementuje **ICloudBlobStreamBinder** povolit **BlobTrigger** atributu bude vazba provedena objekt blob **WebImage** typu.

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

**WebImage** vazby kódu je součástí **WebImageBinder** třídu odvozenou od **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Postupy: zpracování poškozených objektů BLOB
Když **BlobTrigger** funkce selže, sadu SDK nazve je znovu, v případě selhání bylo způsobeno přechodné chybě. Pokud selhání je způsobena obsah objektu blob, funkce selže pokaždé, když se pokusí zpracovat objektu blob. Ve výchozím nastavení sadu SDK volá funkci až 5 výskyty pro daný objekt blob. Pokud pátý zkuste selže, sadu SDK přidá zprávu do fronty s názvem *webjobs. blobtrigger poison*.

Maximální počet opakovaných pokusů je možné konfigurovat. Stejné **MaxDequeueCount** nastavení se používá pro zpracování poškozených objektů blob a zpracování poškozených fronty zpráv.

Zprávy ve frontě pro poškozených objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu *{název webové úlohy}*. Funkce. *{Název funkce}*, například: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* Značka ETag (identifikátor objektu blob verze, například: "0x8D1DC6E70A277EF")

V následující ukázce kódu **CopyBlob** funkce obsahuje kód, který způsobuje, že selžou pokaždé, když je volána. Po nazve je sada SDK pro maximální počet opakovaných pokusů, vytvoření zprávu ve frontě poškozených objektů blob a zpracovává zprávy **LogPoisonBlob** funkce.

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

Sada SDK automaticky deserializuje zpráva JSON. Tady je **PoisonBlobMessage** třídy:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmus dotazování objektů BLOB
Sada WebJobs SDK kontroluje všechny kontejnery určeného **BlobTrigger** atributy při spuštění aplikace. V účtu úložiště velké tato kontrola může trvat nějakou dobu, proto chvíli může být před nebyly nalezeny nové objekty BLOB a **BlobTrigger** provedení funkce.

Ke zjištění nové nebo změněné objekty BLOB po spuštění aplikace, sady SDK pravidelně čte z protokolů úložiště objektů blob. Objekt blob protokoly jsou do vyrovnávací paměti a pouze získat fyzicky zapisují každých 10 minut, nebo Ano, tedy s sebou může být důležité zpoždění po objekt blob se vytvoří nebo aktualizuje před odpovídající **BlobTrigger** provádí funkce.

Dojde k výjimce pro objekty BLOB, které vytvoříte pomocí **Blob** atribut. Když WebJobs SDK vytvoří nový objekt blob, pak předá nový objekt blob okamžitě žádné odpovídající **BlobTrigger** funkce. Proto pokud máte řetěz blob vstupy a výstupy, sadu SDK dokáže zpracovat efektivně. Pokud chcete s nízkou latencí systémem objektu blob služby zpracování funkce pro objekty BLOB, které jsou vytvořeny nebo aktualizovány jiným způsobem, doporučujeme používat, ale **QueueTrigger** místo **BlobTrigger**.

### <a name="blob-receipts"></a>Potvrzení objektů BLOB
Sada WebJobs SDK je zajištěno, že žádné **BlobTrigger** funkce pro stejný objekt blob nové nebo aktualizované volala více než jednou. Dělá to pomocí zachování *blob potvrzení* aby bylo možné zjistit, pokud byla zpracována na verzi daného objektu blob.

Potvrzení BLOB jsou uloženy v kontejneru nazvaném *azure. webové úlohy hostitelů* v účtu úložiště Azure určeného AzureWebJobsStorage připojovací řetězec. Potvrzení o objektu blob obsahuje následující informace:

* Funkce, která byla volána pro tento objekt blob ("*{název webové úlohy}*. Funkce. *{Název funkce}*", například:"WebJob1.Functions.CopyBlob")
* Název kontejneru
* Typ objektu blob ("BlockBlob" nebo "PageBlob")
* Název objektu blob
* Značky ETag (identifikátor objektu blob verze, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, můžete ručně odstranit objekt blob příjmu pro tento objekt blob z *azure. webové úlohy hostitelů* kontejneru.

## <a name="related-topics-covered-by-the-queues-article"></a>Související témata uvedených v článku fronty
Informace o způsobu zpracování objektů blob zpracování aktivovány zprávu fronty, nebo WebJobs SDK scénáře, které nejsou specifické do objektu blob zpracování, projděte si téma [používání úložiště fronty Azure pomocí WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Související témata popsaná v tomto článku zahrnují následující:

* Asynchronní funkce
* Více instancí
* Řádné vypnutí
* Použití atributů WebJobs SDK v tělo funkce
* Nastavte SDK připojovacích řetězců v kódu.
* Nastavení hodnot pro WebJobs SDK parametry konstruktor v kódu
* Konfigurace **MaxDequeueCount** pro zpracování poškozených objektů blob.
* Ruční aktivaci funkce
* Zápis protokolů

## <a name="next-steps"></a>Další postup
Tento článek poskytl ukázek kódu, které ukazují, jak zpracovat běžné scénáře pro práci s objekty BLOB Azure. Další informace o tom, jak používat Azure WebJobs a WebJobs SDK najdete v tématu [zdrojů dokumentace Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

