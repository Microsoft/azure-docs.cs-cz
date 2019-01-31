---
title: Použití sdílených přístupových podpisů (SAS) ve službě Azure Storage | Dokumentace Microsoftu
description: Naučte se používat sdílené přístupové podpisy (SAS) pro delegování přístupu k prostředkům služby Azure Storage, včetně objektů BLOB, fronty, tabulky a soubory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 7b5f4db51fca97f79f2b43bfcd5ce8dead3ba50b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470344"
---
# <a name="using-shared-access-signatures-sas"></a>Použití sdílených přístupových podpisů (SAS)

Sdílený přístupový podpis (SAS) poskytuje způsob, jak udělit omezený přístup k objektům v účtu úložiště, jiným klientům bez vystavení klíč účtu. V tomto článku budeme poskytovat přehled o modelu SAS, SAS osvědčené postupy a podívejte se na několik příkladů.

Další příklady použití SAS nad rámec těch zde uvedený, naleznete v tématu [Začínáme se službou Azure Blob Storage v rozhraní .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) a další ukázky, které jsou k dispozici v [vzorových kódů Azure](https://azure.microsoft.com/documentation/samples/?service=storage) knihovny. Stažení ukázkové aplikace a jejich spuštění nebo procházení kódu na Githubu.

## <a name="what-is-a-shared-access-signature"></a>Co je podpis sdíleného přístupu?
Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Pomocí SAS můžete udělit klientům přístup k prostředkům ve vašem účtu úložiště, aniž byste sdíleli své klíče účtu. Toto je zásadní aspekt používání sdílených přístupových podpisů v aplikacích – SAS představuje bezpečný způsob sdílení prostředků úložiště, aniž byste ohrozili své klíče účtu.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

SAS nabízí detailní kontrolu nad tím typ přístupu, kterou můžete udělit pro klienty, kteří mají sdíleného přístupového podpisu, včetně:

* Interval, přes který je platný, včetně počáteční čas a čas vypršení platnosti SAS.
* Oprávnění udělená SAS. SAS pro objekt blob může například udělit pro čtení a oprávnění k zápisu do tohoto objektu blob ale oprávnění k odstranění.
* Volitelná adresa IP nebo rozsah IP adres, ze kterých se služby Azure Storage přijmout SAS. Například můžete určit rozsah IP adres, které patří vaší organizaci.
* Protokol, přes který bude přijímat služby Azure Storage SAS. Tento nepovinný parametr slouží k omezení přístupu ke klientům pomocí protokolu HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Kdy byste měli použít sdílený přístupový podpis?
SAS můžete použít, pokud chcete poskytnout přístup k prostředkům ve vašem účtu úložiště do libovolného klienta, nikoli má přístupové klíče účtu úložiště. Váš účet úložiště obsahuje jak primární a sekundární přístupový klíč, které udělit přístup ke svému účtu pro správu a všechny prostředky v ní. Vystavení některý z těchto klíčů otevře svůj účet a možnost použití škodlivých aktivit nebo nedbalosti. Sdílené přístupové podpisy poskytnout bezpečné alternativu, která umožňuje klientům pro čtení, zápisu a odstraňování dat v účtu úložiště podle oprávnění, které jste jim explicitně udělili a bez nutnosti klíč účtu.

Běžný scénář, kde je užitečné SAS je služba, kde uživatelé čtení a zápis svá vlastní data do účtu úložiště. Ve scénáři, kde účet úložiště ukládá data uživatelů existují dva způsoby typické návrhu:

1. Klienti nahrávání a stahování dat přes službu proxy front-endu, který provádí ověřování. Tato služba front-endu proxy nabízí výhodu v podobě povolení ověření obchodní pravidla, ale pro velké objemy dat nebo velkého objemu transakcí, vytvoření služby, který se dá škálovat, aby pokryl může být obtížné nebo nákladné.

  ![Diagram scénáře: Služba front-endu proxy](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Jednoduché služby ověřuje klienta podle potřeby a pak vygeneruje sdílený přístupový podpis. Jakmile klient obdrží sdíleného přístupového podpisu, jejich prostředků účtu úložiště přístup přímo se na oprávnění definované pomocí sdíleného přístupového podpisu a intervalu dovolují SAS. SAS snižuje potřebu směrování všech dat přes službu proxy serveru front-endu.

  ![Diagram scénáře: SAS služby zprostředkovatele](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Počet skutečných služby mohou používat v hybridní kombinaci těchto dvou přístupů. Například některá data mohou zpracovat a ověřit prostřednictvím front-endu proxy serveru, zatímco jiná data se uloží a/nebo číst přímo pomocí SAS.

Kromě toho budete muset použít SAS pro autorizaci přístupu k objektu zdroje v operaci kopírování v některých scénářích:

* Při kopírování objektu blob do jiného objektu blob, který se nachází v jiný účet úložiště, musíte použít k autorizaci přístupu ke zdrojovému objektu blob SAS. Můžete volitelně použít SAS pro autorizaci přístupu na cílový objekt blob, stejně.
* Při kopírování souboru do jiného souboru, který se nachází v jiný účet úložiště, musíte použít SAS k autorizaci přístupu ke zdrojovému souboru. Můžete volitelně použít SAS pro autorizaci přístupu k cílovému souboru.
* Při kopírování objektu blob do souboru nebo souboru do objektu blob, musíte použít SAS k autorizaci přístupu k objektu zdroje i v případě, že zdrojové a cílové objektů se nacházejí v rámci stejného účtu úložiště.

## <a name="types-of-shared-access-signatures"></a>Druhy sdílených přístupových podpisů
Můžete vytvořit dva druhy sdílených přístupových podpisů:

* **SAS služby.** SAS služby deleguje přístup k prostředku jen v jedné službě úložiště: službě Blob, Queue, Table nebo File. Zobrazit [vytváření SAS služby](https://msdn.microsoft.com/library/dn140255.aspx) a [příklady SAS služby](https://msdn.microsoft.com/library/dn140256.aspx) podrobné informace o vytváření token SAS služby.
* **Account SAS.** SAS účtu delegáti přístup k prostředkům v jedné nebo více služeb úložiště. Všechny operace, které jsou k dispozici přes SAS služby jsou k dispozici prostřednictvím SAS účtu. Kromě toho s podpisem SAS účtu může delegovat přístup k operacím, které platí pro určitou službu, jako například **Get/Set vlastnosti služby** a **získat statistiky služby**. Můžete taky delegovat přístup k operacím čtení, zápis a odstranění pro kontejnery objektů blob, tabulky a sdílené složky, který se nedá vymezit přes SAS služby. Zobrazit [vytváření SAS účtu](https://msdn.microsoft.com/library/mt584140.aspx) podrobné informace o vytváření token SAS účtu.

## <a name="how-a-shared-access-signature-works"></a>Jak funguje sdíleného přístupového podpisu
Sdílený přístupový podpis je podepsaný identifikátor URI, který odkazuje na jeden nebo více prostředků úložiště a zahrnuje token, který obsahuje speciální sadu parametrů dotazu. Token, který označuje, jak můžou být prostředky dostupné klienta. Jeden z parametrů dotazu, podpisu, je vytvořen z parametrů SAS a podepsán s klíčem účtu. Tento podpis používá Azure Storage k autorizaci přístupu k prostředku úložiště.

Tady je příklad identifikátoru URI SAS, zobrazuje identifikátor URI a tokenu SAS:

![Součástí identifikátoru URI SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

SAS token je řetězec, můžete generovat *klienta* na straně (najdete v článku [SAS příklady](#sas-examples) části s příklady kódu). Token SAS, které vygenerujete pomocí klientskou knihovnu pro úložiště, například není sledována pomocí služby Azure Storage žádným způsobem. Neomezený počet tokenů SAS můžete vytvořit na straně klienta.

Když klient poskytuje jako součást požadavku na identifikátor URI SAS do služby Azure Storage, služba zkontroluje SAS parametry a podpis k ověření, že je platný pro ověření žádosti. Pokud službu ověřuje, že podpis je platný, pak je požadavek autorizován. Požadavek v opačném případě je odmítnuto. kód chyby 403 (zakázáno).

## <a name="shared-access-signature-parameters"></a>Sdílený přístupový podpis parametry
SAS účtu a tokeny SAS služby zahrnují některé společné parametry a také provést několik parametrů, které se liší.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametry, které jsou společné pro SAS účtu a tokeny SAS služby
* **Verze rozhraní API** volitelný parametr, který určuje verzi služby úložiště používat k provedení požadavku.
* **Verze služby** povinný parametr, který určuje verzi služby úložiště používat k ověření požadavku.
* **Čas spuštění.** Toto je doba, jakou SAS začne platit. Čas zahájení pro sdílený přístupový podpis je volitelný. Pokud čas spuštění je vynechán, sdíleného přístupového podpisu je hned platná. Počáteční čas musí být vyjádřena ve standardu UTC (Coordinated Universal Time), se speciální označení UTC ("Z"), třeba `1994-11-05T13:15:30Z`.
* **Čas vypršení platnosti.** Toto je doba, po jejímž uplynutí sdíleného přístupového podpisu již není platný. Osvědčené postupy doporučujeme, abyste zadejte čas vypršení platnosti pro SAS, nebo ho přidružit k uložené zásady přístupu. Čas vypršení platnosti musí být vyjádřena ve standardu UTC (Coordinated Universal Time), se speciální označení UTC ("Z"), například `1994-11-05T13:15:30Z` (Další informace naleznete níže).
* **Oprávnění.** Oprávnění zadaná v protokolu SAS určit, jaké operace klienta můžete provést s prostředkem úložiště pomocí sdíleného přístupového podpisu. K dispozici oprávnění se liší pro SAS účtu a SAS služby.
* **IP.** Volitelný parametr, který určuje IP adresu nebo rozsah IP adres mimo systém Azure (v části [stav konfigurace relace směrování](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) pro Express Route) odkud tak, aby přijímal požadavky.
* **Protocol.** Volitelný parametr, který určuje protokol, povolené pro žádost. Možné hodnoty jsou HTTPS a HTTP (`https,http`), což je pouze výchozí hodnotu, nebo HTTPS (`https`). Všimněte si, že HTTP pouze není povolenou hodnotu.
* **Podpis.** Podpis je vytvořen z ostatní parametry zadané jako součást tokenu a pak se zašifrují. Podpis se používá k autorizaci přístupu k prostředkům zadaným úložiště.

### <a name="parameters-for-a-service-sas-token"></a>Parametry pro token SAS služby
* **Prostředek úložiště.** Prostředky úložiště, pro které můžete delegovat přístup se službou SAS patří:
  * Kontejnerům a objektům BLOB
  * Sdílené složky a soubory
  * Fronty
  * Tabulky a rozsahy tabulkové entity.

### <a name="parameters-for-an-account-sas-token"></a>Parametry pro token SAS účtu
* **Službu nebo služby.** SAS účtu může delegovat přístup k jednomu nebo více služeb úložiště. Můžete například vytvořit SAS účtu, který deleguje přístup ke službě objektů Blob a souborů. Nebo můžete vytvořit SAS, že delegáty přístup na všechny čtyři služby (objekt Blob, fronty, tabulky a souboru).
* **Typy prostředků úložiště.** Účet SAS se vztahuje na jeden nebo více tříd prostředky úložiště, nikoli konkrétní prostředek. Můžete vytvořit SAS pro delegování přístupu k účtu:
  * Rozhraní úrovně služeb API, která je volána před prostředek účtu úložiště. Mezi příklady patří **Get/Set vlastnosti služby**, **získat statistiky služby**, a **seznamu kontejnery/fronty/tabulek/složky**.
  * Kontejner úroveň rozhraní API, která jsou volány u těchto objektů kontejneru u každé služby: Objekt blob, kontejnerů, fronty, tabulky a sdílené složky. Mezi příklady patří **vytvoření/odstranění kontejneru**, **vytvoření nebo odstranění fronty**, **vytvoření/odstranění tabulky**, **sdílenou složku vytvořit/odstranit**a  **Zobrazit seznam objektů BLOB a souborů a adresářů**.
  * API na úrovni objektů, kterému se říká s objekty BLOB, fronty zpráv, tabulka entity a soubory. Například **Put Blob**, **dotazu Entity**, **získání zpráv**, a **vytvořit soubor**.

## <a name="examples-of-sas-uris"></a>Mezi příklady identifikátorů URI SAS

### <a name="service-sas-uri-example"></a>Příklad identifikátor URI SAS služby

Tady je příklad služby identifikátor URI SAS, který poskytuje oprávnění čtení a zápisu do objektu blob. V tabulce rozdělí každou část identifikátoru URI k pochopení jejího přínosu pro SAS:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Název | Část SAS | Popis |
| --- | --- | --- |
| Identifikátor URI objektu BLOB |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adresa objektu blob. Všimněte si, že pomocí protokolu HTTPS se důrazně doporučuje. |
| Verze služby úložiště |`sv=2015-04-05` |Pro verzi 2012-02-12 služeb úložiště a později tento parametr určuje verze se má použít. |
| Počáteční čas |`st=2015-04-29T22%3A18%3A26Z` |Zadaná v čase UTC. Pokud chcete platit okamžitě SAS, vynechejte čas spuštění. |
| Doba konce platnosti |`se=2015-04-30T02%3A23%3A26Z` |Zadaná v čase UTC. |
| Prostředek |`sr=b` |Prostředek je v objektu blob. |
| Oprávnění |`sp=rw` |Oprávnění udělená SAS Read (r) a zápisu (w). |
| Rozsah IP adres |`sip=168.1.5.60-168.1.5.70` |Rozsah IP adres, ze kterých se žádost o přijetí. |
| Protocol (Protokol) |`spr=https` |Jsou povoleny pouze žádosti přes protokol HTTPS. |
| Podpis |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Používá se k autorizaci přístupu k objektu blob. Podpis je kódu HMAC s vypočítaný přes řetězec přihlašování a klíč pomocí algoritmus SHA256 a potom zakódován pomocí kódování Base64. |

### <a name="account-sas-uri-example"></a>Příklad identifikátor URI SAS účtu

Tady je příklad účtu SAS, který používá stejné společné parametry na tokenu. Protože tyto parametry jsou popsané výše, nejsou popsané tady. Jenom parametry, které jsou specifické pro účet, který přidružení zabezpečení jsou popsány v následující tabulce.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Název | Část SAS | Popis |
| --- | --- | --- |
| Identifikátor URI prostředku |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Služba koncový bod služby Blob, s parametry pro získání vlastnosti služby (při volání s GET) nebo nastavení vlastnosti služby (při volání sadou). |
| Služby |`ss=bf` |SAS se vztahuje na služby objektů Blob a souborů |
| Typy prostředků |`srt=s` |SAS se vztahuje na úrovni služby operations. |
| Oprávnění |`sp=rw` |Oprávnění udělit přístup ke čtení a zápisu operace. |

Vzhledem k tomu, že oprávnění jsou omezeny na úrovni služby, jsou přístupné operací s Tento SAS **získat vlastnosti služby Blob Service** (čtení) a **nastavit vlastnosti služby Blob Service** (zápis). Nicméně s jiný identifikátor URI prostředku, stejný token SAS možné využít také pro delegování přístupu k **získat statistiky služby Blob** (čtení).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Řízení SAS s uložené zásady přístupu
Sdílený přístupový podpis můžete provést jednu z těchto dvou tvarů:

* **Ad hoc SAS:** Při vytváření ad hoc SAS, čas zahájení, čas vypršení platnosti a oprávnění pro SAS jsou všechny zadané v identifikátoru URI SAS (nebo předpokládané, a to v případě, pokud je vynechán čas zahájení). Tento typ SAS lze vytvořit jako SAS účtu nebo SAS služby.
* **SAS s uložené zásady přístupu:** Uložené zásady přístupu je definován na kontejner prostředku--kontejner objektů blob, tabulky, fronty, nebo sdílená složka, – a je možné spravovat omezení pro jednu nebo více sdílených přístupových podpisů. Když přiřadíte SAS uložené zásady přístupu, dědí SAS omezení – čas zahájení, čas vypršení platnosti a oprávnění – definice zásady přístupu.

> [!NOTE]
> V současné době SAS účtu musí být ad hoc SAS. Uložených přístupu zásady se zatím nepodporují pro SAS účtu.

Rozdíl mezi dvě různými formami je důležité pro jeden klíč scénář: odvolání. Protože identifikátor URI SAS je adresa URL, kdo, který získá SAS můžete použít, bez ohledu na to, který byl původně vytvořen. Pokud SAS je publikována veřejně, je možné použít kdokoli na světě. SAS uděluje přístup k prostředkům všem uživatelům, kteří prokazují dokud jednu ze čtyř akcí se stane:

1. Je dosaženo času vypršení platnosti zadané v protokolu SAS.
2. Čas vypršení platnosti zadané v zásadách přístupu uložené odkazuje sdíleného přístupového podpisu je dosaženo (Pokud je odkazováno uložené zásady přístupu a určuje, že čas vypršení platnosti). Tato situace může nastat, protože interval uplyne, nebo jste upravili zásady přístupu s času vypršení platnosti v minulosti, což je jeden způsob odvolání SAS.
3. Zásady přístupu uložené odkazuje sdíleného přístupového podpisu je odstranit, což je jiný způsob odvolání SAS. Všimněte si, že pokud znovu vytvoříte zásady přístupu se stejným názvem, všechny stávající tokeny SAS se znovu platit podle oprávnění přidružené k této uložené zásady přístupu (za předpokladu, že, který nebyl předán čas vypršení platnosti na sdíleného přístupového podpisu). Pokud je máte v úmyslu odvolání SAS, je nutné použít jiný název, pokud znovu vytvoříte zásady přístupu s času vypršení platnosti v budoucnu.
4. Klíč účtu, který byl použit k vytvoření sdíleného přístupového podpisu se znova vygeneroval. Obnovuje se klíč účtu způsobí, že všechny součásti aplikace pomocí klíče k selhání k autorizaci, dokud máte aktualizované, aby používaly jiné platný účet klíč nebo klíč účtu nově znovu vygenerovalo.

> [!IMPORTANT]
> Identifikátor URI sdíleného přístupového podpisu je přidružená ke klíči účet použitý k vytvoření podpisu a přidruženého uložené zásady přístupu (pokud existuje). Pokud není zadána žádná uložené zásady přístupu, chcete-li změnit klíč účtu je jediný způsob, jak odebrat sdílený přístupový podpis.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Ověřování z klientské aplikace pomocí SAS
Klient, který je ve vlastnictví SAS pomocí SAS můžete autorizovat požadavek proti účtu úložiště, pro kterou, které nemají klíče účtu. SAS můžete zahrnout v připojovacím řetězci, nebo použít přímo z odpovídajícího konstruktoru nebo metody.

### <a name="using-a-sas-in-a-connection-string"></a>V připojovacím řetězci pomocí SAS
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Pomocí SAS v konstruktoru nebo – metoda
Několik konstruktorů knihovny klienta služby Azure Storage a přetížení metody nabízejí parametr SAS, tak, aby schválíte žádost o službu pomocí SAS.

Například tady identifikátor URI SAS slouží k vytvoření odkazu na objekt blob bloku. SAS poskytuje pověření, kterým jenom potřebné pro žádost. Odkaz na objekt blob bloku se pak použije pro operaci zápisu:

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
Při použití sdílených přístupových podpisů v aplikacích, je třeba vědět dvě potenciální rizika:

* Pokud je úniku SAS, můžete použít libovolný uživatel, který získá, což může ohrozit potenciálně účtu úložiště.
* Li SAS vyprší platnost klientské aplikace a aplikace nedokáže načíst nové SAS služby a potom funkcí aplikace může bránit.

Toto riziko omezit, pomůže následující doporučení pro použití sdílených přístupových podpisů:

1. **Používejte vždy HTTPS** k vytvoření nebo distribuovat SAS. Pokud SAS je předán přes protokol HTTP a zachytit, je moct číst sdíleného přístupového podpisu a pak použít stejně jako určený uživatel může mít potenciálně ohrožení citlivých dat nebo poškození dat uživatelem zlými úmysly umožňující útočník provedení útoku man-in-the-middle.
2. **Odkaz na uložené zásady přístupu, kde je to možné.** Uložené zásady přístupu vám dávají možnost odvolat oprávnění bez nutnosti znovu vygenerovat klíče účtu úložiště. Nastavení vypršení platnosti na tyto velmi úplně v budoucnu (nebo nekonečné) a ujistěte se, že se pravidelně aktualizují dále ji přesunout do budoucna.
3. **Použijte krátkodobém dobách vypršení platnosti na ad hoc SAS.** Tímto způsobem i v případě, že dojde k ohrožení SAS, je platný pouze krátkou dobu. Tento postup je zvlášť důležité, pokud nelze odkazovat uložené zásady přístupu. Ucelené dobách vypršení platnosti také omezit množství dat, které se dají zapisovat do objektu blob tím, že omezíte čas pro nahrání do něj.
4. **Mají klienti automaticky obnovovat SAS v případě potřeby.** Klienti měli obnovit sdíleného přístupového podpisu kontejneru před vypršením platnosti, abyste měli čas opakování, pokud služba poskytující SAS není k dispozici. Pokud se má být použit pro malý počet okamžitě, krátkodobé a jednorázové operace, které se očekává, že dokončit v daném období vypršení platnosti vašeho SAS, potom to může být zbytečné, obnovení není očekávaným SAS. Nicméně pokud máte klienta, který je pravidelně zasílat požadavky přes SAS, pak možnost vypršení platnosti vstupu do play. Klíče aspektem je vyvážení potřeba mít krátkodobé trvání SAS (jak už bylo uvedeno) s je potřeba zajistit, že klient žádá o prodloužení platnosti již v rané fázi dostatek (Pokud chcete zabránit přerušení z důvodu SAS vyprší před úspěšné obnovení).
5. **Buďte opatrní s časem zahájení SAS.** Pokud nastavíte čas zahájení pro SAS na **nyní**, z důvodu hodin zkreslit (rozdíly v současnosti podle různých počítačích), selhání může být dodržen přerušovaně za prvních pár minut. Obecně platí nastavte výchozí čas být aspoň 15 minut v minulosti. Nebo, nenastavujte ho vůbec, což znamená, že platný okamžitě ve všech případech. Obecně platí i pro čas vypršení platnosti také – mějte na paměti, že můžete sledovat až 15 minut od času zkosení v obou směrech na všechny požadavky. Maximální doba trvání SAS, který neodkazuje na uložené zásady přístupu pro klienty pomocí REST verzi nižší než 2012-02-12, je 1 hodina a všechny zásady určující dlouhodobější než, který se nezdaří.
6. **Buďte konkrétní přístup k prostředku.** Z bezpečnostních důvodů je, zadejte uživatele s minimální požadovaná oprávnění. Když uživatel potřebuje pouze přístup pro čtení k jedné entity, potom jim udělte přístup pro čtení k této jedné entity a ne pro čtení/zápis/delete přístup ke všem entitám. Navíc pomáhají snížit poškození v případě ohrožení bezpečnosti SAS, protože sdíleného přístupového podpisu má míň energie v rukou útočník.
7. **Vysvětlení, že váš účet se bude účtovat veškeré využití, která se provádí pomocí SAS.** Pokud poskytnete oprávnění k zápisu do objektu blob, můžete zvolit k nahrání objektu blob 200GB. Pokud jste dali je také přístup pro čtení, se může rozhodnete si ho stáhnout 10krát, bez 2 TB za výchozí přenos dat pro vás. Znovu zadejte omezenými oprávněními, aby mohlo včas reagovat na potenciální akce uživateli se zlými úmysly. Krátkodobé a jednorázové SAS můžete tuto hrozbu zmírnit (ale dávejte zkosení podél koncový čas hodiny).
8. **Ověření dat zapsaných pomocí SAS.** Když klientská aplikace zapíše data do účtu úložiště, mějte na paměti, že může být problémy s daty. Pokud vaše aplikace vyžaduje, aby se data ověření nebo oprávnění předtím, než je připravený k použití, měli byste provést toto ověření po zapsání dat a předtím, než je použit v aplikaci. Tento postup také chrání před poškozený nebo škodlivý data je zapsána do vašeho účtu uživatele, který správně získat SAS nebo uživatelem využívajícím uniklé SAS.
9. **Nepoužívejte vždy SAS.** Někdy rizika spojená s konkrétní operace proti účtu úložiště převážit nad výhodami SAS. Pro tyto operace vytvoření střední vrstvy služby, která zapisuje do vašeho účtu úložiště po provedení obchodní pravidla ověřování, ověřování a auditování. Někdy je také jednodušší Správa přístupu k jiným způsobem. Například pokud chcete, aby všechny objekty BLOB v kontejneru veřejně čitelné, můžete vytvořit kontejner Public, místo poskytování SAS pro přístup na všechny klienty.
10. **Použití Storage Analytics pro monitorování vaší aplikace.** Protokolování a metriky můžete sledovat všechny nárůst selhání ověřování kvůli výpadku služby Zprostředkovatel SAS nebo nechtěnému odstranění uložených zásad přístupu. Zobrazit [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) pro další informace.

## <a name="sas-examples"></a>Příklady SAS
Níže je několik příkladů oba typy sdíleného přístupového podpisu SAS účtu a SAS služby.

Pokud chcete spustit tyto příklady jazyka C#, budete muset odkaz následující balíčky NuGet ve vašem projektu:

* [Klientská knihovna Azure Storage pro .NET](http://www.nuget.org/packages/WindowsAzure.Storage), verze 6.x nebo novější (pro použití účtu SAS).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Další příklady, které ukazují, jak vytvořit a otestovat SAS najdete v tématu [vzorových kódů Azure pro ukládání](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Příklad: Vytvoření a použití SAS účtu
Následující příklad kódu vytvoří účet SAS, který je platný pro objekt Blob a souborové služby a klient získá oprávnění číst, zapisovat a vypsat seznam oprávnění pro přístup k API na úrovni služby. Protokol HTTPS, proto musí být požadavek pomocí protokolu HTTPS omezuje podpisem SAS účtu.

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

Použití SAS účtu pro přístup k API na úrovni služby pro službu Blob service, vytvořte objekt klienta objektů Blob v účtu úložiště pomocí sdíleného přístupového podpisu a koncový bod služby Blob storage.

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

### <a name="example-create-a-stored-access-policy"></a>Příklad: Vytvořit uložené zásady přístupu
Následující kód vytvoří v kontejneru uložené zásady přístupu. Zásady přístupu můžete použít k určení omezení pro SAS služby v kontejneru a jeho objektům BLOB.

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

### <a name="example-create-a-service-sas-on-a-container"></a>Příklad: Vytvoření SAS služby v kontejneru
Následující kód vytvoří SAS v kontejneru. Pokud je zadaný název existující zásady přístupu, této zásady souvisí s SAS. Pokud je k dispozici žádné uložené zásady přístupu, kód vytvoří SAS ad-hoc v kontejneru.

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
Následující kód vytvoří SAS objektu BLOB. Pokud je zadaný název existující zásady přístupu, této zásady souvisí s SAS. Pokud je k dispozici žádné uložené zásady přístupu, kód vytvoří SAS ad-hoc u objektu blob.

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
Sdílené přístupové podpisy jsou užitečná omezená oprávnění k účtu úložiště na klienty, kteří by neměl mít klíč účtu. V důsledku toho jsou důležitou součástí model zabezpečení pro každou aplikaci pomocí služby Azure Storage. Pokud budete postupovat podle osvědčených postupů, které jsou tady uvedené, můžete SAS k zajištění větší flexibility, přístupu k prostředkům ve vašem účtu úložiště bez negativního vlivu zabezpečení vaší aplikace.

## <a name="next-steps"></a>Další kroky
* [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm objektů Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](../blobs/storage-manage-access-to-resources.md)
* [Delegování přístupu se sdíleným přístupovým podpisem](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Úvod do tabulky a SAS fronty.](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
