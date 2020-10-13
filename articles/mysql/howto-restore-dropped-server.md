---
title: Obnovení vynechaného serveru Azure Database for MySQL
description: Tento článek popisuje, jak obnovit vyřazený server v Azure Database for MySQL pomocí Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 1de11a07bdbac153b6813915cfa52edfa6484aec
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876939"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Obnovení vynechaného serveru Azure Database for MySQL

Když je server vyřazený, zálohování na databázovém serveru se může uchovávat až pět dní v rámci služby. Záloha databáze je dostupná a obnovená jenom z předplatného Azure, ve kterém je server původně umístěný. Vyřazený prostředek serveru MySQL je možné obnovit do 5 dnů od odstranění, a to pomocí následujících doporučených kroků. Doporučené kroky budou fungovat pouze v případě, že se záloha serveru neodstranila ze systému a je stále k dispozici. 

## <a name="pre-requisites"></a>Požadavky
Chcete-li obnovit vyřazený Azure Database for MySQL server, budete potřebovat následující:
- Název předplatného Azure, který hostuje původní server
- Umístění, kde byl server vytvořen

## <a name="steps-to-restore"></a>Postup obnovení

1. V okně Azure Portal v okně sledovat v části [Protokol aktivit](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) . 

2. V protokolu aktivit klikněte na **Přidat filtr** , jak je znázorněno, a nastavte následující filtry pro 

    - **Předplatné** = vaše předplatné hostující odstraněný Server
    - **Typ prostředku** = Azure Database for MySQL servery (Microsoft. DBforMySQL/servery) 
    - **Operace** = odstranění serveru MySQL (Microsoft. DBforMySQL/servery/odstranit) 
 
     [![Protokol aktivit filtrovaný pro odstranění operace serveru MySQL](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Poklikejte na událost odstranit server MySQL a klikněte na kartu JSON a Všimněte si atributů resourceId a submissionTimestamp ve výstupu JSON. ResourceId má následující formát:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Přejděte na [stránku vytvořit Server REST API](https://docs.microsoft.com/rest/api/mysql/servers/create) a klikněte na kartu vyzkoušet, která se zvýrazní zeleně, a přihlaste se pomocí účtu Azure.
 
 5. Poskytněte resourceGroupName, servername (odstraněný název serveru), subscriptionId odvozený od atributu resourceId zachyceného v kroku 3, zatímco verze API-Version je předem vyplněná, jak je znázorněno na obrázku.
 
     [![Vytvoření serveru pomocí REST API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
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

7. Pokud se zobrazí kód odpovědi 201 nebo 202, požadavek na obnovení se úspěšně odeslal. 

8. Vytvoření serveru může trvat dlouho v závislosti na velikosti databáze a výpočetních prostředcích, které jsou zřízené na původním serveru. Stav obnovení se dá monitorovat z protokolu aktivit filtrováním pro 
   - **Předplatné** = vaše předplatné
   - **Typ prostředku** = Azure Database for MySQL servery (Microsoft. DBforMySQL/servery) 
   - **Operace** = aktualizace serveru MySQL vytvořit

## <a name="next-steps"></a>Další kroky
- Pokud se pokoušíte obnovit server během pěti dnů a stále se zobrazí chyba po přesném provedení výše popsaného postupu, otevřete incident podpory pro pomoc. Pokud se pokoušíte obnovit vyřazený server po pěti dnech, je očekávána chyba, protože záložní soubor nelze nalézt. V tomto scénáři neotevírejte lístek podpory. Tým podpory nemůže poskytnout žádnou pomoc, pokud je záloha ze systému odstraněna. 
- Abyste zabránili nechtěnému odstranění serverů, důrazně doporučujeme použít [zámky prostředků](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
