---
title: Přehled řízení přístupu ve službě Data Lake Store | Dokumentace Microsoftu
description: Zde se dozvíte, jak funguje řízení přístupu v Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a2e29fd6f2dbd4bd573b780a14bd09c0cd03395f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="access-control-in-azure-data-lake-store"></a>Řízení přístupu v Azure Data Lake Store

Azure Data Lake Store implementuje model řízení přístupu odvozený z HDFS, který je zase odvozený z modelu řízení přístupu POSIX. Tento článek shrnuje základy modelu řízení přístupu pro Data Lake Store. Další informace o modelu řízení přístupu HDFS najdete v příručce [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Průvodce oprávněními v HDFS).

## <a name="access-control-lists-on-files-and-folders"></a>Seznamy řízení přístupu k souborům a složkám

Existují dva druhy seznamů řízení přístupu (ACL) – **přístupové seznamy ACL** a **výchozí seznamy ACL**.

* **Přístupové seznamy ACL:** Řídí přístup k objektu. Přístupové seznamy ACL jsou definovány pro soubory i složky.

* **Výchozí seznamy ACL:** „Šablona“ seznamů ACL přidružených ke složce, které určují přístupové seznamy ACL pro všechny podřízené položky vytvořené v rámci příslušné složky. Výchozí seznamy ACL nejsou definovány pro soubory.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Přístupové seznamy ACL i výchozí seznamy ACL mají stejnou strukturu.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Změna výchozího seznamu ACL u nadřazené položky nemá vliv na přístupový seznam ACL ani na výchozí seznam ACL u podřízených položek, které již existují.
>
>

## <a name="users-and-identities"></a>Uživatelé a identity

Každý soubor a složka má samostatná oprávnění pro tyto identity:

* Vlastnící uživatel
* Vlastnící skupina
* Pojmenovaní uživatelé
* Pojmenované skupiny
* Všichni ostatní uživatelé

Identity uživatelů a skupin jsou identity Azure Active Directory (Azure AD). Takže pokud není uvedeno jinak, „uživatel“ v kontextu služby Data Lake Store může označovat buď uživatele Azure AD, nebo skupinu zabezpečení Azure AD.

## <a name="permissions"></a>Oprávnění

Pro objekt systému souborů jsou definována oprávnění **Číst**, **Zapisovat** a **Provést** a pro soubory a složky je lze používat podle následující tabulky:

|            |    File     |   Složka |
|------------|-------------|----------|
| **Číst (R)** | Může číst obsah souboru | Pro vypsání obsahu složky jsou vyžadována oprávnění **Číst** a **Provést**.|
| **Zapisovat (W)** | Může zapisovat do souboru nebo k němu připojovat data | Pro vytváření podřízených položek ve složce jsou vyžadována oprávnění **Zapisovat** a **Provést**. |
| **Provést (X)** | V kontextu Data Lake Store nemá žádný význam | Je vyžadováno k procházení podřízenými položkami složky. |

### <a name="short-forms-for-permissions"></a>Zkrácené verze oprávnění

Zápis **RWX** se používá k označení **Číst + Zapisovat + Provést**. Používá se i zhuštěná číselná verze, která využívá nahrazení **Číst = 4**, **Zapisovat = 2** a **Provést = 1**, přičemž oprávnění je vyjádřeno součtem. Dále je uvedeno několik příkladů.

| Číselný tvar | Krátký tvar |      Význam     |
|--------------|------------|------------------------|
| 7            | RWX        | Číst + Zapisovat + Provést |
| 5            | R-X        | Číst + Provést         |
| 4            | R--        | Čtení                   |
| 0            | ---        | Žádná oprávnění         |


### <a name="permissions-do-not-inherit"></a>Oprávnění se nedědí

V modelu stylu POSIX používaném službou Data Lake Store jsou oprávnění pro položku uložena přímo s příslušnou položkou. Jinými slovy: oprávnění pro položku nelze zdědit z nadřazených položek.

## <a name="common-scenarios-related-to-permissions"></a>Běžné scénáře týkající se oprávnění

Dále jsou uvedeny některé obvyklé scénáře, které vám pomohou pochopit, která oprávnění jsou nutná k provádění určitých operací s účtem Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Oprávnění potřebná ke čtení souboru

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Ke čtení souboru volající potřebuje oprávnění **Číst**.
* Pro všechny složky ve struktuře složek, které soubor obsahují, volající potřebuje oprávnění **Provést**.

### <a name="permissions-needed-to-append-to-a-file"></a>Oprávnění potřebná k připojení dat k souboru

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Pro soubor, ke kterému se mají připojit data, volající potřebuje oprávnění **Zapisovat**.
* Pro všechny složky, které soubor obsahují, volající potřebuje oprávnění **Provést**.

### <a name="permissions-needed-to-delete-a-file"></a>Oprávnění potřebná k odstranění souboru

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Pro nadřazenou složku volající potřebuje oprávnění **Zapisovat + Provést**.
* Pro všechny ostatní složky v cestě k souboru volající potřebuje oprávnění **Provést**.



> [!NOTE]
> Oprávnění k zápisu pro soubor nejsou vyžadována při jeho odstraňování, pokud jsou splněny obě uvedené podmínky.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Oprávnění k výpisu obsahu složky

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* K výpisu obsahu složky volající potřebuje oprávnění **Číst + Provést**.
* Pro všechny složky předchůdce volající potřebuje oprávnění **Provést**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Oprávnění k zobrazení v rámci Azure Portal

V okně **Průzkumník dat** účtu Data Lake Store klikněte na **Přístup**. V Průzkumníku dat se zobrazí seznamy ACL pro prohlížený soubor nebo složku. Po kliknutí na **Přístup** se zobrazí seznamy ACL pro složku **catalog** v rámci účtu **mydatastore**.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

V horní části tohoto okna se zobrazí oprávnění vlastníka. (Na snímku obrazovky je vlastnícím uživatelem Bob.) Pod tím se zobrazí přiřazené přístupové seznamy ACL. 

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Kliknutím na **Rozšířené zobrazení** přejdete k podrobnějšímu zobrazení, kde jsou uvedené výchozí seznamy ACL, maska a popis superuživatelů.  Toto okno nabízí také možnost rekurzivního nastavení přístupových a výchozích seznamů ACL pro podřízené soubory a složky na základě oprávnění aktuální složky.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Superuživatel

Superuživatel má ze všech uživatelů služby Data Lake Store nejrozsáhlejší oprávnění. Superuživatel:

* Má oprávnění RWX ke **všem** souborům a složkám.
* Může měnit oprávnění pro kterýkoli soubor nebo složku.
* Může měnit vlastnícího uživatele nebo vlastnící skupinu pro kterýkoli soubor nebo složku.

V Azure má účet Data Lake Store několik rolí Azure, včetně rolí:

* Vlastníci
* Přispěvatelé
* Čtenáři

Všichni členové role **Vlastníci** pro účet Data Lake Store jsou automaticky superuživateli pro příslušný účet. Další informace najdete v tématu [Řízení přístupu na základě role](../active-directory/role-based-access-control-configure.md).
Pokud chcete vytvořit vlastní roli řízení přístupu na základě role (RBAC) s oprávněními superuživatele, musí zahrnovat následující oprávnění:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>Vlastnící uživatel

Uživatel, který položku vytvořil, je automaticky jejím vlastníkem. Vlastnící uživatel může:

* Měnit oprávnění souboru, jehož vlastníkem je.
* Měnit vlastnící skupinu pro vlastněný soubor, pokud je vlastnící uživatel členem cílové skupiny.

> [!NOTE]
> Vlastnící uživatel *nemůže* měnit vlastnícího uživatele souboru nebo složky. Vlastnícího uživatele souboru nebo složky, mohou měnit pouze superuživatelé.
>
>

## <a name="the-owning-group"></a>Vlastnící skupina

V seznamech ACL POSIX je ke každému uživateli přiřazena „primární skupina“. Uživatel „alice“ může například patřit do skupiny „finance“. Alice může patřit do více skupin, ale jedna skupina je vždy určena jako její primární skupina. Když Alice vytvoří soubor v rámci specifikace POSIX, bude jako vlastnící skupina tohoto souboru nastavena její primární skupina, což je v tomto případě skupina „finance“.

Když je vytvořena nová položka systému souborů, Data Lake Store přiřadí hodnotu vlastnící skupině.

* **Případ 1:** Kořenová složka „/“. Tato složka se vytvoří při vytvoření účtu Data Lake Store. V takovém případě je vlastnící skupina nastavena podle uživatele, který účet vytvořil.
* **Případ 2** (všechny ostatní případy): Při vytvoření nové položky se vlastnící skupina zkopíruje z nadřazené složky.

Jinak se vlastnící skupina chová podobně jako přiřazená oprávnění pro jiné uživatele nebo skupiny.

Vlastnící skupinu smí změnit:
* Všichni superuživatelé.
* Vlastnící uživatel, pokud je také členem cílové skupiny.

> [!NOTE]
> Vlastnící skupina *nemůže* měnit přístupové seznamy souboru nebo složky.

## <a name="access-check-algorithm"></a>Algoritmus kontroly přístupu

Následující obrázek ilustruje algoritmus kontroly přístupu pro účty Data Lake Store.

![Algoritmus seznamů ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Maska a „efektivní oprávnění“

**Maska** je hodnota RWX, která se používá k omezení přístupu pro **pojmenované uživatele**, **vlastnící skupinu** a **pojmenované skupiny** při použití algoritmu kontroly přístupu. Zde jsou uvedeny klíčové koncepce pro masku.

* Maska vytvoří „efektivní oprávnění“. To znamená, že upraví oprávnění v době kontroly přístupu.
* Masku může přímo upravit vlastník souboru a kterýkoli superuživatel.
* Maska může odebrat oprávnění k vytvoření efektivního oprávnění. Maska *nemůže* přidávat oprávnění k efektivnímu oprávnění.

Podívejme se na několik příkladů. V následujícím příkladu je maska nastavena na hodnotu **RWX**, což znamená, že maska neodebere žádná oprávnění. Efektivní oprávnění pro pojmenovaného uživatele, vlastnící skupinu a pojmenovanou skupinu se během kontroly přístupu nezmění.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

V následujícím příkladu je maska nastavena na hodnotu **R-X**. To znamená, že v době kontroly přístupu **deaktivuje oprávnění Zapisovat** pro **pojmenovaného uživatele**, **vlastnící skupinu** a **pojmenovanou skupinu**.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Pro informaci je zde uvedeno, kde masku pro soubor nebo složku najdete na webu Azure Portal.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Pro nový účet Data Lake Store je použita výchozí maska přístupového seznamu ACL kořenové složky („/“) s hodnotou RWX.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Oprávnění pro nové soubory a složky

Při vytvoření nového souboru nebo složky v rámci existující složky se podle výchozího seznamu ACL pro nadřazenou složku určí:

- Výchozí seznam ACL a přístupový seznam ACL podřízené složky.
- Přístupový seznam ACL podřízeného souboru (pro soubory není definován výchozí seznam ACL).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>Přístupový seznam ACL podřízeného souboru nebo složky

Když je vytvořen podřízený soubor nebo složka, je výchozí seznam ACL nadřazené položky zkopírován jako přístupový seznam ACL podřízeného souboru nebo složky. Pokud má navíc **jiný** uživatel nastavena oprávnění RWX ve výchozím seznamu ACL nadřazené položky, je odebrán z přístupového seznamu ACL podřízené položky.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Ve většině scénářů pokrývají informace uvedené výše vše, co potřebujete vědět o určování přístupového seznamu ACL podřízené položky. Pokud jste však seznámeni se systémy POSIX a chcete podrobněji porozumět postupu dosažení této transformace, najdete informace v části [Role funkce umask při vytváření přístupového seznamu ACL pro nové soubory a složky](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) dále v tomto článku.


### <a name="a-child-folders-default-acl"></a>Výchozí seznam ACL podřízené složky

Když je v rámci nadřazené složky vytvořena podřízená složka, výchozí seznam ACL nadřazené složky se beze změny zkopíruje jako výchozí seznam ACL podřízené složky.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Pokročilá témata pro pochopení seznamů ACL v Data Lake Store

Níže je uvedeno několik pokročilých témat, která vám pomohou pochopit, jak se určují seznamy ACL pro soubory nebo složky Data Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Role funkce umask při vytváření přístupového seznamu ACL pro nové soubory a složky

V systému odpovídajícím standardu POSIX se používá obecná koncepce 9bitové hodnoty vlastnosti umask pro nadřazenou složku. Tato hodnota se používá při převodu oprávnění **vlastnícího uživatele**, **vlastnící skupiny** a **jiné** na přístupový seznam ACL nového podřízeného souboru nebo složky. Bity hodnoty umask identifikují bity, které mají být deaktivovány v přístupovém seznamu ACL podřízené položky. Tento postup se proto používá k selektivnímu zabránění šíření oprávnění pro **vlastnícího uživatele**, **vlastnící skupinu** a **jiné**.

V systému HDFS představuje vlastnost umask zpravidla možnost konfigurace v rámci celé lokality a řídí ji správci. Data Lake Store používá funkci **umask v rámci účtu**, kterou nelze změnit. V následující tabulce je uvedena vlastnost umask služby Data Lake Store.

| Uživatelská skupina  | Nastavení | Vliv na přístupový seznam ACL nové podřízené položky |
|------------ |---------|---------------------------------------|
| Vlastnící uživatel | ---     | Žádný vliv                             |
| Vlastnící skupina| ---     | Žádný vliv                             |
| Ostatní       | RWX     | Odebrání oprávnění Číst + Zapisovat + Provést         |

Následující obrázek znázorňuje praktické použití funkce umask. Výsledkem je odebrání oprávnění **Číst + Zapisovat + Provést** pro **jiné** uživatele. Jelikož vlastnost umask neurčila bity pro **vlastnícího uživatele** a **vlastnící skupinu**, nejsou tato oprávnění transformována.

![Seznamy ACL služby Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>Bit sticky

Bit sticky představuje pokročilejší funkci systému souborů POSIX. V kontextu Data Lake Store bit sticky pravděpodobně nebude potřebný.

V následující tabulce je popsáno fungování bitu sticky ve službě Data Lake Store.

| Uživatelská skupina         | File    | Složka |
|--------------------|---------|-------------------------|
| Bit sticky **VYPNUTÝ** | Žádný vliv   | Žádný vliv           |
| Bit sticky **ZAPNUTÝ**  | Žádný vliv   | Brání všem s výjimkou **superuživatelů** a **vlastnícího uživatele** podřízené položky v odstranění nebo přejmenování této podřízené položky.               |

Bit sticky se na webu Azure Portal nezobrazuje.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Běžné otázky týkající se seznamů ACL ve službě Data Lake Store

Zde je uvedeno několik otázek, které se často vyskytují v souvislosti se seznamy ACL ve službě Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>Je třeba povolit podporu pro seznamy ACL?

Ne. Řízení přístupu prostřednictvím seznamů ACL je pro účet Data Lake Store vždy aktivní.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Jaká oprávnění jsou vyžadována pro rekurzivní odstranění složky a jejího obsahu?

* Pro nadřazenou složku musí být nastavena oprávnění **Zapisovat + Provést**.
* Pro složku, která se má odstranit, a všechny složky, které obsahuje, se vyžadují oprávnění **Číst + Zapisovat + Provést**.

> [!NOTE]
> K odstranění souborů ve složkách nepotřebujete oprávnění Zapisovat. Kořenovou složku „/“ navíc nelze **nijak** odstranit.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Kdo je vlastníkem souboru nebo složky?

Vlastníkem souboru nebo složky se stane příslušný tvůrce.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Jaká skupina je nastavena jako vlastnící skupina souboru nebo složky při vytvoření?

Vlastnící skupina se zkopíruje z vlastnící skupiny nadřazené složky, v rámci které se nový soubor nebo složka vytvoří.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jsem vlastnícím uživatelem souboru, ale nemám potřebné oprávnění RWX. Co mám udělat?

Vlastnící uživatel může změnit oprávnění k souboru a sám si udělit veškerá potřebná oprávnění RWX.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Když se podívám na seznamy ACL na webu Azure Portal, zobrazí se uživatelská jména, ale přes rozhraní API se zobrazí identifikátory GUID. Proč?

Položky v seznamech ACL se ukládají jako identifikátory GUID odpovídající uživatelům ve službě Azure AD. Rozhraní API vrací identifikátory GUID tak, jak jsou. Azure Portal se snaží zjednodušit práci se seznamy ACL tím, že překládá identifikátory GUID na popisné názvy, pokud je to možné.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Proč se někdy na webu Azure Portal v seznamech ACL zobrazují identifikátory GUID?

Identifikátor GUID se zobrazí v případě, že daný uživatel již ve službě Azure AD neexistuje. K tomu obvykle dochází, když uživatel opustí společnost nebo když je jeho účet odstraněn ve službě Azure AD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Podporuje služba Data Lake Store dědění seznamů ACL?

Ne, ale výchozí seznamy ACL je možné použít k nastavení seznamů ACL pro podřízené soubory a složku nově vytvořené v nadřazené složce.  

### <a name="what-is-the-difference-between-mask-and-umask"></a>Jaký je rozdíl mezi vlastnostmi maska a umask?

| Vlastnost maska | Vlastnost umask|
|------|------|
| Vlastnost **maska** je k dispozici u všech souborů a složek. | **Umask** je vlastnost účtu Data Lake Store. Ve službě Data Lake Store tedy existuje pouze jedna vlastnost umask.    |
| Vlastnost maska pro soubor nebo složku může změnit vlastnící uživatel nebo vlastnící skupina souboru či superuživatel. | Vlastnost umask nemůže změnit žádný uživatel, dokonce ani superuživatel. Tato hodnota je neměnná, konstantní.|
| Vlastnost maska se používá při provádění algoritmu kontroly přístupu za běhu a slouží k určení, zda má uživatel oprávnění k provedení operace se souborem nebo složkou. Rolí masky je vytvoření „efektivních oprávnění“ v době kontroly přístupu. | Vlastnost umask se během kontroly přístupu vůbec nepoužívá. Vlastnost umask se používá k určení přístupového seznamu ACL nových podřízených položek složky. |
| Maska je 3bitová hodnota RWX, která se vztahuje na pojmenovaného uživatele, vlastnící skupinu a pojmenovanou skupinu v době kontroly přístupu.| Vlastnost umask je 9bitová hodnota a vztahuje se na vlastnícího uživatele, vlastnící skupinu a **jiné** pro novou podřízenou položku.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Kde najdu další informace o modelu řízení přístupu POSIX?

* [POSIX Access Control Lists on Linux (Seznamy řízení přístupu v rámci specifikace POSIX v Linuxu)](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [HDFS Permissions Guide (Průvodce oprávněními v HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

* [Nejčastější dotazy týkající se specifikace POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [POSIX ACL na Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [ACL: Using Access Control Lists on Linux (Seznamy ACL: Používání seznamů řízení přístupu v Linuxu)](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storu](data-lake-store-overview.md)
