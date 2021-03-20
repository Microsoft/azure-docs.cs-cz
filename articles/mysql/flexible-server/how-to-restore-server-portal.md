---
title: Obnovení-Azure Portal-Azure Database for MySQL-flexibilní Server
description: Tento článek popisuje, jak provádět operace obnovení v Azure Database for MySQL prostřednictvím Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241952"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Obnovení k určitému bodu v čase Azure Database for MySQL-flexibilního serveru (Preview)


> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek poskytuje podrobný postup, jak provádět obnovování v čase v flexibilním serveru pomocí zálohování.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for MySQL flexibilní Server.

## <a name="restore-to-the-latest-restore-point"></a>Obnovení do nejnovějšího bodu obnovení

Postupujte podle těchto kroků a obnovte flexibilní Server pomocí nejstarší existující zálohy.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, ze kterého chcete zálohu obnovit.

2.  V levém panelu klikněte na **Přehled** .

3.  Na stránce Přehled klikněte na tlačítko **obnovit**.

    Symbolu

4.  Příkaz Restore Page se zobrazí s možností výběru mezi **posledním bodem obnovení** a vlastním bodem obnovení.

5.  Vyberte **nejnovější bod obnovení**.


6.  V poli **obnovit do nového serveru** zadejte nový název serveru.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Nejstarší čas obnovení":::

8.  Klikněte na **OK**.

9.  Zobrazí se oznámení o zahájení operace obnovení.

## <a name="restoring-to-a-custom-restore-point"></a>Obnovení do vlastního bodu obnovení

Postupujte podle těchto kroků a obnovte flexibilní Server pomocí nejstarší existující zálohy.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, ze kterého chcete zálohu obnovit.

2.  Na stránce Přehled klikněte na tlačítko **obnovit**.

    Symbolu

3.  Příkaz Restore Page se zobrazí s možností výběru mezi nejstarším bodem obnovení a vlastním bodem obnovení.

4.  Vyberte možnost **vlastní bod obnovení**.

5.  Vyberte datum a čas.

6.  V poli **obnovit do nového serveru** zadejte nový název serveru.

6.  V poli **obnovit do nového serveru** zadejte nový název serveru. 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="Přehled zobrazení":::
 
7.  Klikněte na **OK**.

8.  Zobrazí se oznámení o zahájení operace obnovení.

## <a name="next-steps"></a>Další kroky

Zástupný symbol
