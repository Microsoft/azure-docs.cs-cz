---
title: Upgrade verze Mongo rozhraní API vašeho Azure Cosmos DB pro účet MongoDB
description: Postup upgradu verze MongoDB drát-Protocol pro stávající rozhraní API Azure Cosmos DB pro účty MongoDB bez problémů
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409659"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Upgrade verze MongoDB přenosového protokolu rozhraní API vašeho Azure Cosmos DB pro účet MongoDB

Tento článek popisuje, jak upgradovat verzi přenosového protokolu rozhraní API vašeho Azure Cosmos DB pro účet MongoDB. Po upgradu verze přenosového protokolu můžete využít nejnovější funkce rozhraní Azure Cosmos DB API pro MongoDB. Proces upgradu nepřerušil dostupnost vašeho účtu a nespotřebovává RU/s nebo sníží kapacitu databáze v jakémkoli bodě. Tímto procesem nebudou ovlivněna žádná existující data ani indexy.

>[!Note]
> V tuto chvíli se dá upgradovat jenom opravňující účty, které používají Server verze 3,2, na verzi 3,6. Pokud váš účet nezobrazuje možnost upgradu, uložte [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Probíhá upgrade z verze 3,2 na 3,6.

### <a name="benefits-of-upgrading-to-version-36"></a>Výhody upgradu na verzi 3.6

Níže jsou uvedené nové funkce, které jsou součástí verze 3,6:
- Vyšší výkon a stabilita
- Podpora nových databázových příkazů
- Výchozí podpora kanálu agregace a nové fáze agregace
- Podpora datových proudů změn
- Podpora pro složené indexy
- Podpora více oddílů pro následující operace: aktualizace, odstranění, počet a řazení
- Vylepšený výkon pro následující agregované operace: $count, $skip, $limit a $group
- Indexování zástupných znaků se teď podporuje.

### <a name="changes-from-version-32"></a>Změny ze verze 3,2

- Byly **odebrány RequestRateIsLarge chyby**. Žádosti z klientské aplikace již nevrátí 16500 chyb. Místo toho budou požadavky pokračovat, dokud nebudou dokončeny nebo nesplní časový limit.
- Na časový limit žádosti se nastaví na 60 sekund.
- Kolekce MongoDB vytvořené v nové verzi přenosového protokolu budou mít `_id` ve výchozím nastavení indexovanou vlastnost.

### <a name="action-required"></a>Požadovaná akce

Pro upgrade na verzi 3,6 bude přípona koncového bodu účtu databáze aktualizována v následujícím formátu:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Existující koncový bod musíte nahradit ve svých aplikacích a ovladačích, které se připojují k tomuto účtu databáze. **Pouze připojení, která používají nový koncový bod, budou mít přístup k funkcím v MongoDB verze 3,6**. Předchozí koncový bod by měl mít příponu `.documents.azure.com` .

>[!Note]
> Tento koncový bod může mít mírné rozdíly v případě, že byl váš účet vytvořen v cloudu pro svrchované, státní nebo omezené cloudové prostředí Azure.

### <a name="how-to-upgrade"></a>Postup upgradu

1. Nejprve přejděte na Azure Portal a přejděte do okna Přehled Azure Cosmos DB API pro účet MongoDB. Ověřte, že je serverová verze `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure Portal s přehledem účtu MongoDB" border="false":::

2. Z možností na levé straně vyberte okno `Features` . Tím se odhalí funkce na úrovni účtu, které jsou k dispozici pro váš databázový účet.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal s přehledem účtu MongoDB s zvýrazněným oknem funkcí" border="false":::

3. Klikněte na `Upgrade to Mongo server version 3.6` řádek. Pokud tuto možnost nevidíte, váš účet možná nebude mít nárok na tento upgrade. Pokud se jedná o tento případ, uveďte [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Okno funkcí s možnostmi." border="false":::

4. Projděte si informace o tomto konkrétním upgradu. Všimněte si, že upgrade bude dokončen, dokud vaše aplikace nepoužijí aktualizovaný koncový bod, jak je zvýrazněno v této části. `Enable`Až budete připraveni zahájit proces, klikněte na zapnuto.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Rozšířené pokyny k upgradu." border="false":::

5. Po spuštění procesu se v `Features` nabídce zobrazí stav upgradu. Stav bude přejít z `Pending` , do `In Progress` , do `Upgraded` . Tento proces nebude mít vliv na existující funkce nebo operace databázového účtu.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Stav upgradu po zahájení." border="false":::

6. Po dokončení upgradu se stav zobrazí jako `Upgraded` . Kliknutím na něj zobrazíte další informace o dalších krocích a akcích, které musíte provést k dokončení procesu. Pokud při zpracování vaší žádosti došlo k potížím, obraťte se prosím na [podporu](https://azure.microsoft.com/en-us/support/create-ticket/) .

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Stav upgradovaného účtu" border="false":::

7. **Chcete-li začít používat upgradovanou verzi vašeho databázového účtu**, vraťte se do okna `Overview` a zkopírujte nový připojovací řetězec, který chcete použít ve své aplikaci. Jakmile se aplikace připojí k novému koncovému bodu, začnou tuto upgradovanou verzi používat. Existující připojení nebudou přerušená a bude možné je aktualizovat na pohodlí. Aby se zajistilo konzistentní prostředí, musí všechny vaše aplikace používat nový koncový bod.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nové okno s přehledem" border="false":::

## <a name="next-steps"></a>Další kroky

- Přečtěte si o podporovaných a nepodporovaných [funkcích MongoDB verze 3,6](mongodb-feature-support-36.md).
- Další informace najdete v [Mongo 3,6 – funkce verze](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
