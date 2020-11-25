---
title: Model řízení přístupu pro Azure Data Lake Storage Gen2 | Microsoft Docs
description: Naučte se konfigurovat přístup na úrovni kontejneru, adresáře a souborů v účtech, které mají hierarchický obor názvů.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: normesta
ms.openlocfilehash: 65d1ef76ffae113a4b526eec75301abbfea751e7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017708"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Model řízení přístupu v Azure Data Lake Storage Gen2

Data Lake Storage Gen2 podporuje následující autorizační mechanismy:

- Autorizace sdíleného klíče
- Autorizace sdíleného přístupového podpisu (SAS)
- Řízení přístupu na základě role (Azure RBAC)
- Seznamy řízení přístupu

[Autorizace pomocí sdíleného klíče a SAS](#shared-key-and-shared-access-signature-sas-authorization) uděluje uživateli (nebo aplikaci) přístup, aniž by museli mít v Azure Active Directory (Azure AD) identitu. U těchto dvou forem ověřování nemá Azure RBAC a ACL žádný účinek.

Azure RBAC a seznam řízení přístupu vyžaduje, aby uživatel (nebo aplikace) měl identitu ve službě Azure AD.  Azure RBAC umožňuje udělit "hrubým" přístup k datům účtu úložiště, jako je čtení nebo zápis na **všechna** data v účtu úložiště, zatímco seznamy řízení přístupu (ACL) umožňují udělit "jemně odstupňovaný" přístup, jako je například přístup pro zápis do konkrétního adresáře nebo souboru.  

Tento článek se zaměřuje na Azure RBAC a seznamy ACL a na tom, jak ho systém vyhodnocuje a provádí autorizační rozhodnutí pro prostředky účtu úložiště.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Řízení přístupu na základě role (Azure RBAC)

Azure RBAC používá přiřazení rolí k použití sad oprávnění pro [objekty zabezpečení](../../role-based-access-control/overview.md#security-principal). Objekt zabezpečení je objekt, který představuje uživatele, skupinu, instanční objekt nebo spravovanou identitu, která je definovaná v Azure Active Directory (AD). Sada oprávnění může objektu zabezpečení poskytnout "hrubý" stupeň přístupu, jako je čtení nebo zápis **všech** dat v účtu úložiště nebo **všech** dat v kontejneru. 

Následující role umožňují objektu zabezpečení získat přístup k datům v účtu úložiště. 

|Role|Popis|
|--|--|
| [Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Úplný přístup k kontejnerům a datům služby Blob Storage. Tento přístup umožňuje objektu zabezpečení nastavit vlastníka položky a upravit seznamy ACL pro všechny položky. |
| [Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Čtení, zápis a odstraňování přístupu ke kontejnerům a objektům blob úložiště objektů BLOB. Tento přístup nepovoluje, aby objekt zabezpečení nastavil vlastnictví položky, ale může upravit seznam ACL pro položky, které jsou vlastněny objektem zabezpečení. |
| [Čtenář dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Přečte a vypíše kontejnery a objekty blob služby Blob Storage. |

Role, jako je [vlastník](../../role-based-access-control/built-in-roles.md#owner), [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor), [Čtenář](../../role-based-access-control/built-in-roles.md#reader)a [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor) , umožňují objektu zabezpečení Spravovat účet úložiště, ale neposkytují přístup k datům v rámci tohoto účtu. Tyto role (kromě **čtenářů**) ale můžou získat přístup k klíčům úložiště, které se dají použít v různých nástrojích klienta pro přístup k datům.

## <a name="access-control-lists-acls"></a>Seznamy ACL

Seznamy ACL umožňují použití "jemnějšího" úrovně přístupu k adresářům a souborům. *Seznam ACL* je konstrukce oprávnění, která obsahuje řadu *položek seznamu ACL*. Každá položka seznamu ACL přidruží objekt zabezpečení s úrovní přístupu.  Další informace najdete v tématu [seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Jak se vyhodnocují oprávnění

Při ověřování na základě objektu zabezpečení jsou oprávnění vyhodnocována v následujícím pořadí.

: &nbsp; &nbsp; nejdřív se vyhodnocují přiřazení rolí Azure a mají přednost před libovolnými přiřazeními ACL.

: 2: &nbsp; &nbsp; Pokud je operace plně autorizována na základě přiřazení role Azure, seznamy ACL nebudou vyhodnoceny vůbec.

: tři: &nbsp; &nbsp; Pokud operace není zcela autorizovaná, vyhodnotí se seznamy ACL.

> [!div class="mx-imgBorder"]
> ![tok oprávnění služby Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Kvůli způsobu, jakým systém vyhodnocuje přístupová oprávnění, **nemůžete** použít seznam ACL k **omezení** přístupu, který již byl udělen přiřazením role. To je proto, že systém nejprve vyhodnocuje přiřazení rolí Azure a pokud přiřazení udělí dostatečná přístupová oprávnění, seznamy ACL se ignorují. 

Následující diagram znázorňuje tok oprávnění pro tři běžné operace: výpis obsahu adresáře, čtení souboru a zápis souboru.

> [!div class="mx-imgBorder"]
> ![Příklad toku oprávnění Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Tabulka oprávnění: kombinování služby Azure RBAC a seznamu ACL

V následující tabulce se dozvíte, jak kombinovat role Azure a položky seznamu ACL tak, aby objekt zabezpečení mohl provádět operace uvedené ve sloupci **operace** . Tato tabulka obsahuje sloupec, který představuje jednotlivé úrovně fiktivní hierarchie adresářů. Existuje sloupec pro kořenový adresář kontejneru ( `/` ), podadresář s názvem **Brno**, podadresáře adresáře Brno s názvem **Portland** a textový soubor v adresáři Portland s názvem **Data.txt**. V těchto sloupcích se zobrazují [krátké](data-lake-storage-access-control.md#short-forms-for-permissions) reprezentace položky seznamu ACL, která je vyžadována pro udělení oprávnění. Není **-** li k provedení operace k dispozici, zobrazí se ve sloupci Hodnota _není k dispozici_.

|    Operace             | Přiřazená role Azure               |    /        | Brno     | Portland | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Přečíst Data.txt            |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |  
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | N/A      | N/A      | N/A       | N/A    |
|                          |   Žádné                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Připojit k Data.txt       |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Žádné                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Odstranit Data.txt          |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | `--X`    | `--X`    | `-WX`     | Není k dispozici    |
|                          |   Žádné                           | `--X`    | `--X`    | `-WX`     | Není k dispozici    |
| Vytvořit Data.txt          |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | `--X`    | `--X`    | `-WX`     | Není k dispozici    |
|                          |   Žádné                           | `--X`    | `--X`    | `-WX`     | Není k dispozici    |
| Seznamu                   |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | N/A      | N/A      | N/A       | N/A    |
|                          |   Žádné                           | `R-X`    | N/A      | N/A       | N/A    |
| Seznam/Oregon/            |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | N/A      | N/A      | N/A       | N/A    |
|                          |   Žádné                           | `--X`    | `R-X`    | N/A       | N/A    |
| Seznam/Oregon/Portland/   |   Vlastník dat v objektech blob služby Storage        | N/A      | N/A      | N/A       | N/A    |
|                          |   Přispěvatel dat v objektech blob služby Storage  | N/A      | N/A      | N/A       | N/A    |
|                          |   Čtenář dat v objektech blob služby Storage       | N/A      | N/A      | N/A       | N/A    |
|                          |   Žádné                           | `--X`    | `--X`    | `R-X`     | Není k dispozici    |


> [!NOTE] 
> Chcete-li zobrazit obsah kontejneru v Průzkumník služby Azure Storage, musí se objekty zabezpečení [přihlašovat k Průzkumník služby Storage pomocí Azure AD](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#add-a-resource-via-azure-ad)a (minimálně) mít oprávnění ke čtení (R--) do kořenové složky ( `\` ) kontejneru. Tato úroveň oprávnění jim dává možnost vypisovat obsah kořenové složky. Pokud nechcete, aby se obsah kořenové složky zobrazil, můžete jim přiřadit roli [čtenáře](../../role-based-access-control/built-in-roles.md#reader) . S touto rolí budou moci zobrazit kontejnery v účtu, ale ne obsah kontejneru. Přístup ke konkrétním adresářům a souborům pak můžete udělit pomocí seznamů řízení přístupu (ACL).   

## <a name="security-groups"></a>Skupiny zabezpečení

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Omezení přiřazení rolí Azure a položek seznamu ACL

Při použití skupin je méně pravděpodobnější, že bude překročen maximální počet přiřazení rolí na předplatné a maximální počet položek seznamu ACL na soubor nebo adresář. Následující tabulka popisuje tato omezení.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autorizace sdíleného klíče a sdíleného přístupového podpisu (SAS)

Azure Data Lake Storage Gen2 podporuje také [sdílené klíče](/rest/api/storageservices/authorize-with-shared-key) a metody [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro ověřování. Charakteristikou těchto metod ověřování je, že k volajícímu není přidružena žádná identita, a proto nelze provést autorizaci pomocí zabezpečení na základě oprávnění.

V případě sdíleného klíče volající efektivně získává přístup super uživatele, což znamená úplný přístup ke všem operacím na všech prostředcích, včetně dat, nastavení vlastníků a změně seznamů ACL.

Tokeny SAS zahrnují povolená oprávnění jako součást tokenu. Oprávnění obsažená v tokenu SAS se efektivně aplikují na všechna autorizační rozhodnutí, ale neprovádějí se žádné další kontroly seznamu ACL.

## <a name="next-steps"></a>Další kroky

Další informace o seznamech řízení přístupu najdete  [v tématu seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).