---
title: Zakázat geografické zálohy
description: Návod pro zakázání geografického zálohování vyhrazeného fondu SQL (dříve SQL DW) ve službě Azure synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739098"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Zakázání geografických záloh pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak zakázat geografické zálohy pro vyhrazený fond SQL (dříve SQL DW) Azure Portal.

## <a name="disable-geo-backups-through-azure-portal"></a>Zakázat geografické zálohy prostřednictvím Azure Portal

Pomocí těchto kroků zakážete geografickou zálohu pro vyhrazený fond SQL (dříve SQL DW):

> [!NOTE]
> Pokud nezakážete geografické zálohování, nebudete už moct obnovit vyhrazený fond SQL (dřív SQL DW) do jiné oblasti Azure. 
>

1. Přihlaste se ke svému účtu [Azure Portal](https://portal.azure.com/) .
1. Vyberte vyhrazený prostředek fondu SQL (dříve SQL DW), pro který chcete zakázat geografické zálohování. 
1. V části **Nastavení** v levém navigačním panelu vyberte **zásady geografického zálohování**.

   ![Zakázat geografickou zálohu](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Chcete-li zakázat geografické zálohování, vyberte možnost **zakázáno**. 

   ![Vypnutí geografického zálohování](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Vyberte *Save (Uložit* ) a ujistěte se, že jsou vaše nastavení uložená. 

   ![Uložit nastavení geografického zálohování](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího vyhrazeného fondu SQL (dřív SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného vyhrazeného fondu SQL (dřív SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
