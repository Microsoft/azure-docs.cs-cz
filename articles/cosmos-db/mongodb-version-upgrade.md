---
title: Upgrade verze Mongo rozhraní API vašeho Azure Cosmos DB pro účet MongoDB
description: Postup upgradu verze MongoDB drát-Protocol pro stávající rozhraní API Azure Cosmos DB pro účty MongoDB bez problémů
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661167"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Upgrade verze rozhraní API Azure Cosmos DB API pro účet MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento článek popisuje, jak upgradovat verzi rozhraní API Azure Cosmos DB API pro účet MongoDB. Po upgradu můžete využít nejnovější funkce rozhraní Azure Cosmos DB API pro MongoDB. Proces upgradu nepřerušil dostupnost vašeho účtu a nespotřebovává RU/s nebo sníží kapacitu databáze v jakémkoli bodě. Tímto procesem nebudou ovlivněna žádná existující data ani indexy. 

Při upgradu na novou verzi rozhraní API začněte vývojovým a testovacím zatížením, než provedete upgrade produkčních úloh. Před upgradem Azure Cosmos DB API pro účet MongoDB je důležité upgradovat klienty na verzi, která je kompatibilní s verzí rozhraní API, kterou upgradujete.

>[!Note]
> V tuto chvíli se dá upgradovat jenom opravňující účty, které používají Server verze 3,2, na verzi 3,6 nebo 4,0. Pokud váš účet nezobrazuje možnost upgradu, uložte [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Upgrade na 4,0 nebo 3,6

### <a name="benefits-of-upgrading-to-version-40"></a>Výhody upgradu na verzi 4,0

Níže jsou uvedené nové funkce, které jsou součástí verze 4,0:
- Podpora transakcí s více dokumenty v rámci kolekcí unsharded.
- Nové agregační operátory
- Vylepšený výkon kontroly
- Rychlejší a efektivnější úložiště

### <a name="benefits-of-upgrading-to-version-36"></a>Výhody upgradu na verzi 3.6

Níže jsou uvedené nové funkce, které jsou součástí verze 3,6:
- Vyšší výkon a stabilita
- Podpora nových databázových příkazů
- Výchozí podpora kanálu agregace a nové fáze agregace
- Podpora streamů změn
- Podpora složených indexů
- Podpora následujících operací napříč oddíly: UPDATE, DELETE, COUNT a SORT
- Vyšší výkon následujících agregačních operací: $count, $skip, $limit a $group
- Nově se podporuje indexování se zástupnými znaky.

### <a name="changes-from-version-32"></a>Změny ze verze 3,2

- Ve výchozím nastavení je povolená funkce [(SSR) na straně serveru](prevent-rate-limiting-errors.md) , takže požadavky z klientské aplikace nebudou vracet 16500 chyb. Místo toho budou požadavky pokračovat, dokud nebudou dokončeny, nebo dokud neuplyne druhý časový limit 60.
- Časový limit jednotlivých požadavků je nastavený na 60 sekund.
- U kolekcí MongoDB vytvořených pro novou verzi přenosového protokolu se bude ve výchozím nastavení indexovat pouze vlastnost `_id`.

### <a name="action-required-when-upgrading-from-32"></a>Při upgradu z 3,2 se vyžaduje akce.

Při upgradu z 3,2 bude přípona koncového bodu účtu databáze aktualizována v následujícím formátu:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Pokud provádíte upgrade z verze 3,2, budete muset nahradit existující koncový bod ve svých aplikacích a ovladačích, které se připojují k tomuto účtu databáze. **Pouze připojení, která používají nový koncový bod, budou mít přístup k funkcím v nové verzi rozhraní API**. Předchozí koncový bod 3,2 by měl mít příponu `.documents.azure.com` .

>[!Note]
> Tento koncový bod může mít mírné rozdíly v případě, že byl váš účet vytvořen v cloudu pro svrchované, státní nebo omezené cloudové prostředí Azure.

## <a name="how-to-upgrade"></a>Postup upgradu

1. Přejděte na Azure Portal a přejděte do okna Přehled Azure Cosmos DB API pro účet MongoDB. Ověřte, že aktuální verze serveru je to, co očekáváte.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure Portal s přehledem účtu MongoDB" border="false":::

2. Z možností na levé straně vyberte okno `Features` . Tím se odhalí funkce na úrovni účtu, které jsou k dispozici pro váš databázový účet.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal s přehledem účtu MongoDB s zvýrazněným oknem funkcí" border="false":::

3. Klikněte na `Upgrade Mongo server version` řádek. Pokud tuto možnost nevidíte, váš účet možná nebude mít nárok na tento upgrade. Pokud se jedná o tento případ, uveďte [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Okno funkcí s možnostmi." border="false":::

4. Projděte si informace o upgradu. `Enable`Až budete připraveni zahájit proces, klikněte na zapnuto.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Rozšířené pokyny k upgradu." border="false":::

5. Po spuštění procesu se v `Features` nabídce zobrazí stav upgradu. Stav bude přejít z `Pending` , do `In Progress` , do `Upgraded` . Tento proces nebude mít vliv na existující funkce nebo operace databázového účtu.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Stav upgradu po zahájení." border="false":::

6. Po dokončení upgradu se stav zobrazí jako `Upgraded` . Kliknutím na něj zobrazíte další informace o dalších krocích a akcích, které musíte provést k dokončení procesu. Pokud při zpracování vaší žádosti došlo k potížím, obraťte se prosím na [podporu](https://azure.microsoft.com/en-us/support/create-ticket/) .

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Stav upgradovaného účtu" border="false":::

7. 
    1. Pokud jste provedli upgrade z 3,2, vraťte se do okna `Overview` a zkopírujte nový připojovací řetězec, který chcete použít ve své aplikaci. Starý připojovací řetězec se systémem 3,2 nebude přerušen. Aby se zajistilo konzistentní prostředí, musí všechny vaše aplikace používat nový koncový bod.
    2. Pokud jste provedli upgrade z verze 3,6, bude existující připojovací řetězec upgradován na určenou verzi a měl by být nadále používán.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nové okno s přehledem" border="false":::


## <a name="how-to-downgrade"></a>Postup downgrade
Svůj účet můžete také snížit z 4,0 na 3,6 pomocí stejných kroků v části Jak upgradovat. 

Pokud jste provedli upgrade z 3,2 na (4,0 nebo 3,6) a chcete přejít zpět na 3,2, můžete jednoduše přepnout zpět na použití předchozího (3,2) připojovacího řetězce s hostitelem, `accountname.documents.azure.com` který zůstane aktivní po upgradu běžící verze 3,2.


## <a name="next-steps"></a>Další kroky

- Přečtěte si o podporovaných a nepodporovaných [funkcích MongoDB verze 4,0](mongodb-feature-support-40.md).
- Přečtěte si o podporovaných a nepodporovaných [funkcích MongoDB verze 3,6](mongodb-feature-support-36.md).
- Další informace najdete v [Mongo 3,6 – funkce verze](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
