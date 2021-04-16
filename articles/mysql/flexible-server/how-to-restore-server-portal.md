---
title: Obnovte Azure Database for MySQL flexibilního serveru pomocí Azure Portal.
description: Tento článek popisuje, jak provádět operace obnovení v Azure Database for MySQL flexibilním serveru prostřednictvím Azure Portal
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502041"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Obnovení k určitému bodu v čase Azure Database for MySQL-flexibilního serveru (Preview) pomocí Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek poskytuje podrobný postup, jak provádět obnovování v čase v flexibilním serveru pomocí zálohování.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for MySQL flexibilní Server.

## <a name="restore-to-the-latest-restore-point"></a>Obnovení do nejnovějšího bodu obnovení

Postupujte podle těchto kroků a obnovte flexibilní Server pomocí nejstarší existující zálohy.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, ze kterého chcete zálohu obnovit.

2.  V levém panelu klikněte na **Přehled** .

3.  Na stránce Přehled klikněte na tlačítko **obnovit**.

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

3.  Příkaz Restore Page se zobrazí s možností výběru mezi nejstarším bodem obnovení a vlastním bodem obnovení.

4.  Vyberte možnost **vlastní bod obnovení**.

5.  Vyberte datum a čas.

6.  V poli **obnovit do nového serveru** zadejte nový název serveru.

6.  V poli **obnovit do nového serveru** zadejte nový název serveru.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="Přehled zobrazení":::

7.  Klikněte na **OK**.

8.  Zobrazí se oznámení o zahájení operace obnovení.


## <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení
Po dokončení obnovení byste měli provést následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

- Pokud by nový server chtěl nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
- Aby se uživatelé mohli připojit, zajistěte, aby byla k dismístě vhodná pravidla VNet. Tato pravidla se nekopírují z původního serveru.
- Zajistěte, aby byla zavedena příslušná přihlášení a oprávnění na úrovni databáze.
- Nakonfigurujte výstrahy podle potřeby pro nově obnovený server.


## <a name="next-steps"></a>Další kroky
Další informace o [kontinuitě podnikových aplikací](concepts-business-continuity.md)
