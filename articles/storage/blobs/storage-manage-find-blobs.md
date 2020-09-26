---
title: Správa a hledání dat v úložišti objektů BLOB v Azure pomocí indexu objektů BLOB (Preview)
description: Naučte se používat značky indexu objektů BLOB ke kategorizaci, správě a dotazování objektů BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.custom: references_regions
ms.openlocfilehash: 70d0e31809227d5e27f8f2b22a7703d5a8ccca3c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275060"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-preview"></a>Správa a hledání dat objektů BLOB v Azure pomocí indexu objektů BLOB (Preview)

Protože datové sady získají větší a větší, hledání konkrétního objektu v moři dat může být obtížné a frustrující. Index objektu BLOB poskytuje funkce pro správu a zjišťování dat pomocí atributů značky indexu klíč-hodnota, které umožňují kategorizaci a hledání objektů v jednom kontejneru nebo ve všech kontejnerech v účtu úložiště. Později, jako požadavky na změny dat, lze objekty dynamicky řadit do kategorií tím, že aktualizujete své rejstříkové značky, a přitom zbývajícím způsobem přinesete svou stávající organizaci kontejneru. Použití indexu objektů BLOB může zjednodušit vývoj konsolidací dat objektů BLOB a přidružených atributů indexu ve stejné službě. umožňuje vytvářet efektivní a škálovatelné aplikace s využitím nativních funkcí.

Index objektu BLOB umožňuje:

- Dynamické kategorizace objektů BLOB pomocí značek indexu klíčových hodnot pro správu dat
- Rychlé vyhledání určitých objektů BLOB s příznakem v jednom kontejneru nebo celého účtu úložiště
- Určení podmíněného chování pro rozhraní API objektů blob na základě vyhodnocení značek indexu
- Použití značek indexů k pokročilým kontrolám funkcí objektů blob, jako je [Správa životního cyklu](storage-lifecycle-management-concepts.md)

Vezměte v úvahu scénář, ve kterém máte k dispozici miliony objektů BLOB v účtu úložiště, které jsou k dispozici v mnoha různých aplikacích. Chcete najít všechna související data z jednoho projektu, ale nejste si jisti, co je v oboru, protože data je možné rozložit mezi více kontejnerů s různými zásadami vytváření názvů objektů BLOB. Nicméně víte, že aplikace nahrávají všechna data pomocí značek na základě jejich příslušného projektu a identifikace popisu. Místo hledání v milionech objektů BLOB a porovnávání názvů a vlastností můžete jednoduše použít `Project = Contoso` jako kritéria vyhledávání. Index objektu BLOB filtruje všechny kontejnery napříč celým účtem úložiště, aby bylo možné rychle vyhledat a vrátit jenom sadu objektů BLOB 50 z `Project = Contoso` .

Pokud chcete začít s příklady použití indexu objektů blob, přečtěte si téma [využití indexu objektů BLOB ke správě a hledání dat](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Značky indexu objektů BLOB a správa dat

Předpony názvů kontejnerů a objektů BLOB jsou jednorozměrné kategorizace pro uložená data. Index objektu BLOB teď umožňuje kategorizaci více dimenzí pro všechny [datové typy objektů BLOB (blok, připojení nebo stránka)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) se aplikovanými značkami atributů. Tato multidimenzionální kategorizace je nativně indexována a zpřístupňuje vám pro rychlé dotazování a hledání dat.

Vezměte v úvahu následující pět objektů BLOB v účtu úložiště:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *fotky/bannerphoto.png*
- *archivy/dokončené/2019review.pdf*
- *protokoly/2020/01/01/logfile.txt*


Tyto objekty BLOB se momentálně oddělují pomocí předpony *kontejneru/virtuální složky nebo názvu objektu BLOB*. Pomocí indexu objektů blob můžete nastavit atribut značky indexu `Project = Contoso` u těchto pěti objektů blob, aby je bylo možné kategorizovat společně a přitom zachovat stávající předpony organizace. Tím se eliminuje nutnost přesunu dat vyvoláním možnosti filtrování a hledání dat pomocí multidimenzionálního indexu platformy úložiště.

## <a name="setting-blob-index-tags"></a>Nastavení značek indexu objektů BLOB

Značky indexu objektu BLOB jsou atributy klíč-hodnota, které se dají použít pro nové nebo existující objekty v rámci vašeho účtu úložiště. Můžete zadat rejstříkové značky během procesu nahrávání pomocí operací PutBlob, PutBlockList nebo CopyBlob a volitelné hlavičky x-MS-Tags. Pokud už máte objekty blob ve svém účtu úložiště, můžete volat SetBlobTags pomocí formátovaného dokumentu XML, který určuje atributy značky indexu objektu BLOB v těle požadavku.

Vezměte v úvahu následující příklady značek, které lze nastavit.

V objektu blob můžete použít jednu značku, která popisuje, kdy se data dokončí zpracování.

> "processedDate" = ' 2020-01-01 '

V objektu blob můžete použít více značek, aby byly výstižnější data.

> "Projekt" = ' contoso '  
> "Klasifikované" = ' true '  
> "Stav" = Nezpracováno "  
> "Priorita" = ' 01 '

Chcete-li upravit existující atributy značek indexu, je nutné nejprve načíst existující atributy značky, upravit atributy značky a nahradit ji operací SetBlobTags. Chcete-li odebrat všechny značky indexu z objektu blob, zavolejte operaci SetBlobTags bez zadaných atributů značek. Jelikož jsou značky indexu objektů BLOB dílčím prostředkem k obsahu dat objektů blob, SetBlobTags nemění žádný podkladový obsah a nemění čas poslední změny objektu BLOB nebo eTag (značka entity). Můžete vytvořit nebo upravit značky indexu pro všechny aktuální základní objekty BLOB a předchozí verze. značky pro snímky nebo obnovitelné odstraněné objekty blob však nelze upravovat.

Následující omezení se vztahují na značky indexu objektu BLOB:
- Každý objekt BLOB může mít až 10 značek indexu objektu BLOB.
- Klíče značek musí být v rozmezí od 1 do 128 znaků.
- Hodnoty značek musí být mezi 0 a 256 znaky.
- V klíčích značek a hodnotách se rozlišují malá a velká písmena.
- Klíče a hodnoty značek podporují pouze řetězcové typy dat; všechna čísla, datum, čas nebo speciální znaky budou uloženy jako řetězce.
- Klíče a hodnoty značek musí splňovat následující pravidla pojmenování:
  - Alfanumerické znaky: a – z, A-Z, 0-9
  - Speciální znaky: mezera, znaménko, mínus, tečka, dvojtečka, rovná se, podtržítko, lomítko

## <a name="getting-and-listing-blob-index-tags"></a>Získání a výpis značek indexu objektů BLOB

Značky indexu objektů BLOB se ukládají jako dílčí prostředek spolu s daty objektů BLOB a dají se načíst nezávisle na základním obsahu dat blobu. Po nastavení lze značky indexu objektu BLOB pro jeden objekt BLOB načíst a revidovat okamžitě pomocí operace GetBlobTags. Operace ListBlobs s `include:tags` parametrem vrátí všechny objekty BLOB v rámci kontejneru společně s použitými značkami indexu objektu BLOB.

U všech objektů BLOB s alespoň 1 značkou indexu objektu BLOB se vrátí x-MS-tag-Count v operacích ListBlobs, getblob a GetBlobProperties, které označují počet značek indexu objektu blob, které v objektu BLOB existují.

## <a name="finding-data-using-blob-index-tags"></a>Hledání dat pomocí značek indexu objektů BLOB

Pomocí značek indexu objektů BLOB nastavených v objektech blob modul indexování zpřístupňuje tyto atributy klíč/hodnota do multidimenzionálního indexu. I když existují značky indexu v objektu BLOB a je možné je načíst hned, může trvat nějakou dobu, než se index objektu BLOB aktualizuje pomocí aktualizovaných atributů indexových značek. Po aktualizaci indexu objektu blob můžete teď využít nativní možnosti dotazů a zjišťování nabízené službou BLOB Storage.

Operace FindBlobsByTags umožňuje získat filtrovanou návratovou sadu objektů blob, jejichž značky indexu odpovídají danému výrazu dotazu na index objektu BLOB. Index objektu BLOB podporuje filtrování napříč všemi kontejnery v rámci vašeho účtu úložiště, nebo můžete omezit rozsah filtrování jenom na jeden kontejner. Vzhledem k tomu, že všechny klíče a hodnoty indexu objektu BLOB jsou řetězce, podporované relační operátory používají řazení lexikografickým pořadím podle hodnot značek indexu.

Následující kritéria se vztahují na filtrování indexu objektů BLOB:
- Klíče značek by měly být uzavřeny do dvojitých uvozovek (")
- Hodnoty značek a názvy kontejnerů by měly být uzavřeny v jednoduchých uvozovkách (')
- Znak @ je povolený jenom pro filtrování u konkrétního názvu kontejneru (tj. @container = "ContainerName").
- Filtry se používají pro řazení lexikografickým pořadím řetězců.
- Operace se stejnými oblastmi na stejném klíči jsou neplatné (tj. "Rank" > "10" a "Rank" >= ' 15 ')
- Při použití REST k vytvoření výrazu filtru by měly být znaky kódované identifikátorem URI.

Následující tabulka uvádí všechny platné operátory pro FindBlobsByTags:

|  Operátor  |  Popis  | Příklad |
|------------|---------------|---------|
|     =      |     Je rovno     | "Stav" = probíhá " |
|     >      |  Větší než | "Date" > ' 2018-06-18 ' |
|     >=     |  Větší než nebo rovno | "Priorita" >= "5" |
|     <      |  Menší než   | "Stáří" < "32" |
|     <=     |  Menší než nebo rovno  | "Společnost" <= "contoso" |
|    AND     |  Logický operátor and  | "Rank" >= "010" a "Rank" < "100" |
| @container | Nastavit obor na konkrétní kontejner | @container = "VideoFiles" a "status" = "Hotovo" |

> [!NOTE]
> Seznámení s lexicographical objednání při nastavení a dotazování na značky.
> - Čísla jsou řazena před písmeny. Čísla jsou řazena podle první číslice.
> - Velká písmena jsou řazena před malými písmeny.
> - Symboly nejsou standardní. Některé symboly jsou řazeny před číselnými hodnotami. Další symboly jsou seřazené před nebo za písmeny.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Podmíněné operace objektů BLOB pomocí značek indexu objektu BLOB
V REST verze 2019-10-10 a novějších [rozhraní API služby BLOB Service](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) teď podporuje podmíněné záhlaví x-MS-if-Tags, takže operace bude úspěšná jenom v případě, že je splněná zadaná podmínka indexu objektu BLOB. Pokud není podmínka splněná, zobrazí se `error 412: The condition specified using HTTP conditional header(s) is not met` .

Záhlaví x-MS-if-Tags se může kombinovat s ostatními existujícími podmíněnými hlavičkami HTTP (Pokud-Match, If-None-Match atd.).  Pokud je v požadavku k dispozici více podmíněných hlaviček, musí všechny vyhodnotit hodnotu true, aby operace proběhla úspěšně.  Všechny podmíněné hlavičky jsou efektivně kombinovány s logickými a.

Následující tabulka uvádí všechny platné operátory pro podmíněné operace:

|  Operátor  |  Popis  | Příklad |
|------------|---------------|---------|
|     =      |     Je rovno     | "Stav" = probíhá " |
|     <>     |   Není rovno   | "Status"  <>  "Hotovo"  |
|     >      |  Větší než | "Date" > ' 2018-06-18 ' |
|     >=     |  Větší než nebo rovno | "Priorita" >= "5" |
|     <      |  Menší než   | "Stáří" < "32" |
|     <=     |  Menší než nebo rovno  | "Společnost" <= "contoso" |
|    AND     |  Logický operátor and  | "Rank" >= "010" a "Rank" < "100" |
|     NEBO     | Logický operátor OR   | "Stav" = "dokončeno" nebo "Priorita" >= "05" |

> [!NOTE]
> Existují dva další operátory, nerovnosti a logické nebo, které jsou povoleny v podmíněné hlavičce x-MS-if-Tags pro operaci objektu blob, ale v operaci FindBlobsByTags neexistují.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrace platforem se značkami indexu objektů BLOB

Značky indexu objektů BLOB neumožňují kategorizaci, správu a hledání dat objektů blob, ale také poskytují integraci s dalšími Blob service funkcemi, jako je například [Správa životního cyklu](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Správa životního cyklu

Pomocí nového blobIndexMatch jako filtru pravidla ve správě životního cyklu můžete přesunovat data na vícevrstvé vrstvy nebo odstraňovat data na základě značek indexu aplikovaných na vaše objekty blob. To vám umožňuje podrobněji podrobnější pravidla a přesouvat nebo odstraňovat data pouze v případě, že odpovídají zadaným kritériím značek.

V pravidle životního cyklu můžete nastavit, aby se index objektu BLOB shodoval jako samostatný filtr nastavený na základě akcí u tagovaných dat. Případně můžete kombinovat prefix a shodu indexu objektů blob, aby odpovídaly konkrétnějším datovým sadám. Použití více filtrů na pravidlo životního cyklu se považuje za logické a operace tak, že se akce uplatní pouze v případě, že se všechna kritéria filtru shodují.

Následující pravidlo správy životního cyklu se vztahuje na objekty blob bloku v kontejneru VideoFiles a objekty blob vrstev do úložiště archivu, jenom pokud data odpovídají kritériím značek indexu objektu BLOB ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
![Příklad pravidla shody indexu objektu BLOB pro správu životního cyklu v Azure Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Oprávnění a autorizace

Přístup k indexu objektů blob můžete autorizovat pomocí jednoho z následujících přístupů:

- Pomocí řízení přístupu na základě role (RBAC) udělíte oprávnění objektu zabezpečení služby Azure Active Directory (Azure AD). Microsoft doporučuje používat Azure AD pro zajištění vysokého zabezpečení a snadného použití. Další informace o používání služby Azure AD s operacemi objektů BLOB najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](../common/storage-auth-aad.md).
- Pomocí sdíleného přístupového podpisu (SAS) můžete delegovat přístup k indexu objektů BLOB. Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).
- Použitím přístupových klíčů účtu k autorizaci operací se sdíleným klíčem. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key).

Značky indexu objektu BLOB jsou dílčím prostředkem pro data objektů BLOB. Uživatel s oprávněními nebo tokenem SAS pro čtení nebo zápis objektů BLOB nemůže mít přístup k tagům indexu objektu BLOB.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role
Volajícím, kteří používají [identitu Azure AD](../common/storage-auth-aad.md) , můžou mít k dispozici následující oprávnění pro práci se značkami indexu objektů BLOB.

|   Operace objektu BLOB  |  Akce RBAC   |
|--------------------|----------------|
| Hledání objektů BLOB podle značek | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Filter/Action |
| Nastavení značek objektů BLOB      | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Write |
| Získat značky objektu BLOB      | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Read |

K provozu nad značkami se vyžadují další oprávnění oddělené od základních dat objektů BLOB. Roli vlastníka dat objektu BLOB služby Storage budou udělena všechna tři tato oprávnění. Čtečka dat objektů BLOB úložiště bude mít udělené jenom objekty blob najít pomocí značek a získat oprávnění značek objektů BLOB.

### <a name="sas-permissions"></a>Oprávnění SAS
Volajícím, kteří používají [sdílený přístupový podpis (SAS)](../common/storage-sas-overview.md) , můžou mít udělena vymezená oprávnění pro práci s značkami objektů BLOB.

#### <a name="blob-sas"></a>SAS objektu BLOB
V Blob service SAS můžou být udělena následující oprávnění umožňující přístup k tagům indexu objektů BLOB. Samotná oprávnění ke čtení a zápisu objektů BLOB nejsou dostatečná, aby bylo možné číst ani zapisovat do jejich indexových značek.

|  Oprávnění  |  Symbol identifikátoru URI  | Povolené operace |
|--------------|--------------|--------------------|
|  Rejstřík – značky  |      t      | Získání a nastavení značek indexu objektů BLOB pro objekt BLOB |

#### <a name="container-sas"></a>SAS kontejneru
Následující oprávnění mohou být udělena v rámci SAS služby kontejneru, aby bylo možné filtrovat značky objektů BLOB.  Oprávnění seznam objektů BLOB nestačí k tomu, aby bylo možné filtrovat objekty BLOB pomocí značek jejich indexu.

|  Oprávnění  |  Symbol identifikátoru URI  | Povolené operace |
|--------------|--------------|--------------------|
| Rejstřík – značky   |      f      | Vyhledání objektů BLOB s využitím značek indexu objektů BLOB |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Výběr mezi metadaty a značkami indexu objektu BLOB
Značky indexu objektů BLOB a metadata poskytují možnost ukládat libovolné uživatelsky definované vlastnosti klíč/hodnota společně s prostředkem objektu BLOB. Obě můžou být načtené a nastavované přímo, aniž by se musely vracet nebo měnit obsah objektu BLOB. Je možné využít metadata i značky indexu.

Pouze značky indexu objektu BLOB jsou automaticky indexovány a vytvořeny Queryable nativní službou BLOB Service. Metadata nelze nativně indexovat a dotazovat, pokud nevyužíváte samostatnou službu, například [Azure Search](../../search/search-blob-ai-integration.md). Značky indexu objektů BLOB mají také další oprávnění pro čtení, filtrování a zápis, které jsou oddělené od základních dat objektů BLOB. Metadata využívají stejná oprávnění jako objekt BLOB a jsou vrácena jako hlavičky HTTP v operacích getblob nebo GetBlobProperties. Značky indexu objektů BLOB jsou v klidovém stavu zašifrované pomocí [klíče spravovaného Microsoftem](../common/storage-service-encryption.md) , zatímco metadata jsou v klidovém stavu zašifrovaná pomocí stejného šifrovacího klíče zadaného pro data objektů BLOB.

Následující tabulka shrnuje rozdíly mezi metadaty a značkami indexu objektů BLOB:

|              |   Metadata   |   Značky indexu objektu BLOB  |
|--------------|--------------|--------------------|
| **Omezení**      | Bez číselného limitu; celkem 8 KB; nerozlišuje malá a velká písmena. | 10 značek na objekt BLOB max; 768 bajtů na značku; rozlišovat malá a velká písmena |
| **Aktualizace**    | Není povoleno na úrovni archivu; SetBlobMetadata nahrazuje všechna existující metadata; SetBlobMetadata změní čas poslední změny objektu BLOB. | Povoluje se pro všechny úrovně přístupu. SetBlobTags nahrazuje všechny existující značky; SetBlobTags nemění čas poslední změny objektu BLOB |
| **Storage**     | Uložené s daty objektů BLOB | Dílčí prostředek k datům objektu BLOB |
| **Indexování & dotazování** | Neužívá se nativně; musí používat samostatnou službu, například Azure Search | Ano, nativní indexování a možnosti dotazování, které jsou integrované do úložiště objektů BLOB |
| **Šifrování** | Šifrované v klidovém stavu se stejným šifrovacím klíčem použitým pro data objektů BLOB | Šifrované v klidovém stavu pomocí šifrovacího klíče spravovaného Microsoftem |
| **Ceny** | Velikost metadat je zahrnutá v nákladech na úložiště objektu BLOB. | Pevné náklady na značku indexu |
| **Odpověď záhlaví** | Metadata vracená jako záhlaví v getblob a GetBlobProperties | TagCount vrácených v getblob nebo GetBlobProperties; Značky vracené jenom v GetBlobTags a ListBlobs |
| **Oprávnění**  | Oprávnění ke čtení nebo zápisu pro data objektů BLOB se rozšíří do metadat. | Pro čtení, filtrování a zápis značek jsou vyžadovány další oprávnění. |
| **Pojmenování** | Názvy metadat musí splňovat pravidla pro pojmenování identifikátorů jazyka C#. | Značky indexu objektu BLOB podporují širší rozsah alfanumerických znaků. |

## <a name="pricing"></a>Ceny
Ceny indexu objektů BLOB jsou momentálně ve verzi Public Preview a mohou se změnit pro obecnou dostupnost. Zákazníkům se účtuje celkový počet značek indexu objektů BLOB v rámci účtu úložiště, průměrně za měsíc. Pro modul indexování se neúčtují žádné náklady. Požadavky na SetBlobTags, GetBlobTags a FindBlobsByTags se účtují podle jejich příslušných typů operací. Další informace najdete v tématu [ceny za objekty blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Podpora regionální dostupnosti a účtu úložiště

Index objektu BLOB je v tuto chvíli dostupný jenom u účtů Pro obecné účely v2 (GPv2) s nepovoleným hierarchickým oborem názvů (HNS). Účty Pro obecné účely (GPV1) nejsou podporovány, ale můžete upgradovat libovolný účet GPv1 na účet GPv2. Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Index objektů BLOB je ve verzi Public Preview dostupný jenom v následujících oblastech SELECT:
- Střední Kanada
- Kanada – východ
- Francie – střed
- Francie – jih

Informace o tom, jak začít, najdete v tématu [využití indexu objektů BLOB ke správě a hledání dat](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Viz část podmínky tohoto článku. Pokud se chcete zaregistrovat ve verzi Preview, zaregistrujte si předplatné, abyste mohli použít index objektů blob na svých účtech úložiště.

### <a name="register-your-subscription-preview"></a>Registrace předplatného (Preview)
Vzhledem k tomu, že index objektu BLOB je jenom ve verzi Public Preview, musíte předplatné zaregistrovat, než budete moct funkci používat. Pokud chcete odeslat žádost, spusťte následující příkazy PowerShellu nebo rozhraní příkazového řádku.

#### <a name="register-by-using-powershell"></a>Registrace pomocí PowerShellu
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrace pomocí Azure CLI
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Podmínky a známé problémy (Preview)
Tato část popisuje známé problémy a podmínky v aktuální verzi Public Preview indexu objektu BLOB. Stejně jako u většiny verzí Preview by se tato funkce neměla používat pro produkční úlohy, dokud nedosáhne GA, že se chování může změnit.

- Pro verzi Preview musíte nejdřív zaregistrovat předplatné, abyste mohli použít index objektu BLOB pro svůj účet úložiště v oblastech Preview.
- Ve verzi Preview se aktuálně podporují jenom účty GPv2. Index objektu BLOB v současné době nepodporuje účty datalake Gen2, které jsou povoleny v objektech blob, BlockBlobStorage a HNS. Účty GPv1 se nepodporují.
- Nahrání objektů blob stránky pomocí indexových značek aktuálně neuchovává značky. Značky je nutné nastavit po nahrání objektu blob stránky.
- Pokud je filtrování vymezeno na jeden kontejner, @container lze předat pouze v případě, že všechny značky indexu ve výrazu filtru jsou kontroly rovnosti (Key = hodnota).
- Při použití operátoru Range s podmínkou a můžete zadat jenom stejný název klíče značky indexu (věk > 013 a Age < "100").
- Správa verzí a index objektů BLOB se v tuto chvíli nepodporuje. Značky indexu objektu BLOB jsou zachované pro verze, ale v tuto chvíli nejsou předávané do modulu indexu objektů BLOB.
- Převzetí služeb při selhání účtu se v tuto chvíli nepodporuje. Index objektu BLOB se po převzetí služeb při selhání nemusí správně aktualizovat.
- Správa životního cyklu aktuálně podporuje jenom kontroly rovnosti se shodou indexu objektů BLOB.
- CopyBlob nekopíruje značky indexu objektů BLOB ze zdrojového objektu blob do nového cílového objektu BLOB. V průběhu operace kopírování můžete zadat značky, které chcete použít pro cílový objekt BLOB.
- CopyBlob (asynchronní kopírování) z jiného účtu úložiště s použitými značkami v cílovém objektu BLOB v současné době způsobí, že modul indexu objektů BLOB nevrátí objekt BLOB a jeho značky v sadě filtrů. Doporučuje se v provizorním použití CopyBlob z adresy URL (Synchronization Copy).
- Značky jsou při vytváření snímku trvalé; zvýšení úrovně snímku se ale v tuto chvíli nepodporuje a výsledkem může být prázdná sada značek.

## <a name="faq"></a>Časté otázky

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Může vám index objektu BLOB pomáhat při filtrování a dotazování obsahu uvnitř objektů BLOB?
Ne, značky indexu objektů BLOB vám pomůžou najít objekty blob, které hledáte. Pokud potřebujete hledat v rámci objektů blob, použijte akceleraci dotazů nebo Azure Search.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Existují nějaké zvláštní okolnosti týkající se hodnot značek indexu objektů BLOB?
Značky indexu objektů BLOB podporují pouze řetězcové typy a dotazování vrací výsledky pomocí řazení lexicographical. Pro čísla se doporučuje číslo bez panelu. V případě data a času se doporučuje ukládat jako formát kompatibilního s ISO 8601.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Jsou značky indexu objektů BLOB a Azure Resource Manager souvisejících značek?
Ne, Správce prostředků značky usnadňují uspořádání prostředků řídicích rovin, jako jsou předplatná, skupiny prostředků a účty úložiště. Značky indexu objektů BLOB poskytují správu objektů a zjišťování prostředků roviny dat, jako jsou objekty BLOB v rámci účtu úložiště.

## <a name="next-steps"></a>Další kroky

Příklad použití indexu objektů BLOB najdete v tématu věnovaném [využití indexu objektů BLOB ke správě a hledání dat](storage-blob-index-how-to.md).

Přečtěte si o [správě životního cyklu](storage-lifecycle-management-concepts.md) a nastavte pravidlo se shodou indexu objektů BLOB.

