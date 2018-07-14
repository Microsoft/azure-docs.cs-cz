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
ms.openlocfilehash: a69a739f36854cacd6b361ca2bd17d904e9c4c96
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38753648"
---
Zámky prostředků zabránit uživatelům ve vaší organizaci omylem odstranit nebo upravit důležitých prostředků. Na rozdíl od řízení přístupu na základě role používají zámky prostředků omezení pro všechny uživatele a role. 

Zámek můžete nastavit na úroveň **CanNotDelete** nebo **ReadOnly**. Na portálu se zobrazí zámků úrovně jako **odstranit** a **jen pro čtení** v uvedeném pořadí.

* **CanNotDelete** znamená, že oprávnění uživatelé stále může číst a upravit prostředek, ale jejich nelze odstranit prostředek. 
* **Jen pro čtení** znamená, že oprávnění uživatelé mohou číst prostředek, ale nemůže odstranit nebo aktualizovat prostředek. Použití tohoto uzamknout je podobná omezení všem oprávněným uživatelům oprávnění udělená **čtečky** role. 

> [!TIP]
> Buďte opatrní při použití **jen pro čtení** zámku. Některé operace, které se zdají být čtení operace ve skutečnosti vyžadují další akce. Například **jen pro čtení** výpis klíčů všem uživatelům zabrání zámek účtu úložiště. Seznam, který klíče operace probíhá prostřednictvím funkce požadavek POST, protože vrácený klíče jsou k dispozici pro operace zápisu. A **jen pro čtení** zámek na prostředek služby App Service brání Průzkumníka serveru Visual Studia v zobrazení souborů pro prostředek, protože danou interakci vyžaduje oprávnění k zápisu.

Při použití zámku v nadřazeném oboru dědí všechny prostředky v daném oboru stejný zámku. I prostředky, které přidáte později dědit Zámek z nadřazeného objektu. Nejvíc omezující zámku v dědičnosti má přednost.

Zámky Resource Manageru platí pouze pro operace, ke kterým dochází v rovině správy, který se skládá z operací odesílat `https://management.azure.com`. Zámky Neomezovat o prostředky procesu vlastní funkce. Změn prostředků jsou omezené, ale nejsou operace prostředků s omezeným přístupem. Například brání zámek ReadOnly u SQL Database můžete odstranit nebo upravit databázi. Nebude bránit vytváření, aktualizaci nebo odstranění dat v databázi. Data transakce jsou povolená, protože tyto operace se neodesílají na `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Kdo může vytvářet nebo odstranit zámky pro ve vaší organizaci
K vytvoření nebo odstranění zámků správy musíte mít přístup k akcím `Microsoft.Authorization/locks/*`. Z předdefinovaných rolí má tyto akce povolené pouze **vlastník** a **správce uživatelských přístupů**.
