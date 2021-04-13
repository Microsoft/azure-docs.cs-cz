---
title: Obnovení odstraněného serveru Azure Database for MariaDB
description: Tento článek popisuje, jak obnovit odstraněný Server v Azure Database for MariaDB pomocí Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315411"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>Obnovení odstraněného serveru Azure Database for MariaDB

Po odstranění serveru může být záloha na databázovém serveru ve službě uchována až na pět dní. Záloha databáze je dostupná a obnovená jenom z předplatného Azure, ve kterém je server původně umístěný. K obnovení odstraněného prostředku MariaDB serveru do 5 dní od okamžiku odstranění serveru je možné provést následující doporučené kroky. Doporučené kroky budou fungovat pouze v případě, že se záloha serveru neodstranila ze systému a je stále k dispozici. 

## <a name="pre-requisites"></a>Požadavky
Chcete-li obnovit odstraněný Azure Database for MariaDB Server, budete potřebovat následující:
- Název předplatného Azure, který hostuje původní server
- Umístění, kde byl server vytvořen

## <a name="steps-to-restore"></a>Postup obnovení

1. V okně Azure Portal v okně sledovat v části [Protokol aktivit](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) . 

2. V protokolu aktivit klikněte na **Přidat filtr** , jak je znázorněno, a nastavte následující filtry pro 

    - **Předplatné** = vaše předplatné hostující odstraněný Server
    - **Typ prostředku** = Azure Database for MariaDB servery (Microsoft. DBForMariaDB/servery) 
    - **Operace** = odstranění serveru MariaDB (Microsoft. DBForMariaDB/servery/Delete) 
 
     [![Protokol aktivit filtrovaný pro odstranění operace serveru MariaDB](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Poklikejte na událost odstranit server MariaDB a klikněte na kartu JSON a Všimněte si atributů resourceId a submissionTimestamp ve výstupu JSON. ResourceId má následující formát:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver.
 
 4. Přejděte na [stránku vytvořit Server REST API](/rest/api/mariadb/servers/create) a klikněte na kartu vyzkoušet, která se zvýrazní zeleně, a přihlaste se pomocí účtu Azure.
 
 5. Poskytněte resourceGroupName, servername (odstraněný název serveru), subscriptionId odvozený od atributu resourceId zachyceného v kroku 3, zatímco verze API-Version je předem vyplněná, jak je znázorněno na obrázku.
 
     [![Vytvoření serveru pomocí REST API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. V části tělo žádosti přejděte dolů a vložte následující:
     
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

7. Ve výše uvedeném textu žádosti nahraďte následující hodnoty:
   * Umístění vynechaného serveru v oblasti Azure, ve které byl odstraněný Server původně vytvořený
   * "submissionTimestamp" a "resourceId" s hodnotami zaznamenanými v kroku 3. 
   * Pro "restorePointInTime" zadejte hodnotu "submissionTimestamp" mínus **15 minut** , abyste se ujistili, že příkaz nechybí.

8. Pokud se zobrazí kód odpovědi 201 nebo 202, požadavek na obnovení se úspěšně odeslal. 

9. Vytvoření serveru může trvat dlouho v závislosti na velikosti databáze a výpočetních prostředcích, které jsou zřízené na původním serveru. Stav obnovení se dá monitorovat z protokolu aktivit filtrováním pro 
   - **Předplatné** = vaše předplatné
   - **Typ prostředku** = Azure Database for MariaDB servery (Microsoft. DBForMariaDB/servery) 
   - **Operace** = aktualizace serveru MariaDB vytvořit

## <a name="next-steps"></a>Další kroky
- Pokud se pokoušíte obnovit server během pěti dnů a stále se zobrazí chyba po přesném provedení výše popsaného postupu, otevřete incident podpory pro pomoc. Pokud se pokusíte obnovit odstraněný Server po pěti dnech, je očekávána chyba, protože záložní soubor nelze nalézt. V tomto scénáři neotevírejte lístek podpory. Tým podpory nemůže poskytnout žádnou pomoc, pokud je záloha ze systému odstraněna. 
- Abyste zabránili nechtěnému odstranění serverů, důrazně doporučujeme použít [zámky prostředků](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
