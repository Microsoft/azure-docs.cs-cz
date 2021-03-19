---
title: Správa a hledání dat objektů BLOB v Azure pomocí značek indexu objektů BLOB (Preview)
description: Naučte se používat značky indexu objektů BLOB ke kategorizaci, správě a dotazování objektů BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 540f6ca165c80dd90f5004fb53e1577cdc35d770
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656035"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Správa a hledání dat objektů BLOB v Azure pomocí značek indexu objektů BLOB (Preview)

Protože datové sady získají větší velikost, může být obtížné najít konkrétní objekt v moři dat. Značky indexu objektu BLOB poskytují funkce pro správu a zjišťování dat pomocí atributů značky indexu klíčové hodnoty. Můžete kategorizovat a vyhledávat objekty v jednom kontejneru nebo ve všech kontejnerech v účtu úložiště. S tím, jak se mění požadavky na data, je možné objekty dynamicky kategorizovat aktualizací jejich rejstříkových značek. Objekty mohou zůstat v místě s aktuální organizací kontejneru.

Značky indexu objektů BLOB umožňují:

- Dynamické kategorizace objektů BLOB pomocí značek indexu klíč-hodnota
- Rychlé vyhledání určitých objektů BLOB s příznakem v celém účtu úložiště
- Určení podmíněného chování pro rozhraní API objektů blob na základě vyhodnocení značek indexu
- Použití značek indexů pro pokročilé ovládací prvky pro funkce, jako je [Správa životního cyklu objektů BLOB](storage-lifecycle-management-concepts.md)

Vezměte v úvahu scénář, ve kterém máte v účtu úložiště miliony objektů blob, ke kterým má přistup řada různých aplikací. Chcete najít všechna související data z jednoho projektu. Nejste si jistí, co je v oboru, protože data je možné rozdělit do několika kontejnerů s různými konvencemi vytváření názvů. Vaše aplikace ale nahrávají všechna data pomocí značek na základě jejich projektu. Místo vyhledávání v milionech objektů BLOB a porovnávání názvů a vlastností můžete použít `Project = Contoso` jako kritéria vyhledávání. Index objektu BLOB filtruje všechny kontejnery napříč celým účtem úložiště, aby bylo možné rychle vyhledat a vrátit jenom sadu objektů BLOB 50 z `Project = Contoso` .

> [!IMPORTANT]
> Značky indexu objektů BLOB jsou momentálně ve **verzi Preview** a jsou dostupné ve všech veřejných oblastech. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

Pokud chcete začít s příklady použití indexu objektů blob, přečtěte si téma [použití značek indexu objektů BLOB ke správě a hledání dat](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Značky indexu objektů BLOB a správa dat

Předpony názvů kontejnerů a objektů BLOB jsou jednorozměrné kategorizace. Značky indexu objektu BLOB umožňují multidimenzionální kategorizaci pro [datové typy objektů BLOB (blokovat, připojit nebo stránka)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). Multidimenzionální kategorizace je nativně indexována pomocí Azure Blob Storage, abyste mohli rychle najít data.

Vezměte v úvahu následující pět objektů BLOB v účtu úložiště:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *fotky/bannerphoto.png*
- *archivy/dokončené/2019review.pdf*
- *protokoly/2020/01/01/logfile.txt*

Tyto objekty BLOB se oddělují pomocí předpony *kontejneru/virtuální složky nebo názvu objektu BLOB*. Můžete nastavit atribut značky indexu `Project = Contoso` u těchto pěti objektů blob, aby je bylo možné kategorizovat společně a přitom zachovat aktuální předponu organizace. Přidávání indexových značek eliminuje nutnost přesunu dat vyplněním schopnosti filtrovat a vyhledávat data pomocí indexu.

## <a name="setting-blob-index-tags"></a>Nastavení značek indexu objektů BLOB

Značky indexu objektu BLOB jsou atributy klíč-hodnota, které se dají použít pro nové nebo existující objekty v rámci vašeho účtu úložiště. Můžete zadat rejstříkové značky během procesu nahrávání pomocí [objektu Put BLOB](/rest/api/storageservices/put-blob), [seznam blokovaných](/rest/api/storageservices/put-block-list)objektů nebo [kopie operací objektů BLOB](/rest/api/storageservices/copy-blob) a volitelné `x-ms-tags` hlavičky. Pokud už máte objekty blob ve svém účtu úložiště, můžete [značky objektů BLOB sady](/rest/api/storageservices/set-blob-tags) volání předat FORMÁTOVANÝ dokument XML pomocí značek indexu v těle požadavku.

> [!IMPORTANT]
> Nastavení značek indexu objektů BLOB může provést [vlastník dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a kdokoli se sdíleným přístupovým podpisem, který má oprávnění pro přístup k značkám objektu BLOB ( `t` oprávnění SAS).
>
> Kromě toho `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` může tato operace provádět uživatelé RBAC s oprávněním.

V objektu blob můžete použít jednu značku, která popisuje, kdy se data dokončí zpracování.

> "processedDate" = ' 2020-01-01 '

V objektu blob můžete použít více značek, aby byly výstižnější data.

> "Projekt" = ' contoso '  
> "Klasifikované" = ' true '  
> "Stav" = Nezpracováno "  
> "Priorita" = ' 01 '

Chcete-li upravit existující atributy značek indexů, načtěte existující atributy značky, upravte atributy značky a nahraďte ji operací [nastavit značky objektu BLOB](/rest/api/storageservices/set-blob-tags) . Chcete-li odebrat všechny značky indexu z objektu blob, zavolejte `Set Blob Tags` operaci bez zadaných atributů značek. Jelikož jsou značky indexu objektů BLOB dílčím prostředkem obsahu dat objektů blob, `Set Blob Tags` nedojde k úpravě žádného podkladového obsahu a nezmění se čas poslední změny, času a ETag objektu BLOB. Můžete vytvořit nebo upravit značky indexu pro všechny aktuální základní objekty BLOB a předchozí verze. Značky na snímcích nebo nepodmíněné odstraněné objekty BLOB se ale nedají změnit.

Následující omezení se vztahují na značky indexu objektu BLOB:

- Každý objekt BLOB může mít až 10 značek indexu objektu BLOB.
- Klíče značek musí být mezi 1 a 128 znaky.
- Hodnoty značek musí být mezi 0 a 256 znaky.
- V klíčích značek a hodnotách se rozlišují malá a velká písmena.
- Klíče a hodnoty značek podporují pouze řetězcové typy dat. Všechna čísla, kalendářní data, časy nebo speciální znaky jsou uloženy jako řetězce.
- Klíče a hodnoty značek musí splňovat následující pravidla pojmenování:
  - Alfanumerické znaky:
    - **a** až **z** (malá písmena)
    - **A** až **Z** (velká písmena)
    - **0** až **9** (čísla)
  - Platné speciální znaky: mezera, znaménko, mínus, tečka, dvojtečka, rovná se, podtržítko, lomítko ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Získání a výpis značek indexu objektů BLOB

Značky indexu objektů BLOB se ukládají jako dílčí prostředek spolu s daty objektů BLOB a dají se načíst nezávisle na základním obsahu dat blobu. Značky indexu objektu BLOB pro jeden objekt BLOB se dají načíst pomocí operace [Get BLOB Tags](/rest/api/storageservices/get-blob-tags) . Operace [výpisu objektů BLOB](/rest/api/storageservices/list-blobs) s `include:tags` parametrem vrátí také všechny objekty BLOB v rámci kontejneru spolu s jejich značkami indexu objektu BLOB.

> [!IMPORTANT]
> Získání a výpis značek indexu objektů BLOB může provést [vlastník dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a kdokoli se sdíleným přístupovým podpisem, který má oprávnění pro přístup k značkám objektu BLOB ( `t` oprávnění SAS).
>
> Kromě toho `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` může tato operace provádět uživatelé RBAC s oprávněním.

U všech objektů blob, které mají alespoň jednu značku indexu objektu blob, `x-ms-tag-count` se vrátí do objektů [BLOB seznamu](/rest/api/storageservices/list-blobs), [získat objekt BLOB](/rest/api/storageservices/get-blob)a [načíst vlastnosti objektu BLOB](/rest/api/storageservices/get-blob-properties) , které označují počet značek indexu objektu BLOB.

## <a name="finding-data-using-blob-index-tags"></a>Hledání dat pomocí značek indexu objektů BLOB

Modul indexování zpřístupňuje atributy klíč-hodnota do multidimenzionálního indexu. Po nastavení značek indexu existují v objektu BLOB a je možné je načíst hned. Aktualizace indexu objektů BLOB může nějakou dobu trvat. Po aktualizaci indexu objektů blob můžete použít nativní dotazy a možnosti zjišťování, které nabízí Blob Storage.

Operace [najít objekty BLOB pomocí značek](/rest/api/storageservices/find-blobs-by-tags) umožňuje získat filtrovanou sadu objektů blob, jejichž Indexové značky odpovídají danému výrazu dotazu. `Find Blobs by Tags` podporuje filtrování napříč všemi kontejnery v rámci vašeho účtu úložiště, nebo můžete omezit rozsah filtrování jenom na jeden kontejner. Vzhledem k tomu, že všechny klíče a hodnoty indexových značek jsou řetězce, relační operátory používají řazení lexikografickým pořadím.

> [!IMPORTANT]
> Hledání dat pomocí značek indexu objektů BLOB může provést [vlastník dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a kdokoli se sdíleným přístupovým podpisem, který má oprávnění k hledání objektů BLOB pomocí značek ( `f` oprávnění SAS).
>
> Kromě toho `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` může tato operace provádět uživatelé RBAC s oprávněním.

Následující kritéria se vztahují na filtrování indexu objektů BLOB:

- Klíče značek by měly být uzavřeny do dvojitých uvozovek (")
- Hodnoty značek a názvy kontejnerů by měly být uzavřeny v jednoduchých uvozovkách (')
- Znak @ je povolený jenom pro filtrování u konkrétního názvu kontejneru (například). `@container = 'ContainerName'`
- Filtry se používají pro řazení lexikografickým pořadím řetězců.
- Stejné operace s rozsahem na stejném klíči jsou neplatné (například `"Rank" > '10' AND "Rank" >= '15'` ).
- Při použití REST k vytvoření výrazu filtru by měly být znaky kódované identifikátorem URI.

Následující tabulka uvádí všechny platné operátory pro `Find Blobs by Tags` :

|  Operátor  |  Popis  | Příklad |
|------------|---------------|---------|
|     =      |     Je rovno     | `"Status" = 'In Progress'` |
|     >      |  Větší než | `"Date" > '2018-06-18'` |
|     >=     |  Větší než nebo rovno | `"Priority" >= '5'` |
|     <      |  Menší než   | `"Age" < '32'` |
|     <=     |  Menší než nebo rovno  | `"Company" <= 'Contoso'` |
|    A     |  Logický operátor and  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Nastavit obor na konkrétní kontejner | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Seznámení s lexicographical objednání při nastavení a dotazování na značky.
>
> - Čísla jsou řazena před písmeny. Čísla jsou řazena podle první číslice.
> - Velká písmena jsou řazena před malými písmeny.
> - Symboly nejsou Standard. Některé symboly jsou řazeny před číselnými hodnotami. Další symboly jsou seřazené před nebo za písmeny.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Podmíněné operace objektů BLOB pomocí značek indexu objektu BLOB

V REST verze 2019-10-10 a novějších [rozhraní API služby BLOB Service](/rest/api/storageservices/operations-on-blobs) teď podporuje podmíněnou hlavičku, `x-ms-if-tags` takže operace bude úspěšná jenom v případě, že se splní zadaná podmínka indexu objektu BLOB. Pokud není podmínka splněná, dostanete se `error 412: The condition specified using HTTP conditional header(s) is not met` .

`x-ms-if-tags`Hlavička může být kombinována s ostatními existujícími podmíněnými hlavičkami http (Pokud-Match, If-None-Match a tak dále). Pokud je v požadavku k dispozici více podmíněných hlaviček, musí všechny vyhodnotit hodnotu true, aby operace proběhla úspěšně. Všechny podmíněné hlavičky jsou efektivně kombinovány s logickými a.

Následující tabulka uvádí platné operátory pro podmíněné operace:

|  Operátor  |  Popis  | Příklad |
|------------|---------------|---------|
|     =      |     Je rovno     | `"Status" = 'In Progress'` |
|     <>     |   Není rovno   | `"Status" <> 'Done'` |
|     >      |  Větší než | `"Date" > '2018-06-18'` |
|     >=     |  Větší než nebo rovno | `"Priority" >= '5'` |
|     <      |  Menší než   | `"Age" < '32'` |
|     <=     |  Menší než nebo rovno  | `"Company" <= 'Contoso'` |
|    A     |  Logický operátor and  | `"Rank" >= '010' AND "Rank" < '100'` |
|     NEBO     | Logický operátor OR   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Existují dva další operátory, nerovné a logické nebo, které jsou povoleny v podmíněné `x-ms-if-tags` hlavičce pro operace objektů blob, ale v operaci neexistují `Find Blobs by Tags` .

## <a name="platform-integrations-with-blob-index-tags"></a>Integrace platforem se značkami indexu objektů BLOB

Značky indexu objektů BLOB neumožňují kategorizaci, správu a hledání dat objektů blob, ale také poskytují integraci s dalšími Blob Storage funkcemi, jako je například [Správa životního cyklu](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Správa životního cyklu

Pomocí `blobIndexMatch` filtru pravidla ve správě životního cyklu můžete přesunovat data na vícevrstvé vrstvy nebo odstraňovat data na základě značek indexu aplikovaných na vaše objekty blob. Ve svých pravidlech můžete být podrobnější podrobnější informace a přesunout nebo odstranit objekty blob pouze v případě, že odpovídají zadaným kritériím značek.

V pravidle životního cyklu můžete nastavit, aby se index objektu BLOB shodoval jako samostatný filtr nastavený na základě akcí u tagovaných dat. Případně můžete kombinovat předpony i index objektů blob, aby bylo možné porovnat konkrétnější sady dat. Zadáním více filtrů v pravidle životního cyklu se uplatní logická a operace. Tato akce bude platit pouze v případě, že se *všechna* kritéria filtru shodují.

Následující pravidlo správy životního cyklu se vztahuje na objekty blob bloku v kontejneru s názvem *VideoFiles*. Úrovně pravidla se objekty blob nastaví do úložiště archivu pouze v případě, že data odpovídají kritériím značek indexu objektu BLOB `"Status" == 'Processed' AND "Source" == 'RAW'` .

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

Přístup k tagům indexu objektů blob můžete autorizovat pomocí jednoho z následujících přístupů:

- Použití řízení přístupu na základě role Azure (Azure RBAC) k udělení oprávnění objektu zabezpečení služby Azure Active Directory (Azure AD). Využijte Azure AD pro zajištění kvalitního zabezpečení a snadného použití. Další informace o používání služby Azure AD s operacemi objektů BLOB najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](../common/storage-auth-aad.md).
- Delegování přístupu k indexu objektů BLOB pomocí sdíleného přístupového podpisu (SAS) Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).
- Použití přístupových klíčů účtu k autorizaci operací se sdíleným klíčem. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key).

Značky indexu objektu BLOB jsou podzdrojem dat objektu BLOB. Uživatel s oprávněními nebo tokenem SAS pro čtení nebo zápis objektů BLOB nemůže mít přístup k tagům indexu objektu BLOB.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

Volajícím, kteří používají [identitu Azure AD](../common/storage-auth-aad.md) , můžou mít k dispozici následující oprávnění pro práci se značkami indexu objektů BLOB.

| Operace se značkami indexu objektu BLOB                                          | Akce Azure RBAC                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Nastavení značek objektů BLOB](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Write    |
| [Získat značky objektu BLOB](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Read     |
| [Hledání objektů BLOB podle značek](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Filter/Action |

Pro operace indexových značek jsou vyžadovány další oprávnění oddělené od základních dat objektů BLOB. Role [vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) má oprávnění pro všechny tři operace se značkami indexu objektu BLOB. [Čtečka dat objektu BLOB služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) má udělena pouze oprávnění pro `Find Blobs by Tags` `Get Blob Tags` operace a.

### <a name="sas-permissions"></a>Oprávnění SAS

Volajícím, kteří používají [sdílený přístupový podpis (SAS)](../common/storage-sas-overview.md) , může být uděleno vymezené oprávnění pro práci se značkami indexu objektu BLOB.

#### <a name="blob-sas"></a>SAS objektu BLOB

K povolení přístupu k tagům indexu objektu BLOB se můžou v rámci přidružení zabezpečení objektu BLOB udělit následující oprávnění. Samotná oprávnění ke čtení a zápisu objektů BLOB nejsou dostatečná, aby bylo možné číst nebo zapisovat do značek indexů.

| Oprávnění | Symbol identifikátoru URI | Povolené operace                |
|------------|------------|-----------------------------------|
| Rejstřík – značky |     t      | Získání a nastavení značek indexu pro objekt BLOB |

#### <a name="container-sas"></a>SAS kontejneru

Následující oprávnění mohou být udělena v kontejneru SAS, aby bylo možné filtrovat značky objektů BLOB. `Blob List`Oprávnění není dostatečné, aby bylo možné filtrovat objekty BLOB pomocí značek jejich indexu.

| Oprávnění | Symbol identifikátoru URI | Povolené operace         |
|------------|------------|----------------------------|
| Rejstřík – značky |     f      | Najde objekty BLOB s značkami indexu. |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Výběr mezi metadaty a značkami indexu objektu BLOB

Značky indexu objektů BLOB a metadata poskytují možnost ukládat libovolné uživatelsky definované vlastnosti klíč-hodnota společně s prostředkem objektu BLOB. Obojí lze načíst a nastavit přímo, aniž by bylo nutné vracet nebo měnit obsah objektu BLOB. Je možné použít jak metadata, tak značky indexu.

Pouze rejstříkové značky jsou automaticky indexovány a zpřístupněny nativní službou Blob Storage. Metadata se nedají nativně indexovat ani prohledávat. Je nutné použít samostatnou službu, například [Azure Search](../../search/search-blob-ai-integration.md). Značky indexu objektu BLOB mají další oprávnění pro čtení, filtrování a zápis, které jsou oddělené od základních dat objektů BLOB. Metadata používají stejná oprávnění jako objekt BLOB a jsou vrácena jako hlavičky HTTP pomocí operací [získat objekt BLOB](/rest/api/storageservices/get-blob) a [získat vlastnosti objektu BLOB](/rest/api/storageservices/get-blob-properties) . Značky indexu objektu BLOB jsou v klidovém stavu šifrované pomocí [klíče spravovaného společností Microsoft](../common/storage-service-encryption.md). Metadata jsou v klidovém stavu zašifrovaná pomocí stejného šifrovacího klíče zadaného pro data objektů BLOB.

Následující tabulka shrnuje rozdíly mezi metadaty a značkami indexu objektů BLOB:

|              |   Metadata   |   Značky indexu objektu BLOB  |
|--------------|--------------|--------------------|
| **Omezení**      | Žádný numerický limit, celkem 8 KB, nerozlišuje velká a malá písmena | 10 značek na objekt BLOB Max, 768 bajtů na značku, rozlišuje velká a malá písmena |
| **Aktualizace**    | Není povoleno na úrovni archivu, `Set Blob Metadata` nahrazuje všechna existující metadata, `Set Blob Metadata` mění čas poslední změny objektu BLOB. | Povoluje se pro všechny úrovně přístupu, `Set Blob Tags` nahradí všechny existující značky, `Set Blob Tags` nemění čas poslední změny objektu BLOB. |
| **Storage**     | Uložené s daty objektů BLOB | Podprostředek dat objektu BLOB |
| **Indexování & dotazování** | Musí používat samostatnou službu, například Azure Search | Indexování a dotazování schopností integrovaných do Blob Storage |
| **Šifrování** | Šifrované v klidovém stavu se stejným šifrovacím klíčem použitým pro data objektů BLOB | Šifrované v klidovém stavu pomocí šifrovacího klíče spravovaného Microsoftem |
| **Ceny** | Velikost metadat je zahrnutá v nákladech na úložiště objektu BLOB. | Pevné náklady na značku indexu |
| **Odpověď záhlaví** | Metadata se vrátila jako záhlaví v `Get Blob` a. `Get Blob Properties` | Počet značek vrácený `Get Blob` nebo `Get Blob Properties` , značky vrácené jenom pomocí `Get Blob Tags` a `List Blobs` |
| **Oprávnění**  | Oprávnění ke čtení nebo zápisu pro data objektů BLOB se rozšíří do metadat. | Pro čtení, filtrování a zápis značek indexu jsou vyžadovány další oprávnění. |
| **Pojmenování** | Názvy metadat musí splňovat pravidla pro pojmenování identifikátorů jazyka C#. | Značky indexu objektu BLOB podporují širší rozsah alfanumerických znaků. |

## <a name="pricing"></a>Ceny

Ceny indexu objektů BLOB jsou ve verzi Public Preview a mohou se změnit pro obecnou dostupnost. Účtuje se vám měsíční průměrný počet značek indexu v rámci účtu úložiště. Pro modul indexování se neúčtují žádné náklady. Požadavky na `Set Blob Tags` , `Get Blob Tags` a `Find Blobs by Tags` se účtují podle jejich příslušných typů operací. Další informace najdete v tématu [ceny za objekty blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Podpora regionální dostupnosti a účtu úložiště

Značky indexu objektu BLOB jsou k dispozici pouze v účtech Pro obecné účely v2 (GPv2) se zakázaným hierarchickým oborem názvů (HNS). Účty Pro obecné účely (GPV1) nejsou podporovány, ale můžete upgradovat libovolný účet GPv1 na účet GPv2.

Značky indexu nejsou podporované v účtech Premium Storage. Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Značky indexu objektu BLOB jsou aktuálně k dispozici ve všech veřejných oblastech.

Informace o tom, jak začít, najdete v tématu [použití značek indexu objektů BLOB ke správě a hledání dat](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Než budete moct použít náhled indexu objektů BLOB v účtech úložiště, musíte předplatné zaregistrovat. Viz část [podmínky a známé problémy](#conditions-and-known-issues) tohoto článku.

### <a name="register-your-subscription-preview"></a>Registrace předplatného (Preview)

Vzhledem k tomu, že značky indexu objektů BLOB jsou jenom ve verzi Public Preview, musíte předplatné zaregistrovat, než budete moct funkci používat. Pokud chcete odeslat žádost, spusťte následující příkazy PowerShellu nebo rozhraní příkazového řádku.

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

## <a name="conditions-and-known-issues"></a>Podmínky a známé problémy

Tato část popisuje známé problémy a podmínky ve verzi Public Preview značek indexu objektů BLOB. Tato funkce by se neměla používat pro produkční úlohy, dokud nedosáhne celkové dostupnosti (GA), protože se chování může změnit.

- Pro verzi Preview musíte nejdřív zaregistrovat předplatné, abyste mohli použít index objektu BLOB pro svůj účet úložiště v oblastech Preview.
- Ve verzi Preview jsou podporovány pouze účty GPv2. Účty datalake Gen2 s povoleným objektem blob, BlockBlobStorage a HNS nejsou podporovány. Účty GPv1 se nepodporují.
- Nahrávání objektů blob stránky s značkami indexu neuchovává značky. Nastavte značky po nahrání objektu blob stránky.
- Pokud je filtrování vymezeno na jeden kontejner, `@container` lze předat pouze v případě, že všechny značky indexu ve výrazu filtru jsou kontroly rovnosti (Key = hodnota).
- Při použití operátoru Range s `AND` podmínkou lze zadat pouze stejný název klíče značky indexu ( `"Age" > '013' AND "Age" < '100'` ).
- Správa verzí a index objektů BLOB se nepodporuje. Značky indexu objektu BLOB jsou uchovány pro verze, ale nejsou předány modulu indexu objektů BLOB.
- Neexistuje žádné rozhraní API k určení, zda jsou rejstříkové štítky indexovány.
- Převzetí služeb při selhání účtu se nepodporuje. Index objektu BLOB se po převzetí služeb při selhání nemusí správně aktualizovat.
- Správa životního cyklu podporuje jenom kontroly rovnosti se shodou indexu objektů BLOB.
- `Copy Blob` nekopíruje značky indexu objektů BLOB ze zdrojového objektu blob do nového cílového objektu BLOB. V průběhu operace kopírování můžete zadat značky, které chcete použít pro cílový objekt BLOB.
- `Copy Blob` (Asynchronní kopírování) z jiného účtu úložiště s použitými značkami v cílovém objektu BLOB způsobí, že modul indexu objektů BLOB nevrátí objekt BLOB a jeho značky v sadě filtrů. Použijte `Copy Blob` z adresy URL (Synchronization Copy).
- Značky jsou při vytváření snímku trvalé. Zvýšení úrovně snímku ale není podporované a výsledkem může být prázdná sada značek.

## <a name="faq"></a>Časté otázky

**Může vám index objektu BLOB pomáhat při filtrování a dotazování obsahu uvnitř objektů BLOB?**

Ne. Pokud potřebujete hledat v datech objektu blob, použijte akceleraci dotazů nebo Azure Search.

**Existují nějaké požadavky na hodnoty značek indexu?**

Značky indexu objektů BLOB podporují pouze řetězcové typy a dotazování vrací výsledky pomocí řazení lexicographical. Pro čísla odsadí číslo nulou. Pro data a časy uložte jako formát kompatibilního s ISO 8601.

**Jsou značky indexu objektů BLOB a Azure Resource Manager souvisejících značek?**

Ne, Správce prostředků značky usnadňují uspořádání prostředků řídicích rovin, jako jsou předplatná, skupiny prostředků a účty úložiště. Značky indexu poskytují správu a zjišťování objektů blob na rovině dat.

## <a name="next-steps"></a>Další kroky

Příklad použití indexu objektů BLOB najdete v tématu [Správa a hledání dat pomocí indexu objektů BLOB](storage-blob-index-how-to.md).

Přečtěte si o [správě životního cyklu](storage-lifecycle-management-concepts.md) a nastavte pravidlo s porovnáním indexu objektů BLOB.
