---
title: Použití sdílené přístupové podpisy (SAS) ve službě Azure Storage | Microsoft Docs
description: Naučte se používat sdílené přístupové podpisy (SAS) pro delegování přístupu k prostředkům Azure Storage, včetně objektů BLOB, fronty, tabulky a soubory.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: cshoe
ms.openlocfilehash: 4f20e79ea6cb2d9d403f4451f595516d5c2e9373
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650736"
---
# <a name="using-shared-access-signatures-sas"></a>Použití sdílených přístupových podpisů (SAS)

Sdílený přístupový podpis (SAS) poskytuje způsob, jak udělit omezený přístup k objektům v účtu úložiště pro ostatní klienty bez vystavení klíč účtu. V tomto článku jsme poskytovat přehled o modelu SAS, SAS osvědčené postupy a podívejte se na některé příklady.

Další příklady kódu pomocí SAS nad rámec těch, které jsou tu popsané, najdete v části [Začínáme s Azure Blob Storage v rozhraní .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) a ostatních vzorků, které jsou k dispozici v [ukázky kódu Azure](https://azure.microsoft.com/documentation/samples/?service=storage) knihovny. Stažení ukázkových aplikací a jejich spuštění nebo procházet kód na Githubu.

## <a name="what-is-a-shared-access-signature"></a>Co je sdílený přístupový podpis?
Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Pomocí SAS můžete udělit klientům přístup k prostředkům ve vašem účtu úložiště bez sdílení klíče účtu. Toto je zásadní aspekt používání sdílených přístupových podpisů v aplikacích – SAS představuje bezpečný způsob sdílení prostředků úložiště, aniž byste ohrozili své klíče účtu.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

SAS vám poskytuje podrobnou kontrolu nad typ přístupu, kterou byste udělit klientům, kteří mají SAS, včetně:

* Interval, za které je platný, včetně počáteční čas a čas vypršení platnosti SAS.
* Oprávnění udělená pomocí SAS. SAS pro objekt blob může například udělit pro čtení a zápisu oprávnění k tomuto objektu blob ale oprávnění k odstranění.
* Volitelné IP adresu nebo rozsah IP adres, ze kterých Azure Storage bude přijímat SAS. Například můžete určit rozsah IP adres, které patří do vaší organizace.
* Protokol, přes který bude přijímat Azure Storage SAS. Tento volitelný parametr můžete omezit přístup ke klientům pomocí protokolu HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Když budete používat sdílený přístupový podpis?
Pokud chcete poskytnout přístup k prostředkům ve vašem účtu úložiště libovolnému klientovi, které nevykazují přístupových klíčů k účtu úložiště, můžete použít SAS. Váš účet úložiště zahrnuje jak primární a sekundární přístupový klíč, které obě udělit přístup správce ke svému účtu a všechny prostředky v něm. Vystavení některý z nich otevře účet tak, aby možnost škodlivý nebo nedbalosti použití. Sdílené přístupové podpisy zadejte bezpečné alternativu, která umožňuje klientům čtení, zápisu a odstranění dat ve vašem účtu úložiště podle oprávnění, která jste explicitně udělí oprávnění a bez nutnosti klíč účtu.

Běžný scénář, kde je užitečné SAS je služba, kde uživatelé číst a zapisovat svá vlastní data do účtu úložiště. Ve scénáři, kde účet úložiště ukládá data o uživatele existují dva vzory typické návrhu:

1. Klienti nahrávání a stahování dat přes službu proxy serveru front-end, který provádí ověřování. Tuto službu front-endu proxy nabízí výhodu v podobě povolení ověření obchodní pravidla, ale pro velké objemy dat nebo vysoký počet transakcí, vytváření služby, který můžete přizpůsobit tak, aby odpovídaly vyžádání může být nákladné nebo obtížná.

  ![Diagram scénáře: Front-end proxy služby](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Jednoduché služby ověřuje klienta podle potřeby a pak vygeneruje SAS. Jakmile klient obdrží SAS, můžete přímo s definována SAS a pro interval povolený SAS oprávnění přístupu k prostředkům účet úložiště. SAS snižuje potřebu směrování všechna data prostřednictvím služby proxy serveru front-end.

  ![Diagram scénáře: SAS zprostředkovatel služby](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Mnoho reálných služby mohou používat hybridní tyto dva přístupy. Například některá data mohou zpracovat a ověřit prostřednictvím proxy serveru front-end, zatímco jiné dat je uložit nebo číst přímo pomocí SAS.

Kromě toho budete muset použít SAS pro ověření zdroje objektu v operaci kopírování v některých scénářích:

* Pokud kopírujete objekt blob do jiného objektu blob, který se nachází v jiný účet úložiště, musíte použít SAS k ověření zdroje objektu blob. Volitelně můžete SAS k ověření cílový objekt blob také.
* Při kopírování souboru do jiného souboru, který se nachází v jiný účet úložiště, musíte použít SAS k ověření zdrojový soubor. Volitelně můžete SAS k ověření cílového souboru.
* Při kopírování objektu blob do souboru nebo soubor do objektu blob, musíte použít SAS k ověření zdroje objektu, i v případě, že zdrojové a cílové objekty jsou umístěny ve stejném účtu úložiště.

## <a name="types-of-shared-access-signatures"></a>Druhy sdílených přístupových podpisů
Můžete vytvořit dva druhy sdílených přístupových podpisů:

* **SAS služby.** SAS služby deleguje přístup k prostředku jen v jedné službě úložiště: službě Blob, Queue, Table nebo File. V tématu [vytváření SAS služby](https://msdn.microsoft.com/library/dn140255.aspx) a [příklady SAS služby](https://msdn.microsoft.com/library/dn140256.aspx) podrobné informace o vytváření token SAS služby.
* **SAS účtu.** Účet SAS delegáti přístup k prostředkům v jedné nebo více službách úložiště. Všechny operace, které jsou dostupné přes SAS služby jsou dostupné prostřednictvím SAS účtu. Kromě toho s účtem SAS, můžete delegovat přístup k operace, které platí pro danou službu, jako například **vlastnosti služby Get/Set** a **získat statistiky služby**. Můžete taky delegovat přístup k operacím čtení, zápis a odstranění pro kontejnery objektů blob, tabulky a sdílené složky, který se nedá vymezit přes SAS služby. V tématu [vytváření SAS účtu](https://msdn.microsoft.com/library/mt584140.aspx) podrobné informace o vytváření tokenu SAS účtu.

## <a name="how-a-shared-access-signature-works"></a>Jak funguje sdílený přístupový podpis
Sdílený přístupový podpis je podepsaný identifikátor URI, který odkazuje na jeden nebo více prostředků úložiště a obsahuje token, který obsahuje speciální sadu parametry dotazu. Token Určuje, jak můžete získat přístup k prostředkům na klientem. Jeden z parametrů dotazu, podpisu, se vytvářejí na základě parametrů SAS a podepsaný pomocí klíč účtu. Tento podpis úložiště Azure slouží k ověřování SAS.

Tady je příklad identifikátoru URI SAS, zobrazuje identifikátor URI a tokenu SAS:

![Komponenty identifikátoru URI SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

SAS token je řetězec vygenerujete na *klienta* straně (najdete v článku [příklady SAS](#sas-examples) části Příklady kódu). Token SAS, který generovat s Klientská knihovna pro úložiště, například není sledována úložiště Azure žádným způsobem. Můžete vytvořit neomezený počet tokeny SAS na straně klienta.

Když klient poskytuje identifikátor URI SAS pro úložiště Azure jako součást požadavku, služba zkontroluje parametry SAS a definice virů, aby ověřte, zda je platný pro ověření žádosti. Pokud službu ověřuje, že podpis je platný, pak je požadavek ověřen. Jinak je žádost odmítnuta s kódem chyby 403 (zakázáno).

## <a name="shared-access-signature-parameters"></a>Sdílený přístupový podpis parametry
SAS účtu a tokeny SAS služby zahrnují některé společné parametry a také provést několik parametrů, které se liší.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametry, které jsou společné pro SAS účtu a tokeny SAS služby
* **Verze rozhraní API** volitelný parametr, který určuje verzi služby úložiště používat k provedení požadavku.
* **Verze služby** povinný parametr, který určuje verzi služby úložiště používat k ověření žádosti.
* **Počáteční čas.** Toto je čas, kdy začne platit SAS. Čas zahájení pro sdílený přístupový podpis je volitelný. Pokud čas spuštění je vynechán, SAS je hned platná. Čas spuštění musí být vyjádřena v UTC (Coordinated Universal Time), s speciální označení UTC ("Z"), například `1994-11-05T13:15:30Z`.
* **Čas vypršení platnosti.** Toto je čas, po jejímž uplynutí SAS již není platný. Osvědčené postupy doporučujeme, abyste zadejte čas vypršení platnosti pro SAS, nebo přidružit k zásadám uložené přístup. Čas vypršení platnosti musí být vyjádřena v UTC (Coordinated Universal Time), s speciální označení UTC ("Z"), například `1994-11-05T13:15:30Z` (Další informace níže).
* **Oprávnění.** Oprávnění určená v SAS znamenat jakým operacím klienta můžete provést u prostředků úložiště pomocí SAS. K dispozici oprávnění se liší pro SAS účtu a SAS služby.
* **IP.** Volitelný parametr, který určuje IP adresu nebo rozsah IP adres mimo Azure (naleznete v části [stav konfigurace relace směrování](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) pro Express Route) ze kterého tak, aby přijímal požadavky.
* **Protocol.** Volitelný parametr, který určuje protokol, povolené pro žádost. Možné hodnoty jsou protokolů HTTPS a HTTP (`https,http`), který je pouze výchozí hodnotu, nebo HTTPS (`https`). Všimněte si, že HTTP jenom není povolenou hodnotu.
* **Podpis.** Podpis se vytvářejí na základě ostatní parametry zadaný jako součást tokenu a pak se zašifrují. Slouží k ověření SAS.

### <a name="parameters-for-a-service-sas-token"></a>Parametry pro token SAS služby
* **Úložiště prostředků.** Úložiště prostředků, pro které můžete delegovat přístup se službou SAS patří:
  * Kontejnery a objekty BLOB
  * Sdílených složek a souborů
  * Fronty
  * Tabulky a rozsahy adres entity tabulky.

### <a name="parameters-for-an-account-sas-token"></a>Parametry pro token SAS účtu
* **Služba nebo služby.** SAS účtu můžete delegovat přístup k jednomu nebo více službách úložiště. Například můžete vytvořit účet SAS, delegáti přístup ke službě Blob a souboru. Nebo můžete vytvořit SAS, delegáti přístup k všechny čtyři služeb (objekt Blob, fronty, tabulky a soubor).
* **Typy prostředků úložiště.** Účet SAS se vztahuje na jeden nebo více tříd prostředky úložiště, nikoli konkrétní prostředek. Můžete vytvořit SAS pro delegování přístupu k účtu:
  * API úrovně služeb, které se nazývají proti prostředků účtu úložiště. Mezi příklady patří **vlastnosti služby Get/Set**, **získat statistiky služby**, a **seznamu kontejnery nebo fronty nebo tabulky nebo sdílené složky**.
  * Kontejner úrovni rozhraní API, které se nazývají u těchto objektů kontejneru pro každou službu: blob kontejnery, front, tabulky a sdílené složky. Mezi příklady patří **vytvoření nebo odstranění kontejneru**, **vytvoření nebo odstranění fronty**, **vytvoření nebo odstranění tabulky**, **vytvoření nebo odstranění sdílené složky**a  **Seznam objektů BLOB nebo souborů a adresářů**.
  * API úrovni objektů, které se nazývají proti objekty BLOB, fronty zpráv, entity tabulky a soubory. Například **Put Blob**, **dotazu Entity**, **získat zprávy**, a **vytvořit soubor**.

## <a name="examples-of-sas-uris"></a>Příklady identifikátorů URI SAS

### <a name="service-sas-uri-example"></a>Příklad identifikátor URI SAS služby

Tady je příklad služby identifikátor URI SAS, který poskytuje oprávnění čtení a zápisu do objektu blob. Dojde-li jednotlivých součástí identifikátoru URI pochopit, jak přispívá ke SAS v tabulce:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Název | Část SAS | Popis |
| --- | --- | --- |
| Identifikátor URI objektu BLOB |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adresa objektu blob. Všimněte si, že pomocí protokolu HTTPS se důrazně doporučuje. |
| Verze služby úložiště |`sv=2015-04-05` |Pro úložiště služby verze 2012-02-12 a novější, tento parametr určuje verze se má použít. |
| Čas spuštění |`st=2015-04-29T22%3A18%3A26Z` |Zadat ve formátu času UTC. Pokud chcete SAS platit okamžitě, vynechejte čas spuštění. |
| Doba konce platnosti |`se=2015-04-30T02%3A23%3A26Z` |Zadat ve formátu času UTC. |
| Prostředek |`sr=b` |Daný prostředek k objektu blob. |
| Oprávnění |`sp=rw` |Oprávnění udělená pomocí sdíleného přístupového podpisu zahrnout Read (r) a zápis (w). |
| Rozsah IP adres |`sip=168.1.5.60-168.1.5.70` |Rozsah IP adres, ze kterých budou přijímány žádost. |
| Protocol (Protokol) |`spr=https` |Jsou povoleny pouze požadavky pomocí protokolu HTTPS. |
| Podpis |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Používá k ověření přístupu k objektu blob. Podpis je klíčem HMAC, vypočítán na řetězec k podepsání a klíč pomocí algoritmus SHA256 a pak zakódován pomocí kódování Base64. |

### <a name="account-sas-uri-example"></a>Příklad identifikátor URI pro SAS účtu

Tady je příklad účtu SAS, který používá stejné společné parametry na tokenu. Vzhledem k tomu, že tyto parametry jsou popsané výše, nejsou popsané v tomto poli. Jenom parametry, které jsou specifické pro účet, který SAS, které jsou popsané v následující tabulce.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Název | Část SAS | Popis |
| --- | --- | --- |
| Identifikátor URI |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Objekt Blob koncový bod služby, s parametry pro získávání vlastnosti služby (Pokud je volána s GET) nebo nastavení vlastností služby (při volání sadou). |
| Služby |`ss=bf` |SAS se vztahuje na služby objektů Blob a souborů |
| Typy prostředků |`srt=s` |SAS se vztahuje k operacím na úrovni služby. |
| Oprávnění |`sp=rw` |Oprávnění udělit přístup k operacích čtení a zápisu. |

Vzhledem k tomu, že oprávnění jsou omezeny na úrovni služby, jsou přístupné operací s Tento SAS **získat vlastnosti objektu Blob služby** (čtení) a **nastavit vlastnosti služby objektů Blob** (zápisu). Ale a jiný zdroj v URI stejný token SAS také může delegovat přístup k **získat statistiky služby objektů Blob** (načíst).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Řízení SAS se zásadami uložené přístupu
Sdílený přístupový podpis může trvat dvě formy:

* **Ad hoc SAS:** když vytvoříte ad hoc SAS, čas zahájení, čas vypršení platnosti a oprávnění pro SAS se všechny zadaný identifikátor URI SAS (nebo v případě, kdy je čas spuštění vynechán odvozených). Tento typ SAS se dá vytvořit jako SAS účtu nebo SAS služby.
* **SAS se zásadami přístupu uložené:** zásadu uložené přístupu je definovaný na kontejner prostředku--kontejner objektů blob, tabulky, fronty, nebo sdílení – souborů a slouží ke správě omezení pro jeden nebo více sdílených přístupových podpisů. Pokud přidružíte SAS se zásadami přístupu uložené, zdědí SAS omezení – čas spuštění, čas vypršení platnosti a--definována pro zásady uložené přístupu oprávnění.

> [!NOTE]
> SAS účtu musí být v současné době ad hoc SAS. Uložených přístupu zásady se ještě nepodporují pro SAS účtu.

Rozdíl mezi dvěma formuláři je důležité pro jeden klíč scénář: odvolaných certifikátů. Protože identifikátor URI SAS je adresa URL, každý uživatel, který získá SAS, můžete použít bez ohledu na to, který byl původně vytvořen. Pokud veřejně publikována SAS, můžete použít kdokoli na světě. SAS udělí přístup k prostředkům všem uživatelům, kteří mají dokud jednu ze čtyř akcí se stane:

1. Je dosaženo času vypršení platnosti, zadaný na SAS.
2. Čas vypršení platnosti zadané v zásadách přístupu uložené odkazuje SAS je dosaženo (Pokud je odkazována zásadu uložené přístupu a určuje, že čas vypršení platnosti). Tato situace může nastat, protože uplyne, nebo úpravě zásad přístupu uložené, doba vypršení platnosti v minulosti, což je jeden způsob odvolání SAS.
3. Zásady přístupu uložené odkazuje SAS je odstranit, což je další způsob odvolání SAS. Upozorňujeme, že pokud je znovu vytvořit zásady uložené přístupu se stejným názvem, všechny existující tokeny SAS bude znovu být platný podle oprávnění spojená s uložené přístup zásad (za předpokladu, že který neuplynul čas vypršení platnosti na sdíleného přístupového podpisu). Pokud se hodláte odvolání SAS, je nutné použít jiný název, pokud je znovu vytvořit zásady přístupu, doba vypršení platnosti v budoucnu.
4. Znovu vygeneruje klíč účtu, který byl použit k vytvoření přidružení zabezpečení. Znovu vygenerovat klíč účtu způsobí, že všechny součásti aplikace pomocí tohoto klíče selhání k ověření, dokud se aktualizovány používat další platný účet klíč nebo klíč nově se znova vygeneroval účtu.

> [!IMPORTANT]
> Sdílený přístupový podpis identifikátor URI je spojena s účet klíč používaný k vytvoření podpisu a přidruženého uložené zásady přístupu (pokud existuje). Pokud je zadána žádná zásada uložené přístup, jediný způsob, jak odvolat sdílený přístupový podpis je změnit klíč účtu.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Ověřování z klientské aplikace s SAS
Klienta, která je vlastníkem SAS slouží k ověření požadavku vůči účet úložiště, pro kterou, které nemají klíče účtu SAS. SAS můžete zahrnout do připojovací řetězec nebo použití přímo z metodu, nebo odpovídající konstruktor.

### <a name="using-a-sas-in-a-connection-string"></a>Pomocí SAS v připojovacím řetězci
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Pomocí SAS v konstruktoru nebo – metoda
Několik konstruktorů knihovny klienta Azure Storage a přetížení metody nabízejí parametr SAS, aby se můžete ověřit žádost o služby pomocí SAS.

Zde je například identifikátor URI SAS použít k vytvoření odkaz na objekt blob bloku. SAS poskytuje jenom přihlašovacích údajů nezbytných pro požadavek. Odkaz na objekt blob bloku se pak použije pro operaci zápisu:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Osvědčené postupy při použití SAS
Při použití sdílených přístupových podpisů v aplikacích, budete muset mít na paměti dva potenciální rizika:

* Pokud SAS došlo k úniku, můžete použít libovolný uživatel, který získává, což může potenciálně ohrozit váš účet úložiště.
* Pokud SAS poskytované vyprší platnost klientskou aplikaci a aplikace se nepodařilo načíst novou SAS ze služby, pak může být narušen funkcí aplikace.

Následující doporučení pro použití sdílených přístupových podpisů můžete zmírnit tato rizika:

1. **Vždycky používají protokol HTTPS** k vytvoření nebo distribuovat SAS. Pokud SAS je předán přes protokol HTTP a zachycení, je možné číst SAS a použít jej jako určený uživatel může mít potenciálně ohrožení citlivých dat nebo aby vám umožnil poškození dat podle uživateli se zlými úmysly útočník provádění útok man-in-the-middle.
2. **Odkaz na uložený zásady přístupu, kde je to možné.** Zásady přístupu uložené získáte možnost odvolat oprávnění bez nutnosti obnovit klíče účtu úložiště. Nastavit dobu platnosti na tyto velmi daleké budoucnosti (nebo nekonečné) a ujistěte se, že se pravidelně aktualizují přesune dále do budoucna.
3. **Použijte ucelené časy vypršení platnosti na ad hoc SAS.** Tímto způsobem i když je ohrožení SAS, je platná pouze pro po krátkou dobu. Tento postup je zvlášť důležité, pokud nemůže odkazovat zásadu uložené přístupu. Ucelené časy vypršení platnosti také omezit množství dat, který lze zapisovat do objektu blob omezením čas nahrát do něj k dispozici.
4. **Mají klienti automaticky obnovte SAS v případě potřeby.** Klienti musí obnovit SAS dobře před vypršením platnosti, aby bylo možné dobu opakovaných pokusů, pokud není k dispozici služba poskytující SAS. Pokud vaše SAS je určen pro použití pro malý počet okamžitou, krátkodobou operace, které se očekává, že být dokončeny v rámci dobu platnosti, pak to může být zbytečné, není-li třeba obnovit očekávaným SAS. Ale pokud máte klienta, který je pravidelně zajistit požadavky přes SAS, pak možnost vypršení platnosti dodává do play. Klíče význam je vyvážit potřebu SAS být krátkodobou (jako výše uvedená) s potřeba zajistit, že klient požaduje obnovení již v rané fázi dostatek (aby se zabránilo přerušení z důvodu vypršení platnosti před úspěšné obnovení sdíleného přístupového podpisu).
5. **Pečlivě se časem spuštění SAS.** Pokud nastavíte čas zahájení pro SAS k **nyní**, z důvodu hodin zkreslit (rozdíly v aktuální čas podle různých počítačů), selhání může být dodržen občas u prvních několika minut. Obecně platí nastavte výchozí čas být alespoň 15 minut v minulosti. Nebo si ho nastavit vůbec, což znamená, že platný okamžitě ve všech případech. Obecně platí i pro také – čas vypršení platnosti mějte na paměti, můžete pozorovat až 15 minut od času zkosení v obou směrech na žádnou žádostí. Pro klienty pomocí verze REST před 2012-02-12 je maximální doba trvání SAS, která neodkazuje na zásadu uložené přístupu 1 hodinu a všechny zásady zadání dlouhodobější než, se nezdaří.
6. **Buďte konkrétní prostředek nelze přistupovat.** Nejlepším postupem zabezpečení je poskytnout minimální požadovaná oprávnění uživatele. Když uživatel potřebuje pouze přístup pro čtení k jedné entity, pak jim udělte přístup pro čtení k této jedné entity a ne pro čtení, zápisu a odstraňování přístup k všechny entity. Navíc pomáhají zmenšit prostor škody, pokud SAS dojde k ohrožení bezpečnosti vzhledem k tomu SAS má méně do nesprávných rukou útočníka.
7. **Pochopte, že váš účet bude účtován na jakékoli využití, včetně udělat pomocí SAS.** Pokud zadáte oprávnění k zápisu do objektu blob, uživatel může zvolit nahrát objekt blob 200GB. Pokud jste dali je také přístup pro čtení, se rozhodnout ho stáhnout 10krát, by docházelo 2 TB v náklady na celkový výstup za vás. Znovu zadejte omezenými oprávněními pro zmírnění potenciální akce uživatelé se zlými úmysly. Pomocí krátkodobou SAS snížit této hrozby (ale být s vědomím zkosení podél koncový čas hodiny).
8. **Ověření dat zapsaných pomocí SAS.** Když klientské aplikace zapisuje data do účtu úložiště, mějte na paměti, že mohou být problémy s daty. Pokud vaše aplikace vyžaduje, aby se tato data ověřit nebo oprávnění předtím, než je připravený k použití, byste měli provést toto ověření po zapsání dat, a než bude použit v aplikaci. Tento postup také chrání před poškozený nebo škodlivý dat, zapisuje do vašeho účtu, uživatel, který správně získali SAS nebo uživatelem zneužitím uniklé SAS.
9. **Nepoužívejte vždy SAS.** Někdy rizika spojená s konkrétní operaci u vašeho účtu úložiště převažují nad přínosy SAS. Pro tyto operace vytvoření služby střední vrstvy, který zapisuje do svého účtu úložiště po provedení obchodní pravidla ověřování, ověřování a auditování. Někdy je také jednodušší ke správě přístupu k jiným způsobem. Například, pokud chcete, aby všechny objekty BLOB v kontejneru veřejně čitelné, když vytváříte kontejneru Public, místo pro přístup k poskytování SAS každého klienta.
10. **Použijte analytika úložiště do monitorování vaší aplikace.** Pomocí protokolování a metriky můžete sledovat všechny Špička při selhání ověřování kvůli výpadku ve službě zprostředkovatele SAS nebo nechtěnému odstranění zásady uložené přístupu. Najdete v článku [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) Další informace.

## <a name="sas-examples"></a>Příklady SAS
Zde jsou některé příklady oba typy sdílené přístupové podpisy SAS účtu a SAS služby.

Pokud chcete spustit tyto příklady C#, budete muset odkazují následující balíčky NuGet ve vašem projektu:

* [Azure Storage Client Library for .NET](http://www.nuget.org/packages/WindowsAzure.Storage), verze 6.x nebo novější (pro použití účtu SAS).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Další příklady, které ukazují, jak vytvořit a otestovat SAS naleznete v tématu [ukázky kódu Azure pro úložiště](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Příklad: Vytvoření a použití SAS účtu
Následující příklad kódu vytvoří účet SAS, který je platný pro služby objektů Blob a soubor a klient získá oprávnění pro čtení, zápisu a seznam oprávnění k přístupu k rozhraním API úrovně služeb. SAS účtu omezuje protokol HTTPS, musí žádosti s protokolem HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Použití SAS účtu pro přístup k rozhraní API pro úroveň služby pro službu Blob, vytvořte objekt Blob klienta pomocí SAS a koncový bod úložiště objektů Blob pro váš účet úložiště.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Příklad: Vytvoření zásady uložené přístupu
Následující kód vytvoří zásadu uložené přístup do kontejneru. Zásady přístupu můžete použít k určení omezení pro SAS služby na kontejneru nebo jeho objekty BLOB.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Příklad: Vytvoření SAS služby na kontejneru
Následující kód vytvoří SAS do kontejneru. Pokud je zadaný název existující zásady uložené přístup, je přidružen SAS tuto zásadu. Pokud je k dispozici žádné uložené přístup zásady, kód vytvoří SAS ad-hoc v kontejneru.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Příklad: Vytvoření SAS služby na objekt blob
Následující kód vytvoří SAS na objekt blob. Pokud je zadaný název existující zásady uložené přístup, je přidružen SAS tuto zásadu. Pokud je k dispozici žádné uložené přístup zásady, kód vytvoří SAS ad-hoc u objektu blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Závěr
Sdílené přístupové podpisy jsou užitečné pro zajištění omezené oprávnění k účtu úložiště na klienty, kteří by neměl mít klíč účtu. Jako takový jsou sice podstatná součást model zabezpečení pro všechny aplikace pomocí Azure Storage. Pokud budete postupovat podle osvědčených postupů, které jsou zde uvedeny, můžete zajistit větší flexibilita při přístupu k prostředkům ve vašem účtu úložiště, aniž by to ohrozilo zabezpečení aplikace SAS.

## <a name="next-steps"></a>Další kroky
* [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](../blobs/storage-manage-access-to-resources.md)
* [Delegování přístupu se sdíleným přístupovým podpisem](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Představení tabulky a fronty SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
