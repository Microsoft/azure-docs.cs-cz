---
title: Funkce ve verzi Preview v Azure Database for PostgreSQL – Citus (škálování)
description: Aktualizovaný seznam funkcí, které jsou aktuálně ve verzi Preview
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 775785e1b5130499d69b269e72f5c774e9e5f3f9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024058"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>Funkce ve verzi Preview pro PostgreSQL – škálovatelné (Citus)

Azure Database for PostgreSQL – Citus () nabízí verze Preview pro nevydané funkce. Verze Preview se poskytují bez smlouvy o úrovni služeb a nedoporučují se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.  Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.

## <a name="features-currently-in-preview"></a>Funkce, které jsou aktuálně ve verzi Preview

Tady jsou funkce, které jsou aktuálně dostupné pro verzi Preview:

* **[Sloupcové úložiště](concepts-hyperscale-columnar.md)**.
  Ukládat sloupce vybraných tabulek (nikoli řádky) na disk souvisle. Podporuje kompresi na disku. Vhodné pro úlohy pro analytické a datové sklady.
* **[PostgreSQL 12 a 13](concepts-hyperscale-versions.md)**.
  Ve skupině serverů použijte nejnovější verzi databáze.
* **[Úroveň Basic](concepts-hyperscale-tiers.md)** Spusťte skupinu serverů pouze pomocí uzlu koordinátora a žádného pracovního uzlu. Ekonomický způsob, jak provádět počáteční testování a vývoj a zpracovávat malé produkční úlohy.
* **[Čtení replik](howto-hyperscale-read-replicas-portal.md)** (pouze v současnosti stejné oblasti). Jakékoli změny, ke kterým dojde ve skupině primárního serveru, se projeví ve své replice a dotazy na repliku nezpůsobí žádné další zatížení originálu.
  Repliky jsou užitečným nástrojem pro zlepšení výkonu úloh jen pro čtení.
* **[Spravované PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Připojení Pooler, které umožňuje mnoha klientům připojit se ke skupině serverů najednou a zároveň omezit počet aktivních připojení. Splňuje požadavky na připojení a udržuje plynulý chod uzlu koordinátora.
* **[PgAudit](concepts-hyperscale-audit.md)**. Poskytuje podrobné protokolování relací a objektů auditu prostřednictvím standardního protokolovacího zařízení PostgreSQL. Vytváří protokoly auditu, které jsou nutné pro splnění určitých státních nebo finančních auditů certifikace nebo ISO.

### <a name="available-regions-for-preview-features"></a>Dostupné oblasti pro funkce ve verzi Preview

Rozšíření pgAudit je k dispozici ve všech [oblastech podporovaných škálovatelným škálováním (Citus)](concepts-hyperscale-configuration-options.md#regions).
Ostatní funkce verze Preview jsou k dispozici pouze v **východní USA** .

## <a name="does-my-server-group-have-access-to-preview-features"></a>Má má skupina serverů přístup k funkcím verze Preview?

Pokud chcete zjistit, jestli má skupina serverů Citus () ve verzi Preview povolených funkcích Preview, přejděte na stránku **Přehled** skupiny serverů v Azure Portal.
Pokud se zobrazí vrstva vlastností **: Basic (Preview)** nebo **úroveň: Standard (Preview)** , má skupina serverů přístup k funkcím verze Preview.

### <a name="how-to-get-access"></a>Jak získat přístup

Když vytváříte novou skupinu serverů Citus, zaškrtněte políčko **Povolit funkce ve verzi Preview.**

## <a name="contact-us"></a>Kontaktujte nás

Informujte nás o vašich zkušenostech s využitím funkcí ve verzi Preview, které [požadují Azure DB pro PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
(Tato e-mailová adresa není kanálem technické podpory. V případě technických problémů otevřete [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).)
