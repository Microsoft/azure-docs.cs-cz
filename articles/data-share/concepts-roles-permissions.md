---
title: Role a požadavky pro Azure Data Share
description: Přečtěte si o oprávněních potřebných ke sdílení a přijímání dat pomocí Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386876"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Role a požadavky pro Azure Data Share 

Tento článek popisuje role a oprávnění nutná ke sdílení a příjmu dat pomocí služby Azure Data Share. 

## <a name="roles-and-requirements"></a>Role a požadavky

Pomocí služby Azure data Shared můžete sdílet data bez výměn přihlašovacích údajů mezi poskytovatelem dat a příjemcem. Služba sdílení dat Azure používá spravované identity (dříve označované jako MSIs) k ověřování v Azure Data Store. 

Spravované identitě prostředku Azure Data Share se musí udělit přístup k úložišti dat Azure. Služba sdílení dat Azure pak pomocí této spravované identity čte a zapisuje data pro sdílení na základě snímků a k vytvoření symbolického odkazu pro místní sdílení. 

Aby uživatel mohl sdílet nebo přijímat data z úložiště dat Azure, potřebuje alespoň následující oprávnění. Pro sdílení založené na jazyce SQL jsou vyžadována další oprávnění.
* Oprávnění k zápisu do úložiště dat Azure. Toto oprávnění se obvykle nachází v roli **Přispěvatel** .
* Oprávnění k vytvoření přiřazení role v úložišti dat Azure. Oprávnění k vytváření přiřazení rolí obvykle existují v roli **vlastníka** , roli správce přístupu uživatele nebo vlastní role s přiřazenými oprávněními Microsoft. Authorization/role/oprávnění k zápisu. Toto oprávnění není vyžadováno, pokud je spravované identitě prostředku sdílení dat již udělen přístup k úložišti dat Azure. Požadovaná role najdete v tabulce níže.

Níže je uveden souhrn rolí přiřazených k spravované identitě prostředku sdílení dat:

| |  |  |
|---|---|---|
|**Typ úložiště dat**|**Zdrojové úložiště dat Zprostředkovatel dat**|**Datové úložiště cílových uživatelů dat**|
|Azure Blob Storage| Čtečka dat objektů BLOB úložiště | Přispěvatel dat objektu BLOB služby Storage
|Azure Data Lake Gen1 | Vlastník | Nepodporuje se
|Azure Data Lake Gen2 | Čtečka dat objektů BLOB úložiště | Přispěvatel dat objektu BLOB služby Storage
|SQL Server Azure | Přispěvatel databáze SQL | Přispěvatel databáze SQL
|Cluster Azure Data Exploreru | Přispěvatel | Přispěvatel
|

Pro sdílení založené na SQL je potřeba vytvořit uživatele SQL z externího poskytovatele v databázi SQL se stejným názvem, jaký má prostředek sdílené složky Azure. Níže je uveden souhrn oprávnění vyžadovaných uživatelem SQL.

| |  |  |
|---|---|---|
|**Typ SQL Database**|**Oprávnění uživatele Zprostředkovatel dat SQL**|**Oprávnění uživatele SQL pro příjemce dat**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter db_ddladmin
|Azure Synapse Analytics (dříve SQL DW) | db_datareader | db_datareader, db_datawriter db_ddladmin
|


### <a name="data-provider"></a>Poskytovatel dat 
Chcete-li přidat datovou sadu ve službě Azure data Shared, musí být spravované identitě prostředku sdílení dat zprostředkovatele udělen přístup ke zdrojovému úložišti dat Azure. Například v případě účtu úložiště je spravované identitě prostředku sdílení dat udělená role čtečky dat objektů BLOB úložiště. 

To je prováděno automaticky službou Sdílení dat Azure, když uživatel přidává datovou sadu prostřednictvím Azure Portal a uživatel má příslušné oprávnění. Uživatel je například vlastníkem úložiště dat Azure nebo je členem vlastní role, která má přiřazené oprávnění Microsoft. Authorization/role/Write. 

Uživatel také může mít vlastníka úložiště dat Azure a přidat spravovanou identitu prostředku sdílení dat do úložiště dat Azure ručně. Tuto akci je třeba provést pouze jednou pro každý prostředek sdílení dat.

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte do úložiště dat Azure.
1. Vyberte **Access Control (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. V části *role*vyberte roli v tabulce přiřazení role výše (například pro účet úložiště vyberte možnost *čtečka dat objektů BLOB úložiště*).
1. V části *Vybrat*zadejte název vašeho prostředku Azure Data Share.
1. Klikněte na *Uložit*.

Pro zdroje založené na SQL se kromě výše uvedených kroků musí uživatel SQL vytvořit z externího poskytovatele v databázi SQL se stejným názvem, jako je prostředek sdílené složky Azure. Tomuto uživateli musí být uděleno oprávnění *db_datareader* . Ukázkový skript spolu s dalšími předpoklady pro sdílení na základě SQL najdete v kurzu [sdílení vašich dat](share-your-data.md) . 

### <a name="data-consumer"></a>Příjemce dat
Aby bylo možné přijímat data, musí mít spravovaná identita prostředku zdroje dat uživatele udělen přístup k cílovému úložišti dat Azure. Například v případě účtu úložiště má spravovaná identita prostředku sdílení dat přiřazenou roli Přispěvatel dat objektů BLOB úložiště. 

To je prováděno automaticky službou Sdílení dat Azure, pokud uživatel zadá cílové úložiště dat prostřednictvím Azure Portal a uživatel má správné oprávnění. Uživatel je například vlastníkem úložiště dat Azure nebo je členem vlastní role, ke které je přiřazeno přiřazení Microsoft. Authorization/role/oprávnění k zápisu. 

Uživatel také může mít vlastníka úložiště dat Azure a přidat spravovanou identitu prostředku sdílení dat do úložiště dat Azure ručně. Tuto akci je třeba provést pouze jednou pro každý prostředek sdílení dat.

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte do úložiště dat Azure.
1. Vyberte **Access Control (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. V části *role*vyberte roli v tabulce přiřazení role výše (například pro účet úložiště vyberte možnost *čtečka dat objektů BLOB úložiště*).
1. V části *Vybrat*zadejte název vašeho prostředku Azure Data Share.
1. Klikněte na *Uložit*.

V případě cíle založeného na jazyce SQL musí být kromě výše uvedeného postupu vytvořen uživatel SQL z externího poskytovatele v databázi SQL se stejným názvem, jaký má prostředek sdílené složky Azure. Tento uživatel musí mít udělená oprávnění *db_datareader, db_datawriter db_ddladmin* . Ukázkový skript spolu s dalšími předpoklady pro sdílení založené na SQL najdete v kurzu [přijetí a přijetí dat](subscribe-to-data-share.md) . 

Pokud sdílíte data pomocí rozhraní REST API, je potřeba vytvořit Tato přiřazení rolí ručně. 

Další informace o tom, jak přidat přiřazení role, najdete v [této dokumentaci](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registrace poskytovatele prostředků 

Pokud chcete ve svém tenantovi Azure zobrazit pozvání ke sdílení dat Azure poprvé, bude možná potřeba do svého předplatného Azure ručně zaregistrovat poskytovatele prostředků Microsoft. datashare. Pomocí těchto kroků zaregistrujete poskytovatele prostředků Microsoft. datashare do svého předplatného Azure. K registraci poskytovatele prostředků potřebujete přístup *přispěvatele* k předplatnému Azure.

1. V Azure Portal přejděte na **odběry**.
1. Vyberte předplatné, které používáte pro sdílení dat Azure.
1. Klikněte na **poskytovatelé prostředků**.
1. Vyhledejte Microsoft. datashare.
1. Klikněte na **zaregistrovat**.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích v Azure – [pochopení definic rolí](../role-based-access-control/role-definitions.md)

