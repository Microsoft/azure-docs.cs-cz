---
title: Obnovení vynechaného serveru Azure Database for PostgreSQL
description: Tento článek popisuje, jak obnovit vyřazený server v Azure Database for PostgreSQL pomocí Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 81764294cc29ad74d5a77f2055f10498d69b59e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342993"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Obnovení vynechaného serveru Azure Database for PostgreSQL

Když je server vyřazený, zálohování na databázovém serveru se může uchovávat až pět dní v rámci služby. Záloha databáze je dostupná a obnovená jenom z předplatného Azure, ve kterém je server původně umístěný. Pomocí následujících doporučených kroků můžete obnovit vyřazený prostředek PostgreSQL serveru do 5 dní od okamžiku odstranění serveru. Doporučené kroky budou fungovat pouze v případě, že se záloha serveru neodstranila ze systému a je stále k dispozici. 

## <a name="pre-requisites"></a>Požadavky
Chcete-li obnovit vyřazený Azure Database for PostgreSQL Server, budete potřebovat následující:
- Název předplatného Azure, který hostuje původní server
- Umístění, kde byl server vytvořen

## <a name="steps-to-restore"></a>Postup obnovení

1. Přejděte na [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Vyberte službu **Azure monitor** a pak vyberte **Protokol aktivit**.

2. V protokolu aktivit klikněte na **Přidat filtr** , jak je znázorněno, a pro následující filtry nastavte následující filtry.

    - **Předplatné** = vaše předplatné hostující odstraněný Server
    - **Typ prostředku** = Azure Database for PostgreSQL servery (Microsoft. DBforPostgreSQL/servery)
    - **Operace** = odstranění serveru PostgreSQL (Microsoft. DBforPostgreSQL/servery/Delete)
 
    ![Protokol aktivit filtrovaný pro odstranění operace serveru PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Vyberte událost **odstranění serveru PostgreSQL** a pak vyberte **kartu JSON**. Zkopírujte `resourceId` atributy a `submissionTimestamp` ve výstupu JSON. ResourceId má následující formát: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Přejděte na stránku PostgreSQL [vytvořit Server REST API](/rest/api/PostgreSQL/servers/create) a vyberte kartu **vyzkoušet,** která se zvýrazní zeleně. Přihlaste se pomocí svého účtu Azure.

 5. Zadejte **resourceGroupName** , **servername** (název odstraněného serveru) a **SUBSCRIPTIONID** vlastnosti na základě hodnoty JSON atributu ResourceID zaznamenané v předchozím kroku 3. Vlastnost verze rozhraní API je předem vyplněná a může být ponechána tak, jak je znázorněno na následujícím obrázku.

    ![Vytvoření serveru pomocí REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. V části tělo žádosti se posuňte dolů a vložte následující text nahrazující "umístění vyřazeného serveru", "submissionTimestamp" a "resourceId". Pro "restorePointInTime" zadejte hodnotu "submissionTimestamp" mínus **15 minut** , abyste se ujistili, že příkaz nechybí.
    ```json
        {
          "location": "Dropped Server Location",  
          "properties": 
              {
                  "restorePointInTime": "submissionTimestamp - 15 minutes",
                  "createMode": "PointInTimeRestore",
                  "sourceServerId": "resourceId"
            }
        }
    ```
    Pokud je aktuální čas například 2020-11-02T23:59:59.0000000 Z, doporučujeme minimálně 15 minut před bodem obnovení v čase 2020-11-02T23:44:59.0000000 Z.
    > [!Important]
    > Po zahození serveru je časový limit pět dní. Po pěti dnech se očekává chyba, protože se nepovedlo najít záložní soubor.
    
7. Pokud se zobrazí kód odpovědi 201 nebo 202, požadavek na obnovení se úspěšně odeslal. 

    Vytvoření serveru může trvat dlouho v závislosti na velikosti databáze a výpočetních prostředcích, které jsou zřízené na původním serveru. Stav obnovení se dá monitorovat z protokolu aktivit filtrováním pro 
   - **Předplatné** = vaše předplatné
   - **Typ prostředku** = Azure Database for PostgreSQL servery (Microsoft. DBforPostgreSQL/servery) 
   - **Operace** = aktualizace serveru PostgreSQL vytvořit

## <a name="next-steps"></a>Další kroky
- Pokud se pokoušíte obnovit server během pěti dnů a stále se zobrazí chyba po přesném provedení výše popsaného postupu, otevřete incident podpory pro pomoc. Pokud se pokoušíte obnovit vyřazený server po pěti dnech, je očekávána chyba, protože záložní soubor nelze nalézt. V tomto scénáři neotevírejte lístek podpory. Tým podpory nemůže poskytnout žádnou pomoc, pokud je záloha ze systému odstraněna. 
- Abyste zabránili nechtěnému odstranění serverů, důrazně doporučujeme použít [zámky prostředků](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
