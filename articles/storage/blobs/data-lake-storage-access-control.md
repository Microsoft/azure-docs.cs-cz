---
title: Přehled řízení přístupu v Azure Data Lake Storage Gen2 | Microsoft Docs
description: Pochopte, jak řízení přístupu funguje v Azure Data Lake Storage Gen2. Podporují se řízení přístupu na základě role v Azure (Azure RBAC) a seznamy ACL typu POSIX.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 31d67daebf2e15fb11b5ebe30c4f7741a09eed2d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716109"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Řízení přístupu ve službě Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementuje model řízení přístupu, který podporuje řízení přístupu na základě role Azure (Azure RBAC) a seznamy řízení přístupu (ACL) typu POSIX. Tento článek shrnuje základy modelu řízení přístupu pro Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac"></a>

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Azure RBAC používá přiřazení rolí, aby efektivně používala sady oprávnění pro *objekty zabezpečení*. Objekt *zabezpečení* je objekt, který představuje uživatele, skupinu, instanční objekt nebo spravovanou identitu, která je definovaná v Azure Active Directory (AD), která žádá o přístup k prostředkům Azure.

Tyto prostředky Azure jsou obvykle omezené na prostředky nejvyšší úrovně (například: Azure Storage účty). V případě Azure Storage a následně Azure Data Lake Storage Gen2 tento mechanismus byl rozšířen na prostředek kontejneru (systém souborů).

Informace o tom, jak přiřadit role k objektům zabezpečení v oboru účtu úložiště, najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Uživatel typu Host nemůže vytvořit přiřazení role.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Dopad přiřazení rolí na seznam řízení přístupu na úrovni souborů a adresářů

Zatímco použití přiřazení rolí Azure je účinným mechanismem pro řízení přístupových oprávnění, jedná se o velmi hrubý mechanismus vzhledem k seznamům ACL. Nejmenší členitost pro Azure RBAC je na úrovni kontejneru a ta se vyhodnotí s vyšší prioritou než seznamy ACL. Proto pokud přiřadíte roli objektu zabezpečení v oboru kontejneru, má tento objekt zabezpečení úroveň autorizace přidruženou k této roli pro všechny adresáře a soubory v tomto kontejneru bez ohledu na přiřazení seznamu ACL.

Když je objektu zabezpečení uděleno oprávnění k datům RBAC Azure pomocí [předdefinované role](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)nebo prostřednictvím vlastní role, budou tato oprávnění vyhodnocena jako první při autorizaci žádosti. Pokud je požadovaná operace autorizována přiřazeními role Azure objektu zabezpečení, pak se autorizace okamžitě vyřeší a neprovádí se žádné další kontroly seznamu řízení přístupu (ACL). Případně platí, že pokud objekt zabezpečení nemá přiřazení role Azure, nebo se operace požadavku neshoduje s přiřazeným oprávněním, provedou se kontroly seznamů ACL, abyste zjistili, jestli je objekt zabezpečení autorizovaný k provedení požadované operace.

> [!NOTE]
> Pokud byl objektu zabezpečení přiřazeno přiřazení role úložiště dat objektu BLOB úložiště, pak je objekt zabezpečení považován za *superuživatele* a má plný přístup ke všem následným operacím, včetně nastavení vlastníka adresáře nebo souboru a seznamů ACL pro adresáře a soubory, pro které nejsou vlastníkem. Přístup super uživatele je jediný autorizovaný způsob, jak změnit vlastníka prostředku.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Ověřování sdíleného klíče a sdíleného přístupového podpisu (SAS)

Azure Data Lake Storage Gen2 podporuje sdílené klíče a metody SAS pro ověřování. Charakteristikou těchto metod ověřování je, že k volajícímu není přidružena žádná identita, a proto nelze provést autorizaci pomocí zabezpečení na základě oprávnění.

V případě sdíleného klíče volající efektivně získává přístup super uživatele, což znamená úplný přístup ke všem operacím na všech prostředcích, včetně nastavení vlastník a změny seznamů ACL.

Tokeny SAS zahrnují povolená oprávnění jako součást tokenu. Oprávnění obsažená v tokenu SAS se efektivně aplikují na všechna autorizační rozhodnutí, ale neprovádějí se žádné další kontroly seznamu ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Seznamy řízení přístupu pro soubory a adresáře

K souborům a adresářům můžete přidružit objekt zabezpečení s úrovní přístupu. Tato přidružení jsou zachycena v *seznamu řízení přístupu (ACL)*. Každý soubor a adresář v účtu úložiště má seznam řízení přístupu.

> [!NOTE]
> Seznamy ACL se vztahují pouze na objekty zabezpečení ve stejném tenantovi. 

Pokud jste přiřadili roli k objektu zabezpečení na úrovni účtu úložiště, můžete použít seznamy řízení přístupu a udělit tak tomuto objektu zabezpečení vyšší přístup ke konkrétním souborům a adresářům.

Seznam řízení přístupu nemůžete použít k zajištění úrovně přístupu, která je nižší než úroveň udělená přiřazením role. Pokud například přiřadíte roli [Přispěvatel dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) k objektu zabezpečení, nemůžete použít seznamy řízení přístupu, abyste zabránili zápisu tohoto objektu zabezpečení do adresáře.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Nastavení oprávnění na úrovni souborů a adresářů pomocí seznamů řízení přístupu

Chcete-li nastavit oprávnění na úrovni souborů a adresářů, přečtěte si některé z následujících článků:

| Prostředí | Článek |
|--------|-----------|
|Azure Storage Explorer |[Správa adresářů, souborů a seznamů ACL ve službě Azure Data Lake Storage Gen2 pomocí Průzkumníka služby Azure Storage](data-lake-storage-explorer.md#managing-access)|
|.NET |[Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí .NET](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí jazyka Java](data-lake-storage-directory-file-acl-java.md)|
|Python|[Použití Pythonu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Použití PowerShellu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Použití Azure CLI ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[Cesta – aktualizace](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Pokud *je objekt zabezpečení instanční objekt* , je důležité použít ID objektu instančního objektu a nikoli ID objektu související registrace aplikace. Pokud chcete získat ID objektu instančního objektu, otevřete Azure CLI a pak použijte tento příkaz: `az ad sp show --id <Your App ID> --query objectId` . `<Your App ID>`zástupný text nahraďte ID aplikace registrace vaší aplikace.

### <a name="types-of-access-control-lists"></a>Typy seznamů řízení přístupu

Existují dva druhy seznamů řízení přístupu: *přístupové seznamy ACL* a *Výchozí seznamy ACL*.

Přístup k ovládacímu prvku ACL přístup k objektu. Přístupové seznamy ACL mají přístup k souborům a adresářům.

Výchozí seznamy ACL jsou šablony seznamů ACL přidružených k adresáři, které určují přístupové seznamy ACL pro všechny podřízené položky, které jsou vytvořeny v tomto adresáři. Soubory nemají výchozí seznamy ACL.

Přístupové seznamy ACL a výchozí seznamy ACL mají stejnou strukturu.

> [!NOTE]
> Změna výchozího seznamu ACL u nadřazeného objektu nemá vliv na seznam ACL přístupu ani na výchozí seznam ACL podřízených položek, které již existují.

### <a name="levels-of-permission"></a>Úrovně oprávnění

Oprávnění pro objekt kontejneru jsou **čtení**, **zápis**a **spouštění**a lze je použít u souborů a adresářů, jak je znázorněno v následující tabulce:

|            |    Soubor     |   Adresář |
|------------|-------------|----------|
| **Číst (R)** | Může číst obsah souboru | K vypsání obsahu adresáře vyžaduje **čtení** a **provedení** . |
| **Zapisovat (W)** | Může zapisovat do souboru nebo k němu připojovat data | Pro vytváření podřízených položek v adresáři vyžaduje **zápis** a **provedení** . |
| **Provést (X)** | Neznamená cokoli v kontextu Data Lake Storage Gen2 | Vyžaduje se pro procházení podřízených položek adresáře. |

> [!NOTE]
> Pokud udělujete oprávnění jenom pomocí seznamů ACL (žádná služba Azure RBAC) a potom udělíte objektu zabezpečení přístup pro čtení nebo zápis do souboru, musíte objektu zabezpečení udělit oprávnění ke **spuštění** kontejneru a ke každé složce v hierarchii složek, které soubor zavede.

#### <a name="short-forms-for-permissions"></a>Zkrácené verze oprávnění

Zápis **RWX** se používá k označení **Číst + Zapisovat + Provést**. Používá se i zhuštěná číselná verze, která využívá nahrazení **Číst = 4**, **Zapisovat = 2** a **Provést = 1**, přičemž oprávnění je vyjádřeno součtem. Dále je uvedeno několik příkladů.

| Číselný tvar | Krátký tvar |      Význam     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Číst + Zapisovat + Provést |
| 5            | `R-X`        | Číst + Provést         |
| 4            | `R--`        | Číst                   |
| 0            | `---`        | Žádná oprávnění         |

#### <a name="permissions-inheritance"></a>Dědičnost oprávnění

V modelu stylu POSIX, který používá Data Lake Storage Gen2, jsou oprávnění pro položku uložena na samotné položce. Jinými slovy oprávnění pro položku nelze zdědit z nadřazených položek, pokud jsou oprávnění nastavena po vytvoření podřízené položky. Oprávnění jsou zděděna pouze v případě, že pro nadřazené položky byly nastaveny výchozí oprávnění před vytvořením podřízených položek.

### <a name="common-scenarios-related-to-permissions"></a>Běžné scénáře týkající se oprávnění

V následující tabulce jsou uvedeny některé běžné scénáře, které vám pomohou pochopit, která oprávnění jsou nutná k provádění určitých operací s účtem úložiště.

|    Operace             |    /    | Brno | Portland | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Přečíst Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Připojit k Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Odstranit Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Vytvořit Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Seznamu                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Seznam/Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Seznam/Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Oprávnění k zápisu souboru není nutné odstranit, pokud jsou splněny předchozí dvě podmínky.

### <a name="users-and-identities"></a>Uživatelé a identity

Každý soubor a adresář má odlišná oprávnění pro tyto identity:

- Vlastnící uživatel
- Vlastnící skupina
- Pojmenovaní uživatelé
- Pojmenované skupiny
- Pojmenované objekty služby
- Pojmenované spravované identity
- Všichni ostatní uživatelé

Identity uživatelů a skupin jsou identity Azure Active Directory (Azure AD). Takže pokud není uvedeno jinak, může *uživatel*v kontextu Data Lake Storage Gen2 odkazovat na uživatele služby Azure AD, instanční objekt, spravovanou identitu nebo skupinu zabezpečení.

#### <a name="the-owning-user"></a>Vlastnící uživatel

Uživatel, který položku vytvořil, je automaticky jejím vlastníkem. Vlastnící uživatel může:

* Měnit oprávnění souboru, jehož vlastníkem je.
* Měnit vlastnící skupinu pro vlastněný soubor, pokud je vlastnící uživatel členem cílové skupiny.

> [!NOTE]
> Vlastnící uživatel *nemůže* měnit vlastnícího uživatele souboru nebo adresáře. Pouze superuživatele můžou změnit vlastnícího uživatele souboru nebo adresáře.

#### <a name="the-owning-group"></a>Vlastnící skupina

V seznamech ACL pro POSIX je každý uživatel přidružený k *primární skupině*. Uživatel "Alice" může například patřit do skupiny "finance". Alice může patřit také do více skupin, ale jedna skupina je vždy označena jako jejich primární skupina. Když Alice vytvoří soubor v rámci specifikace POSIX, bude jako vlastnící skupina tohoto souboru nastavena její primární skupina, což je v tomto případě skupina „finance“. Jinak se vlastnící skupina chová podobně jako přiřazená oprávnění pro jiné uživatele nebo skupiny.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Přiřazení vlastnící skupiny pro nový soubor nebo adresář

* **Případ 1**: kořenový adresář "/". Tento adresář se vytvoří při vytvoření kontejneru Data Lake Storage Gen2. V takovém případě je vlastnící skupina nastavena na uživatele, který kontejner vytvořil, pokud byl proveden pomocí protokolu OAuth. Pokud je kontejner vytvořený pomocí sdíleného klíče, SAS účtu nebo SAS služby, pak je vlastník a vlastnící skupina nastavená na **$superuser**.
* **Případ 2** (všechny ostatní případy): při vytvoření nové položky se vlastnící skupina zkopíruje z nadřazeného adresáře.

##### <a name="changing-the-owning-group"></a>Změna vlastnící skupiny

Vlastnící skupinu smí změnit:
* Všichni superuživatelé.
* Vlastnící uživatel, pokud je také členem cílové skupiny.

> [!NOTE]
> Vlastnící skupina nemůže změnit seznamy řízení přístupu (ACL) souboru nebo adresáře.  I když je vlastnící skupina nastavená na uživatele, který účet vytvořil v případě kořenového adresáře, výše uvedený **případ 1** , jeden uživatelský účet není platný pro poskytování oprávnění prostřednictvím vlastnící skupiny. Toto oprávnění můžete přiřadit platné skupině uživatelů, pokud nějaká existuje.

### <a name="access-check-algorithm"></a>Algoritmus kontroly přístupu

Následující pseudokódu představuje algoritmus kontroly přístupu pro účty úložiště.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
mask = get_mask( path )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    if ((desired_perms & entry.permissions & mask) == desired_perms)
        return True 
        
# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Maska

Jak je znázorněno v algoritmu kontroly přístupu, maska omezuje přístup pro pojmenované uživatele, vlastnící skupinu a pojmenované skupiny.  

> [!NOTE]
> Pro nový kontejner Data Lake Storage Gen2 je maska pro seznam ACL přístupu kořenového adresáře ("/") standardně 750 pro adresáře a 640 pro soubory. Soubory neobdrží bit X, protože to není podstatné pro soubory v systému pouze v úložišti.
>
> Maska může být určena pro volání podle volání. To umožňuje různým náročným systémům, jako jsou clustery, mít různé efektivní masky pro jejich souborové operace. Pokud je pro daný požadavek zadána maska, zcela přepíše výchozí masku.

#### <a name="the-sticky-bit"></a>Bit sticky

Bit Sticky je pokročilejší funkcí kontejneru POSIX. V kontextu Data Lake Storage Gen2 je nepravděpodobné, že bude potřeba nacházet v rychlém bitu. Pokud je v adresáři zapnutý bit s rychlým nastavením, může být podřízená položka pouze smazána nebo přejmenována vlastníkem uživatele podřízené položky.

Bit Sticky není zobrazený v Azure Portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Výchozí oprávnění pro nové soubory a adresáře

Když se v existujícím adresáři vytvoří nový soubor nebo adresář, určí výchozí seznam ACL v nadřazeném adresáři:

- Výchozí seznam ACL a přístupový seznam ACL podřízeného adresáře.
- Přístupový seznam ACL podřízeného souboru (soubory nemají výchozí seznam ACL).

#### <a name="umask"></a>umask

Při vytváření souboru nebo adresáře se umask používá k úpravě způsobu nastavení výchozích seznamů ACL pro podřízenou položku. umask je 9 bitová hodnota v nadřazených adresářích, které obsahují hodnotu RWX pro **vlastnícího uživatele**, **vlastnící skupinu**a **Další**.

Umask pro Azure Data Lake Storage Gen2 konstantní hodnotu, která je nastavena na 007. Tato hodnota se převede na:

| komponenta umask     | Číselný tvar | Krátký tvar | Význam |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Pro vlastnícího uživatele zkopírujte výchozí seznam řízení přístupu nadřazeného objektu do seznamu ACL podřízeného objektu. | 
| umask.owning_group  |    0         |   `---`      | Pro vlastnící skupinu zkopírujte výchozí seznam řízení přístupu nadřazeného objektu do seznamu ACL podřízeného objektu. | 
| umask. other         |    7         |   `RWX`      | Pro jiné odeberte všechna oprávnění pro přístupový seznam ACL podřízeného objektu. |

Hodnota umask používaná Azure Data Lake Storage Gen2 efektivně znamená, že hodnota pro **jinou** se nikdy nepřenáší ve výchozím nastavení u nových podřízených objektů, pokud není výchozí seznam ACL definovaný v nadřazeném adresáři. V takovém případě je umask ve skutečnosti ignorováno a oprávnění definovaná výchozím seznamem ACL jsou použita pro podřízenou položku. 

Následující pseudokódu ukazuje, jak je použit umask při vytváření seznamů ACL pro podřízenou položku.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Běžné otázky týkající se seznamů ACL v Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Je třeba povolit podporu pro seznamy ACL?

Ne. Řízení přístupu prostřednictvím seznamů ACL je povolené pro účet úložiště, pokud je zapnutá funkce hierarchického oboru názvů (HNS).

Pokud je funkce HNS vypnutá, autorizační pravidla Azure RBAC se pořád použijí.

### <a name="what-is-the-best-way-to-apply-acls"></a>Jaký je nejlepší způsob, jak použít seznamy ACL?

Skupiny zabezpečení Azure AD vždycky používejte jako přiřazený objekt zabezpečení v seznamech ACL. Naodporujte příležitosti k přímému přiřazení jednotlivých uživatelů nebo instančních objektů. Pomocí této struktury budete moct přidávat a odebírat uživatele nebo instanční objekty, aniž byste museli znovu použít seznamy ACL pro celou adresářovou strukturu. Místo toho je stačí přidat nebo odebrat z příslušné skupiny zabezpečení Azure AD. Mějte na paměti, že seznamy ACL nejsou zděděné, a proto je nutné znovu použít seznamy ACL pro všechny soubory a podadresáře. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Která oprávnění jsou nutná k rekurzivnímu odstranění adresáře a jeho obsahu?

- Volající má oprávnění Super User,

Nebo

- Nadřazený adresář musí mít oprávnění zapisovat + provést.
- Adresář, který se má odstranit, a každý adresář v něm vyžaduje oprávnění číst + zapisovat + provést.

> [!NOTE]
> K odstraňování souborů v adresářích nemusíte potřebovat oprávnění k zápisu. Kořenový adresář "/" také nelze nikdy odstranit.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Kdo je vlastníkem souboru nebo adresáře?

Autor souboru nebo adresáře se stal vlastníkem. V případě kořenového adresáře se jedná o identitu uživatele, který kontejner vytvořil.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Která skupina je nastavená jako vlastnící skupina souboru nebo adresáře při vytvoření?

Vlastnící skupina je zkopírována z vlastnící skupiny nadřazeného adresáře, pod nímž je vytvořen nový soubor nebo adresář.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jsem vlastnící uživatel souboru, ale nemáte oprávnění RWX, která potřebuji. Co mám udělat?

Vlastnící uživatel může změnit oprávnění k souboru a sám si udělit veškerá potřebná oprávnění RWX.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Proč se někdy v seznamech ACL zobrazují identifikátory GUID?

Zobrazí se identifikátor GUID, pokud položka představuje uživatele a tento uživatel ve službě Azure AD již neexistuje. K tomu obvykle dochází, když uživatel opustí společnost nebo když je jeho účet odstraněn ve službě Azure AD. Instanční objekty a skupiny zabezpečení navíc nemají hlavní název uživatele (UPN), aby je identifikovali a tak byly reprezentované atributem OID (identifikátor GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Návody pro instanční objekt správně nastavit seznamy ACL?

Při definování seznamů ACL pro instanční objekty je důležité použít ID objektu (OID) *instančního* objektu pro registraci aplikace, kterou jste vytvořili. Je důležité si uvědomit, že registrované aplikace mají samostatný instanční objekt v konkrétním tenantovi služby Azure AD. Registrované aplikace mají identifikátor OID, který je viditelný v Azure Portal, ale *instanční objekt* má jiný (odlišný) identifikátor OID.

K získání OID objektu služby, který odpovídá registraci aplikace, můžete použít `az ad sp show` příkaz. Jako parametr zadejte ID aplikace. Tady je příklad získání OID objektu služby, který odpovídá registraci aplikace s ID aplikace = 18218b12-1895-43E9-ad80-6e8fc1ea88ce. V Azure CLI spusťte následující příkaz:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

Zobrazí se identifikátor OID.

Pokud máte pro instanční objekt správný identifikátor OID, přejděte na stránku Průzkumník služby Storage **spravovat přístup** a přidejte identifikátor OID a přiřaďte příslušná oprávnění identifikátoru objektu. Ujistěte se, že jste vybrali možnost **Uložit**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Podporuje Data Lake Storage Gen2 dědění seznamů ACL?

Přiřazení rolí Azure je děděné. Přiřazení toku z předplatného, skupiny prostředků a prostředků účtu úložiště dolů do prostředku kontejneru.

Seznamy ACL nedědí. Výchozí seznamy ACL je ale možné použít k nastavení seznamů ACL pro podřízené podadresáře a soubory vytvořené v nadřazeném adresáři. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Kde najdu další informace o modelu řízení přístupu POSIX?

* [POSIX Access Control Lists on Linux (Seznamy řízení přístupu v rámci specifikace POSIX v Linuxu)](https://www.linux.com/news/posix-acls-linux)
* [Příručka k oprávnění HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Nejčastější dotazy týkající se specifikace POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL na Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Using Access Control Lists on Linux (Seznamy ACL: Používání seznamů řízení přístupu v Linuxu)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
