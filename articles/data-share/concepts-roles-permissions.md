---
title: Role a požadavky pro Azure Data Share Preview
description: Role a požadavky pro Azure Data Share Preview
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7bf98f8774551292574d4f1951eba44657fa7de0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307345"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Role a požadavky pro Azure Data Share Preview

Tento článek popisuje role potřebné ke sdílení dat pomocí Azure Data Share Preview a také k přijímání a přijímání dat pomocí Azure Data Share Preview. 

## <a name="roles-and-requirements"></a>Role a požadavky

Azure Data Share používá spravované identity pro služby Azure (dříve označované jako MSIs) k ověření v podkladových účtech úložiště, aby bylo možné číst data, která jsou sdílena poskytovatelem dat, a přijímat data sdílená jako příjemce dat. V důsledku toho neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. 

Identita spravované služby musí být udělen přístup k příslušným účtům úložiště. Služba sdílení dat Azure používá ke čtení a zápisu dat Identita spravované služby prostředku sdílené složky Azure. Uživatel Azure Data Share potřebuje možnost vytvořit přiřazení role pro Identita spravované služby účtu úložiště, ze kterého sdílí data, z/do. Oprávnění k vytváření přiřazení rolí existuje v roli **vlastníka** , roli správce přístupu uživatele nebo vlastní role s přiřazenými oprávněními Microsoft. Authorization/role/oprávnění k zápisu. 

Pokud nejste vlastníkem daného účtu úložiště a nemůžete vytvořit přiřazení role pro spravovanou identitu prostředku Azure Data Share sami, můžete požádat správce Azure, aby za vás vytvořil přiřazení role. 

Níže je uveden souhrn rolí přiřazených k identitě spravované zdroji dat:

| |  |  |
|---|---|---|
|**Typ úložiště**|**Účet Zprostředkovatel dat zdrojového úložiště**|**Účet úložiště cílového příjemce dat**|
|Azure Blob Storage| Čtenář dat v objektech blob služby Storage | Přispěvatel dat v objektech blob služby Storage
|Azure Data Lake Gen1 | Owner | Není podporováno
|Azure Data Lake Gen2 | Čtenář dat v objektech blob služby Storage | Přispěvatel dat v objektech blob služby Storage
|
### <a name="data-providers"></a>Poskytovatelé dat 
Pokud chcete přidat datovou sadu do sdílené složky Azure Data Provider, musí se do role čtečky dat objektů BLOB úložiště přidat identita, která je sdílená zdroji dat. To je prováděno automaticky službou Sdílení dat Azure, pokud uživatel přidává datové sady prostřednictvím Azure a je vlastníkem účtu úložiště, nebo je členem vlastní role s přiřazenými oprávněními Microsoft. Authorization/role/Write. 

Případně může uživatel mít správce Azure přidat identitu spravovaného prostředku do role data Reader (BLOB Storage) pomocí prostředků. Ruční vytvoření tohoto přiřazení role správcem bude mít za neplatný účet úložiště nebo vlastní přiřazení role. To platí pro data sdílená z Azure Storage nebo Azure Data Lake Gen2. 

Pokud sdílíte data z Azure Data Lake Gen1, musí být přiřazení role k roli vlastníka. 

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte k účtu úložiště.
1. Vyberte **řízení přístupu (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. V části *role*vyberte *čtečka dat objektů BLOB úložiště*.
1. V části *Vybrat*zadejte název vašeho účtu Azure Data Share.
1. Klikněte na *Uložit*.

### <a name="data-consumers"></a>Příjemci dat
Aby bylo možné přijímat data, je nutné do role Přispěvatel dat objektu BLOB úložiště přidat identitu data příjemce sdílené zdroje. Tato role se vyžaduje, aby služba Azure Data Share mohla zapisovat do účtu úložiště. To je prováděno automaticky službou Sdílení dat Azure, pokud uživatel přidává datové sady prostřednictvím Azure a je vlastníkem účtu úložiště, nebo je členem vlastní role, ke které je přiřazeno přiřazení Microsoft. Authorization/role/oprávnění k zápisu. 

Případně může uživatel mít správce Azure, aby do role Přispěvatel dat objektů BLOB úložiště přidal identitu spravovanou pomocí prostředků. Ruční vytvoření tohoto přiřazení role správcem bude mít za neplatný účet úložiště nebo vlastní přiřazení role. Všimněte si, že to platí pro data sdílená do Azure Storage nebo Azure Data Lake Gen2. Příjem dat Azure Data Lake Gen1 není podporován. 

Chcete-li vytvořit přiřazení role pro spravovanou identitu prostředku sdílení dat, postupujte podle následujících kroků:

1. Přejděte k účtu úložiště.
1. Vyberte **řízení přístupu (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. V části *role*vyberte *Přispěvatel dat objektu BLOB úložiště*. 
1. V části *Vybrat*zadejte název vašeho účtu Azure Data Share.
1. Klikněte na *Uložit*.

Pokud sdílíte data pomocí našich rozhraní REST API, budete muset ručně vytvořit Tato přiřazení role přidáním účtu sdílení dat do příslušných rolí. 

Další informace o tom, jak přidat přiřazení role, najdete v [této dokumentaci,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) která popisuje, jak přidat přiřazení role do prostředku Azure. 

## <a name="resource-provider-registration"></a>Registrace poskytovatele prostředků 

Když přijmete pozvánku ke sdílené složce Azure, budete muset ručně zaregistrovat poskytovatele prostředků Microsoft. datashare v rámci vašeho předplatného. Pomocí těchto kroků zaregistrujete poskytovatele prostředků Microsoft. datashare do svého předplatného Azure. 

1. V Azure Portal přejděte na **odběry**.
1. Vyberte předplatné, které používáte pro sdílení dat Azure.
1. Klikněte na **poskytovatelé prostředků**.
1. Vyhledejte Microsoft. datashare.
1. Klikněte na **zaregistrovat**.

## <a name="next-steps"></a>Další postup

- Další informace o rolích v Azure – [pochopení definic rolí](../role-based-access-control/role-definitions.md)

