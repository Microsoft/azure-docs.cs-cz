---
title: Sledování změn dat ve službě Azure SQL Edge (Preview)
description: Další informace o sledování změn a Change Data Capture ve službě Azure SQL Edge (Preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597265"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Sledování změn dat ve službě Azure SQL Edge (Preview)

Azure SQL Edge podporuje dvě SQL Server funkce, které sledují změny dat v databázi:[Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) a funkce [Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Tyto funkce umožňují aplikacím určit změny DML (operace vložení, aktualizace a odstranění), které byly provedeny v tabulkách uživatelů v databázi. Funkci Change Data Capture a Change Tracking lze povolit ve stejné databázi. nevyžadují se žádné zvláštní požadavky.

Možnost dotazování na data, která se změnila v databázi, je důležitým požadavkem, aby některé aplikace byly efektivní. Aby bylo možné určit změny dat, vývojáři aplikací musí obvykle implementovat vlastní metodu sledování ve svých aplikacích pomocí kombinace triggerů, sloupců časových razítek a dalších tabulek. Vytváření těchto aplikací obvykle zahrnuje značnou práci, která má být implementována, vede k aktualizacím schématu a často přináší vysoké nároky na výkon. V případě řešení IoT, kde je potřeba pravidelně přesouvat data z hraničního zařízení do cloudu nebo datového centra, může být sledování změn velmi užitečné. To umožňuje využít k rychlému a efektivnímu dotazování jenom rozdílové změny od poslední synchronizace a nahrajte tyto změny do cíle cloudu nebo datového centra. Další podrobnosti o výhodách používání Change Tracking a Change Data Capture najdete v tématu [použití služby Change Data Capture nebo Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Chcete-li pochopit rozdíly mezi funkcemi Change Tracking a Change Data Capture, přečtěte si [rozdíly mezi funkcemi pro funkci Change Data Capture a Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Change Data Capture

Podrobné informace o tom, jak funguje funkce Change Data Capture, najdete v článku [o funkci Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Pokud chcete zjistit, jak povolit nebo zakázat funkce Change Data Capture, přečtěte si téma [povolení a zakázání funkce Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) .

Pokud chcete spravovat a sledovat službu Change Data Capture, přečtěte si téma [Správa a sledování Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server) .

Informace o tom, jak dotazovat a pracovat se změněnými daty, najdete v článku [práce s daty změny](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server) .

## <a name="change-tracking"></a>Sledování změn

Podrobnosti o tom, jak Change Tracking funguje, najdete v článku [o Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Pokud chcete pochopit, jak povolit nebo zakázat Change Tracking, přečtěte si téma [povolení a zakázání Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Pokud chcete spravovat, monitorovat a spravovat Change Tracking, přečtěte si téma [Správa a monitorování Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Informace o tom, jak dotazovat a pracovat se změněnými daty, najdete v článku [práce s daty změny](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server) .

## <a name="temporal-tables"></a>Dočasné tabulky

Kromě podpory Change Tracking a změny funkcí pro zachycení dat v SQL Server Azure SQL Edge podporuje také funkci dočasné tabulky SQL Server. Dočasné tabulky (označované také jako dočasné tabulky se systémovou správou verzí) jako funkce databáze, která přináší integrovanou podporu pro poskytování informací o datech uložených v tabulce v libovolném časovém okamžiku, a ne jenom data, která jsou v aktuálním okamžiku v čase správná.

Dočasná tabulka se systémovou správou verzí je typ uživatelské tabulky, která je navržená tak, aby udržovala úplnou historii změn dat a umožňovala snadnou analýzu bodu v čase. Tento typ dočasné tabulky se označuje jako dočasná tabulka se systémovou správou verzí, protože doba platnosti pro každý řádek je spravovaná systémem (tj. databázový stroj).

Každá dočasná tabulka má dva explicitně definované sloupce, z nichž každý má datový typ datetime2. Tyto sloupce jsou označovány jako sloupce period. Tyto sloupce období jsou používány výhradně systémem k záznamu doby platnosti pro každý řádek při každé změně řádku.

Kromě těchto sloupců období obsahuje dočasná tabulka také odkaz na jinou tabulku se zrcadlovým schématem. Systém používá tuto tabulku k automatickému uložení předchozí verze řádku pokaždé, když se řádek v dočasné tabulce aktualizuje nebo odstraní. Tato další tabulka je označována jako tabulka historie, zatímco hlavní tabulka, která ukládá aktuální (skutečné) verze řádku, je označována jako aktuální tabulka nebo jednoduše jako dočasná tabulka. Během dočasného vytváření tabulek můžou uživatelé zadat existující tabulku historie (musí se jednat o schéma kompatibilní se schématem) nebo nechat tabulku System Create default History.

Další informace o dočasné tabulky najdete v tématu [dočasné tabulky](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) .

## <a name="see-also"></a>Viz také

- [Streamování dat ve službě Azure SQL Edge (Preview)](stream-data.md)
- [Machine Learning a AI s ONNX ve službě Azure SQL Edge (Preview)](onnx-overview.md)
- [Konfigurace replikace na Edge Azure SQL (Preview)](configure-replication.md)
- [Zálohování a obnovení databází v Azure SQL Edge (Preview)](backup-restore.md)



