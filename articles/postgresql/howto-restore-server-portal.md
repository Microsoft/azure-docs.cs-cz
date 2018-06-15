---
title: Jak obnovit na Server v Azure databáze pro PostgreSQL
description: Tento článek popisuje, jak k obnovení serveru se v databázi Azure pro PostgreSQL pomocí portálu Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: b4fe1151337dc6f5874e6ad102c6e905e90fd963
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412752"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Postup zálohování a obnovení serveru v databázi Azure pro PostgreSQL pomocí portálu Azure

## <a name="backup-happens-automatically"></a>Zálohování se automaticky stane
Azure databázi PostgreSQL serverů jsou pravidelně zálohovat k povolení funkce obnovení. Pomocí této funkce můžete obnovit na server a všechny jeho databáze do starší bodu v čase, na nový server.

## <a name="set-backup-configuration"></a>Konfigurace zálohování sady

Zkontrolujte výběr mezi konfigurace vašeho serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru v **cenová úroveň** okno.

> [!NOTE]
> Po vytvoření serveru druh redundance, kterou má, nelze přepnout místně redundantní geograficky redundantní vs.
>

Při vytváření serveru přes portál Azure, **cenová úroveň** je okno, kde můžete vybrat buď **místně redundantní** nebo **geograficky redundantní** zálohy pro váš server. Toto okno slouží také k výběru **dobu uchování zálohování** – jak dlouho (ve dnech) chcete uložených pro záloh serveru.

   ![Cenová úroveň - vyberte zálohování redundance](./media/howto-restore-server-portal/pricing-tier.png)

Další informace o nastavení tyto hodnoty během vytváření najdete v tématu [databáze Azure pro rychlý start serveru PostgreSQL](quickstart-create-server-database-portal.md).

Doba uchovávání záloh serveru lze změnit pomocí následujících kroků:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte svou databázi Azure pro PostgreSQL server. Tato akce otevře **přehled** stránky.
3. Vyberte **cenová úroveň** v nabídce v části **nastavení**. Pomocí posuvníku můžete změnit **dobu uchování zálohování** na vaši volbu mezi 7 až 35 dnů.
Na tomto snímku obrazovky má bylo zvýšeno na 34 dnů.
![Doba uchovávání záloh vyšší](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klikněte na tlačítko **OK** pro potvrzení změny.

Doba uchovávání záloh řídí jak daleko zpět v době obnovení bodu v čase se dá načíst, protože je založena na zálohování, které jsou k dispozici. Obnovení bodu v čase je popsána dále v následující části. 

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase
Azure databázi PostgreSQL umožňuje obnovení serveru zpět bodu v čase a do k o novou kopii tohoto serveru. Můžete použít tento nový server Pokud chcete obnovit svá data, nebo mají vaší klientské aplikace, přejděte na tento nový server.

Například pokud tabulka byla omylem vyřadit v poledne v současné době můžete obnovit na čas těsně před poledne a načíst chybějící tabulku a data z této novou kopii tohoto serveru. Obnovení bodu v čase není na serveru úrovni, na úrovni databáze.

Následující kroky obnovit ukázkový server bodu v čase:
1. Na portálu Azure vyberte svou databázi Azure pro PostgreSQL server. 

2. Na panelu nástrojů serveru **přehled** vyberte **obnovení**.

   ![Azure databáze pro obnovení PostgreSQL – přehled – tlačítko](./media/howto-restore-server-portal/2-server.png)

3. Vyplňte formulář obnovení potřebné informace:

   ![Azure databázi PostgreSQL - informace o obnovení ](./media/howto-restore-server-portal/3-restore.png)
  - **Bod obnovení**: vyberte v daném okamžiku chcete obnovit.
  - **Cílový server**: Zadejte název pro nový server.
  - **Umístění**: nelze vybrat oblast. Ve výchozím nastavení je stejný jako zdrojový server.
  - **Cenová úroveň**: při provádění obnovení bodu v čase nelze změnit tyto parametry. Je stejná jako u zdrojového serveru. 

4. Klikněte na tlačítko **OK** k obnovení serveru k obnovení v daném okamžiku. 

5. Po dokončení obnovení vyhledejte nový server, který je vytvořen k ověření, že data byla obnovena podle očekávání.

>[!Note]
>Nový server vytvořené v okamžiku obnovení má stejné přihlašovací jméno správce serveru a zvolili hesla, která byla platná pro existující server v bodu v čase. Heslo můžete změnit na nový server **přehled** stránky.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste nakonfigurovali server pro geograficky redundantní zálohy, lze vytvořit nový server ze zálohy tento existující server. Tento nový server lze vytvořit v libovolné oblasti, že databáze Azure pro PostgreSQL je k dispozici.  

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu. Vyberte **Databáze** > **Azure Database for PostgreSQL**.

   ![Možnost Azure Database for PostgreSQL](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Ve formuláři na **vybrat zdroj** rozevíracího seznamu, vyberte **zálohování**. Tato akce načte seznam serverů, které mají geograficky redundantní zálohy povolena. Vyberte jednu z těchto zálohy jako zdroj nový server.
   ![Vyberte zdroj: Zálohování a seznam geograficky redundantní zálohy](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Při prvním vytvoření serveru nemusí být okamžitě k dispozici pro geografické obnovení. Může trvat několik hodin, než nezbytné metadata vyplnit.
   >

3. Vyplňte zbytek formulář s vašich předvolbách. Můžete vybrat libovolný **umístění**. Až vyberete umístění, můžete vybrat **cenová úroveň**. Ve výchozím nastavení se zobrazí parametry, které obnovujete z existující server. Můžete kliknout na **OK** bez jakýchkoli změn pro dědění těchto nastavení. Nebo můžete změnit **výpočetní generování** (Pokud k dispozici v oblasti jste vybrali), počet **vCores**, **dobu uchování zálohování**, a **zálohování Možnost redundance**. Změna **cenová úroveň** (Basic, obecné účely nebo k paměťově optimalizovaným) nebo **úložiště** velikost během obnovení není podporována.

>[!Note]
>Nový server vytvořené geografické obnovení má stejný server správce přihlašovací jméno a heslo, která byla platná pro existující server v době, kdy bylo zahájeno obnovení. Heslo můžete změnit z nového serveru **přehled** stránky.


## <a name="next-steps"></a>Další postup
- Další informace o službě service [zálohování](concepts-backup.md).
- Další informace o [kontinuity podnikových procesů](concepts-business-continuity.md) možnosti.
