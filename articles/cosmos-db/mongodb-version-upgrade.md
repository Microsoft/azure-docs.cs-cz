---
title: Upgrade verze Mongo rozhraní API vašeho Azure Cosmos DB pro účet MongoDB
description: Postup upgradu verze MongoDB drát-Protocol pro stávající rozhraní API Azure Cosmos DB pro účty MongoDB bez problémů
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771793"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Upgrade verze rozhraní API Azure Cosmos DB API pro účet MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento článek popisuje, jak upgradovat verzi rozhraní API Azure Cosmos DB API pro účet MongoDB. Po upgradu můžete využít nejnovější funkce rozhraní Azure Cosmos DB API pro MongoDB. Proces upgradu nepřerušil dostupnost vašeho účtu a nespotřebovává RU/s nebo sníží kapacitu databáze v jakémkoli bodě. Tímto procesem nebudou ovlivněna žádná existující data ani indexy. 

Při upgradu na novou verzi rozhraní API začněte vývojovým a testovacím zatížením, než provedete upgrade produkčních úloh. Před upgradem Azure Cosmos DB API pro účet MongoDB je důležité upgradovat klienty na verzi, která je kompatibilní s verzí rozhraní API, kterou upgradujete.

>[!Note]
> V tuto chvíli se dá upgradovat jenom opravňující účty, které používají Server verze 3,2, na verzi 3,6 nebo 4,0. Pokud váš účet nezobrazuje možnost upgradu, uložte [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Upgrade na verzi 4.0 nebo 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Výhody upgradu na verzi 4.0

Níže jsou uvedené nové funkce, které jsou součástí verze 4,0:
- Podpora transakcí s více dokumenty v rámci kolekcí unsharded.
- Nové operátory agregace
- Vylepšený výkon prohledávání
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

### <a name="changes-from-version-32"></a>Změny od verze 3.2

- Ve výchozím nastavení je povolená funkce [opakování na straně serveru (SSR)](prevent-rate-limiting-errors.md), požadavky z klientské aplikace tedy nebudou vracet chyby 16500. Místo toho budou požadavky pokračovat, dokud se nedokončí nebo dokud nedosáhnou časového limitu 60 sekund.
- Časový limit jednotlivých požadavků je nastavený na 60 sekund.
- U kolekcí MongoDB vytvořených pro novou verzi přenosového protokolu se bude ve výchozím nastavení indexovat pouze vlastnost `_id`.

### <a name="action-required-when-upgrading-from-32"></a>Akce vyžadovaná při upgradu z verze 3.2

Při upgradu z verze 3.2 se přípona koncového bodu účtu databáze aktualizuje na následující formát:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Pokud provádíte upgrade z verze 3,2, budete muset nahradit existující koncový bod ve svých aplikacích a ovladačích, které se připojují k tomuto účtu databáze. **K funkcím v nové verzi rozhraní API budou mít přístup pouze připojení, která používají nový koncový bod**. Předchozí koncový bod verze 3.2 by měl mít příponu `.documents.azure.com`.

>[!Note]
> Tento koncový bod může mít mírné rozdíly v případě, že byl váš účet vytvořen v cloudu pro svrchované, státní nebo omezené cloudové prostředí Azure.

## <a name="how-to-upgrade"></a>Postup upgradu

1. Přihlaste se k [Azure Portal.](https://portal.azure.com/)

1. Přejděte na rozhraní Azure Cosmos DB API pro účet MongoDB. Otevřete podokno **Přehled** a ověřte, že aktuální **verze serveru** je buď 3,2, nebo 3,6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Ověřte aktuální verzi účtu MongoDB z Azure Portal." border="true":::

1. V nabídce vlevo otevřete `Features` podokno. V tomto podokně se zobrazují funkce na úrovni účtu, které jsou k dispozici pro váš databázový účet.

1. Vyberte řádek `Upgrade MongoDB server version`. Pokud tuto možnost nevidíte, váš účet pravděpodobně nemá na tento upgrade nárok. Pokud se jedná o tento případ, uveďte [lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Otevřete okno funkce a upgradujte svůj účet." border="true":::

1. Projděte si zobrazené informace o upgradu. Vyberte `Set server version to 4.0` (nebo 3,6 v závislosti na aktuální verzi).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Přečtěte si pokyny k upgradu a vyberte upgradovat." border="true":::

1. Po zahájení upgradu je nabídka **funkce** šedá a stav je nastaven na *čeká na vyřízení*. Dokončení upgradu trvá přibližně 15 minut. Tento proces nebude mít vliv na existující funkce nebo operace vašeho databázového účtu. Po dokončení **aktualizace stav verze serveru MongoDB Update** zobrazí upgradovanou verzi. Pokud při zpracování vaší žádosti došlo k potížím, obraťte se prosím na [podporu](https://azure.microsoft.com/en-us/support/create-ticket/) .

1. Níže jsou uvedeny některé předpoklady po upgradu vašeho účtu:

    1. Pokud jste provedli upgrade z 3,2, vraťte se do podokna **Přehled** a zkopírujte nový připojovací řetězec, který chcete použít ve své aplikaci. Původní připojovací řetězec používaný ve verzi 3.2 se nepřeruší. Pokud chcete zajistit konzistentní prostředí, musí všechny vaše aplikace používat nový koncový bod.

    1. Pokud jste upgradovali z verze 3.6, stávající připojovací řetězec se upgraduje na zadanou verzi a měli byste ho dál používat.

## <a name="how-to-downgrade"></a>Postup downgradu

Svůj účet můžete také snížit z 4,0 na 3,6 pomocí stejných kroků v části Jak upgradovat.

Pokud jste provedli upgrade z 3,2 na (4,0 nebo 3,6) a chcete přejít zpět na 3,2, můžete jednoduše přepnout zpět na použití předchozího (3,2) připojovacího řetězce s hostitelem, `accountname.documents.azure.com` který zůstane aktivní po upgradu běžící verze 3,2.

## <a name="next-steps"></a>Další kroky

- Projděte si informace o podporovaných a nepodporovaných [funkcích MongoDB verze 4.0](mongodb-feature-support-40.md).
- Projděte si informace o podporovaných a nepodporovaných [funkcích MongoDB verze 3.6](mongodb-feature-support-36.md).
- Další informace najdete v tématu věnovaném [funkcím Mongo verze 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/).
