---
title: Zálohování a obnovení-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak obnovit server v Azure Database for MySQL pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f8005bccf3a726a6022efdecb2eb5738669f3bc2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74762620"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Postup zálohování a obnovení serveru v Azure Database for MySQL pomocí Azure Portal

## <a name="backup-happens-automatically"></a>K zálohování dochází automaticky
Azure Database for MySQL servery se pravidelně zálohují, aby se povolily funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze k dřívějšímu bodu v čase na novém serveru.

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto průvodce budete potřebovat:
- [Server a databáze Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Nastavení konfigurace zálohování

V okně **cenová úroveň** můžete vybrat konfiguraci serveru pro buď místně redundantní zálohy, nebo geograficky redundantní zálohy při vytváření serveru.

> [!NOTE]
> Po vytvoření serveru se nedá přepnout druh redundance, který je geograficky redundantní vs místně redundantní.
>

Při vytváření serveru prostřednictvím Azure Portal v okně **cenová úroveň** vyberete buď **místně redundantní** , nebo **geograficky redundantní** zálohy pro váš server. V tomto okně můžete také vybrat **dobu uchování zálohy** – jak dlouho (za kolik dní) chcete ukládat zálohy serveru.

   ![Cenová úroveň – výběr redundance zálohy](./media/howto-restore-server-portal/pricing-tier.png)

Další informace o nastavení těchto hodnot během vytváření najdete v [rychlém startu Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-portal.md).

Dobu uchovávání záloh můžete na serveru změnit pomocí následujících kroků:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte server Azure Database for MySQL. Tato akce otevře stránku s **přehledem** .
3. V nabídce v části **Nastavení**vyberte **cenová úroveň** . Pomocí posuvníku můžete změnit **dobu uchovávání záloh** na svou předvolbu mezi 7 a 35 dny.
Na snímku obrazovky níže byl zvýšen na 34 dní.
období uchování zálohy ![zvýšené](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Potvrďte změnu kliknutím na tlačítko **OK** .

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Obnovení k bodu v čase je popsáno dále v následující části. 

## <a name="point-in-time-restore"></a>Obnovení k určitému časovému okamžiku
Azure Database for MySQL umožňuje obnovení serveru zpět k určitému bodu v čase a k nové kopii serveru. Tento nový server můžete použít k obnovení dat nebo k tomu, aby klientské aplikace odkazovaly na tento nový server.

Pokud se například tabulka omylem zahodila v poledne, můžete ji obnovit do času těsně před poledne a načíst chybějící tabulku a data z této nové kopie serveru. Obnovení k bodu v čase probíhá na úrovni serveru, nikoli na úrovni databáze.

Následující kroky obnovují ukázkový Server k určitému bodu v čase:
1. V Azure Portal vyberte server Azure Database for MySQL. 

2. Na panelu nástrojů na stránce **Přehled** serveru vyberte **obnovit**.

   ![Azure Database for MySQL – přehled – tlačítko obnovit](./media/howto-restore-server-portal/2-server.png)

3. Vyplňte formulář obnovení požadovanými informacemi:

   ![Informace o obnovení Azure Database for MySQL](./media/howto-restore-server-portal/3-restore.png)
   - **Bod obnovení**: Vyberte bod v čase, který chcete obnovit.
   - **Cílový server**: zadejte název nového serveru.
   - **Umístění**: oblast nelze vybrat. Ve výchozím nastavení je stejný jako zdrojový server.
   - **Cenová úroveň**: při obnovení k určitému bodu v čase nelze tyto parametry změnit. Je stejná jako u zdrojového serveru. 

4. Kliknutím na tlačítko **OK** obnovte server, který chcete obnovit do bodu v čase. 

5. Až se obnovení dokončí, najděte nově vytvořený server, abyste ověřili, že se data obnovila podle očekávání.

Nový server vytvořený nástrojem obnovení k bodu v čase má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době, kdy je zvolený časový okamžik. Heslo můžete změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá pravidla brány firewall nebo koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla je potřeba nastavit samostatně pro tento nový server.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, můžete vytvořit nový server ze zálohy stávajícího serveru. Tento nový server se dá vytvořit v libovolné oblasti, kterou Azure Database for MySQL k dispozici.  

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu. Vyberte **Databáze** > **Azure Database for MySQL**.

   ![Možnost Azure Database for MySQL](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. V rozevíracím seznamu **Vybrat zdroj vyberte** možnost **zálohovat**. Tato akce načte seznam serverů, u kterých je povoleno geograficky redundantní zálohy. Vyberte jeden z těchto záloh, který bude zdrojem nového serveru.
   ![vybrat zdroj: zálohování a seznam geograficky redundantních záloh](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Při prvním vytvoření serveru nemusí být pro geografickou obnovu k dispozici okamžitě. Naplnění potřebných metadat může trvat několik hodin.
   >

3. Vyplňte zbytek formuláře vlastními preferencemi. Můžete vybrat libovolné **umístění**. Po výběru umístění můžete vybrat **cenovou úroveň**. Ve výchozím nastavení se zobrazí parametry pro existující server, který obnovujete. Můžete kliknout na tlačítko **OK** bez provedení změn, které by zdědily tato nastavení. Můžete také změnit **výpočetní generaci** (Pokud je k dispozici v oblasti, kterou jste zvolili), **počet virtuální jádra**, **dobu uchování zálohy**a **možnost redundance zálohy**. Změna **cenové úrovně** (Basic, pro obecné účely nebo paměťově optimalizovaná) nebo velikosti **úložiště** během obnovení není podporovaná.

Nový server vytvořený geografickým obnovením má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době zahájení obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá pravidla brány firewall nebo koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla je potřeba nastavit samostatně pro tento nový server.



## <a name="next-steps"></a>Další kroky
- Další informace o [zálohování](concepts-backup.md) služby
- Další informace o [replikách](concepts-read-replicas.md)
- Další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md)
