---
title: 'Kurz: Začínáme přidat správce'
description: V tomto kurzu se dozvíte, jak do svého pracovního prostoru přidat dalšího administrativního uživatele.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553494"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Přidání správce k pracovnímu prostoru synapse

V tomto kurzu se dozvíte, jak přidat správce k pracovnímu prostoru synapse. Tento uživatel bude mít plnou kontrolu nad pracovním prostorem.

## <a name="overview"></a>Přehled

V úvodní příručce jsme se zaměřili na aktivity, *které* v pracovním prostoru provedete. Vzhledem k tomu, že jste pracovní prostor vytvořili v kroku 1, jste správcem pracovního prostoru synapse. Teď provedeme jinou uživatelskou Ryanu ( `ryan@contoso.com` ) správce. Až to bude hotové, Děkujeme, že vám bude moct všechno, co můžete dělat v pracovním prostoru.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: role vlastníka pracovního prostoru

Přiřaďte `ryan@contoso.com` roli **vlastníka** role Azure RBAC v pracovním prostoru.

1. Otevřete Azure Portal a otevřete synapse pracovní prostor.
1. Na levé straně vyberte **Access Control (IAM)**.
1. Přidejte `ryan@contoso.com` do role **vlastníka** . 
1. Klikněte na **Uložit**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: role správce synapse pro pracovní prostor

Přiřaďte k `ryan@contoso.com` roli správce synapse RBAC **synapse** v pracovním prostoru.

1. Otevřete pracovní prostor v synapse studiu.
1. Na levé straně kliknutím na **Spravovat** otevřete centrum pro správu.
1. V části **zabezpečení** klikněte na **řízení přístupu**.
1. Klikněte na **Přidat**.
1. Nechejte **Rozsah** nastaven na **pracovní prostor**.
1. Přidejte `ryan@contoso.com` do role **správce synapse** . 
1. Pak klikněte na **použít**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: přiřazení rolí k primárnímu účtu úložiště

Přiřaďte k `ryan@contoso.com` roli **vlastníka** v primárním účtu úložiště pracovního prostoru.
Přiřaďte `ryan@contoso.com` k **Azure Storage BLOB roli Přispěvatel dat** v primárním účtu úložiště pracovního prostoru.

1. Otevřete v Azure Portal primární účet úložiště pracovního prostoru.
1. Na levé straně klikněte na **Access Control (IAM)**.
1. Přidejte `ryan@contoso.com` do role **vlastníka** . 
1. Přidat `ryan@contoso.com` do role **přispěvatel dat Azure Storage BLOB**

## <a name="dedicated-sql-pools-db_owner-role"></a>Vyhrazené fondy SQL: role db_owner

Přiřaďte `ryan@contoso.com` **db_owner** v každém vyhrazeném fondu SQL v pracovním prostoru.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
