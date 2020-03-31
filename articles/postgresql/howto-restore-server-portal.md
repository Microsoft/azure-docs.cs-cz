---
title: Zálohování a obnovení – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak obnovit server v Azure Database for PostgreSQL – jeden server pomocí portálu Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: fb13e4f062976e39c3cec607001e6982db228873
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765626"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Jak zálohovat a obnovovat server v Databázi Azure pro PostgreSQL – jeden server pomocí portálu Azure

## <a name="backup-happens-automatically"></a>Zálohování probíhá automaticky
Azure Database pro postgreSQL servery jsou pravidelně zálohovány, aby povolit funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze na dřívější bod v čase, na novém serveru.

## <a name="set-backup-configuration"></a>Nastavit konfiguraci zálohování

Můžete provést volbu mezi konfigurací serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru v okně **Cenové úrovně.**

> [!NOTE]
> Po vytvoření serveru nelze přepnout druh redundance, kterou má, geograficky redundantní vs místně redundantní.
>

Při vytváření serveru prostřednictvím portálu Azure, okno **Cenové úrovně** je místo, kde vyberete **místně redundantní** nebo **geograficky redundantní** zálohy pro váš server. Toto okno je také místo, kde vyberete **dobu uchování zálohy** - jak dlouho (v počtu dní) chcete zálohy serveru uloženy.

   ![Cenová úroveň – zvolte redundanci záloh](./media/howto-restore-server-portal/pricing-tier.png)

Další informace o nastavení těchto hodnot během vytváření najdete v tématu [Azure Database for PostgreSQL server quickstart](quickstart-create-server-database-portal.md).

Dobu uchování záloh serveru lze změnit pomocí následujících kroků:
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Vyberte svůj server Azure Database for PostgreSQL. Tato akce otevře stránku **Přehled.**
3. V nabídce v části **NASTAVENÍ**vyberte **Cenovou úroveň** . Pomocí posuvníku můžete změnit **dobu uchování zálohy** podle vašich preferencí mezi 7 a 35 dny.
Na následujícím snímku obrazovky byla zvýšena na 34 dní.
![Prodloužena doba uchovávání záloh](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klepnutím na **tlačítko OK** potvrďte změnu.

Doba uchovávání záloh určuje, jak daleko zpět v čase lze načíst obnovení bodu v čase, protože je založen na zálohování k dispozici. Obnovení bodu v čase je popsáno dále v následující části. 

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase
Azure Database pro PostgreSQL umožňuje obnovit server zpět do bodu v čase a do nové kopie serveru. Tento nový server můžete použít k obnovení dat nebo chcete, aby klientské aplikace překážely na tento nový server.

Pokud byla například tabulka dnes v poledne omylem vynechána, můžete obnovit čas těsně před polednem a načíst chybějící tabulku a data z této nové kopie serveru. Obnovení bodu v čase je na úrovni serveru, nikoli na úrovni databáze.

Následující kroky obnoví ukázkový server do bodu v čase:
1. Na webu Azure Portal vyberte azure databázi pro postgresql server. 

2. Na panelu nástrojů na stránce **Přehled** serveru vyberte **Obnovit**.

   ![Databáze Azure pro PostgreSQL – přehled – tlačítko Obnovit](./media/howto-restore-server-portal/2-server.png)

3. Do formuláře Restore zadejte požadované údaje:

   ![Databáze Azure pro PostgreSQL – obnovení informací](./media/howto-restore-server-portal/3-restore.png)
   - **Bod obnovení**: Vyberte bod v čase, do kterého chcete obnovit.
   - **Cílový server**: Zadejte název nového serveru.
   - **Umístění**: Oblast nelze vybrat. Ve výchozím nastavení je stejný jako zdrojový server.
   - **Cenová úroveň**: Tyto parametry nelze změnit při obnovení bodu v čase. Je stejná jako u zdrojového serveru. 

4. Klepnutím na **tlačítko OK** obnovíte server a obnovíte jej do bodu v čase. 

5. Po dokončení obnovení vyhledejte nový server, který je vytvořen k ověření data byla obnovena podle očekávání.

Nový server vytvořený obnovením bodu v čase má stejné přihlašovací jméno správce serveru a heslo, které bylo platné pro existující server v okamžiku. Heslo můžete změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovení nemá pravidla brány firewall nebo koncové body služby Virtuální síť, které existovaly na původním serveru. Tato pravidla je třeba nastavit samostatně pro tento nový server.


## <a name="geo-restore"></a>Geografické obnovení

Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, lze ze zálohy tohoto existujícího serveru vytvořit nový server. Tento nový server lze vytvořit v libovolné oblasti, která Azure Database pro PostgreSQL je k dispozici.  

1. V levém horním rohu **portálu** vyberte tlačítko Vytvořit zdroj (+). Vyberte **databáze** > **databáze Azure databáze pro PostgreSQL**.

   ![Možnost Azure Database for PostgreSQL](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. V rozevíracím seznamu **Vybrat zdroj** formuláře zvolte **Zálohovat**. Tato akce načte seznam serverů, které mají povoleny geograficky redundantní zálohy. Vyberte jednu z těchto záloh, která bude zdrojem nového serveru.
   ![Vybrat zdroj: Zálohování a seznam geograficky redundantních záloh.](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Při prvním vytvoření serveru nemusí být okamžitě k dispozici pro geografické obnovení. Může trvat několik hodin, než budou naplněna potřebná metadata.
   >

3. Vyplňte zbytek formuláře s vašimi preferencemi. Můžete vybrat libovolné **umístění**. Po výběru umístění můžete vybrat **cenovou úroveň**. Ve výchozím nastavení jsou zobrazeny parametry existujícího serveru, ze kterého obnovujete. Můžete klepnout na **tlačítko OK,** aniž byste mohli provádět změny, které by tato nastavení zdědily. Nebo můžete změnit **compute generation** (pokud je k dispozici v oblasti, kterou jste zvolili), počet **virtuálních jader**, **doba uchování zálohy**a **možnost redundance zálohování**. Změna **cenové úrovně** (základní, obecné účely nebo optimalizované pro paměť) nebo **velikostúložiště** během obnovení není podporována.


Nový server vytvořený geografickým obnovením má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době, kdy bylo obnovení zahájeno. Heslo lze změnit ze stránky **Přehled** nového serveru.

Nový server vytvořený během obnovení nemá pravidla brány firewall nebo koncové body služby Virtuální síť, které existovaly na původním serveru. Tato pravidla je třeba nastavit samostatně pro tento nový server.


## <a name="next-steps"></a>Další kroky
- Další informace o [zálohách služby](concepts-backup.md).
- Přečtěte si další informace o možnostech [kontinuity provozu.](concepts-business-continuity.md)
