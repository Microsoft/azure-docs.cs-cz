---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402493"
---
Uzamčení prostředků zabránit uživatelům ve vaší organizaci neúmyslnému odstranění nebo úprava důležitých prostředků. Na rozdíl od řízení přístupu na základě rolí uzamčení prostředků platí omezení ve všech uživatelů a rolí. 

Můžete nastavit zámek na úrovni **CanNotDelete** nebo **jen pro čtení**. Na portálu, úrovně zámky zobrazují jako **odstranit** a **jen pro čtení** v uvedeném pořadí.

* **CanNotDelete** znamená Autorizovaní uživatelé stále mohou číst a upravovat prostředek, ale jejich nelze odstranit prostředek. 
* **Jen pro čtení** znamená Autorizovaní uživatelé mohou číst prostředek, ale jejich nelze odstranit nebo aktualizovat prostředek. Je podobná omezení všem oprávněným uživatelům oprávnění udělují použití této zámku **čtečky** role. 

> [!TIP]
> Buďte opatrní při použití **jen pro čtení** zámku. Některé operace, které vypadají podobně jako čtení operace ve skutečnosti vyžadují další akce. Například **jen pro čtení** výpis klíčů všem uživatelům zabrání zámku na účet úložiště. Seznam klíčů operaci je zpracováván prostřednictvím požadavek POST, protože vrácený klíče jsou k dispozici pro operace zápisu. A **jen pro čtení** zámku na prostředek aplikace služby zabrání zobrazení souborů pro daný prostředek, protože interakce vyžaduje oprávnění k zápisu Průzkumníka serveru Visual Studia.

Když použijete zámku v nadřazeném oboru, zdědí všechny prostředky v rámci tohoto oboru stejné zámek. I prostředky, které přidáte později zámek dědí z nadřazeného objektu. Nejvíc omezující zámek v dědičnosti přednost.

Zámky správce prostředků se vztahují pouze na operace, které dojít v rovině řízení, sestávající operací posílá `https://management.azure.com`. Zámky nemáte omezit, jak zpracovat prostředky vlastní funkce. Změny prostředku jsou s omezeným přístupem, ale nejsou operací s prostředky s omezeným přístupem. Například jen pro čtení zámku v databázi SQL brání odstranění nebo úprava databáze. Nezabrání vytváření, aktualizaci nebo odstranění dat v databázi. Data transakce jsou povolená, protože tyto operace se neodesílají na `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Kdo může vytvářet nebo odstraňovat zámky ve vaší organizaci
Vytvořit nebo odstranit zámky správy, musíte mít přístup k `Microsoft.Authorization/locks/*` akce. Z předdefinovaných rolí pouze **vlastníka** a **správce přístupu uživatelů** mají tyto akce.
