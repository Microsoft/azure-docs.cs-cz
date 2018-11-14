---
title: Zabezpečení databáze SQL Azure | Microsoft Docs
description: Seznamte se s technikami a funkcemi určenými k zabezpečení databáze SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: DRediske
ms.author: daredis
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 827b3b6776656619314af3053cb05f8cfc3754c0
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914379"
---
# <a name="tutorial-secure-your-azure-sql-database"></a>Kurz: Zabezpečení služby Azure SQL Database

SQL Database zajišťuje zabezpečení vašich dat prostřednictvím: 
- Omezení přístupu k vaší databázi pomocí pravidel brány firewall 
- Použití mechanismů ověřování vyžadujících identitu
- Ověřování přístupu k datům prostřednictvím členství na základě role a oprávnění 
- Zabezpečení na úrovni řádku
- Dynamické maskování dat

SQL Database zahrnuje také pokročilé monitorování, auditování a detekci hrozeb. 

Ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem můžete zlepšit v několika jednoduchých krocích. V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Nastavení pravidel brány firewall na úrovni serveru na webu Azure Portal
> * Nastavení pravidel brány firewall na úrovni databáze pomocí aplikace SSMS
> * Připojení k databázi pomocí zabezpečeného připojovacího řetězce
> * Správa uživatelského přístupu
> * Ochrana dat pomocí šifrování
> * Povolení auditování služby SQL Database
> * Povolení detekce hrozeb služby SQL Database

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Instalace nejnovější verze aplikace [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Instalace aplikace Microsoft Excel.
- Vytvořený server a databázi SQL Azure – Viz [Vytvoření databáze SQL Azure na webu Azure Portal](sql-database-get-started-portal.md), [Vytvoření izolované databáze SQL Azure pomocí Azure CLI](sql-database-cli-samples.md) a [Vytvoření izolované databáze SQL Azure pomocí PowerShellu](sql-database-powershell-samples.md). 

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

Databáze SQL jsou chráněné bránou firewall v Azure. Ve výchozím nastavení se všechna připojení k serveru a databázím uvnitř serveru odmítají, s výjimkou připojení z dalších služeb Azure. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](sql-database-firewall-configure.md).

Nejbezpečnější konfigurací je nastavit možnost Povolit přístup ke službám Azure na VYPNUTO. Pokud se k databázi potřebujete připojit z cloudové služby nebo virtuálního počítače Azure, měli byste vytvořit [vyhrazenou IP adresu (klasické nasazení)](../virtual-network/virtual-networks-reserved-public-ip.md) a povolit přístup přes bránu firewall pouze této vyhrazené IP adrese. Pokud používáte model nasazení [Resource Manager](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm), k prostředku se přiřadí vyhrazená veřejná IP adresa a této IP adrese byste měli povolit přístup přes bránu firewall.

Pomocí těchto kroků vytvořte [pravidlo brány firewall na úrovni serveru SQL Database](sql-database-firewall-configure.md), aby váš server umožňoval připojení z konkrétní IP adresy. 

> [!NOTE]
> Pokud jste vytvořili ukázkovou databázi v Azure podle některého z předchozích kurzů nebo rychlých startů a provádíte tento kurz na počítači se stejnou IP adresou, kterou měl při procházení daných kurzů, můžete tento krok přeskočit, protože pravidlo brány firewall na úrovni serveru už budete mít vytvořené.
>

1. V nabídce vlevo klikněte na **Databáze SQL** a na stránce **Databáze SQL** klikněte na databázi, pro kterou chcete pravidlo brány firewall nakonfigurovat. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170313.database.windows.net**) a možnosti pro další konfiguraci.

      ![pravidlo brány firewall serveru](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Klikněte na **Nastavit bránu firewall serveru** na panelu nástrojů, jak je vidět na předchozím obrázku. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database. 

3. Kliknutím na **Přidat IP adresu klienta** na panelu nástrojů přidejte veřejnou IP adresu počítače připojeného k portálu nebo zadejte pravidlo brány firewall ručně a pak klikněte na **Uložit**.

      ![nastavení pravidla brány firewall serveru](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Kliknutím na **OK** a pak na **X** zavřete stránku **Nastavení brány firewall**.

Nyní se můžete pomocí zadané IP adresy nebo rozsahu IP adres připojit k jakékoli databázi na serveru.

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Vytvoření pravidla brány firewall na úrovni databáze pomocí aplikace SSMS

Pravidla brány firewall na úrovni databáze umožňují vytvářet různá nastavení brány firewall pro různé databáze v rámci stejného logického serveru a vytvářet přenositelná pravidla brány firewall – to znamená, že nejsou uložená na serveru SQL, ale při [převzetí služeb při selhání](sql-database-geo-replication-overview.md) se přenášejí spolu s databází. Pravidla brány firewall na úrovni databáze je možné konfigurovat pouze pomocí příkazů jazyka Transact-SQL a pouze po nakonfigurování prvního pravidla brány firewall na úrovni serveru. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](sql-database-firewall-configure.md).

Postupujte podle těchto kroků a vytvořte pravidlo brány firewall specifické pro databázi.

1. Připojte se ke své databázi například pomocí aplikace [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. V Průzkumníku objektů klikněte pravým tlačítkem na databázi, pro kterou chcete přidat pravidlo brány firewall, a pak klikněte na **Nový dotaz**. Otevře se prázdné okno dotazu připojené k vaší databázi.

3. V okně dotazu upravte IP adresu na svou veřejnou IP adresu a pak proveďte následující dotaz:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Na panelu nástrojů klikněte na **Provést** a vytvořte pravidlo brány firewall.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Postup pro připojení aplikace k databázi pomocí zabezpečeného připojovacího řetězce

Pokud chcete zajistit zabezpečené a šifrované připojení mezi klientskou aplikací a službou SQL Database, připojovací řetězec je potřeba nakonfigurovat tak, aby:

- Vyžadoval šifrované připojení
- Nedůvěřoval certifikátu serveru 

Tím se naváže připojení pomocí protokolu TLS (Transport Layer Security) a sníží riziko napadení útočníky, kteří se vydávají za prostředníky. Správně nakonfigurované připojovací řetězce pro vaši službu SQL Database pro podporované ovladače klienta můžete získat z webu Azure Portal, jak je znázorněno na tomto snímku obrazovky pro ADO.NET. Informace o protokolu TLS a možnostech připojení najdete v části věnované [důležitým informacím o protokolu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. V nabídce vlevo vyberte **Databáze SQL** a na stránce **Databáze SQL** klikněte na vaši databázi.

2. Na stránce **Přehled** pro vaši databázi klikněte na **Zobrazit databázové připojovací řetězce**.

3. Zkontrolujte úplný připojovací řetězec **ADO.NET**.

    ![Připojovací řetězec pro ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Vytváření uživatelů databáze

Před vytvořením uživatelů musíte nejprve zvolit jeden ze dvou typů ověřování, které podporuje služba Azure SQL Database: 

**Ověřování SQL**, které pro přihlášení používá uživatelské jméno a heslo a uživatele, kteří jsou platní pouze v kontextu konkrétní databáze v rámci logického serveru. 

**Ověřování Azure Active Directory,**, které používá identity spravované v Azure Active Directory. 

Pokud chcete k ověřování ve službě SQL Database použít [Azure Active Directory](./sql-database-aad-authentication.md), musí existovat naplněná služba Azure Active Directory, abyste mohli pokračovat.

Postupujte podle těchto kroků a vytvořte uživatele využívajícího ověřování SQL:

1. Připojte se ke své databázi například pomocí aplikace [SQL Server Management Studio](./sql-database-connect-query-ssms.md) s použitím svých přihlašovacích údajů správce serveru.

2. V Průzkumníku objektů klikněte pravým tlačítkem na databázi, pro kterou chcete přidat nového uživatele, a pak klikněte na **Nový dotaz**. Otevře se prázdné okno dotazu připojené k vybrané databázi.

3. Do okna dotazu zadejte následující dotaz:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Na panelu nástrojů klikněte na **Provést** a vytvořte uživatele.

5. Ve výchozím nastavení se uživatel může připojit k databázi, ale nemá žádná oprávnění ke čtení nebo zápisu dat. Pokud chcete nově vytvořenému uživateli tato oprávnění udělit, v novém okně dotazu spusťte následující dva příkazy:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Pro připojení k databázi je osvědčeným postupem vytvořit tyto účty bez oprávnění správce na úrovni databáze, pokud nepotřebujete provádět úlohy správce, jako je vytváření nových uživatelů. Informace o ověřování pomocí Azure Active Directory najdete v [kurzu pro Azure Active Directory](./sql-database-aad-authentication-configure.md).


## <a name="protect-your-data-with-encryption"></a>Ochrana dat pomocí šifrování

Transparentní šifrování dat služby Azure SQL Database automaticky šifruje neaktivní uložená data, aniž by vyžadovalo jakékoli změny aplikace, která k šifrované databázi přistupuje. U nově vytvořených databází je transparentní šifrování dat ve výchozím nastavení zapnuté. Pokud chcete povolit transparentní šifrování dat pro svou databázi nebo ověřit, že je transparentní šifrování dat zapnuté, postupujte podle těchto kroků:

1. V nabídce vlevo vyberte **Databáze SQL** a na stránce **Databáze SQL** klikněte na vaši databázi. 

2. Kliknutím na **Transparentní šifrování dat** otevřete konfigurační stránku pro transparentní šifrování dat.

    ![Transparentní šifrování dat](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. V případě potřeby nastavte **Šifrování dat** na ZAPNUTO a klikněte na **Uložit**.

Na pozadí se spustí proces šifrování. Průběh můžete monitorovat po připojení ke službě SQL Database pomocí aplikace [SQL Server Management Studio](./sql-database-connect-query-ssms.md) a dotazováním sloupce encryption_state v zobrazení [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). Stav 3 značí, že je databáze šifrovaná. 

## <a name="enable-sql-database-auditing-if-necessary"></a>Povolení auditování služby SQL Database v případě potřeby

Auditování služby Azure SQL Database sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu Azure Storage. Auditování pomáhá zajistit dodržování legislativních předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit potenciální narušení zabezpečení. Postupujte podle těchto kroků a vytvořte pro svou databázi SQL výchozí zásadu auditování:

1. V nabídce vlevo vyberte **Databáze SQL** a na stránce **Databáze SQL** klikněte na vaši databázi. 

2. V okně Nastavení vyberte **Auditování a detekce hrozeb**. Všimněte si vypnutého auditování na straně serveru a odkazu **Zobrazit nastavení serveru**, který umožňuje zobrazit nebo upravit nastavení auditování serveru z tohoto kontextu.

    ![Okno Auditování](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Pokud upřednostňujete povolení jiného typu auditu (nebo umístění), než je zadaný na úrovni serveru, **zapněte** auditování a zvolte typ auditování **Objekt blob**. Pokud je povolené auditování objektů blob serveru, nakonfigurovaný audit databáze bude existovat vedle auditu objektů blob serveru.

    ![Zapnutí auditování](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Vyberte **Podrobnosti o úložišti** a otevřete okno Úložiště protokolů auditů. Vyberte účet úložiště Azure, do kterého se protokoly budou ukládat, a období uchovávání, po jehož uplynutí se staré protokoly odstraní, a pak klikněte na **OK** v dolní části. 

   > [!TIP]
   > Pro všechny auditované databáze použijte stejný účet úložiště, abyste získali maximum z šablon sestav auditování.
   > 

5. Klikněte na **Uložit**.

> [!IMPORTANT]
> Pokud chcete přizpůsobit auditované události, můžete to provést prostřednictvím PowerShellu nebo rozhraní REST API – další informace najdete v tématu [Auditování databáze SQL](sql-database-auditing.md).
>

## <a name="enable-sql-database-threat-detection"></a>Povolení detekce hrozeb služby SQL Database

Detekce hrozeb poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje díky poskytování výstrah zabezpečení na neobvyklé aktivity detekovat a reagovat na potenciální hrozby, když k nim dojde. Uživatelé můžou pomocí auditování služby SQL Database prozkoumat podezřelé události a určit, jestli jsou důsledkem pokusu o přístup, porušení zabezpečení nebo zneužití dat v databázi. Detekce hrozeb usnadňuje řešení potenciálních ohrožení databáze, aniž byste museli být odborníkem na zabezpečení nebo museli spravovat pokročilé systémy monitorování zabezpečení.
Detekce hrozeb například detekuje určité neobvyklé databázové aktivity značící potenciální pokusy o útok prostřednictvím injektáže SQL. Injektáž SQL představuje jeden z nejběžnějších problémů zabezpečení webových aplikací na internetu a používá se k útokům na aplikace založené na datech. Útočníci využívají ohrožení zabezpečení aplikací k injektáži škodlivých příkazů jazyka SQL do vstupních polí aplikace za účelem porušení zabezpečení nebo úpravy dat v databázi.

1. Přejděte do okna konfigurace databáze SQL, kterou chcete monitorovat. V okně Nastavení vyberte **Auditování a detekce hrozeb**.

    ![Navigační podokno](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. V navigačním okně **Auditování a detekce hrozeb** **zapněte** auditování. Tím se zobrazí nastavení detekce hrozeb.

3. **Zapněte** detekci hrozeb.

4. Nakonfigurujte seznam emailů, které budou přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.

5. Kliknutím na **Uložit** v okně **Auditování a detekce hrozeb** uložte novou nebo aktualizovanou zásadu auditování a detekce hrozeb.

    ![Navigační podokno](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Pokud dojde k detekci neobvyklých databázových aktivit, obdržíte e-mailové oznámení. E-mail bude obsahovat informace o podezřelé události zabezpečení, včetně povahy neobvyklých aktivit, názvu databáze, názvu serveru a času události. Kromě toho bude obsahovat informace o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení databáze. Další kroky vás provedou doporučeným postupem po přijetí takového e-mailu:

    ![E-mail detekce hrozeb](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. V e-mailu klikněte na odkaz **Protokol auditování SQL Azure**. Tím se otevře Azure Portal, na kterém se zobrazí relevantní záznamy auditování v době výskytu podezřelé události.

    ![Záznamy auditu](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Kliknutím na záznamy auditu zobrazíte další informace o podezřelých databázových aktivitách, jako je příkaz jazyka SQL, důvod selhání a IP adresa klienta.

    ![Podrobnosti o záznamu](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. V okně Záznamy auditování klikněte na **Otevřít v aplikaci Excel** a otevřete předkonfigurovanou šablonu aplikace Excel umožňující import a spuštění hlubší analýzy protokolu auditu v době výskytu podezřelé události.

    > [!NOTE]
    > V aplikaci Excel 2010 a novější se vyžaduje doplněk Power Query a nastavení **Rychle zkombinovat**.

    ![Otevřené záznamy v aplikaci Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Konfigurace nastavení **Rychle zkombinovat** – Na záložce pásu karet **POWER QUERY** vyberte **Možnosti** a zobrazte dialogové okno Možnosti. Vyberte část Osobní údaje a zvolte druhou možnost – Ignorovat úrovně ochrany osobních údajů a potenciálně tak vylepšit výkon:

    ![Rychle zkombinovat v aplikaci Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Pokud chcete načíst protokoly auditu SQL, ujistěte se, že jsou správně nastavené parametry na kartě Nastavení, pak vyberte pás karet Data a klikněte na tlačítko Aktualizovat vše.

    ![Parametry v aplikaci Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Výsledky se zobrazí na listu **Protokoly auditu SQL**, na kterém můžete spustit hlubší analýzu detekovaných neobvyklých aktivit a zmírnit dopad události zabezpečení na vaši aplikaci.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak v několika jednoduchých krocích zlepšit ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem.  Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Nastavení pravidel brány firewall pro server nebo databázi
> * Připojení k databázi pomocí zabezpečeného připojovacího řetězce
> * Správa uživatelského přístupu
> * Ochrana dat pomocí šifrování
> * Povolení auditování služby SQL Database
> * Povolení detekce hrozeb služby SQL Database

V dalším kurzu se dozvíte, jak implementovat geograficky distribuovanou databázi.

> [!div class="nextstepaction"]
>[Implementace geograficky distribuované databáze](sql-database-implement-geo-distributed-database.md)

