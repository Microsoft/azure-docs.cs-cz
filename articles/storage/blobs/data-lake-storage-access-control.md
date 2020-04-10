---
title: Přehled řízení přístupu v Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft
description: Zjistěte, jak funguje řízení přístupu v Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992211"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Řízení přístupu ve službě Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementuje model řízení přístupu, který podporuje jak řízení přístupu na základě rolí Azure (RBAC) a posix-like seznamy řízení přístupu (ACLs). Tento článek shrnuje základy modelu řízení přístupu pro data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

RBAC používá přiřazení rolí efektivně použít sady oprávnění na *objekty zabezpečení*. *Zaregistrovaný objekt zabezpečení* je objekt, který představuje uživatele, skupinu, instanční objekt nebo spravovanou identitu, která je definována ve službě Azure Active Directory (AD), který požaduje přístup k prostředkům Azure.

Tyto prostředky Azure jsou obvykle omezené na prostředky nejvyšší úrovně (například: účty Azure Storage). V případě Azure Storage a následně Azure Data Lake Storage Gen2, tento mechanismus byl rozšířen na prostředek kontejneru (systémsouborů).

Informace o tom, jak přiřadit role k objektům zabezpečení v oboru vašeho účtu úložiště, najdete v tématu [Udělení přístupu k objektům blob Azure a dat fronty s RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Uživatel typu Host nemůže vytvořit přiřazení role.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Dopad přiřazení rolí na seznamy řízení přístupu na úrovni souborů a adresářů

Při použití přiřazení rolí RBAC je výkonný mechanismus pro řízení přístupových oprávnění, je velmi hrubě odstupňovaný mechanismus vzhledem k ACLs. Nejmenší rozlišovací schopnost pro RBAC je na úrovni kontejneru a to bude vyhodnocena s vyšší prioritou než ACLs. Pokud tedy přiřadíte roli objektu zabezpečení v oboru kontejneru, má tento zaregistrovaný objekt zabezpečení úroveň autorizace přidruženou k této roli pro všechny adresáře a soubory v tomto kontejneru, bez ohledu na přiřazení acl.

Pokud je zaregistrovaný objekt zabezpečení udělena oprávnění dat RBAC prostřednictvím [předdefinované role](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)nebo prostřednictvím vlastní role, jsou tato oprávnění vyhodnocena nejprve po autorizaci požadavku. Pokud je požadovaná operace autorizována přiřazením RBAC hlavního povinného zabezpečení, je autorizace okamžitě vyřešena a nejsou prováděny žádné další kontroly ACL. Případně pokud objekt zabezpečení nemá přiřazení RBAC nebo operace požadavku neodpovídá přiřazenému oprávnění, jsou provedeny kontroly ACL, aby se zjistilo, zda je objekt zabezpečení oprávněn provést požadovanou operaci.

> [!NOTE]
> Pokud byl objektzabezpečení přiřazen předdefinovanému přiřazení role Vlastník objektů blob úložiště, je za registrovaný objekt zabezpečení považován za *superuživatele* a je jim udělen úplný přístup ke všem operacím mutování, včetně nastavení vlastníka adresáře nebo souboru a také seznamů ACpro adresáře a soubory, pro které nejsou vlastníkem. Přístup superuživatele mj.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Ověřování pomocí sdíleného klíče a sdíleného přístupového podpisu (SAS)

Azure Data Lake Storage Gen2 podporuje metody sdíleného klíče a SAS pro ověřování. Charakteristikou těchto metod ověřování je, že k volajícímu není přidružena žádná identita, a proto nelze provést autorizaci na základě oprávnění na základě primárního zabezpečení.

V případě sdíleného klíče volající efektivně získá přístup "superuživatele", což znamená plný přístup ke všem operacím na všech prostředcích, včetně nastavení vlastníka a změny seznamů ACL.

Tokeny SAS zahrnují povolená oprávnění jako součást tokenu. Oprávnění zahrnutá v tokenu SAS jsou efektivně použita pro všechna rozhodnutí o autorizaci, ale nejsou prováděny žádné další kontroly seznamu ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Seznamy řízení přístupu k souborům a adresářům

Objekt zabezpečení můžete přidružit k úrovni přístupu pro soubory a adresáře. Tato přidružení jsou zachycena v *seznamu řízení přístupu (ACL).* Každý soubor a adresář v účtu úložiště má seznam řízení přístupu.

> [!NOTE]
> Počet aklů platí pouze pro objekty zabezpečení ve stejném tenantovi. 

Pokud jste přiřadili roli k objektu zabezpečení na úrovni účtu úložiště, můžete pomocí seznamů řízení přístupu udělit tomuto zaregistrovaný objekt zabezpečení zvýšený přístup k určitým souborům a adresářům.

Seznamy řízení přístupu nelze použít k zajištění úrovně přístupu, která je nižší než úroveň udělená přiřazením role. Pokud například přiřadíte roli [přispěvatele dat objektů blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) k objektu zabezpečení, nemůžete použít seznamy řízení přístupu, abyste zabránili tomuto objektu zabezpečení v zápisu do adresáře.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Nastavení oprávnění na úrovni souborů a adresářů pomocí seznamů řízení přístupu

Pokud chcete nastavit oprávnění na úrovni souborů a adresářů, podívejte se na některý z následujících článků:

|||
|--------|-----------|
|Azure Storage Explorer |[Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí Průzkumníka úložišť Azure](data-lake-storage-explorer.md#managing-access)|
|.NET |[Použití rozhraní .NET ke správě adresářů, souborů a seznamů ACL v azure data lake storage gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí Javy](data-lake-storage-directory-file-acl-java.md)|
|Python|[Použití Pythonu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Použití PowerShellu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Použití azure cli ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[Cesta – aktualizace](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Pokud je *instanční* objekt zabezpečení instanční objekt, je důležité použít ID objektu instančního objektu a nikoli ID objektu související registrace aplikace. Chcete-li získat ID objektu instančního objektu otevřete `az ad sp show --id <Your App ID> --query objectId`příkaz cli Azure a potom použijte tento příkaz: . nezapomeňte `<Your App ID>` zástupný symbol nahradit ID aplikace při registraci aplikace.

### <a name="types-of-access-control-lists"></a>Typy seznamů řízení přístupu

Existují dva druhy seznamů řízení přístupu: *přístup alokace* a *výchozí seznamy ACL*.

Přístup k knihovnám AC řídí přístup k objektu. Soubory i adresáře mají přístup k seznamům ACL.

Výchozí seznamy AC jsou šablony seznamů AC přidružených k adresáři, které určují přístupové seznamy AC pro všechny podřízené položky, které jsou vytvořeny v tomto adresáři. Soubory nemají výchozí seznamy ACL.

Přístupové hodnoty AC a výchozí hodnoty AC mají stejnou strukturu.

> [!NOTE]
> Změna výchozího přístupového bodu v nadřazeném objektu nemá vliv na přístupový přístupový kód nebo výchozí acl podřízených položek, které již existují.

### <a name="levels-of-permission"></a>Úrovně oprávnění

Oprávnění k objektu kontejneru jsou **Čtení**, **Zápis**a **Spuštění**a lze je použít u souborů a adresářů, jak je znázorněno v následující tabulce:

|            |    File     |   Adresář |
|------------|-------------|----------|
| **Číst (R)** | Může číst obsah souboru | Vyžaduje, **aby bylo vyseznamovat** obsah adresáře čtení a **spouštění.** |
| **Zapisovat (W)** | Může zapisovat do souboru nebo k němu připojovat data | K vytvoření podřízených položek v adresáři vyžaduje **zápis** a **spuštění.** |
| **Provést (X)** | Neznamená nic v souvislosti s Data Lake Storage Gen2 | Požadováno procházet podřízené položky adresáře |

> [!NOTE]
> Pokud udělujete oprávnění pomocí pouze acl (bez RBAC), pak udělit zabezpečení hlavní čtení nebo zápis přístup k souboru, budete muset udělit oprávnění k **provedení** zabezpečení spustit kontejneru a každé složky v hierarchii složek, které vedou k souboru.

#### <a name="short-forms-for-permissions"></a>Zkrácené verze oprávnění

Zápis **RWX** se používá k označení **Číst + Zapisovat + Provést**. Používá se i zhuštěná číselná verze, která využívá nahrazení **Číst = 4**, **Zapisovat = 2** a **Provést = 1**, přičemž oprávnění je vyjádřeno součtem. Dále je uvedeno několik příkladů.

| Číselný tvar | Krátký tvar |      Význam     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Číst + Zapisovat + Provést |
| 5            | `R-X`        | Číst + Provést         |
| 4            | `R--`        | Čtení                   |
| 0            | `---`        | Žádná oprávnění         |

#### <a name="permissions-inheritance"></a>Dědičnost oprávnění

V modelu stylu POSIX, který používá Data Lake Storage Gen2, jsou oprávnění pro položku uložena na samotné položce. Jinými slovy, oprávnění pro položku nelze zdědit z nadřazených položek, pokud jsou oprávnění nastavena po vytvoření podřízené položky. Oprávnění jsou zděděna pouze v případě, že byla nastavena výchozí oprávnění u nadřazených položek před vytvořením podřízených položek.

### <a name="common-scenarios-related-to-permissions"></a>Běžné scénáře týkající se oprávnění

V následující tabulce jsou uvedeny některé běžné scénáře, které vám pomohou pochopit, která oprávnění jsou potřebná k provedení určitých operací s účtem úložiště.

|    Operace             |    /    | Oregon/ | Portland/ | Soubor Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Čtení souboru Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Připojit k souboru Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Odstranit soubor Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Vytvořit soubor Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Seznam /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Seznam /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Seznam /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Oprávnění k zápisu do souboru nejsou nutné k jeho odstranění, pokud jsou splněny předchozí dvě podmínky.

### <a name="users-and-identities"></a>Uživatelé a identity

Každý soubor a adresář má různá oprávnění pro tyto identity:

- Vlastnící uživatel
- Vlastnící skupina
- Pojmenovaní uživatelé
- Pojmenované skupiny
- Pojmenované instanční objekty
- Pojmenované spravované identity
- Všichni ostatní uživatelé

Identity uživatelů a skupin jsou identity Azure Active Directory (Azure AD). Takže pokud není uvedeno jinak, *uživatel*, v kontextu Data Lake Storage Gen2, může odkazovat na uživatele Azure AD, instanční objekt služby, spravované identity nebo skupiny zabezpečení.

#### <a name="the-owning-user"></a>Vlastnící uživatel

Uživatel, který položku vytvořil, je automaticky jejím vlastníkem. Vlastnící uživatel může:

* Měnit oprávnění souboru, jehož vlastníkem je.
* Měnit vlastnící skupinu pro vlastněný soubor, pokud je vlastnící uživatel členem cílové skupiny.

> [!NOTE]
> Vlastnící uživatel *nemůže* změnit vlastnícího uživatele souboru nebo adresáře. Pouze superuživatelé mohou změnit vlastnící uživatele souboru nebo adresáře.

#### <a name="the-owning-group"></a>Vlastnící skupina

V posix aklů je každý uživatel přidružen k *primární skupině*. Uživatel "Alice" může například patřit do skupiny "finance". Alice může také patřit do více skupin, ale jedna skupina je vždy označena jako jejich primární skupina. Když Alice vytvoří soubor v rámci specifikace POSIX, bude jako vlastnící skupina tohoto souboru nastavena její primární skupina, což je v tomto případě skupina „finance“. Jinak se vlastnící skupina chová podobně jako přiřazená oprávnění pro jiné uživatele nebo skupiny.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Přiřazení vlastnící skupiny pro nový soubor nebo adresář

* **Případ 1**: Kořenový adresář "/". Tento adresář je vytvořen při vytvoření kontejneru Data Lake Storage Gen2. V tomto případě vlastnící skupina je nastavena na uživatele, který vytvořil kontejner, pokud bylo provedeno pomocí OAuth. Pokud je kontejner vytvořen pomocí sdíleného klíče, SAS účtu nebo služby SAS, je vlastník a vlastnící skupina nastavena na **$superuser**.
* **Případ 2** (Každý jiný případ): Při vytvoření nové položky je vlastnící skupina zkopírována z nadřazeného adresáře.

##### <a name="changing-the-owning-group"></a>Změna vlastnící skupiny

Vlastnící skupinu smí změnit:
* Všichni superuživatelé.
* Vlastnící uživatel, pokud je také členem cílové skupiny.

> [!NOTE]
> Vlastnící skupina nemůže změnit seznamy AC souboru nebo adresáře.  Zatímco vlastnící skupina je nastavena na uživatele, který vytvořil účet v případě kořenového **adresáře, případ 1** výše, jeden uživatelský účet není platný pro poskytování oprávnění prostřednictvím vlastnící skupiny. Toto oprávnění můžete přiřadit platné skupině uživatelů, pokud nějaká existuje.

### <a name="access-check-algorithm"></a>Algoritmus kontroly přístupu

Následující pseudokód představuje algoritmus kontroly přístupu pro účty úložiště.

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
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Maska

Jak je znázorněno v algoritmu kontroly přístupu, maska omezuje přístup pro pojmenované uživatele, vlastnící skupinu a pojmenované skupiny.  

> [!NOTE]
> Pro nový kontejner Data Lake Storage Gen2 maska pro přístup acl kořenového adresáře ("/") výchozí 750 pro adresáře a 640 pro soubory. Soubory neobdrží bit X, protože je irelevantní pro soubory v systému pouze pro ukládání.
>
> Maska může být specifikována na základě volání. To umožňuje různým náročným systémům, jako jsou clustery, mít různé účinné masky pro své operace se soubory. Pokud je maska zadána v daném požadavku, zcela přepíše výchozí masku.

#### <a name="the-sticky-bit"></a>Bit sticky

Lepkavý bit je pokročilejší funkce kontejneru POSIX. V kontextu Data Lake Storage Gen2 je nepravděpodobné, že bude potřeba lepkavý bit. Stručně řečeno, pokud je v adresáři povolen nepřístupný bit, může být podřízená položka odstraněna nebo přejmenována pouze vlastnícím uživatelem podřízené položky.

Sticky bit se nezobrazuje na webu Azure Portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Výchozí oprávnění pro nové soubory a adresáře

Při vytvoření nového souboru nebo adresáře pod existujícím adresářem určuje výchozí seznam ACL v nadřazeném adresáři:

- Výchozí seznam ACL podřízeného adresáře a přístup ový seznam ACL.
- Přístup k přístupu podřízeného souboru (soubory nemají výchozí seznam ACL).

#### <a name="umask"></a>Vlastnost umask

Při vytváření souboru nebo adresáře se umask používá k úpravě nastavení výchozích seznamů ACL pro podřízenou položku. umask je 9bitová hodnota nadřazených adresářů, která obsahuje hodnotu RWX pro **vlastnící uživatele**, **vlastnící skupinu**a **další**.

Umask pro Azure Data Lake Storage Gen2 konstantní hodnotu, která je nastavena na 007. Tato hodnota se promítá do:

| komponenta umask     | Číselný tvar | Krátký tvar | Význam |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Pro vlastnící uživatele zkopírujte výchozí přístupový soubor ACL nadřazeného dítěte do přístupového přístupu dítěte. | 
| umask.owning_group  |    0         |   `---`      | Pro vlastnící skupinu zkopírujte výchozí přístupový soubor ACL nadřazeného do přístupového bodu Přístup dítěte. | 
| umask.other         |    7         |   `RWX`      | Pro ostatní odebrat všechna oprávnění na přístup dítěte ACL |

Hodnota umask používaná Azure Data Lake Storage Gen2 efektivně znamená, že hodnota pro **ostatní** se nikdy nepřenáší ve výchozím nastavení na nové podřízené položky, bez ohledu na to, co označuje výchozí přístupový bod. 

Následující pseudokód ukazuje, jak je maska použita při vytváření seznamů ACPro pro podřízenou položku.

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Časté otázky týkající se aklů v úložišti datových jezer Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Je třeba povolit podporu pro seznamy ACL?

Ne. Řízení přístupu prostřednictvím aklů je povoleno pro účet úložiště, pokud je zapnuta funkce Hierarchický obor názvů (HNS).

Pokud je hns vypnuto, pravidla autorizace Azure RBAC stále platí.

### <a name="what-is-the-best-way-to-apply-acls"></a>Jaký je nejlepší způsob, jak použít ACLs?

Vždy používejte skupiny zabezpečení Azure AD jako přiřazený objekt zabezpečení v akl. Odolejte možnosti přímo přiřadit jednotlivé uživatele nebo instanční objekty. Použití této struktury vám umožní přidat a odebrat uživatele nebo instanční objekty bez nutnosti znovu použít seznamy ACL pro celou adresářovou strukturu. ) Místo toho stačí přidat nebo odebrat z příslušné skupiny zabezpečení Azure AD. Mějte na paměti, že seznamy ACL nejsou zděděny, a proto opětovné použití seznamů ACL vyžaduje aktualizaci seznamu ACL v každém souboru a podadresáři. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Která oprávnění jsou vyžadována k opakovanému odstranění adresáře a jeho obsahu?

- Volající má oprávnění "superuživatele",

Nebo

- Nadřazený adresář musí mít oprávnění k zápisu + spuštění.
- Adresář, který má být odstraněn, a každý adresář v něm vyžaduje oprávnění ke čtení + zápisu + spuštění.

> [!NOTE]
> K odstranění souborů v adresářích nepotřebujete oprávnění k zápisu. Také kořenový adresář "/" nelze nikdy odstranit.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Kdo je vlastníkem souboru nebo adresáře?

Tvůrce souboru nebo adresáře se stane vlastníkem. V případě kořenového adresáře se jedná o identitu uživatele, který kontejner vytvořil.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Která skupina je nastavena jako vlastnící skupina souboru nebo adresáře při vytváření?

Vlastnící skupina je zkopírována ze skupiny vlastnící adresáře, pod kterým je vytvořen nový soubor nebo adresář.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jsem vlastnící uživatel souboru, ale nemám RWX oprávnění, které potřebuji. Co mám udělat?

Vlastnící uživatel může změnit oprávnění k souboru a sám si udělit veškerá potřebná oprávnění RWX.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Proč někdy vidím identifikátory GUID v aklách?

Guid se zobrazí, pokud položka představuje uživatele a že uživatel již neexistuje ve službě Azure AD. K tomu obvykle dochází, když uživatel opustí společnost nebo když je jeho účet odstraněn ve službě Azure AD. Kromě toho instanční objekty a skupiny zabezpečení nemají hlavní název uživatele (UPN) k jejich identifikaci, a tak jsou reprezentovány jejich OID atribut (guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Jak správně nastavím akly pro instanční objekt?

Když definujete akly pro instanční objekty, je důležité použít ID objektu (OID) *instančního objektu* pro registraci aplikace, kterou jste vytvořili. Je důležité si uvědomit, že registrované aplikace mají samostatný instanční objekt v konkrétním tenantovi Azure AD. Registrované aplikace mají OID, který je viditelný na portálu Azure, ale *instanční objekt má* jiný (jiný) OID.

Chcete-li získat OID pro instanční objekt, který odpovídá `az ad sp show` registraci aplikace, můžete použít příkaz. Jako parametr zadejte ID aplikace. Tady je příklad získání OID pro instanční objekt, který odpovídá registraci aplikace s ID aplikace = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. V příkazovém příkazu Azure CLI spusťte následující příkaz:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

ZOBRAZÍ SE OID.

Pokud máte správný OID pro instanční objekt, přejděte na stránku Storage Explorer **Manage Access** a přidejte oid a přiřaďte příslušná oprávnění pro OID. Ujistěte se, že jste vybrali **uložit**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Podporuje data Lake Storage Gen2 dědičnost aklů?

Azure RBAC přiřazení dědit. Přiřazení toku z předplatného, skupiny prostředků a prostředků účtu úložiště až do prostředku kontejneru.

AcLs nedědí. Výchozí seznamy AC však lze použít k nastavení seznamů AC pro podřízené podadresáře a soubory vytvořené pod nadřazeným adresářem. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Kde najdu další informace o modelu řízení přístupu POSIX?

* [POSIX Access Control Lists on Linux (Seznamy řízení přístupu v rámci specifikace POSIX v Linuxu)](https://www.linux.com/news/posix-acls-linux)
* [Průvodce oprávněními HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Nejčastější dotazy týkající se specifikace POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL na Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Using Access Control Lists on Linux (Seznamy ACL: Používání seznamů řízení přístupu v Linuxu)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
