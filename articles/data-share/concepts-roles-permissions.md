---
title: Role a požadavky pro Azure Data Share
description: Přečtěte si o oprávněních potřebných ke sdílení a přijímání dat pomocí Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f5c5d6da239d302b57bdb37e9d49116a29c1ccb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558131"
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

|**Typ úložiště dat**|**Zdrojové úložiště dat Zprostředkovatel dat**|**Datové úložiště cílových uživatelů dat**|
|---|---|---|
|Azure Blob Storage| Čtenář dat v objektech blob služby Storage | Přispěvatel dat v objektech blob služby Storage
|Azure Data Lake Gen1 | Vlastník | Nepodporuje se
|Azure Data Lake Gen2 | Čtenář dat v objektech blob služby Storage | Přispěvatel dat v objektech blob služby Storage
|Cluster Azure Data Exploreru | Přispěvatel | Přispěvatel
|

Pro sdílení založené na SQL je potřeba vytvořit uživatele SQL z externího poskytovatele v Azure SQL Database se stejným názvem jako prostředek sdílené složky Azure. K vytvoření tohoto uživatele se vyžaduje oprávnění správce Azure Active Directory. Níže je uveden souhrn oprávnění vyžadovaných uživatelem SQL.

|**Typ SQL Database**|**Oprávnění uživatele Zprostředkovatel dat SQL**|**Oprávnění uživatele SQL pro příjemce dat**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter db_ddladmin
|

### <a name="data-provider"></a>Poskytovatel dat

Chcete-li přidat datovou sadu ve službě Azure data Shared, musí být spravované identitě prostředku sdílení dat zprostředkovatele udělen přístup ke zdrojovému úložišti dat Azure. Například v případě účtu úložiště je spravované identitě prostředku sdílení dat udělená role čtečky dat objektů BLOB úložiště. 

To je prováděno automaticky službou Sdílení dat Azure, když uživatel přidává datovou sadu prostřednictvím Azure Portal a uživatel má příslušné oprávnění. Uživatel je například vlastníkem úložiště dat Azure nebo je členem vlastní role, která má přiřazené oprávnění Microsoft. Authorization/role/Write. 

Uživatel také může mít vlastníka úložiště dat Azure a přidat spravovanou identitu prostředku sdílení dat do úložiště dat Azure ručně. Tuto akci je třeba provést pouze jednou pro každý prostředek sdílení dat.

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků.  

1. Přejděte do úložiště dat Azure.
1. Vyberte **Access Control (IAM)**.
1. Vyberte **Přidat přiřazení role**.
1. V části *role* vyberte roli v tabulce přiřazení role výše (například pro účet úložiště vyberte možnost *čtečka dat objektů BLOB úložiště*).
1. V části *Vybrat* zadejte název vašeho prostředku Azure Data Share.
1. Klikněte na *Uložit*.

Další informace o přiřazení rolí najdete [v tématu přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md). Pokud sdílíte data pomocí rozhraní REST API, můžete přiřazení rolí vytvořit pomocí rozhraní API tak, že na ni [přiřadíte role Azure pomocí REST API](../role-based-access-control/role-assignments-rest.md). 

Pro zdroje založené na SQL je potřeba vytvořit uživatele SQL z externího poskytovatele v SQL Database se stejným názvem jako s prostředkem sdílené složky Azure při připojování k SQL Database pomocí ověřování Azure Active Directory. Tomuto uživateli musí být uděleno oprávnění *db_datareader* . Ukázkový skript spolu s dalšími předpoklady pro sdílení založené na SQL najdete v kurzu [sdílení z Azure SQL Database nebo Azure synapse Analytics](how-to-share-from-sql.md) . 

### <a name="data-consumer"></a>Příjemce dat
Aby bylo možné přijímat data, musí mít spravovaná identita prostředku zdroje dat uživatele udělen přístup k cílovému úložišti dat Azure. Například v případě účtu úložiště má spravovaná identita prostředku sdílení dat přiřazenou roli Přispěvatel dat objektů BLOB úložiště. 

To je prováděno automaticky službou Sdílení dat Azure, pokud uživatel zadá cílové úložiště dat prostřednictvím Azure Portal a uživatel má správné oprávnění. Uživatel je například vlastníkem úložiště dat Azure nebo je členem vlastní role, ke které je přiřazeno přiřazení Microsoft. Authorization/role/oprávnění k zápisu. 

Uživatel také může mít vlastníka úložiště dat Azure a přidat spravovanou identitu prostředku sdílení dat do úložiště dat Azure ručně. Tuto akci je třeba provést pouze jednou pro každý prostředek sdílení dat.

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků. 

1. Přejděte do úložiště dat Azure.
1. Vyberte **Access Control (IAM)**.
1. Vyberte **Přidat přiřazení role**.
1. V části *role* vyberte roli v tabulce přiřazení role výše (například pro účet úložiště vyberte možnost *čtečka dat objektů BLOB úložiště*).
1. V části *Vybrat* zadejte název vašeho prostředku Azure Data Share.
1. Klikněte na *Uložit*.

Další informace o přiřazení rolí najdete [v tématu přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md). Pokud přijímáte data pomocí rozhraní REST API, můžete přiřazení rolí vytvořit pomocí rozhraní API tak, že na ni [přiřadíte role Azure pomocí REST API](../role-based-access-control/role-assignments-rest.md). 

V případě cíle založeného na jazyce SQL je nutné vytvořit uživatele SQL z externího poskytovatele v SQL Database se stejným názvem jako s prostředkem sdílené složky Azure při připojování k databázi SQL pomocí ověřování Azure Active Directory. Tento uživatel musí mít udělená oprávnění *db_datareader, db_datawriter db_ddladmin* . Ukázkový skript spolu s dalšími předpoklady pro sdílení založené na SQL najdete v kurzu [sdílení z Azure SQL Database nebo Azure synapse Analytics](how-to-share-from-sql.md) . 

## <a name="resource-provider-registration"></a>Registrace poskytovatele prostředků 

Do svého předplatného Azure možná budete muset ručně zaregistrovat poskytovatele prostředků Microsoft. datashare v následujících scénářích: 

* Zobrazit pozvání ke sdílení dat Azure poprvé ve vašem tenantovi Azure
* Sdílení dat z úložiště dat Azure v jiném předplatném Azure z vašeho prostředku Azure Data Share
* Příjem dat do úložiště dat Azure v jiném předplatném Azure z vašeho prostředku Azure Data Share

Pomocí těchto kroků zaregistrujete poskytovatele prostředků Microsoft. datashare do svého předplatného Azure. K registraci poskytovatele prostředků potřebujete přístup *přispěvatele* k předplatnému Azure.

1. V Azure Portal přejděte na **odběry**.
1. Vyberte předplatné, které používáte pro sdílení dat Azure.
1. Klikněte na **poskytovatelé prostředků**.
1. Vyhledejte Microsoft. datashare.
1. Klikněte na **Zaregistrovat**.
 
Další informace o poskytovateli prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Další kroky

- Další informace o rolích v Azure – [pochopení definic rolí Azure](../role-based-access-control/role-definitions.md)