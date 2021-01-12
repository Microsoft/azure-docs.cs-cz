---
title: Sledování změn dat ve službě Azure SQL Edge
description: Přečtěte si o sledování změn a Change Data Capture v Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108258"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Sledování změn dat ve službě Azure SQL Edge

Azure SQL Edge podporuje dvě SQL Server funkce, které sledují změny dat v databázi: [sledování změn](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) a [Change Data Capture](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Tyto funkce umožňují aplikacím určit změny jazyka úprav dat (operace vložení, aktualizace a odstranění), které byly provedeny v tabulkách uživatelů v databázi. Můžete povolit Change Data Capture a sledování změn ve stejné databázi. Nevyžadují se žádné zvláštní požadavky.

Možnost dotazování na data, která se změnila v databázi, je důležitým požadavkem, aby některé aplikace byly efektivní. Aby bylo možné určit změny dat, vývojáři aplikací musí obvykle implementovat vlastní metodu sledování ve svých aplikacích pomocí kombinace triggerů, sloupců časových razítek a dalších tabulek. Vytváření těchto aplikací obvykle zahrnuje značnou práci, která má být implementována, vede k aktualizacím schématu a často přináší vysoké nároky na výkon.

V případě řešení IoT, kdy potřebujete pravidelně přesouvat data z hraničního prostředí do cloudu nebo datového centra, může být sledování změn velmi užitečné. Uživatelé můžou rychle a efektivně dotazovat se jenom na změny od poslední synchronizace a nahrajte tyto změny do cloudu nebo do cíle Datacenter. Další podrobnosti najdete v tématu [výhody použití Change Data Capture nebo sledování změn](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Tyto dvě funkce nejsou stejné. Další informace najdete v tématu [rozdíly mezi funkcemi Change Data Capture a sledování změn](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking) .

## <a name="change-data-capture"></a>Change data capture

Podrobnosti o tom, jak tato funkce funguje, najdete v tématu [o Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Informace o tom, jak tuto funkci povolit nebo zakázat, najdete v tématu [povolení a zakázání Change Data Capture](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Pokud chcete tuto funkci spravovat a monitorovat, přečtěte si téma [Správa a sledování Change Data Capture](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Informace o tom, jak pomocí změněných dat dotazovat a pracovat s nimi, najdete v tématu [práce s daty změny](/sql/relational-databases/track-changes/work-with-change-data-sql-server).

> [!NOTE]
> Funkce Change Data Capture, které jsou závislé na CLR, nejsou podporovány na Edge SQL Azure.

## <a name="change-tracking"></a>Sledování změn

Podrobné informace o tom, jak tato funkce funguje, najdete v tématu [o sledování změn](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Informace o tom, jak tuto funkci povolit nebo zakázat, najdete v tématu [povolení a zakázání sledování změn](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Pokud chcete tuto funkci spravovat, sledovat a spravovat, přečtěte si téma [Správa sledování změn a](/sql/relational-databases/track-changes/manage-change-tracking-sql-server)sledování.

Informace o tom, jak pomocí změněných dat dotazovat a pracovat s nimi, najdete v tématu [práce s daty změny](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Dočasné tabulky

Azure SQL Edge podporuje také funkci dočasné tabulky SQL Server. Tato funkce (označovaná také jako *dočasné tabulky se systémovou správou verzí*) přináší integrovanou podporu pro poskytování informací o datech uložených v tabulce v libovolném časovém okamžiku. Tato funkce neposkytuje jenom informace o datech, která jsou v aktuálním okamžiku v čase správná.

Dočasná tabulka se systémovou správou verzí je typ uživatelské tabulky, která je navržená tak, aby udržovala úplnou historii změn dat a umožňovala snadnou analýzu určitého bodu v čase. Tento typ dočasné tabulky je označován jako dočasná tabulka se systémovou správou verzí, protože období platnosti pro každý řádek je spravováno systémem (tj. databázový stroj).

Každá dočasná tabulka má dva explicitně definované sloupce, z nichž každý má `datetime2` datový typ. Tyto sloupce jsou označovány jako sloupce *period* . Systém použije tyto sloupce periody výhradně k zaznamenání intervalu platnosti pro každý řádek při každé změně řádku.

Kromě těchto sloupců období obsahuje dočasná tabulka také odkaz na jinou tabulku se zrcadlovým schématem. Systém používá tuto tabulku k automatickému uložení předchozí verze řádku pokaždé, když se řádek v dočasné tabulce aktualizuje nebo odstraní. Tato další tabulka je označována jako tabulka *Historie* , zatímco hlavní tabulka, která ukládá aktuální (skutečné) verze řádku, je označována jako *aktuální* tabulka nebo jednoduše jako dočasná tabulka. Během vytváření dočasné tabulky můžou uživatelé zadat existující tabulku historie (musí být kompatibilní se schématem), nebo nechat systém vytvořit výchozí tabulku historie.

Další informace najdete v tématu [dočasné tabulky](/sql/relational-databases/tables/temporal-tables).

## <a name="next-steps"></a>Další kroky

- [Streamování dat ve službě Azure SQL Edge ](stream-data.md)
- [Machine Learning a AI s ONNX ve službě Azure SQL Edge ](onnx-overview.md)
- [Konfigurace replikace na Edge Azure SQL](configure-replication.md)
- [Zálohování a obnovení databází v Azure SQL Edge](backup-restore.md)
