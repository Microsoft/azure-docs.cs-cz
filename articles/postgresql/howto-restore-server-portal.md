---
title: Backup and Restore-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak obnovit server v Azure Database for PostgreSQL-Single server pomocí Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 9ff62a568af4f60318ba0e5bdf2144bb43c9d2b1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884312"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Postup zálohování a obnovení serveru v Azure Database for PostgreSQL-Single server pomocí Azure Portal

## <a name="backup-happens-automatically"></a>K zálohování dochází automaticky
Azure Database for PostgreSQL servery se pravidelně zálohují, aby se povolily funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze k dřívějšímu bodu v čase na novém serveru.

## <a name="set-backup-configuration"></a>Nastavení konfigurace zálohování

V okně **cenová úroveň** můžete vybrat konfiguraci serveru pro buď místně redundantní zálohy, nebo geograficky redundantní zálohy při vytváření serveru.

> [!NOTE]
> Po vytvoření serveru se nedá přepnout druh redundance, který je geograficky redundantní vs místně redundantní.
>

Při vytváření serveru prostřednictvím Azure Portal v okně **cenová úroveň** vyberete buď **místně redundantní** , nebo **geograficky redundantní** zálohy pro váš server. V tomto okně můžete také vybrat **dobu uchování zálohy** – jak dlouho (za kolik dní) chcete ukládat zálohy serveru.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Cenová úroveň – výběr redundance zálohy":::

Další informace o nastavení těchto hodnot během vytváření najdete v [rychlém startu Azure Database for PostgreSQL serveru](quickstart-create-server-database-portal.md).

Dobu uchovávání záloh serveru lze změnit pomocí následujících kroků:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte svůj server Azure Database for PostgreSQL. Tato akce otevře stránku s **přehledem** .
3. V nabídce v části **Nastavení**vyberte **cenová úroveň** . Pomocí posuvníku můžete změnit **dobu uchovávání záloh** na svou předvolbu mezi 7 a 35 dny.
Na snímku obrazovky níže byl zvýšen na 34 dní.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="Období uchování zálohy se zvýšilo.":::

4. Potvrďte změnu kliknutím na tlačítko **OK** .

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Obnovení k bodu v čase je popsáno dále v následující části. 

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase
Azure Database for PostgreSQL umožňuje obnovení serveru zpět k určitému bodu v čase a k nové kopii serveru. Tento nový server můžete použít k obnovení dat nebo k tomu, aby klientské aplikace odkazovaly na tento nový server.

Pokud se například tabulka omylem zahodila v poledne, můžete ji obnovit do času těsně před poledne a načíst chybějící tabulku a data z této nové kopie serveru. Obnovení k bodu v čase probíhá na úrovni serveru, nikoli na úrovni databáze.

Následující kroky obnovují ukázkový Server k určitému bodu v čase:
1. V Azure Portal vyberte server Azure Database for PostgreSQL. 

2. Na panelu nástrojů na stránce **Přehled** serveru vyberte **obnovit**.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL – přehled – tlačítko obnovit":::

3. Do formuláře Restore zadejte požadované údaje:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Informace o obnovení Azure Database for PostgreSQL":::
   - **Bod obnovení**: Vyberte bod v čase, který chcete obnovit.
   - **Cílový server**: zadejte název nového serveru.
   - **Umístění**: oblast nelze vybrat. Ve výchozím nastavení je stejný jako zdrojový server.
   - **Cenová úroveň**: při obnovení k určitému bodu v čase nelze tyto parametry změnit. Je stejná jako u zdrojového serveru. 

4. Kliknutím na tlačítko **OK** obnovte server, který chcete obnovit do bodu v čase. 

5. Až se obnovení dokončí, najděte nově vytvořený server, abyste ověřili, že se data obnovila podle očekávání.

Nový server vytvořený nástrojem obnovení k bodu v čase má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době, kdy je zvolený časový okamžik. Heslo můžete změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovování neobsahuje pravidla brány firewall nebo koncové body služeb virtuální sítě, které byly na původním serveru. Tato pravidla je potřeba pro tento nový server nastavit samostatně.

## <a name="geo-restore"></a>Geografické obnovení

Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, můžete vytvořit nový server ze zálohy stávajícího serveru. Tento nový server se dá vytvořit v libovolné oblasti, kterou Azure Database for PostgreSQL k dispozici.  

1. V levém horním rohu portálu vyberte tlačítko **vytvořit prostředek** (+). Vyberte **databáze**  >  **Azure Database for PostgreSQL**.

   :::image type="content" source="./media/howto-restore-server-portal/1-navigate-to-postgres.png" alt-text="Přejděte na Azure Database for PostgreSQL.":::

2. Vyberte možnost nasazení na **jeden server** .

   :::image type="content" source="./media/howto-restore-server-portal/2-select-deployment-option.png" alt-text="Vyberte možnost nasazení na jeden server Azure Database for PostgreSQL.":::
 
3. Zadejte předplatné, skupinu prostředků a název nového serveru. 

4. Jako **zdroj dat**vyberte **Backup** . Tato akce načte rozevírací seznam, který obsahuje seznam serverů s povolenými geografickými redundantními zálohami.
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore.png" alt-text="Vyberte zdroj dat.":::
    
   > [!NOTE]
   > Při prvním vytvoření serveru nemusí být pro geografickou obnovu k dispozici okamžitě. Naplnění potřebných metadat může trvat několik hodin.
   >

5. Vyberte rozevírací seznam **zálohování** .
   
   :::image type="content" source="./media/howto-restore-server-portal/5-geo-restore-backup.png" alt-text="Vyberte rozevírací seznam zálohování.":::

6. Vyberte zdrojový server, ze kterého chcete obnovit.
   
   :::image type="content" source="./media/howto-restore-server-portal/6-select-backup.png" alt-text="Vyberte zálohování.":::

7. Na serveru se nastaví výchozí hodnoty pro počet **virtuální jádra**, **Doba uchování záloh**, **možnost redundance zálohy**, **verze modulu**a **přihlašovací údaje správce**. Vyberte **pokračovat**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/7-accept-backup.png" alt-text="Pokračujte v zálohování.":::

8. Vyplňte zbytek formuláře vlastními preferencemi. Můžete vybrat libovolné **umístění**.

    Po výběru umístění můžete vybrat **Konfigurovat Server** a aktualizovat **výpočetní generaci** (Pokud je dostupné v oblasti, kterou jste zvolili), počet **virtuální jádra**, **dobu uchování zálohy**a **možnost redundance zálohy**. Změna **cenové úrovně** (Basic, pro obecné účely nebo paměťově optimalizovaná) nebo velikosti **úložiště** během obnovení není podporovaná.

   :::image type="content" source="./media/howto-restore-server-portal/8-create.png" alt-text="Vyplnit formulář"::: 

9. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte výběr. 

10. Vyberte **Vytvořit**, aby se server zřídil. Tato operace může trvat několik minut.

Nový server vytvořený geografickým obnovením má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době zahájení obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovování neobsahuje pravidla brány firewall nebo koncové body služeb virtuální sítě, které byly na původním serveru. Tato pravidla je potřeba pro tento nový server nastavit samostatně.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [zálohování](concepts-backup.md)služby.
- Přečtěte si další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md) .