---
title: Obnovení-Citus)-Azure Database for PostgreSQL-Azure Portal
description: Tento článek popisuje, jak provádět operace obnovení ve Azure Database for PostgreSQL – Citus (škálovatelné) prostřednictvím Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: aebfeed055fad7c1108620ab494236640285aa1e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495024"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Obnovení skupiny serverů Citus () na základě bodu v čase

Tento článek poskytuje podrobné postupy pro provádění [obnovy v určitém časovém intervalu](concepts-hyperscale-backup.md#point-in-time-restore-pitr) pro skupinu serverů Citus () pomocí záloh. V rámci doby uchování můžete obnovit buď na nejstarší zálohu, nebo na vlastní bod obnovení.

## <a name="restoring-to-the-earliest-restore-point"></a>Obnovení na nejstarší bod obnovení

Postupujte podle těchto kroků a obnovte skupinu serverů Citus () na nejstarší existující zálohu.

1.  V [Azure Portal](https://portal.azure.com/)vyberte skupinu serverů, kterou chcete obnovit.

2.  V levém panelu klikněte na **Přehled** a pak klikněte na **obnovit**.

    > [!IMPORTANT]
    > Pokud pro skupinu serverů ještě není k dispozici tlačítko pro **obnovení** , otevřete prosím žádost o podporu Azure.

3.  Stránka obnovit vás vyzve k výběru mezi **nejstaršími** a **vlastním** bodem obnovení a zobrazí nejstarší datum.

4.  Vyberte **nejstarší bod obnovení**.

5.  V poli **obnovit do nového serveru** zadejte nový název skupiny serverů. Ostatní pole (předplatné, skupina prostředků a umístění) se zobrazí, ale nelze je upravovat.

6.  Klikněte na **OK**.

7.  Zobrazí se oznámení o zahájení operace obnovení.

Nakonec postupujte podle [úloh po obnovení](#post-restore-tasks).

## <a name="restoring-to-a-custom-restore-point"></a>Obnovení do vlastního bodu obnovení

Postupujte podle těchto kroků a obnovte skupinu serverů Citus () na datum a čas svého výběru.

1.  V [Azure Portal](https://portal.azure.com/)vyberte skupinu serverů, kterou chcete obnovit.

2.  Klikněte na **Přehled** na levém panelu a pak klikněte na **obnovit** .

    > [!IMPORTANT]
    > Pokud pro skupinu serverů ještě není k dispozici tlačítko pro **obnovení** , otevřete prosím žádost o podporu Azure.

3.  Stránka obnovit vás vyzve k výběru mezi **nejstaršími** a **vlastním** bodem obnovení a zobrazí nejstarší datum.

4.  Vyberte možnost **vlastní bod obnovení**.

5.  Vyberte datum a čas **bodu obnovení (UTC)** a zadejte nový název skupiny serverů v poli **obnovit do nového serveru** . Ostatní pole (předplatné, skupina prostředků a umístění) se zobrazí, ale nelze je upravovat.
 
6.  Klikněte na **OK**.

7.  Zobrazí se oznámení o zahájení operace obnovení.

Nakonec postupujte podle [úloh po obnovení](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Post-restore tasks

Po obnovení byste měli provést následující kroky, aby se uživatelé a aplikace mohli zálohovat a spustit:

* Pokud má nový server nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
* Zajistěte, aby se uživatelé mohli připojit na příslušné bráně firewall na úrovni serveru. Tato pravidla se nekopírují z původní skupiny serverů.
* Podle potřeby upravte parametry serveru PostgreSQL. Parametry se nekopírují z původní skupiny serverů.
* Zajistěte, aby byla zavedena příslušná přihlášení a oprávnění na úrovni databáze.
* Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [zálohování a obnovení](concepts-hyperscale-backup.md) v Citus (Scale).
* Nastavte [navrhované výstrahy](./howto-hyperscale-alert-on-metric.md#suggested-alerts) na skupiny serverů Citus ().
