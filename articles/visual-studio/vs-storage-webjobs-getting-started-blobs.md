---
title: Začínáme s úložištěm objektů blob pomocí sady Visual Studio (projekty WebJob)
description: Jak začít používat úložiště objektů Blob v projektu WebJob po připojení k úložišti Azure pomocí připojených služeb Visual Studia.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299981"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s úložištěm objektů blob Azure a připojenými službami Visual Studia (projekty WebJob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje ukázky kódu jazyka C#, které ukazují, jak spustit proces při vytvoření nebo aktualizaci objektu blob Azure. Ukázky kódu používají [webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verze 1.x. Když přidáte účet úložiště do projektu WebJob pomocí dialogového okna **Přidat připojené služby** sady Visual Studio, nahraje se příslušný balíček Azure Storage NuGet, do projektu se přidají příslušné odkazy .NET a v souboru App.config se aktualizují připojovací řetězce pro účet úložiště.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak spustit funkci při vytvoření nebo aktualizaci objektu blob
Tato část ukazuje, jak používat atribut **BlobTrigger.**

 **Poznámka:** Sada WebJobs SDK prohledá soubory protokolu a sleduje nové nebo změněné objekty BLOB. Tento proces je ze své podstaty pomalý; funkce nemusí získat spuštěna až několik minut nebo déle po vytvoření objektu blob.  Pokud vaše aplikace potřebuje okamžitě zpracovat objekty BLOB, doporučuje se vytvořit zprávu fronty při vytváření objektu blob a použít atribut **QueueTrigger** namísto atributu **BlobTrigger** na funkci, která objekt blob zpracovává.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Jediný zástupný symbol pro název objektu blob s příponou
Následující ukázkový kód zkopíruje objekty BLOB textu, které se zobrazují ve *vstupním* kontejneru, do *výstupního* kontejneru:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktor atributu přebírá parametr řetězce, který určuje název kontejneru a zástupný symbol pro název objektu blob. V tomto příkladu pokud je ve *vstupním* kontejneru vytvořen objekt blob s názvem *Blob1.txt,* funkce vytvoří objekt blob s názvem *Blob1.txt* ve *výstupním* kontejneru.

Můžete určit vzor názvu se zástupným symbolem názvu objektu blob, jak je znázorněno v následující ukázce kódu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Tento kód zkopíruje pouze objekty BLOB, které mají názvy začínající na "original-". Například *original-Blob1.txt* ve *vstupním* kontejneru je zkopírován do *copy-Blob1.txt* ve *výstupním* kontejneru.

Pokud potřebujete zadat vzor názvu pro názvy objektů blob, které mají složené závorky v názvu, zdvojnásobte složené závorky. Například pokud chcete najít objekty BLOB v kontejneru *obrázků,* které mají názvy, jako je tento:

        {20140101}-soundfile.mp3

použijte pro váš vzor:

        images/{{20140101}}-{name}

V příkladu by zástupná hodnota *názvu* byla *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Samostatné zástupné symboly názvů objektů blob a rozšíření
Následující ukázka kódu změní příponu souboru, protože kopíruje objekty BLOB, které se zobrazují ve *vstupním* kontejneru, do *výstupního* kontejneru. Kód protokoluje rozšíření *vstupního* objektu blob a nastaví rozšíření *výstupního* objektu blob na *.txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, které lze svázat s objekty BLOB
Atribut **BlobTrigger** můžete použít u následujících typů:

* **Řetězec**
* **Textreader**
* **Datový proud**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Jiné typy rekonstruované pomocí [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Pokud chcete pracovat přímo s účtem úložiště Azure, můžete také přidat parametr **CloudStorageAccount** k podpisu metody.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Získání obsahu textového objektu blob vazbou na řetězec
Pokud jsou očekávány objekty BLOB textu, **blobTrigger** lze použít na parametr **řetězce.** Následující ukázka kódu sváže textový objekt blob s parametrem **řetězce** s názvem **logMessage**. Funkce používá tento parametr k zápisu obsahu objektu blob na řídicí panel WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Získání serializovaného obsahu objektů blob pomocí iCloudBlobStreamBinder
Následující ukázka kódu používá třídu, která implementuje **ICloudBlobStreamBinder** k povolení atributu **BlobTrigger** k vytvoření svázání objektu blob s typem **WebImage.**

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

Kód vazby **WebImage** je k dispozici ve třídě **WebImageBinder,** která je odvozena od **iCloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Jak zacházet s jedovatými kuličky
Pokud funkce **BlobTrigger** selže, sada SDK ji znovu zavolá v případě, že selhání bylo způsobeno přechodnou chybou. Pokud je selhání způsobeno obsahem objektu blob, funkce se nezdaří pokaždé, když se pokusí zpracovat objekt blob. Ve výchozím nastavení sada SDK volá funkci až pětkrát pro daný objekt blob. Pokud pátý pokus selže, sada SDK přidá zprávu do fronty s názvem *webjobs-blobtrigger-poison*.

Maximální počet opakování je konfigurovatelný. Stejné nastavení **MaxDequeueCount** se používá pro zpracování objektu blob poison a zpracování zpráv fronty poison.

Zpráva fronty pro poškozená objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu *{WebJob name}*. Funkce. *{Název funkce}*, například WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* Název objektu BlobName
* ETag (identifikátor verze objektu blob, například "0x8D1DC6E70A277EF")

V následující ukázce kódu **copyblob** funkce má kód, který způsobí, že se nezdaří pokaždé, když je volána. Poté, co sada SDK volá pro maximální počet opakování, je vytvořena zpráva ve frontě poškozená objektová báze a tato zpráva je zpracována funkcí **LogPoisonBlob.**

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

Sada SDK automaticky rekonstruuje zprávu JSON. Zde je **Třída PoisonBlobMessage:**

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmus dotazování objektů blob
Sada WebJobs SDK prohledá všechny kontejnery určené atributy **blobTrigger** při spuštění aplikace. Ve velkém účtu úložiště toto skenování může nějakou dobu trvat, takže může chvíli trvat, než se najdou nové objekty BLOB a spustí se funkce **blobTrigger.**

Chcete-li zjistit nové nebo změněné objekty BLOB po spuštění aplikace, sada SDK pravidelně čte z protokolů úložiště objektů blob. Protokoly objektů blob jsou uloženy do vyrovnávací paměti a pouze získat fyzicky zapsány každých 10 minut nebo tak, takže může být významné zpoždění po vytvoření nebo aktualizaci objektu blob před spuštěním odpovídající funkce **BlobTrigger.**

Existuje výjimka pro objekty BLOB, které vytvoříte pomocí atributu **Objekt blob.** Když webjobs SDK vytvoří nový objekt blob, okamžitě předá nový objekt blob všechny odpovídající funkce **BlobTrigger.** Proto pokud máte řetězec vstupu objektů blob a výstupy, sada SDK je můžete efektivně zpracovat. Ale pokud chcete s nízkou latencí spuštění funkce zpracování objektů blob pro objekty BLOB, které jsou vytvořeny nebo aktualizovány jinými prostředky, doporučujeme použít **QueueTrigger** spíše než **BlobTrigger**.

### <a name="blob-receipts"></a>Příjmy objektu blob
Sada WebJobs SDK zajišťuje, že žádná funkce **BlobTrigger** se nenazývá více než jednou pro stejný nový nebo aktualizovaný objekt blob. Je to tím, že udržuje *blob příjmy* s cílem zjistit, zda byla zpracována daná verze objektu blob.

Účtenky s objektem blob jsou uložené v kontejneru s názvem *azure-webjobs-hosts* v účtu úložiště Azure určeném připojovacím řetězcem AzureWebJobsStorage. Příjem k blob má následující informace:

* Funkce, která byla volána pro objekt blob *("název_webjob}*. Funkce. *{Název funkce}*", například WebJob1.Functions.CopyBlob")
* Název kontejneru
* Typ objektu blob ("BlockBlob" nebo "PageBlob")
* Název objektu blob
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, můžete ručně odstranit příjemka objektu blob pro tento objekt blob z kontejneru *azure-webjobs-hosts.*

## <a name="related-topics-covered-by-the-queues-article"></a>Související témata, na která se vztahuje fronty článku
Informace o tom, jak zpracovat zpracování objektů blob spuštěné zprávou fronty nebo pro scénáře sady WebJobs SDK, které nejsou specifické pro zpracování objektů blob, naleznete v [tématu Jak používat úložiště front Azure s sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Mezi související témata uvedená v tomto článku patří následující:

* Asynchronní funkce
* Několik instancí
* Bezproblémové vypnutí
* Použití atributů sady WebJobs SDK v těle funkce
* Nastavte připojovací řetězce sady SDK v kódu.
* Nastavení hodnot parametrů konstruktoru sady WebJobs SDK v kódu
* Konfigurace **MaxDequeueCount** pro zpracování objektu blob poison.
* Ruční spuštění funkce
* Zápis protokolů

## <a name="next-steps"></a>Další kroky
Tento článek poskytl ukázky kódu, které ukazují, jak zpracovat běžné scénáře pro práci s objekty BLOB Azure. Další informace o tom, jak používat Azure WebJobs a WebJobs SDK, najdete v [tématu Materiály pro dokumentaci Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

