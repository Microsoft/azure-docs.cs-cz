---
title: Zakázat spravovanou identitu účtu Azure Automation
description: Tento článek vysvětluje, jak zakázat a odebrat spravovanou identitu pro účet Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2021
ms.topic: conceptual
ms.openlocfilehash: 74d029db48f64b38eb323150068e173746d379b7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495031"
---
# <a name="disable-your-azure-automation-account-managed-identity"></a>Zakázat spravovanou identitu účtu Azure Automation

Existují dva způsoby, jak zakázat identitu přiřazenou systémem v Azure Automation. Tuto úlohu můžete dokončit z Azure Portal nebo pomocí šablony Azure Resource Manager (ARM).

## <a name="disable-managed-identity-in-the-azure-portal"></a>Zakázat spravovanou identitu v Azure Portal

Spravovanou identitu můžete zakázat z Azure Portal bez ohledu na to, jak se spravovaná identita původně nastavila.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Přejděte do svého účtu Automation a v části **Nastavení účtu** vyberte **Identita** .

1. Nastavte možnost **systém přiřazeno** na **vypnuto** a stiskněte **Uložit**. Po zobrazení výzvy k potvrzení klikněte na tlačítko **Ano**.

Spravovaná identita se odebere a už nebude mít přístup k cílovému prostředku.

## <a name="disable-using-azure-resource-manager-template"></a>Zakázat použití šablony Azure Resource Manager

Pokud jste vytvořili spravovanou identitu pro účet Automation pomocí šablony Azure Resource Manager, můžete spravovanou identitu zakázat tím, že tuto šablonu znovu použijete a upravíte její nastavení. Nastavte typ podřízené vlastnosti objektu identity na **none** , jak je znázorněno v následujícím příkladu, a pak šablonu znovu spusťte.

```json
"identity": { 
   "type": "None" 
} 
```

Odebráním identity přiřazené systémem pomocí této metody se odstraní taky z Azure AD. Identity přiřazené systémem se taky z Azure AD automaticky odeberou, když se odstraní prostředek aplikace, ke kterému jsou přiřazené.

## <a name="next-steps"></a>Další kroky

- Další informace o povolení spravované identity v Azure Automation najdete v tématu [povolení a použití spravované identity pro automatizaci](enable-managed-identity-for-automation.md).

- Přehled zabezpečení účtu Automation najdete v tématu [Přehled ověřování účtu Automation](automation-security-overview.md).
