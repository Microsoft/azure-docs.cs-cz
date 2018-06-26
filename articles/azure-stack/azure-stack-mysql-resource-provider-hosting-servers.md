---
title: MySQL hostitelské servery v Azure zásobníku | Microsoft Docs
description: Postup přidání instance databáze MySQL pro zřizování prostřednictvím poskytovatele prostředků adaptér MySQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938381"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Přidání hostitelské servery pro poskytovatele prostředků MySQL

Instance databáze MySQL na virtuálním počítači (VM) můžete hostovat v [zásobník Azure](azure-stack-poc.md), nebo na virtuálním počítači mimo prostředí Azure zásobníku, pokud poskytovatel prostředků MySQL se můžete připojit k instanci.

## <a name="connect-to-a-mysql-hosting-server"></a>Připojení k serveru pro hostování databáze MySQL

Ujistěte se, že máte přihlašovací údaje k účtu s oprávněními správce systému. Chcete-li přidat hostitelský server, postupujte takto:

1. Přihlaste se k portálu Azure zásobníku operátor jako správce služby
2. Vyberte **další služby**.
3. Vyberte **prostředky pro správu** > **MySQL hostitelské servery** > **+ přidat**. Tím se otevře **přidání serveru pro hostování databáze MySQL** dialogové okno, znázorňuje následující snímek obrazovky.

   ![Konfiguruje server pro hostování](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Zadejte podrobnosti připojení vaší instance serveru MySQL.

   * Pro **název serveru pro hostování databáze MySQL**, zadejte plně kvalifikovaný název domény (FQDN) nebo platná IPv4 adresa. Nepoužívejte krátký název virtuálního počítače.
   * Výchozí instance databáze MySQL není k dispozici, takže budete muset zadat **velikost hostování služby v GB**. Zadejte velikost, která je blízko kapacitu databázového serveru.
   * Zachovat výchozí nastavení pro **předplatné**.
   * Pro **skupiny prostředků**, vytvořte novou nebo použít stávající skupinu.

   > [!NOTE]
   > Pokud instance databáze MySQL byla přístupná pomocí klienta a správce Azure Resource Manager, můžete ji umístit pod kontrolou zprostředkovatele prostředků. Ale instance databáze MySQL **musí** přidělit výhradně k poskytovateli prostředků.

5. Vyberte **SKU** otevřete **vytvořit SKU** dialogové okno.

   ![Vytvoření databáze MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Verze SKU **název** odrážet vlastnosti verze SKU, mohou uživatelé nasadit své databáze příslušné SKU.

   >[!IMPORTANT]
   >Speciální znaky, včetně mezery a tečky, nejsou podporovány v **název** nebo **vrstvy** při vytváření SKU pro poskytovatele prostředků MySQL.

6. Vyberte **OK** vytvořit verze SKU.
7. V části **přidání serveru pro hostování databáze MySQL**, vyberte **vytvořit**.

Při přidávání serverů, můžete je přiřadíte k nové nebo existující SKU k odlišení nabídky služeb. Například můžete mít instanci enterprise MySQL, která poskytuje vyšší databáze a automatické zálohování. Tento server vysoce výkonné pro různá oddělení může rezervovat ve vaší organizaci.

## <a name="increase-backend-database-capacity"></a>Zvýšení kapacity back-end databáze

Nasazení více serverů MySQL na portálu Azure zásobníku, může zvýšit kapacitu databáze back-end. Tyto servery přidáte do nové nebo existující SKU. Pokud přidáte server do existující SKU, ujistěte se, že vlastnosti serveru jsou stejné jako ostatní servery v verze SKU.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Servery databáze MySQL zpřístupnit uživatelům.

Vytvoření plánu a nabídky na serverech databáze MySQL zpřístupnit uživatelům. Přidání služby Microsoft.MySqlAdapter k plánu a přidat výchozí kvótu nebo vytvořit novou kvótu.

![Vytvoření plánu a nabídky pro databáze](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>Další postup

[Vytvoření databáze MySQL](azure-stack-mysql-resource-provider-databases.md)