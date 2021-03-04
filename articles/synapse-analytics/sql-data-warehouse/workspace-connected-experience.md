---
title: Povolení funkcí synapse pracovního prostoru ve vyhrazeném fondu SQL (dřív SQL DW)
description: Tento dokument popisuje, jak může zákazník získat přístup k existující samostatné instanci SQL DW v pracovním prostoru a použít ji.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694610"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Povolení funkcí synapse pracovního prostoru v existujícím vyhrazeném fondu SQL (dřív SQL DW)

Všichni zákazníci SQL Data Warehouse mají teď přístup k existující vyhrazené instanci SQL fondu (dřív SQL DW) pomocí synapse studia a pracovního prostoru, aniž by to ovlivnilo automatizaci, připojení nebo nástroje. V tomto článku se dozvíte, jak existující zákazník Azure synapse Analytics může vytvořit a rozšířit svoje existující analytické řešení díky využití nových funkcí bohatých na funkce, které jsou teď dostupné prostřednictvím pracovního prostoru synapse a studia.   

## <a name="experience"></a>Prostředí
 
Teď, když je synapse pracovní prostor v GA, je k dispozici nová funkce na portálu datového skladu s přehledem, která umožňuje vytvořit pracovní prostor synapse pro existující vyhrazené instance fondu SQL (dříve SQL DW). Tato nová funkce vám umožní připojit logický Server, který je hostitelem stávajících instancí datového skladu, do nového pracovního prostoru synapse. Připojení zajistí, že všechny datové sklady hostované na tomto serveru budou přístupné z pracovního prostoru a studia a dají se použít společně s partnerskými službami synapse (bez serveru SQL, fond Apache Spark a ADF). Po dokončení kroků zřizování a navázání připojení k nově vytvořenému pracovnímu prostoru můžete začít pracovat s prostředky a používat je.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Pracovní prostor připojené synapse":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Použití funkcí synapse v pracovním prostoru a studiu k přístupu a používání vyhrazeného fondu SQL (dřív SQL DW)
 
Následující informace budou platit při používání vyhrazené databáze SQL DW (dříve SQL DW) s povolenými funkcemi synapse Workspace: 
- **Schopnosti SQL** Až bude funkce pracovního prostoru synapse povolená, všechny možnosti SQL zůstanou v logickém SQL serveru. Přístup k serveru přes poskytovatele prostředků SQL bude i nadále možné po povolení pracovního prostoru. Všechny funkce správy lze iniciovat prostřednictvím pracovního prostoru a operace bude provedena na logickém SQL Server hostujících vaše fondy SQL. Pokud je pracovní prostor povolený, žádné existující automatizace, nástroje nebo připojení se nepřerušily nebo přeruší.  
- **Přesunout prostředek**  Při zahájení přesunu prostředků na serveru s povolenou funkcí pracovní prostor synapse dojde k přerušení propojení mezi serverem a pracovním prostorem a nebudete už mít přístup k existujícím vyhrazeným instancím fondu SQL (dříve SQL DW) z pracovního prostoru. Aby se zajistilo, že se připojení zachová, doporučuje se, aby oba prostředky zůstaly v rámci stejného předplatného a skupiny prostředků. 
- **Monitorování** Žádosti SQL odeslané prostřednictvím synapse studia v pracovním prostoru vyhrazeného fondu SQL (dříve SQL DW) se dají zobrazit v centru monitorování. Pro všechny ostatní monitorovací aktivity můžete přejít na Azure Portal vyhrazeného fondu SQL (dříve SQL DW). 
- Řízení **zabezpečení** a **přístupu** jak je uvedeno výše, všechny funkce správy pro SQL Server a vyhrazené fondy SQL (dříve SQL DW) se budou i nadále nacházet na logickém SQL serveru. Mezi tyto funkce patří Správa pravidel brány firewall, nastavení správce Azure AD serveru a veškeré řízení přístupu k datům ve vyhrazeném fondu SQL (dřív SQL DW). Aby bylo zajištěno, že vyhrazený fond SQL (dříve SQL DW) je dostupný a dá se použít v pracovním prostoru synapse, je potřeba provést následující kroky. Členství v rolích pracovního prostoru neuděluje uživatelům oprávnění k datům ve vyhrazených instancích fondu SQL (dříve SQL DW). Použijte normální zásady [ověřování SQL](sql-data-warehouse-authentication.md) , abyste měli jistotu, že uživatelé mají přístup k vyhrazeným instancím fondu SQL (dříve SQL DW) na logickém serveru. Pokud má vyhrazený server hostitele SQL (dřív SQL DW) přiřazenou spravovanou identitu, bude název této spravované identity stejný jako název spravované identity v pracovním prostoru, který se automaticky vytvoří, aby podporoval partnerské služby pracovního prostoru (např. kanály ADF).  V připojeném scénáři můžou existovat dvě spravované identity se stejným názvem. Spravované identity je možné odlišit pomocí jejich ID objektů Azure AD, funkce k vytváření uživatelů SQL pomocí ID objektů budou brzy dostupné.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > V části připojený pracovní prostor synapse Studio se zobrazí názvy vyhrazených fondů na základě oprávnění, která má uživatel v Azure. Objekty v rámci fondů nebudou dostupné, pokud uživatel nemá oprávnění pro fondy SQL. 

- **Zabezpečení sítě** Pokud je pracovní prostor synapse, který jste povolili ve stávajícím vyhrazeném fondu SQL (dřív ve službě SQL DW), povolený pro ochranu infiltrování dat. Vytvořte připojení spravovaného privátního koncového bodu z pracovního prostoru k logickému SQL serveru. Schvalte požadavek na připojení privátního koncového bodu, aby bylo možné povolit komunikaci mezi serverem a pracovním prostorem.
- **Studio** Fondy SQL v **datových rozbočovačích** s povoleným vyhrazeným fondem SQL (dříve SQL DW) se dají identifikovat pomocí tipu nástroje v datovém centru. 
- **Vytváření nového vyhrazeného fondu SQL (dřív SQL DW)** Nové vyhrazené fondy SQL se dají vytvořit přes pracovní prostor synapse a Studio po povolení funkce pracovního prostoru a zřízení nového fondu na logickém SQL serveru. Nové prostředky se zobrazí na portálu a Studio po dokončení zřizování.      

## <a name="next-steps"></a>Další kroky
Povolení [funkcí pracovního prostoru synapse](workspace-connected-create.md) ve stávajícím vyhrazeném fondu SQL (dřív SQL DW)
