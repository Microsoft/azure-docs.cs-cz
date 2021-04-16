---
title: Zálohování a obnovení – Citus (-Scale) – Azure Database for PostgreSQL
description: Ochrana dat před náhodným poškozením nebo odstraněním
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520169"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Zálohování a obnovení v Azure Database for PostgreSQL – Citus (škálování)

Azure Database for PostgreSQL – Citus () automaticky vytváří zálohy každého uzlu a ukládá je do místně redundantního úložiště. Zálohy se dají použít k obnovení skupiny serverů Citus (Your Scale) na určitou dobu.
Zálohování a obnovení jsou základní součástí jakékoli strategie kontinuity podnikových procesů, protože chrání data před náhodným poškozením nebo odstraněním.

## <a name="backups"></a>Zálohování

Alespoň jednou denně Azure Database for PostgreSQL přebírá snímky datových souborů a transakčního protokolu databáze snímků. Zálohy umožňují obnovit server do libovolného bodu v čase v rámci doby uchování. (Doba uchovávání dat je v současnosti 35 dní pro všechny skupiny serverů.) Všechny zálohy se šifrují pomocí šifrování AES 256-bit.

V oblastech Azure, které podporují zóny dostupnosti, se záložní snímky ukládají ve třech zónách dostupnosti. Pokud je aspoň jedna zóna dostupnosti online, Skupina serverů Citus (škálování) je obnovitelné.

Záložní soubory se nedají exportovat. Dají se použít jenom pro operace obnovení v Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Aktuální ceny za úložiště zálohování najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)Azure Database for PostgreSQL – Citus (škálování na úrovni cloudu).

## <a name="restore"></a>Obnovení

Skupinu serverů Citus () můžete obnovit do libovolného bodu v čase během posledních 35 dnů.  Obnovení k bodu v čase je užitečné ve více scénářích. Například když uživatel omylem odstraní data, zahodí důležitou tabulku nebo databázi, nebo když aplikace náhodně přepíše správná data špatnými daty.

> [!IMPORTANT]
> Odstraněné skupiny serverů Citus) se nedají obnovit. Pokud odstraníte skupinu serverů, odstraní se všechny uzly patřící do skupiny serverů a nebude možné je obnovit. Pro ochranu prostředků skupiny serverů, po nasazení, před náhodným odstraněním nebo neočekávaným změnám můžou správci využít [zámky pro správu](../azure-resource-manager/management/lock-resources.md).

Proces obnovení vytvoří novou skupinu serverů ve stejné oblasti Azure, předplatném a skupině prostředků jako původní. Skupina serverů má původní konfiguraci: stejný počet uzlů, počet virtuální jádra, velikost úložiště, uživatelské role, verze PostgreSQL a verze rozšíření Citus.

Nastavení brány firewall a parametry serveru PostgreSQL nejsou zachované z původní skupiny serverů, obnoví se výchozí hodnoty. Brána firewall zabrání všem připojením. Tato nastavení budete muset po obnovení upravit ručně. Obecné informace najdete v našem seznamu navrhovaných [úloh po obnovení](howto-hyperscale-restore-portal.md#post-restore-tasks).

## <a name="next-steps"></a>Další kroky

* Přečtěte si postup [obnovení skupiny serverů](howto-hyperscale-restore-portal.md) v Azure Portal.
* Přečtěte si o [zónách dostupnosti Azure](../availability-zones/az-overview.md).
