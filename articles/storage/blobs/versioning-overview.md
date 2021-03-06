---
title: Správa verzí objektů BLOB
titleSuffix: Azure Storage
description: Správa verzí úložiště objektů BLOB automaticky udržuje předchozí verze objektu a identifikuje je pomocí časových razítek. Můžete obnovit předchozí verzi objektu BLOB a obnovit data, pokud jsou nesprávně upravená nebo Odstraněná.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 268de3e8ea168ac721362d42149389b9f37c86fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305051"
---
# <a name="blob-versioning"></a>Správa verzí objektů BLOB

Chcete-li automaticky zachovat předchozí verze objektu, můžete povolit správu verzí služby Blob Storage. Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Doporučená konfigurace ochrany dat

Správa verzí objektů BLOB je součástí komplexní strategie ochrany dat pro data objektů BLOB. Pro zajištění optimální ochrany dat objektů BLOB doporučuje Microsoft povolit všechny následující funkce ochrany dat:

- Správa verzí objektů BLOB pro automatické udržování předchozích verzí objektu BLOB. Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila. Informace o tom, jak povolit správu verzí objektů blob, najdete v tématu [povolení a správa verzí objektů BLOB](versioning-enable.md).
- Dočasná odstranění kontejneru pro obnovení kontejneru, který byl odstraněn. Pokud chcete zjistit, jak povolit obnovitelné odstranění kontejneru, přečtěte si téma [povolení a Správa obnovitelného odstranění kontejnerů](soft-delete-container-enable.md).
- Obnovitelné odstranění objektu BLOB pro obnovení objektu blob, snímku nebo verze, který byl odstraněn. Informace o tom, jak povolit obnovitelné odstranění objektů blob, najdete v tématu [povolení a Správa obnovitelného odstranění pro objekty blob](soft-delete-blob-enable.md).

Další informace o doporučeních Microsoftu pro ochranu dat najdete v tématu [Přehled ochrany dat](data-protection-overview.md).

## <a name="how-blob-versioning-works"></a>Jak funguje Správa verzí objektů BLOB

Verze zachytí stav objektu BLOB v daném časovém okamžiku. Každá verze se identifikuje s ID verze. Když je pro účet úložiště povolená Správa verzí objektů blob, Azure Storage automaticky vytvoří novou verzi s jedinečným ID při prvním vytvoření objektu BLOB a pokaždé, když se objekt BLOB následně upraví.

ID verze může identifikovat aktuální verzi nebo předchozí verzi. Objekt BLOB může mít v jednom okamžiku pouze jednu aktuální verzi.

Když vytvoříte nový objekt blob, existuje jedna verze a tato verze je aktuální verze. Když upravíte existující objekt blob, aktuální verze se změní na předchozí verzi. Vytvoří se nová verze pro zachycení aktualizovaného stavu a tato nová verze je aktuální. Při odstranění objektu BLOB se stane aktuální verze objektu BLOB předchozí verzí a už neexistuje aktuální verze. Všechny předchozí verze objektu BLOB jsou trvalé.

Následující diagram znázorňuje, jak se vytvářejí verze při operacích zápisu a jak může být předchozí verze povýšená na aktuální verzi:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagram znázorňující, jak funguje Správa verzí objektů BLOB":::

Verze objektů BLOB jsou neměnné. Nemůžete změnit obsah nebo metadata existující verze objektu BLOB.

Velký počet verzí na jeden objekt BLOB může zvýšit latenci pro operace výpisu objektů BLOB. Společnost Microsoft doporučuje udržovat méně než 1000 verzí na jeden objekt BLOB. Pokud chcete automaticky odstranit staré verze, můžete použít správu životního cyklu. Další informace o správě životního cyklu najdete v tématu [optimalizace nákladů díky automatizaci úrovní přístupu v Azure Blob Storage](storage-lifecycle-management-concepts.md).

Správa verzí objektů BLOB je k dispozici pro standardní účty blob bloku verze 2 pro obecné účely, Premium a starší účty BLOB Storage. Účty úložiště s hierarchickým oborem názvů povoleným pro použití s Azure Data Lake Storage Gen2 nejsou aktuálně podporovány.

Verze 2019-10-10 a vyšší z Azure Storage REST API podporuje správu verzí objektů BLOB.

> [!IMPORTANT]
> Správa verzí objektů BLOB vám neumožňuje obnovit z náhodného odstranění účtu úložiště nebo kontejneru. Pokud chcete zabránit nechtěnému odstranění účtu úložiště, nakonfigurujte zámek u prostředku účtu úložiště. Další informace o uzamykání účtu úložiště najdete v tématu [použití zámku Azure Resource Manager k účtu úložiště](../common/lock-account-resource.md).

### <a name="version-id"></a>ID verze

Každá verze objektu BLOB je identifikována jedinečným ID verze. Hodnota ID verze je časové razítko, se kterým se aktualizoval objekt BLOB. ID verze je přiřazeno v době, kdy je vytvořena verze.

Můžete provádět operace čtení nebo odstranění u konkrétní verze objektu BLOB zadáním jeho ID verze. Pokud ID verze vynecháte, operace bude fungovat s aktuální verzí.

Když zavoláte operaci zápisu pro vytvoření nebo úpravu objektu blob, Azure Storage v odpovědi vrátí hlavičku *x-MS-Version-ID* . Tato hlavička obsahuje ID verze pro aktuální verzi objektu blob, která byla vytvořena operací zápisu.

ID verze zůstane pro dobu života verze stejné.

### <a name="versioning-on-write-operations"></a>Správa verzí při operacích zápisu

Když je zapnutá Správa verzí objektů blob, každá operace zápisu do objektu BLOB vytvoří novou verzi. Operace zápisu zahrnují [objekt BLOB pro vložení](/rest/api/storageservices/put-blob), [seznam blokovaných](/rest/api/storageservices/put-block-list) [objektů, kopírování objektů BLOB](/rest/api/storageservices/copy-blob)a [nastavení metadat objektů BLOB](/rest/api/storageservices/set-blob-metadata).

Pokud operace zápisu vytvoří nový objekt blob, bude výsledný objekt blob aktuální verzí objektu BLOB. Pokud operace zápisu změní existující objekt blob, aktuální verze se změní na předchozí verzi a vytvoří se nová aktuální verze pro zachycení aktualizovaného objektu BLOB.

Následující diagram ukazuje, jak operace zápisu ovlivňují verze objektů BLOB. V diagramech, které jsou uvedené v tomto článku, se pro jednoduchost zobrazuje ID verze jako jednoduchá celočíselná hodnota. Ve skutečnosti je ID verze časové razítko. Aktuální verze je zobrazena modře a předchozí verze jsou zobrazeny šedě.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagram znázorňující, jak operace zápisu ovlivňují objekty blob ve verzi":::

> [!NOTE]
> Objekt blob, který byl vytvořen před správou verzí povolený pro účet úložiště, nemá ID verze. Když se tento objekt BLOB upraví, změní se stávající objekt blob na aktuální verzi a vytvoří se verze, aby se před aktualizací uložil stav objektu BLOB. Verzi je přiřazeno ID verze, které je jeho časem vytvoření.

Pokud je pro účet úložiště povolená Správa verzí objektů blob, všechny operace zápisu u objektů blob bloku aktivují vytvoření nové verze s výjimkou operace [blok Put](/rest/api/storageservices/put-block) .

Pro objekty blob stránky a doplňovací objekty BLOB se při vytváření verze aktivuje jenom podmnožina operací zápisu. Mezi tyto operace patří:

- [Vložení objektu blob](/rest/api/storageservices/put-blob)
- [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list)
- [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Zkopírování objektu blob](/rest/api/storageservices/copy-blob)

Následující operace neaktivují vytvoření nové verze. Chcete-li zachytit změny z těchto operací, proveďte ruční snímek:

- [Vložit stránku](/rest/api/storageservices/put-page) (objekt blob stránky)
- [Připojit blok](/rest/api/storageservices/append-block) (doplňovací objekt BLOB)

Všechny verze objektu BLOB musí být stejného typu BLOB. Pokud má objekt BLOB předchozí verze, nelze přepsat objekt BLOB jednoho typu jiným typem, pokud nejprve neodstraníte objekt BLOB a všechny jeho verze.

### <a name="versioning-on-delete-operations"></a>Správa verzí při operacích odstranění

Když zavoláte operaci [odstranění objektu BLOB](/rest/api/storageservices/delete-blob) bez zadání ID verze, stane se aktuální verze předchozí verze a už neexistuje aktuální verze. Všechny existující předchozí verze objektu BLOB jsou zachovány.

Následující diagram znázorňuje efekt operace odstranění u objektu BLOB s verzí:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagram znázorňující odstranění objektu BLOB se správou verzí":::

Pokud chcete odstranit konkrétní verzi objektu blob, zadejte pro tuto verzi na operaci odstranit ID. Pokud je pro účet úložiště zapnuté i obnovitelné odstranění objektů blob, verze se udržuje v systému, dokud neuplyne doba uchování obnovitelného odstranění.

Zápis nových dat do objektu BLOB vytvoří novou aktuální verzi objektu BLOB. Jakékoli existující verze nejsou ovlivněny, jak je znázorněno v následujícím diagramu.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagram znázorňující opětovné vytvoření objektu BLOB s verzí po odstranění":::

### <a name="access-tiers"></a>Úrovně přístupu

Můžete přesunout libovolnou verzi objektu blob bloku, včetně aktuální verze, na jinou úroveň přístupu objektu BLOB voláním operace [nastavit úroveň objektu BLOB](/rest/api/storageservices/set-blob-tier) . Přesunutím starší verze objektu blob do studené nebo archivní úrovně můžete využít výhod nižších cen. Další informace najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

K automatizaci procesu přesunu objektů blob bloku do příslušné úrovně použijte správu životního cyklu objektů BLOB. Další informace o správě životního cyklu najdete v tématu [Správa životního cyklu úložiště objektů BLOB v Azure](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Povolit nebo zakázat správu verzí objektů BLOB

Informace o tom, jak povolit nebo zakázat správu verzí objektů blob, najdete v tématu [povolení a správa verzí objektů BLOB](versioning-enable.md).

Zakázání správy verzí objektů BLOB neodstraní existující objekty blob, verze ani snímky. Když vypnete správu verzí objektů blob, všechny existující verze budou v účtu úložiště dostupné. Následně se nevytvoří žádné nové verze.

Pokud byl vytvořený nebo změněný objekt BLOB v účtu úložiště, pak přepsání objektu BLOB vytvoří novou verzi. Aktualizovaný objekt BLOB už nemá aktuální verzi a nemá ID verze. Všechny následné aktualizace objektu BLOB přepíšou jeho data bez uložení předchozího stavu.

Po zakázání verze můžete číst nebo odstraňovat verze s použitím ID verze. Po zakázání správy verzí můžete také uvést verze objektu BLOB.

Následující diagram ukazuje, jak se mění objekt BLOB po zakázání správy verzí, vytváří objekt blob, který není ve verzi. Všechny existující verze přidružené k tomuto objektu BLOB jsou trvalé.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagram znázorňující, že se základní objekt BLOB změnil po zakázání správy verzí":::

## <a name="blob-versioning-and-soft-delete"></a>Správa verzí a obnovitelné odstranění objektů BLOB

Microsoft doporučuje, aby se pro vaše účty úložiště povolila jak Správa verzí, tak i předběžné odstranění objektů BLOB pro optimální ochranu dat. Další informace o obnovitelném odstranění objektů BLOB najdete v tématu [obnovitelné odstranění pro objekty blob Azure Storage](./soft-delete-blob-overview.md).

### <a name="overwriting-a-blob"></a>Přepsání objektu BLOB

Pokud pro účet úložiště povolíte správu verzí objektů BLOB a obnovitelné odstranění objektů blob, přepsání objektu BLOB automaticky vytvoří novou verzi. Nová verze není Odstraněná a při vypršení doby uchování dočasného odstranění se neodebere. Nevytvořily se žádné snímky odstraněné.

### <a name="deleting-a-blob-or-version"></a>Odstranění objektu BLOB nebo verze

Pokud je pro účet úložiště zapnutá Správa verzí a obnovitelné odstranění, v případě odstranění objektu BLOB se z aktuální verze objektu BLOB vytvoří předchozí verze. Nevytvoří se žádná nová verze a nevytvoří se žádné snímky odstraněné. Doba uchování obnovitelného odstranění není platná pro odstraněný objekt BLOB.

Obnovitelné odstranění nabízí další ochranu pro odstraňování verzí objektů BLOB. Odstraníte-li předchozí verzi objektu blob, bude tato verze odstraněna. Odstraněná verze je zachovaná, dokud doba uchování obnovitelného odstranění neuplyne a v tomto okamžiku je trvale odstraní.

Pokud chcete odstranit předchozí verzi objektu blob, zavolejte operaci **Odstranit objekt BLOB** a zadejte ID verze.

Následující diagram ukazuje, co se stane, když odstraníte objekt BLOB nebo verzi objektu BLOB.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagram znázorňující odstranění verze s povoleným obnovitelném odstraněním":::

### <a name="restoring-a-soft-deleted-version"></a>Obnovení softwarové odstraněné verze

Pomocí operace obnovení [objektu BLOB](/rest/api/storageservices/undelete-blob) můžete obnovit dočasně odstraněné verze během doby uchovávání obnovitelného odstranění. Operace obnovení **objektu BLOB** vždy obnoví všechny místně odstraněné verze objektu BLOB. Není možné obnovit pouze jednu odstraněnou verzi.

Obnovení softwarově odstraněných verzí pomocí operace **obnovení objektu BLOB** nezvýší žádnou verzi na aktuální verzi. Chcete-li obnovit aktuální verzi, nejprve obnovte všechny dočasně odstraněné verze a pak pomocí operace [kopírování objektu BLOB](/rest/api/storageservices/copy-blob) zkopírujte předchozí verzi do nové aktuální verze.

Následující diagram ukazuje, jak obnovit obnovitelné odstraněné verze objektů BLOB pomocí operace obnovení **objektu BLOB** a jak obnovit aktuální verzi objektu BLOB pomocí operace **kopírování objektu** BLOB.

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagram znázorňující, jak obnovit obnovitelné odstraněné verze":::

Po uplynutí doby uchování obnovitelného odstranění budou všechny odstraněné verze objektů BLOB trvale odstraněny.

## <a name="blob-versioning-and-blob-snapshots"></a>Vytváření verzí objektů BLOB a snímky objektů BLOB

Snímek objektu BLOB je kopie objektu BLOB jen pro čtení, která je pořízena v určitém časovém okamžiku. Snímky objektů BLOB a verze objektů BLOB jsou podobné, ale snímek je vytvořený ručně nebo vaší aplikací, zatímco verze objektu BLOB je automaticky vytvořená na operaci zápisu nebo odstranění, pokud je pro váš účet úložiště povolená Správa verzí objektů BLOB.

> [!IMPORTANT]
> Microsoft doporučuje, abyste po povolení správy verzí objektů BLOB aktualizovali také aplikaci tak, aby zastavila vytváření snímků objektů blob bloku. Pokud je pro váš účet úložiště povolená Správa verzí, všechny aktualizace a odstranění objektů blob bloku se zachytí a uchovávají ve verzích. Pořízení snímků nenabízí žádné další ochrany dat objektů blob bloku, pokud je povolená Správa verzí objektů BLOB a může zvyšovat náklady a složitost aplikace.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Vytvoření snímku objektu blob, když je povolená Správa verzí

I když se to nedoporučuje, můžete pořídit snímek objektu blob, který je také ve verzi. Pokud aplikaci nemůžete aktualizovat, aby zastavila vytváření snímků objektů blob, když povolíte správu verzí, aplikace může podporovat snímky i verze.

Při pořizování snímku objektu BLOB s verzí se vytvoří nová verze ve stejnou chvíli, kdy se snímek vytvoří. Při pořízení snímku se vytvoří také nová aktuální verze.

Následující diagram ukazuje, co se stane při pořizování snímku objektu BLOB s verzí. V diagramu verze a snímky objektů BLOB s ID verze 2 a 3 obsahují stejná data.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagram znázorňující snímky objektu BLOB s verzí":::

## <a name="authorize-operations-on-blob-versions"></a>Autorizovat operace s verzemi objektů BLOB

Přístup k verzím objektů blob můžete autorizovat pomocí jednoho z následujících přístupů:

- Pomocí řízení přístupu na základě role Azure (Azure RBAC) udělíte oprávnění objektu zabezpečení služby Azure Active Directory (Azure AD). Microsoft doporučuje používat Azure AD pro zajištění vysokého zabezpečení a snadného použití. Další informace o používání služby Azure AD s operacemi objektů BLOB najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](../common/storage-auth-aad.md).
- Pomocí sdíleného přístupového podpisu (SAS) můžete delegovat přístup k verzím objektů BLOB. Zadejte ID verze pro typ podepsaného prostředku `bv` , který představuje verzi objektu BLOB pro vytvoření tokenu SAS pro operace v konkrétní verzi. Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).
- Použitím přístupových klíčů účtu k autorizaci operací s verzemi objektů BLOB se sdíleným klíčem. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key).

Správa verzí objektů BLOB je navržená tak, aby chránila vaše data před náhodným nebo škodlivým odstraněním. Kvůli vylepšení ochrany vyžaduje odstranění verze objektu BLOB zvláštní oprávnění. Následující části popisují oprávnění potřebná k odstranění verze objektu BLOB.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Akce Azure RBAC pro odstranění verze objektu BLOB

Následující tabulka uvádí, které akce Azure RBAC podporují odstranění objektu BLOB nebo verze objektu BLOB.

| Description | Operace Blob service | Vyžaduje se akce s daty služby Azure RBAC. | Podpora integrované role Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Odstraňuje se aktuální verze. | Odstranění objektu blob | **Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/DELETE** | Přispěvatel dat v objektech blob služby Storage |
| Odstranění předchozí verze | Odstranění objektu blob | **Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/deleteBlobVersion/Action** | Vlastník dat v objektech blob služby Storage |

### <a name="shared-access-signature-sas-parameters"></a>Parametry sdíleného přístupového podpisu (SAS)

Podepsaný prostředek pro verzi objektu BLOB je `bv` . Další informace najdete v tématech [Vytvoření SAS služby](/rest/api/storageservices/create-service-sas) nebo [Vytvoření SAS pro delegování uživatelů](/rest/api/storageservices/create-user-delegation-sas).

Následující tabulka uvádí oprávnění požadovaná na SAS k odstranění verze objektu BLOB.

| **Oprávnění** | **Symbol identifikátoru URI** | **Povolené operace** |
|----------------|----------------|------------------------|
| Odstranit         | x              | Odstraní verzi objektu BLOB. |

## <a name="pricing-and-billing"></a>Ceny a fakturace

Povolení správy verzí objektů BLOB může mít za následek další poplatky za úložiště dat vašeho účtu. Při návrhu aplikace je důležité vědět, jak se tyto poplatky můžou snížit, abyste mohli minimalizovat náklady.

Verze objektů blob, jako jsou snímky objektů blob, se účtují stejnou sazbou jako aktivní data. Způsob, jakým se účtují verze, záleží na tom, jestli jste explicitně nastavili vrstvu základního objektu BLOB nebo pro kteroukoli z jeho verzí (nebo snímků). Další informace o úrovních objektů BLOB najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

Pokud jste nezměnili úroveň objektu BLOB nebo verze, budou se vám účtovat jedinečné bloky dat v rámci tohoto objektu blob, jeho verze a jakékoli snímky, které může mít. Další informace najdete v tématu [fakturace, pokud nebyla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Pokud jste změnili úroveň objektu BLOB nebo verze, bude se vám účtovat celý objekt bez ohledu na to, jestli je objekt BLOB a verze nakonec ve stejné vrstvě. Další informace najdete v tématu [fakturace, pokud byla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> Povolení správy verzí pro často přepsaná data může vést ke zvýšení poplatků za kapacitu úložiště a zvýšené latenci během výpisu operací. Aby se tyto otázky zmírnily, ukládejte často přepsaná data v samostatném účtu úložiště s vypnutou správou verzí.

Další informace o fakturaci pro snímky objektů BLOB najdete v tématu [snímky objektů BLOB](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Fakturace v případě, že vrstva objektů BLOB nebyla explicitně nastavena

Pokud jste vrstvu objektů BLOB pro základní objekt BLOB nebo některou z jeho verzí nestavili explicitně, budou se vám účtovat jedinečné bloky nebo stránky v rámci objektu blob, jeho verze a všechny snímky, které může mít. Data sdílená v rámci objektu BLOB a její verze se účtují jenom jednou. Po aktualizaci objektu BLOB se data v základním objektu BLOB odchylují od dat uložených ve svých verzích a jedinečná data se účtují podle bloku nebo stránky.

Když nahradíte blok v rámci objektu blob bloku, tento blok se následně účtuje jako jedinečný blok. To platí i v případě, že má blok stejné ID bloku a stejná data jako v předchozí verzi. Až se blok znovu potvrdí, odliší se od jeho protějšku v předchozí verzi a bude vám účtováno za jeho data. Totéž platí pro stránku v objektu blob stránky, který je aktualizovaný se stejnými daty.

Úložiště objektů BLOB nemá způsob, jak určit, zda dva bloky obsahují identická data. Každý blok, který je nahraný a potvrzený, se považuje za jedinečný, a to i v případě, že má stejná data a stejné ID bloku. Vzhledem k tomu, že se poplatky za jedinečné bloky dostanou, je důležité mít na paměti, že pokud je povolená Správa verzí, je potřeba vzít v úvahu, že aktualizace objektu BLOB je v případě, že je povolené

Pokud je povolená Správa verzí objektů blob, volejte operace aktualizace pro objekty blob bloku tak, aby aktualizovaly nejnižší možný počet bloků. Operace zápisu, které umožňují jemně odstupňovanou kontrolu nad bloky, jsou [vloženy do bloku](/rest/api/storageservices/put-block) a [seznamu blokovaných umístění](/rest/api/storageservices/put-block-list). Operace [Put BLOB](/rest/api/storageservices/put-blob) na druhé straně nahrazuje celý obsah objektu blob, takže může vést k dalším poplatkům.

Následující scénáře ukazují, jak se účtují poplatky za objekty blob bloku a její verze, když není vrstva objektů BLOB explicitně nastavená.

#### <a name="scenario-1"></a>Scénář 1

Ve scénáři 1 má objekt BLOB předchozí verzi. Objekt BLOB se od vytvoření verze neaktualizoval. poplatky se účtují jenom pro jedinečné bloky 1, 2 a 3.

![Diagram 1 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a předchozí verzi](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénář 2

Ve scénáři 2 se aktualizoval jeden blok (blok 3 v diagramu) v objektu BLOB. I když aktualizovaný blok obsahuje stejná data a stejné ID, není stejný jako blok 3 v předchozí verzi. Výsledkem je, že se účtu účtuje čtyři bloky.

![Diagram 2 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a předchozí verzi](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénář 3

Ve scénáři 3 se objekt BLOB aktualizoval, ale jeho verze ne. Blok 3 byl nahrazen blokem 4 v základním objektu blob, ale předchozí verze stále odráží blok 3. Výsledkem je, že se účtu účtuje čtyři bloky.

![Diagram 3 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a předchozí verzi](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénář 4

Ve scénáři 4 byl základní objekt BLOB zcela aktualizován a neobsahuje žádné původní bloky. V důsledku toho se účet účtuje za všechny osm jedinečných bloků &mdash; ve čtyřech základních objektech blob a čtyři v předchozí verzi. K tomuto scénáři může dojít, pokud píšete do objektu BLOB s operací [Put BLOB](/rest/api/storageservices/put-blob) , protože nahrazuje celý obsah základního objektu BLOB.

![Diagram 4 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a předchozí verzi](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Fakturace při explicitně nastavené úrovni objektu BLOB

Pokud jste explicitně nastavili úroveň objektu BLOB pro objekt BLOB nebo verzi (nebo snímek), bude se vám účtovat celá délka obsahu objektu v nové vrstvě bez ohledu na to, jestli sdílí bloky s objektem v původní úrovni. Účtuje se vám také celá velikost obsahu nejstarší verze v původní úrovni. Všechny další předchozí verze nebo snímky, které zůstanou v původní úrovni, se účtují podle jedinečných bloků, které mohou sdílet, jak je popsáno v tématu [fakturace, pokud nebyla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Přesun objektu BLOB na novou úroveň

Následující tabulka popisuje chování fakturace objektu BLOB nebo verze při přesunu do nové úrovně.

| Pokud je vrstva objektů BLOB nastavena explicitně na... | Pak se vám bude účtovat... |
|-|-|
| Základní objekt BLOB s předchozí verzí | Základní objekt BLOB v nové vrstvě a nejstarší verzi v původní vrstvě a všechny jedinečné bloky v jiných verzích. <sup>1</sup> |
| Základní objekt BLOB s předchozí verzí a snímkem | Základní objekt BLOB v nové vrstvě, nejstarší verze v původní vrstvě a nejstarší snímek v původní vrstvě, včetně všech jedinečných bloků v jiných verzích nebo snímcích<sup>1</sup>. |
| Předchozí verze | Verze v nové vrstvě a základním objektu BLOB v původní vrstvě a všechny jedinečné bloky v jiných verzích. <sup>1</sup> |

<sup>1</sup> Pokud jsou k dispozici jiné předchozí verze nebo snímky, které nebyly přesunuty z původní úrovně, jsou tyto verze nebo snímky účtovány na základě počtu jedinečných bloků, které obsahují, jak je popsáno v tématu [fakturace, pokud nebyla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Následující diagram znázorňuje, jak se účtují objekty při přesunu objektu BLOB s verzí do jiné úrovně.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagram znázorňující, jakým způsobem se účtují objekty, když je explicitně vrstvená verze objektu BLOB":::

Explicitní nastavení vrstvy pro objekt blob, verzi nebo snímek nelze vrátit zpět. Pokud objekt BLOB přesunete do nové úrovně a pak ho přesunete zpátky do původní úrovně, bude se vám účtovat úplná délka obsahu objektu, i když sdílí bloky s jinými objekty v původní úrovni.

Operace, které explicitně nastavily úroveň objektu blob, verze nebo snímku, zahrnují:

- [Nastavení úrovně objektu blob](/rest/api/storageservices/set-blob-tier)
- [Vložit objekt BLOB](/rest/api/storageservices/put-blob) se zadanou úrovní
- [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list) se zadanou úrovní
- [Kopírovat objekt BLOB](/rest/api/storageservices/copy-blob) se specifikovanou úrovní

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Odstranění objektu blob, když je povolené obnovitelné odstranění

Pokud je povolené obnovitelné odstranění objektů blob, pokud odstraníte nebo přepíšete základní objekt blob, který má explicitně nastavenou vrstvu, budou se všechny předchozí verze objektu BLOB s přístupným odstraněnou délkou účtovat s plnou délkou obsahu. Další informace o tom, jak se ve spolupráci se správou verzí a obnovitelného odstraňování objektů blob, najdete v tématu [Správa verzí objektů BLOB a obnovitelné odstranění](#blob-versioning-and-soft-delete).

Následující tabulka popisuje chování fakturace u objektu blob, který je podmíněně odstraněn, v závislosti na tom, jestli je Správa verzí povolená nebo zakázaná. Pokud je povolená Správa verzí, vytvoří se verze, když se odstraněný objekt BLOB. Když je Správa verzí zakázaná, měkké odstranění objektu BLOB vytvoří snímek s jemným odstraněním.

| Pokud přepíšete základní objekt BLOB s explicitně nastavenou vrstvou... | Pak se vám bude účtovat... |
|-|-|
| Pokud jsou povolené obnovitelné odstranění objektů BLOB a správa verzí | Všechny existující verze s plnou délkou obsahu bez ohledu na úroveň. |
| Pokud je povolené obnovitelné odstraňování objektů blob, ale je zakázaná správa verzí | Všechny existující snímky obnovitelného odstranění s plnou délkou obsahu bez ohledu na úroveň. |

## <a name="see-also"></a>Viz také

- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
- [Vytvoření snímku objektu BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Obnovitelné odstranění pro objekty blob Azure Storage](./soft-delete-blob-overview.md)