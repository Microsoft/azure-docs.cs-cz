---
title: Začínáme s úložištěm objektů BLOB a připojenými službami sady Visual Studio (projekty WebJob) | Microsoft Docs
description: Jak začít používat úložiště objektů BLOB v projektu webové úlohy po připojení ke službě Azure Storage pomocí připojených služeb sady Visual Studio.
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
ms.openlocfilehash: 1e951fde7e47ccfcce5f64db4ef27ac767d63480
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510652"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Začínáme s Azure Blob Storage a připojenými službami sady Visual Studio (projekty WebJob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje C# ukázky kódu, které ukazují, jak aktivovat proces při vytvoření nebo aktualizaci objektu blob Azure. Ukázky kódu používají [sadu WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verze 1. x. Když přidáte účet úložiště do projektu webové úlohy pomocí dialogu **Přidat připojené služby** sady Visual Studio, nainstaluje se příslušný balíček NuGet Azure Storage, do projektu se přidají příslušné odkazy .NET a připojovací řetězce pro účet úložiště se aktualizuje v souboru App. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak aktivovat funkci při vytvoření nebo aktualizaci objektu BLOB
V této části se dozvíte, jak použít atribut **BlobTrigger** .

 **Poznámka:** Sada WebJobs SDK prohledává soubory protokolů, které se mají sledovat pro nové nebo změněné objekty blob. Tento proces je z jeho podstaty pomalý. funkce se nemusí aktivovat, dokud neproběhne několik minut nebo déle po vytvoření objektu BLOB.  Pokud vaše aplikace potřebuje zpracovat objekty blob okamžitě, doporučuje se při vytváření objektu BLOB vytvořit zprávu fronty a použít atribut **QueueTrigger** namísto atributu **BlobTrigger** ve funkci, která objekt BLOB zpracovává. .

### <a name="single-placeholder-for-blob-name-with-extension"></a>Jeden zástupný symbol pro název objektu BLOB s příponou
Následující ukázka kódu kopíruje textové objekty blob, které se zobrazí ve vstupním kontejneru do *výstupního* kontejneru:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktor atributu přebírá řetězcový parametr, který určuje název kontejneru a zástupný symbol pro název objektu BLOB. Pokud je v tomto příkladu vytvořený objekt BLOB s názvem *Blob1. txt* ve *vstupním* kontejneru, funkce vytvoří ve *výstupním* kontejneru objekt BLOB s názvem *Blob1. txt* .

Můžete zadat vzor názvu se zástupným symbolem názvu objektu blob, jak je znázorněno v následujícím příkladu kódu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Tento kód zkopíruje pouze objekty blob, jejichž názvy začínají řetězcem "původní –". Například *Original-Blob1. txt* ve vstupním kontejneru je zkopírován do *copy-Blob1. txt* ve výstupním kontejneru .

Pokud potřebujete zadat vzor názvu pro názvy objektů blob, které mají složené závorky v názvu, poklikejte na složené závorky. Například pokud chcete najít objekty BLOB v kontejneru images, které mají název takto:

        {20140101}-soundfile.mp3

Použijte tento vzor:

        images/{{20140101}}-{name}

V příkladu je hodnota zástupný symbol *názvu* *soundfile. mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Oddělitelné zástupné symboly pro název a rozšíření objektu BLOB
Následující ukázka kódu změní příponu souboru při kopírování objektů blob, které se zobrazí ve *vstupním* kontejneru do *výstupního* kontejneru. Kód protokoluje rozšíření *vstupního* objektu BLOB a nastaví rozšíření výstupního objektu BLOB na *. txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, které můžete navazovat na objekty blob
Atribut **BlobTrigger** lze použít pro následující typy:

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Jiné typy deserializovatelné pomocí [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Pokud chcete pracovat přímo s účtem služby Azure Storage, můžete do signatury metody přidat také parametr **CloudStorageAccount** .

## <a name="getting-text-blob-content-by-binding-to-string"></a>Získání obsahu objektu BLOB s textem vazbou na řetězec
Pokud je očekáváno textové objekty blob, lze **BlobTrigger** použít na **řetězcový** parametr. Následující ukázka kódu váže objekt BLOB objektu na **řetězcový** parametr s názvem **LogMessage –** . Funkce používá tento parametr k zápisu obsahu objektu blob do řídicího panelu sady WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Získání serializovaného obsahu objektů BLOB pomocí ICloudBlobStreamBinder
Následující ukázka kódu používá třídu, která implementuje **ICloudBlobStreamBinder** , aby atribut **BlobTrigger** mohl vytvořit vazby objektu BLOB k typu webimage.

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

Kód vazby webimage je k dispozici ve třídě **WebImageBinder** , která je odvozena od **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Postup zpracování poškozených objektů BLOB
Pokud dojde k selhání funkce **BlobTrigger** , sada SDK ji znovu volá, pokud by selhání způsobila přechodná chyba. Pokud je selhání způsobeno obsahem objektu blob, funkce selže při každém pokusu o zpracování objektu BLOB. Sada SDK ve výchozím nastavení volá funkci pro daný objekt BLOB až pětkrát. Pokud se pátý pokus nezdařil, sada SDK přidá zprávu do fronty s názvem *WebJobs-blobtrigger-otrav*.

Maximální počet opakovaných pokusů lze konfigurovat. Stejné nastavení **MaxDequeueCount** se používá pro zpracování poškozeného objektu BLOB a zpracování zpráv z fronty otrav.

Zpráva fronty pro poškozené objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu *{název webové úlohy}* . POZVYHLEDAT. *{Function Name}* například: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

V následující ukázce kódu má funkce **CopyBlob** kód, který způsobí, že se chyba pokaždé, když je volána. Po volání sady SDK k maximálnímu počtu opakovaných pokusů se vytvoří zpráva ve frontě objektů BLOB v nepoškozeném systému a tato zpráva se zpracuje funkcí **LogPoisonBlob** .

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

Sada SDK automaticky rekonstruuje zprávu JSON. Tady je třída **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmus cyklického dotazování objektů BLOB
Sada WebJobs SDK prohledává všechny kontejnery určené atributy **BlobTrigger** při spuštění aplikace. V případě velkého účtu úložiště může tato kontrola nějakou dobu trvat, takže se může stát, že předtím, než budou nalezeny nové objekty blob, a **BlobTrigger** funkce se spustí.

Pro detekci nových nebo změněných objektů BLOB po spuštění aplikace SDK pravidelně čte z protokolů služby Blob Storage. Protokoly objektů BLOB se ukládají do vyrovnávací paměti a můžou se fyzicky zapisovat jenom každých 10 minut, takže může po vytvoření nebo aktualizaci objektu BLOB způsobit značnou prodlevu, než se spustí odpovídající funkce **BlobTrigger** .

Pro objekty blob, které vytvoříte pomocí atributu **BLOB** , existuje výjimka. Když Sada WebJobs SDK vytvoří nový objekt blob, předává nový objekt BLOB hned všem vyhovujícím funkcím **BlobTrigger** . Proto, pokud máte řetězové vstupy a výstupy objektů blob, sada SDK je může efektivně zpracovat. Pokud ale chcete, aby u objektů blob, které jsou vytvořené nebo aktualizované jinými prostředky, běžela nízká latence, doporučujeme místo **BlobTrigger**používat **QueueTrigger** .

### <a name="blob-receipts"></a>Příjem objektů BLOB
Sada WebJobs SDK zajistí, že se žádná funkce **BlobTrigger** nevolá více než jednou pro stejný nový nebo aktualizovaný objekt BLOB. Díky tomu udržují *příjmy objektů BLOB* , aby bylo možné zjistit, zda byla daná verze objektu BLOB zpracována.

Příjmy objektů BLOB se ukládají do kontejneru s názvem *Azure-WebJobs – hostitelé* v účtu služby Azure Storage, který je určený připojovacím řetězcem AzureWebJobsStorage. Příjem objektů BLOB obsahuje následující informace:

* Funkce, která byla volána pro objekt BLOB (" *{název webové úlohy}* . POZVYHLEDAT. *{Function Name}* , například: "WebJob1. Functions. CopyBlob")
* Název kontejneru
* Typ objektu BLOB ("BlockBlob" nebo "PageBlob")
* Název objektu BLOB
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, můžete ručně odstranit příjem objektů BLOB pro tento objekt BLOB z kontejneru *Azure-WebJobs-Hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Související témata, která jsou popsaná v článku fronty
Informace o tom, jak zpracovat zpracování objektů BLOB aktivované zprávami ve frontě nebo ve scénářích služby WebJobs SDK, které nejsou specifické pro zpracování objektů blob, najdete v tématu [Jak používat Azure Queue Storage se sadou WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Mezi související témata zahrnutá v tomto článku patří následující:

* Asynchronní funkce
* Několik instancí
* Řádné vypnutí
* Použití atributů sady WebJobs SDK v těle funkce
* Nastavte připojovací řetězce sady SDK v kódu.
* Nastavení hodnot pro parametry konstruktoru sady WebJobs SDK v kódu
* Nakonfigurujte **MaxDequeueCount** pro zpracování poškození objektů BLOB.
* Ruční aktivace funkce
* Zápis protokolů

## <a name="next-steps"></a>Další kroky
V tomto článku jsou uvedené ukázky kódu, které ukazují, jak zpracovávat běžné scénáře pro práci s objekty blob Azure. Další informace o tom, jak používat Azure WebJobs a sadu WebJobs SDK, najdete v [dokumentaci k prostředkům Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

