---
title: Role a požadavky pro Azure Data Share
description: Přečtěte si informace o rolích a požadavcích řízení přístupu pro poskytovatele dat a uživatele dat ke sdílení dat v Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490577"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Role a požadavky pro Azure Data Share 

Tento článek popisuje role, které jsou potřebné ke sdílení dat pomocí sdílené složky Azure, a také k přijímání a přijímání dat pomocí Azure Data Share. 

## <a name="roles-and-requirements"></a>Role a požadavky

Azure Data Share používá spravované identity pro služby Azure (dříve označované jako MSIs) k ověření v podkladových účtech úložiště, aby bylo možné číst data, která jsou sdílena poskytovatelem dat, a přijímat data sdílená jako příjemce dat. V důsledku toho neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. 

Identita spravované služby musí mít udělený přístup k základnímu účtu úložiště nebo databázi SQL. Služba sdílení dat Azure používá ke čtení a zápisu dat Identita spravované služby prostředku sdílené složky Azure. Uživatel Azure Data Share potřebuje možnost vytvořit přiřazení role pro Identita spravované služby k účtu úložiště nebo službě SQL Database, pro kterou sdílí data z/do. 

V případě úložiště existuje oprávnění k vytváření přiřazení rolí v roli **vlastníka** , roli správce přístupu uživatele nebo vlastní role s přiřazenými oprávněními Microsoft. Authorization/role/oprávnění k zápisu. 

Pokud nejste vlastníkem daného účtu úložiště a nemůžete vytvořit přiřazení role pro spravovanou identitu prostředku Azure Data Share sami, můžete požádat správce Azure, aby za vás vytvořil přiřazení role. 

Níže je uveden souhrn rolí přiřazených k identitě spravované zdroji dat:

| |  |  |
|---|---|---|
|**Typ úložiště**|**Zprostředkovatel dat Store**|**Cílové úložiště dat uživatele**|
|Azure Blob Storage| Čtečka dat objektů BLOB úložiště | Přispěvatel dat objektu BLOB služby Storage
|Azure Data Lake Gen1 | Vlastník | Nepodporuje se
|Azure Data Lake Gen2 | Čtečka dat objektů BLOB úložiště | Přispěvatel dat objektu BLOB služby Storage
|Azure SQL | dbo | dbo 
|

### <a name="data-providers"></a>Poskytovatelé dat 
Pokud chcete přidat datovou sadu do sdílené složky Azure Data Provider, musí se do role čtečky dat objektů BLOB úložiště přidat identita, která je sdílená zdroji dat. To je prováděno automaticky službou Sdílení dat Azure, pokud uživatel přidává datové sady prostřednictvím Azure a je vlastníkem účtu úložiště, nebo je členem vlastní role s přiřazenými oprávněními Microsoft. Authorization/role/Write. 

Případně může uživatel mít správce Azure přidat identitu spravovaného prostředku do role data Reader (BLOB Storage) pomocí prostředků. Ruční vytvoření tohoto přiřazení role správcem bude mít za neplatný účet úložiště nebo vlastní přiřazení role. To platí pro data sdílená z Azure Storage nebo Azure Data Lake Gen2. 

Pokud sdílíte data z Azure Data Lake Gen1, musí být přiřazení role k roli vlastníka. 

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte k účtu úložiště.
1. Vyberte **Access Control (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. V části *role*vyberte *čtečka dat objektů BLOB úložiště*.
1. V části *Vybrat*zadejte název vašeho účtu Azure Data Share.
1. Klikněte na *Uložit*.

Pro zdroje založené na SQL se musí uživatel vytvořit z externího poskytovatele v databázi SQL, ze které se data sdílí, a to se stejným názvem jako má účet Azure Data Share. Ukázkový skript spolu s dalšími předpoklady pro sdílení na základě SQL najdete v kurzu [sdílení vašich dat](share-your-data.md) . 

### <a name="data-consumers"></a>Příjemci dat
Aby bylo možné přijímat data, je třeba do role Přispěvatel dat objektu BLOB úložiště a/nebo role dbo databáze SQL přidat data příjemce dat, pokud jsou přijímána data do databáze SQL. 

V případě úložiště to provádí služba Azure Data Share automaticky, pokud uživatel přidává datové sady prostřednictvím Azure a je vlastníkem účtu úložiště, nebo je členem vlastní role, která má oprávnění Microsoft. Authorization/role/Write. přiřazení. 

Případně může uživatel mít správce Azure, aby do role Přispěvatel dat objektů BLOB úložiště přidal identitu spravovanou pomocí prostředků. Ruční vytvoření tohoto přiřazení role správcem bude mít za neplatný účet úložiště nebo vlastní přiřazení role. Všimněte si, že to platí pro data sdílená do Azure Storage nebo Azure Data Lake Gen2. Příjem dat Azure Data Lake Gen1 není podporován. 

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte k účtu úložiště.
1. Vyberte **Access Control (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. V části *role*vyberte *Přispěvatel dat objektu BLOB úložiště*. 
1. V části *Vybrat*zadejte název vašeho účtu Azure Data Share.
1. Klikněte na *Uložit*.

Pokud sdílíte data pomocí našich rozhraní REST API, budete muset ručně vytvořit Tato přiřazení role přidáním účtu sdílení dat do příslušných rolí. 

Pokud přijímáte data do zdroje založeného na jazyce SQL, zajistěte, aby byl nový uživatel vytvořen z externího poskytovatele se stejným názvem, jaký má váš účet Azure Data Share. Viz předpoklady v kurzu [přijetí a přijetí dat](subscribe-to-data-share.md) . 

Další informace o tom, jak přidat přiřazení role, najdete v [této dokumentaci,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) která popisuje, jak přidat přiřazení role do prostředku Azure. 

## <a name="resource-provider-registration"></a>Registrace poskytovatele prostředků 

Když přijmete pozvánku ke sdílené složce Azure, budete muset ručně zaregistrovat poskytovatele prostředků Microsoft. datashare v rámci vašeho předplatného. Pomocí těchto kroků zaregistrujete poskytovatele prostředků Microsoft. datashare do svého předplatného Azure. 

1. V Azure Portal přejděte na **odběry**.
1. Vyberte předplatné, které používáte pro sdílení dat Azure.
1. Klikněte na **poskytovatelé prostředků**.
1. Vyhledejte Microsoft. datashare.
1. Klikněte na **zaregistrovat**.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích v Azure – [pochopení definic rolí](../role-based-access-control/role-definitions.md)

