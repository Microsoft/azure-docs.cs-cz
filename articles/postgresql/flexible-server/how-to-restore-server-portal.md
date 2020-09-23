---
title: Obnovení-Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak provádět operace obnovení v Azure Database for PostgreSQL prostřednictvím Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935895"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Obnovení flexibilního serveru v určitém časovém okamžiku

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Tento článek poskytuje podrobný postup, jak provádět obnovování v čase v flexibilním serveru pomocí zálohování. V rámci doby uchování můžete provést buď na nejstarší bod obnovení, nebo na vlastní bod obnovení.

## <a name="pre-requisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for PostgreSQL Server, který je flexibilní. Stejný postup platí i pro flexibilní server nakonfigurovaný s redundancí zóny.

## <a name="restoring-to-the-earliest-restore-point"></a>Obnovení na nejstarší bod obnovení

Postupujte podle těchto kroků a obnovte flexibilní Server pomocí nejstarší existující zálohy.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, ze kterého chcete zálohu obnovit.

2.  Klikněte na **Přehled** na levém panelu a pak klikněte na **obnovit** .
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Přehled obnovení":::

3.  Příkaz Restore Page se zobrazí s možností výběru mezi nejstarším bodem obnovení a vlastním bodem obnovení.

4.  Vyberte **nejstarší bod obnovení** a v poli **obnovit do nového serveru** zadejte nový název serveru. Zobrazí se nejbližší časové razítko, které můžete obnovit. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Nejstarší čas obnovení":::

5.  Klikněte na **OK**.

6.  Zobrazí se oznámení o zahájení operace obnovení.

## <a name="restoring-to-a-custom-restore-point"></a>Obnovení do vlastního bodu obnovení

Postupujte podle těchto kroků a obnovte flexibilní Server pomocí nejstarší existující zálohy.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, ze kterého chcete zálohu obnovit.

2.  Na stránce Přehled klikněte na tlačítko **obnovit**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Přehled obnovení":::
    
3.  Příkaz Restore Page se zobrazí s možností výběru mezi nejstarším bodem obnovení a vlastním bodem obnovení.

4.  Vyberte možnost **vlastní bod obnovení**.

5.  Vyberte datum a čas a zadejte nový název serveru v poli **obnovit na nový server** . 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Čas vlastního obnovení":::
 
6.  Klikněte na **OK**.

7.  Zobrazí se oznámení o zahájení operace obnovení.

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
-   Další informace o [zálohování a obnovení](./concepts-backup-restore.md)
