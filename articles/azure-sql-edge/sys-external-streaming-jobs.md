---
title: sys.external_streaming_jobs (Transact-SQL) – Azure SQL Edge
description: Další informace o použití sys.external_streaming_jobs ve službě Azure SQL Edge
keywords: sys.external_streaming_jobs, Edge SQL
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900335"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Vrátí řádek pro každou externí úlohu streamování vytvořenou v rámci oboru databáze.

|Název sloupce|Datový typ|Popis|  
|-----------------|---------------|-----------------|
|**Jméno**|**musí**|Název datového proudu. Je v rámci databáze jedinečný.|
|**object_id**|**int**|identifikační číslo objektu pro objekt datového proudu. Je v rámci databáze jedinečný.|
|**principal_id**|**int**|ID objektu zabezpečení, který vlastní toto sestavení|
|**schema_id**|**int**| ID schématu, které obsahuje objekt|
|**parent_object_id**|**id**| identifikační číslo objektu pro nadřazený objekt tohoto datového proudu. V aktuální implementaci je tato hodnota vždycky null.|
|**textový**|**char (2)**|Typ objektu. U objektů Stream je typ vždycky "EJ".|
|**type_desc**|**nvarchar (60)**| Popis typu objektu U objektů Stream je typ vždycky EXTERNAL_STREAMING_JOB.|
|**create_date**|**hodnotu**| Datum, kdy byl objekt vytvořen.|
|**modify_date**|**hodnotu**| V aktuální implementaci je tato hodnota stejná jako create_date objektu Stream. |
|**is_ms_shipped**|**bit**| Objekt vytvořený interní komponentou.|  
|**is_published**|**bit**| Objekt je publikovaný.|  
|**is_schema_published**|**bit**|Je Publikováno pouze schéma objektu.|
|**uses_ansi_nulls**|**bit**| Objekt Stream byl vytvořen pomocí možnosti SET ANSI_NULLS Database ON.|
|**vydá**|**varchar(max)**| Text dotazu Stream Analytics pro úlohu streamování Další informace najdete v tématu [sp_create_streaming_job](overview.md) |
|**stav**|**int**| Aktuální stav úlohy streamování. Možné hodnoty jsou <br /><br /> **Vytvořeno** = 0. Úloha streamování se vytvořila, ale ještě se nespustila. <br /><br /> **Začíná** = 1. Úloha streamování je ve fázi zahájení. <br /><br /> **Neúspěšné** = 6. Úloha streamování se nezdařila. To je obvykle indikace závažné chyby během zpracování. <br /><br /> **Zastaveno** = 4. Úloha streamování se zastavila. <br /><br /> **Nečinné** = 7. Úloha streamování je spuštěná, ale není k dispozici žádný vstup ke zpracování. <br /><br /> **Zpracovává** se = 8. Úloha streamování je spuštěná a zpracovává vstupy. Tento stav označuje stav v pořádku pro úlohu streamování. <br /><br /> **Snížený výkon** = 9. Úloha streamování je spuštěná, ale během zpracování vstupu došlo k některému nezávažnému vstupu/výstupu nebo chybě serializace/zrušení serializace. Vstupní úloha bude pokračovat v běhu, ale zahodí vstupy, které nastanou chyby.|

## <a name="permissions"></a>Oprávnění

Viditelnost metadat v zobrazeních katalogu je omezená na to, že uživatel buď vlastní, nebo ke kterému má uživatel udělené oprávnění. Další informace najdete v tématu [Konfigurace viditelnosti metadat](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Viz také

- [Zobrazení katalogu T-SQL streamování](overview.md)
- [Zobrazení katalogu (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systémová zobrazení (Transact-SQL)](/sql/t-sql/language-reference/)

