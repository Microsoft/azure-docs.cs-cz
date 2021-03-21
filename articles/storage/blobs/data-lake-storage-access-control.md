---
title: Seznamy řízení přístupu v Azure Data Lake Storage Gen2 | Microsoft Docs
description: Pochopte, jak seznamy řízení přístupu (ACL) typu Standard POSIX fungují v Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4d75e60d0e497dcdd2aa121f8da73f11a7e2af5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015212"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementuje model řízení přístupu, který podporuje řízení přístupu na základě role Azure (Azure RBAC) a seznamy řízení přístupu (ACL) typu POSIX. Tento článek popisuje seznamy řízení přístupu v Data Lake Storage Gen2. Další informace o tom, jak začlenit službu Azure RBAC společně s seznamy ACL a jak ji systém vyhodnocuje k rozhodování o autorizaci, najdete v tématu [model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>O seznamech ACL

K souborům a adresářům můžete přidružit [objekt zabezpečení](../../role-based-access-control/overview.md#security-principal) s úrovní přístupu. Tato přidružení jsou zachycena v *seznamu řízení přístupu (ACL)*. Každý soubor a adresář v účtu úložiště má seznam řízení přístupu. Když se objekt zabezpečení pokusí operaci u souboru nebo adresáře, zkontroluje seznam řízení přístupu (ACL), jestli má tento objekt zabezpečení (uživatel, skupina, instanční objekt nebo spravovaná identita) správnou úroveň oprávnění k provedení operace.

> [!NOTE]
> Seznamy ACL platí jenom pro objekty zabezpečení ve stejném tenantovi a nevztahují se na uživatele, kteří používají ověřování pomocí sdíleného klíče nebo sdíleného přístupového podpisu (SAS). Důvodem je skutečnost, že k volajícímu není přidružena žádná identita, a proto nelze provést autorizaci na základě oprávnění zabezpečení.  

<a id="set-access-control-lists"></a>

## <a name="how-to-set-acls"></a>Postup nastavení seznamů ACL

Chcete-li nastavit oprávnění na úrovni souborů a adresářů, přečtěte si některé z následujících článků:

| Prostředí | Článek |
|--------|-----------|
|Průzkumník služby Azure Storage |[Použití Průzkumník služby Azure Storage k nastavení seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md)|
|.NET |[Použití .NET k nastavení seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md)|
|Java|[Použití jazyka Java k nastavení seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-java.md)|
|Python|[Použití Pythonu k nastavení seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md)|
|PowerShell|[Použití PowerShellu k nastavení seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md)|
|Azure CLI|[Použití rozhraní příkazového řádku Azure k nastavení seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)|
|REST API |[Cesta – aktualizace](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Pokud *je objekt zabezpečení instanční objekt* , je důležité použít ID objektu instančního objektu a nikoli ID objektu související registrace aplikace. Pokud chcete získat ID objektu instančního objektu, otevřete Azure CLI a pak použijte tento příkaz: `az ad sp show --id <Your App ID> --query objectId` . `<Your App ID>`zástupný text nahraďte ID aplikace registrace vaší aplikace.

## <a name="types-of-acls"></a>Typy seznamů ACL

Existují dva druhy seznamů řízení přístupu: *přístupové seznamy ACL* a *Výchozí seznamy ACL*.

Přístup k ovládacímu prvku ACL přístup k objektu. Přístupové seznamy ACL mají přístup k souborům a adresářům.

Výchozí seznamy ACL jsou šablony seznamů ACL přidružených k adresáři, které určují přístupové seznamy ACL pro všechny podřízené položky, které jsou vytvořeny v tomto adresáři. Soubory nemají výchozí seznamy ACL.

Přístupové seznamy ACL a výchozí seznamy ACL mají stejnou strukturu.

> [!NOTE]
> Změna výchozího seznamu ACL u nadřazeného objektu nemá vliv na seznam ACL přístupu ani na výchozí seznam ACL podřízených položek, které již existují.

## <a name="levels-of-permission"></a>Úrovně oprávnění

Oprávnění k adresářům a souborům v kontejneru, jsou **čtení**, **zápis** a **spouštění** a lze je použít u souborů a adresářů, jak je znázorněno v následující tabulce:

|            |    Soubor     |   Adresář |
|------------|-------------|----------|
| **Číst (R)** | Může číst obsah souboru | K vypsání obsahu adresáře vyžaduje **čtení** a **provedení** . |
| **Zapisovat (W)** | Může zapisovat do souboru nebo k němu připojovat data | Pro vytváření podřízených položek v adresáři vyžaduje **zápis** a **provedení** . |
| **Provést (X)** | Neznamená cokoli v kontextu Data Lake Storage Gen2 | Vyžaduje se pro procházení podřízených položek adresáře. |

> [!NOTE]
> Pokud udělujete oprávnění jenom pomocí seznamů ACL (bez služby Azure RBAC), pak udělíte objektu zabezpečení přístup pro čtení nebo zápis do souboru, musíte objektu zabezpečení udělit oprávnění ke **spouštění** kořenové složky kontejneru a ke každé složce v hierarchii složek, které soubor zavedl.

### <a name="short-forms-for-permissions"></a>Zkrácené verze oprávnění

Zápis **RWX** se používá k označení **Číst + Zapisovat + Provést**. Používá se i zhuštěná číselná verze, která využívá nahrazení **Číst = 4**, **Zapisovat = 2** a **Provést = 1**, přičemž oprávnění je vyjádřeno součtem. Dále je uvedeno několik příkladů.

| Číselný tvar | Krátký tvar |      Význam     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Číst + Zapisovat + Provést |
| 5            | `R-X`        | Číst + Provést         |
| 4            | `R--`        | Číst                   |
| 0            | `---`        | Žádná oprávnění         |

### <a name="permissions-inheritance"></a>Dědičnost oprávnění

V modelu stylu POSIX, který používá Data Lake Storage Gen2, jsou oprávnění pro položku uložena na samotné položce. Jinými slovy oprávnění pro položku nelze zdědit z nadřazených položek, pokud jsou oprávnění nastavena po vytvoření podřízené položky. Oprávnění jsou zděděna pouze v případě, že pro nadřazené položky byly nastaveny výchozí oprávnění před vytvořením podřízených položek.

## <a name="common-scenarios-related-to-acl-permissions"></a>Běžné scénáře související s oprávněními ACL

V následující tabulce jsou uvedeny položky seznamu ACL, které jsou potřeba k tomu, aby objekt zabezpečení mohl provádět operace uvedené ve sloupci **operace** . 

Tato tabulka obsahuje sloupec, který představuje jednotlivé úrovně fiktivní hierarchie adresářů. Existuje sloupec pro kořenový adresář kontejneru ( `\` ), podadresář s názvem **Brno**, podadresáře adresáře Brno s názvem **Portland** a textový soubor v adresáři Portland s názvem **Data.txt**. 

> [!IMPORTANT]
> V této tabulce se předpokládá, že používáte **jenom** seznamy ACL bez přiřazení rolí Azure. Podobnou tabulku, která kombinuje službu Azure RBAC spolu s seznamy ACL, najdete v tématu [Tabulka oprávnění: kombinování služby Azure RBAC a ACL](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl).

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

## <a name="users-and-identities"></a>Uživatelé a identity

Každý soubor a adresář má odlišná oprávnění pro tyto identity:

- Vlastnící uživatel
- Vlastnící skupina
- Pojmenovaní uživatelé
- Pojmenované skupiny
- Pojmenované objekty služby
- Pojmenované spravované identity
- Všichni ostatní uživatelé

Identity uživatelů a skupin jsou identity Azure Active Directory (Azure AD). Takže pokud není uvedeno jinak, může *uživatel* v kontextu Data Lake Storage Gen2 odkazovat na uživatele služby Azure AD, instanční objekt, spravovanou identitu nebo skupinu zabezpečení.

### <a name="the-owning-user"></a>Vlastnící uživatel

Uživatel, který položku vytvořil, je automaticky jejím vlastníkem. Vlastnící uživatel může:

* Měnit oprávnění souboru, jehož vlastníkem je.
* Měnit vlastnící skupinu pro vlastněný soubor, pokud je vlastnící uživatel členem cílové skupiny.

> [!NOTE]
> Vlastnící uživatel *nemůže* měnit vlastnícího uživatele souboru nebo adresáře. Pouze superuživatele můžou změnit vlastnícího uživatele souboru nebo adresáře.

### <a name="the-owning-group"></a>Vlastnící skupina

V seznamech ACL pro POSIX je každý uživatel přidružený k *primární skupině*. Uživatel "Alice" může například patřit do skupiny "finance". Alice může patřit také do více skupin, ale jedna skupina je vždy označena jako jejich primární skupina. Když Alice vytvoří soubor v rámci specifikace POSIX, bude jako vlastnící skupina tohoto souboru nastavena její primární skupina, což je v tomto případě skupina „finance“. Jinak se vlastnící skupina chová podobně jako přiřazená oprávnění pro jiné uživatele nebo skupiny.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Přiřazení vlastnící skupiny pro nový soubor nebo adresář

* **Případ 1**: kořenový adresář "/". Tento adresář se vytvoří při vytvoření kontejneru Data Lake Storage Gen2. V takovém případě je vlastnící skupina nastavena na uživatele, který kontejner vytvořil, pokud byl proveden pomocí protokolu OAuth. Pokud je kontejner vytvořený pomocí sdíleného klíče, SAS účtu nebo SAS služby, pak je vlastník a vlastnící skupina nastavená na **$superuser**.
* **Případ 2** (všechny ostatní případy): při vytvoření nové položky se vlastnící skupina zkopíruje z nadřazeného adresáře.

#### <a name="changing-the-owning-group"></a>Změna vlastnící skupiny

Vlastnící skupinu smí změnit:
* Všichni superuživatelé.
* Vlastnící uživatel, pokud je také členem cílové skupiny.

> [!NOTE]
> Vlastnící skupina nemůže změnit seznamy řízení přístupu (ACL) souboru nebo adresáře.  I když je vlastnící skupina nastavená na uživatele, který účet vytvořil v případě kořenového adresáře, výše uvedený **případ 1** , jeden uživatelský účet není platný pro poskytování oprávnění prostřednictvím vlastnící skupiny. Toto oprávnění můžete přiřadit platné skupině uživatelů, pokud nějaká existuje.

## <a name="access-check-algorithm"></a>Algoritmus kontroly přístupu

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

### <a name="the-mask"></a>Maska

Jak je znázorněno v algoritmu kontroly přístupu, maska omezuje přístup pro pojmenované uživatele, vlastnící skupinu a pojmenované skupiny.  

Pro nový kontejner Data Lake Storage Gen2 je maska pro seznam ACL přístupu kořenového adresáře ("/") standardně **750** pro adresáře a **640** pro soubory. V následující tabulce jsou uvedeny symbolické zápisy těchto úrovní oprávnění.

|Entita|Adresáře|Soubory|
|--|--|--|
|Vlastnící uživatel|`rwx`|`r-w`|
|Vlastnící skupina|`r-x`|`r--`|
|Jiné|`---`|`---`|

Soubory neobdrží bit X, protože to není podstatné pro soubory v systému pouze v úložišti. 

Maska může být určena pro volání podle volání. To umožňuje různým náročným systémům, jako jsou clustery, mít různé efektivní masky pro jejich souborové operace. Pokud je pro daný požadavek zadána maska, zcela přepíše výchozí masku.

### <a name="the-sticky-bit"></a>Bit sticky

Bit Sticky je pokročilejší funkcí kontejneru POSIX. V kontextu Data Lake Storage Gen2 je nepravděpodobné, že bude potřeba nacházet v rychlém bitu. Pokud je v adresáři zapnutý bit s rychlým nastavením, může být podřízená položka pouze smazána nebo přejmenována vlastníkem uživatele podřízené položky.

Bit Sticky není zobrazený v Azure Portal.

## <a name="default-permissions-on-new-files-and-directories"></a>Výchozí oprávnění pro nové soubory a adresáře

Když se v existujícím adresáři vytvoří nový soubor nebo adresář, určí výchozí seznam ACL v nadřazeném adresáři:

- Výchozí seznam ACL a přístupový seznam ACL podřízeného adresáře.
- Přístupový seznam ACL podřízeného souboru (soubory nemají výchozí seznam ACL).

### <a name="umask"></a>umask

Při vytváření souboru nebo adresáře se umask používá k úpravě způsobu nastavení výchozích seznamů ACL pro podřízenou položku. umask je 9 bitová hodnota v nadřazených adresářích, které obsahují hodnotu RWX pro **vlastnícího uživatele**, **vlastnící skupinu** a **Další**.

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

## <a name="faq"></a>Časté otázky

### <a name="do-i-have-to-enable-support-for-acls"></a>Je třeba povolit podporu pro seznamy ACL?

No. Řízení přístupu prostřednictvím seznamů ACL je povolené pro účet úložiště, pokud je zapnutá funkce hierarchického oboru názvů (HNS).

Pokud je funkce HNS vypnutá, pravidla autorizace služby Azure Azure RBAC se pořád použijí.

### <a name="what-is-the-best-way-to-apply-acls"></a>Jaký je nejlepší způsob, jak použít seznamy ACL?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Jak se vyhodnocuje oprávnění Azure RBAC a ACL?

Informace o tom, jak systém vyhodnocuje v Azure RBAC a seznamy ACL dohromady a provádí autorizační rozhodnutí pro prostředky účtu úložiště, najdete v tématu [jak se vyhodnocují oprávnění](data-lake-storage-access-control-model.md#how-permissions-are-evaluated).

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Jaká jsou omezení pro přiřazení rolí Azure a položky seznamu ACL?

V následující tabulce najdete Přehled limitů, které je potřeba vzít v úvahu při používání Azure RBAC ke správě "hrubých" oprávnění (oprávnění, která se vztahují na účty úložiště nebo kontejnery) a používání seznamů ACL ke správě "jemně odstupňovaného" oprávnění (oprávnění, která se vztahují na soubory a adresáře). Použijte skupiny zabezpečení pro přiřazení seznamu ACL. Při použití skupin je méně pravděpodobnější, že bude překročen maximální počet přiřazení rolí na předplatné a maximální počet položek seznamu ACL na soubor nebo adresář. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Podporuje Data Lake Storage Gen2 dědění Azure RBAC?

Přiřazení rolí Azure je děděné. Přiřazení toku z předplatného, skupiny prostředků a prostředků účtu úložiště dolů do prostředku kontejneru.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Podporuje Data Lake Storage Gen2 dědění seznamů ACL?

Výchozí seznamy ACL lze použít k nastavení seznamů ACL pro nové podřízené podadresáře a soubory vytvořené v nadřazeném adresáři. Chcete-li aktualizovat seznamy ACL pro existující podřízené položky, budete muset pro požadovanou hierarchii adresářů přidat, aktualizovat nebo odebrat seznamy ACL rekurzivně. Pokyny najdete v části [nastavení seznamů ACL](#set-access-control-lists) v tomto článku. 

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

### <a name="can-i-set-the-acl-of-a-container"></a>Můžu nastavit seznam řízení přístupu kontejneru?

No. Kontejner nemá seznam ACL. Můžete ale nastavit seznam řízení přístupu kořenového adresáře kontejneru. Každý kontejner má kořenový adresář a sdílí stejný název jako kontejner. Například pokud má kontejner název `my-container` , pak kořenový adresář má název `myContainer/` . 

Azure Storage REST API obsahuje operaci s názvem [seznam ACL kontejneru](/rest/api/storageservices/set-container-acl), ale tato operace se nedá použít k nastavení seznamu ACL kontejneru nebo kořenového adresáře kontejneru. Místo toho se tato operace používá k označení, zda mohou být objekty BLOB v kontejneru [přístupné veřejně](anonymous-read-access-configure.md). 

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

- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
