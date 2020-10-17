---
title: Správa instance PostgreSQL pomocí Azure Data Studio
description: Správa instance PostgreSQL pomocí Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a073df78adbf0104d81a96eb92ea427f60e53b01
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151836"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Správa PostgreSQL skupiny serverů s povoleným rozšířením Azure ARC pomocí Azure Data Studio


Tento článek popisuje, jak:
- Spravujte PostgreSQL instance pomocí zobrazení řídicích panelů, jako je přehled, připojovací řetězce, vlastnosti, Resource Health...
- práce s Vašimi daty a schématy

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Předpoklady

- [Instalace azdata, Azure Data Studio a Azure CLI](install-client-tools.md)
- Instalace v Azure Data Studio rozhraní příkazového **řádku Azure Data CLI** a rozšíření **Azure ARC** a **PostgreSQL**
- Vytvoření [kontroleru dat ARC Azure](create-data-controller-using-azdata.md)
- Spustit Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Připojení k řadiči dat ARC Azure

V Azure Data Studio rozbalte uzel **řadiče Azure ARC** a vyberte tlačítko **připojit kontrolér** :

Zadejte informace o připojení ke svému řadiči dat Azure:

- **Adresa URL kontroleru:**

    Adresa URL pro připojení k řadiči v Kubernetes. Zadáno ve formě `https://<IP_address_of_the_controller>:<Kubernetes_port.` například:

    ```console
    https://12.345.67.890:30080
    ```
- **Uživatelské jméno:**

    Název uživatelského účtu, který používáte pro připojení k řadiči. Použijte název, který obvykle používáte při spuštění `azdata login` . Nejedná se o jméno uživatele PostgreSQL, kterého používáte pro připojení k databázovému stroji PostgreSQL, obvykle od psql.
- **Heslo:** Heslo uživatelského účtu, který používáte pro připojení k řadiči


Azure Data Studio zobrazuje váš řadič dat ARC. Rozbalte ho a zobrazí seznam PostgreSQL instancí, které spravuje.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Správa skupin serverů s povoleným PostgreSQLm rozšířením Azure ARC

Klikněte pravým tlačítkem na instanci PostgreSQL, kterou chcete spravovat, a vyberte [spravovat].

Zobrazení řídicího panelu PostgreSQL:

Tato funkce obsahuje několik řídicích panelů uvedených na levé straně tohoto podokna:

- **Přehled:** Zobrazí souhrnné informace o vaší instanci, jako je název, uživatelské jméno správce PostgreSQL, ID předplatného Azure, konfigurace, verze databázového stroje, koncové body pro Grafana a Kibana...
- **Připojovací řetězce:** Zobrazí různé připojovací řetězce, které možná budete muset připojit k instanci PostgreSQL, jako je psql, Node.js, PHP, Ruby...
- **Diagnostika a řešení problémů:** Je cílová stránka, kde najdete různé prostředky, které vám pomůžou vyřešit potíže s instancí při rozšiřování poznámkových bloků pro řešení potíží.
- **Nová žádost o podporu:** Je cílová stránka, ze které budete moci požádat o pomoc od našich služeb podpory počínaje oznámením Public Preview.

## <a name="work-with-your-data-and-schema"></a>Práce s Vašimi daty a schématy

Na levé straně okna Azure Data Studio rozbalte uzel **servery**:

A vyberte [Přidat připojení] a vyplňte podrobnosti o připojení ke své instanci PostgreSQL:
- **Typ připojení:** PostgreSQL
- **Název serveru:** zadejte název vaší instance PostgreSQL. Příklad: postgres01
- **Typ ověřování:** Zadáno
- **Uživatelské jméno:** můžete použít například standardní nebo výchozí uživatelské jméno správce PostgreSQL. Všimněte si, že v tomto poli se rozlišují malá a velká písmena.
- **Heslo:** v připojovacím řetězci psql najdete heslo k uživatelskému jménu PostgreSQL ve výstupu `azdata postgres server endpoint -n postgres01` příkazu.
- **Název databáze:** nastavte název databáze, ke které se chcete připojit. Můžete nechat nastavené __výchozí hodnoty__ .
- **Skupina serverů:** můžete nechat nastavenou __výchozí__ hodnotu.
- **Název (volitelné):** můžete nechat prázdné.
- **Upřesnit**
    - **IP adresa hostitele:** je veřejná IP adresa clusteru Kubernetes.
    - **Port:** je port, na kterém vaše instance PostgreSQL naslouchá. Tento port najdete na konci připojovacího řetězce psql ve výstupu `azdata postgres server endpoint -n postgres01` příkazu. Nejedná se o port 30080, na kterém naslouchá Kubernetes a který jste zadali při připojování k řadiči dat Azure v Azure Data Studio.
    - **Další parametry:** Měly by být samostatné, můžete živě používat výchozí nebo prázdné hodnoty, se kterými se zobrazují.

Pokud se chcete připojit k serveru, vyberte **[OK] a [Connect]** .

Po připojení je k dispozici několik prostředí:
- **Nový dotaz**
- **Nový Poznámkový blok**
- **Rozbalí zobrazení serveru a procházejte/Pracujte na objektech v databázi.**
- **...**

## <a name="next-step"></a>Další krok
[Monitorování skupiny serverů](monitor-grafana-kibana.md)
