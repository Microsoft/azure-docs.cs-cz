---
title: Role a požadavky pro Azure Data Share
description: Přečtěte si o oprávněních potřebných ke sdílení a přijímání dat pomocí služby Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265503"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Role a požadavky pro Azure Data Share 

Tento článek popisuje role a oprávnění potřebná ke sdílení a přijímání dat pomocí služby Azure Data Share. 

## <a name="roles-and-requirements"></a>Role a požadavky

Se službou Azure Data Share můžete sdílet data bez výměny přihlašovacích údajů mezi poskytovatelem dat a spotřebitelem. Služba Azure Data Share používá spravované identity (dříve označované jako MSI) k ověření do úložiště dat Azure. 

Spravovaná identita prostředků Azure Data Share musí být udělena přístup k úložišti dat Azure. Služba Azure Data Share pak používá tuto spravovanou identitu ke čtení a zápisu dat pro sdílení na základě snímků a k vytvoření symbolického odkazu pro sdílení na místě. 

Chcete-li sdílet nebo přijímat data z úložiště dat Azure, uživatel potřebuje alespoň následující oprávnění. Pro sdílení založené na SQL jsou vyžadována další oprávnění.
* Oprávnění k zápisu do úložiště dat Azure. Toto oprávnění obvykle existuje v roli **přispěvatele.**
* Oprávnění k vytvoření přiřazení rolí v úložišti dat Azure. Oprávnění k vytváření přiřazení rolí obvykle existuje v roli **Vlastník,** Role správce přístupu uživatele nebo vlastní role s přiřazeným oprávněním k autorizaci/role/zápisu. Toto oprávnění není vyžadováno, pokud je spravované identitě prostředku sdílení dat již udělen přístup k úložišti dat Azure. Požadovanou roli naleznete v tabulce níže.

Níže je uveden souhrn rolí přiřazených ke spravované identitě prostředku sdílení dat:

| |  |  |
|---|---|---|
|**Typ úložiště dat**|**Úložiště zdrojových dat zprostředkovatele dat**|**Úložiště cílových dat pro spotřebitele dat**|
|Azure Blob Storage| Čtečka dat objektu blob úložiště | Přispěvatel dat objektu blob úložiště
|Azure Data Lake Gen1 | Vlastník | Nepodporuje se
|Azure Data Lake Gen2 | Čtečka dat objektu blob úložiště | Přispěvatel dat objektu blob úložiště
|Azure SQL Server | Přispěvatel SQL DB | Přispěvatel SQL DB
|Cluster Průzkumník dat Azure | Přispěvatel | Přispěvatel
|

Pro sdílení na základě SQL musí být uživatel SQL vytvořen z externího zprostředkovatele v databázi SQL se stejným názvem jako prostředek Azure Data Share. Níže je uveden souhrn oprávnění vyžadované ho uživatelem SQL.

| |  |  |
|---|---|---|
|**Typ databáze SQL**|**Oprávnění uživatele SQL zprostředkovatele dat**|**Oprávnění uživatele SQL pro spotřebitele dat**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (dříve SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Poskytovatel dat 
Pokud chcete přidat datovou sadu ve službě Azure Data Share, musí být spravované identitě prostředků sdíleného slučování dat zprostředkovatele udělen přístup ke zdrojovému úložišti dat Azure. Například v případě účtu úložiště je spravované identitě prostředku sdílení dat udělena role čtečka dat objektů blob úložiště. 

To se provádí automaticky službou Azure Data Share, když uživatel přidává datovou sadu přes portál Azure a uživatel má správná oprávnění. Uživatel je například vlastníkem úložiště dat Azure nebo je členem vlastní role, která má přiřazeno oprávnění microsoft.autorizace/role nebo zápis. 

Případně může mít uživatel vlastníka úložiště dat Azure přidat spravované identity prostředku sdílení dat do úložiště dat Azure ručně. Tuto akci je třeba provést pouze jednou na prostředek sdílení dat.

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte do úložiště dat Azure.
1. Vyberte **řízení přístupu (IAM).**
1. Vyberte **Přidat přiřazení role**.
1. V *části Role*vyberte roli v tabulce přiřazení rolí výše (například pro účet úložiště vyberte *čtečku dat objektů blob úložiště).*
1. V *části Vybrat*zadejte název prostředku Azure Data Share.
1. Klikněte na *Uložit*.

Pro zdroje založené na SQL, kromě výše uvedených kroků, uživatel SQL musí být vytvořen z externího zprostředkovatele v databázi SQL se stejným názvem jako prostředek Azure Data Share. Tomuto uživateli musí být uděleno *oprávnění db_datareader.* Ukázkový skript spolu s dalšími předpoklady pro sdílení na základě SQL najdete v [kurzu sdílení dat.](share-your-data.md) 

### <a name="data-consumer"></a>Příjemce dat
Chcete-li přijímat data, musí být spravované identitě prostředku sdílení spotřebitelských dat udělen přístup k cílovému úložišti dat Azure. Například v případě účtu úložiště je spravované identitě prostředku sdílení dat udělena role přispěvatele dat objektů blob úložiště. 

To se provádí automaticky službou Azure Data Share, pokud uživatel určuje cílové úložiště dat prostřednictvím portálu Azure a uživatel má správná oprávnění. Uživatel je například vlastníkem úložiště dat Azure nebo je členem vlastní role, která má přiřazeno oprávnění microsoft.autorizace/role nebo oprávnění k zápisu. 

Případně může mít uživatel vlastníka úložiště dat Azure přidat spravované identity prostředku sdílení dat do úložiště dat Azure ručně. Tuto akci je třeba provést pouze jednou na prostředek sdílení dat.

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat ručně, postupujte podle následujících kroků:

1. Přejděte do úložiště dat Azure.
1. Vyberte **řízení přístupu (IAM).**
1. Vyberte **Přidat přiřazení role**.
1. V *části Role*vyberte roli v tabulce přiřazení rolí výše (například pro účet úložiště vyberte *čtečku dat objektů blob úložiště).*
1. V *části Vybrat*zadejte název prostředku Azure Data Share.
1. Klikněte na *Uložit*.

Pro cíl založený na SQL, kromě výše uvedených kroků, uživatel SQL musí být vytvořen z externího zprostředkovatele v databázi SQL se stejným názvem jako prostředek Azure Data Share. Tomuto uživateli musí být uděleno *oprávnění db_datareader, db_datawriter db_ddladmin.* Ukázkový skript spolu s dalšími předpoklady pro sdílení na základě SQL lze nalézt v kurzu [přijmout a přijímat data.](subscribe-to-data-share.md) 

Pokud sdílíte data pomocí rest api, je třeba vytvořit tato přiřazení rolí ručně. 

Další informace o přidání přiřazení role naleznete v [této dokumentaci](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registrace poskytovatele prostředků 

Pokud chcete pozvánku ke sdílené službě Azure zobrazit poprvé ve svém tenantovi Azure, možná budete muset ručně zaregistrovat poskytovatele prostředků Microsoft.DataShare do předplatného Azure. Podle těchto kroků zaregistrujte poskytovatele prostředků Microsoft.DataShare do předplatného Azure. K *registraci* poskytovatele prostředků potřebujete přístup přispěvatele k předplatnému Azure.

1. Na webu Azure Portal přejděte na **Předplatná**.
1. Vyberte předplatné, které používáte pro Azure Data Share.
1. Klikněte na **Zprostředkovatelé prostředků**.
1. Vyhledejte microsoft.DataShare.
1. Klepněte na tlačítko **Registrovat**.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích v Azure – [principy definic rolí](../role-based-access-control/role-definitions.md)

