---
title: Přesun databáze Azure AD Connect z SQL Serveru Express na SQL Server. | Dokumenty Microsoft
description: Tento dokument popisuje, jak přesunout databázi Azure AD Connect z místního serveru SQL Server Express na vzdálený SQL Server.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c25aef949b99bb4dc348bd2392ada325fcd466ea
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169611"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Přesun databáze Azure AD Connect z SQL Serveru Express na SQL Server 

Tento dokument popisuje, jak přesunout databázi Azure AD Connect z místního serveru SQL Server Express na vzdálený SQL Server.  K provedení této úlohy můžete použít následující postupy.

## <a name="about-this-scenario"></a>O tomto scénáři
Následuje několik stručných informací o tomto scénáři.  V tomto scénáři je na jednom řadiči domény s Windows Serverem 2016 nainstalovaný nástroj Azure AD Connect verze 1.1.819.0.  Pro svou databázi využívá integrovaný SQL Server 2012 Express Edition.  Databáze se přesune na server SQL Server 2017.

![](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Přesun databáze Azure AD Connect
Pomocí následujících kroků přesuňte databázi Azure AD Connect na vzdálený SQL Server.

1.  Na serveru Azure AD Connect přejděte do části **Služby** a zastavte službu **Microsoft Azure AD Sync**.
2. Vyhledejte **% Program Files%\Microsoft Azure AD Sync/Data/** složku a Kopírovat **ADSync.mdf** a **ADSync_log.ldf** soubory na vzdálený server SQL.
3. Na serveru Azure AD Connect restartujte službu **Microsoft Azure AD Sync**.
4. Odinstalujte Azure AD Connect tak, že přejdete do části Ovládací panely > Programy > Programy a funkce.  Vyberte Microsoft Azure AD Connect a v horní části klikněte na Odinstalovat.
5. Na vzdáleném SQL Serveru otevřete SQL Server Management Studio.
6. V části Databáze klikněte pravým tlačítkem a vyberte Připojit.
7. Na obrazovce **Připojit databáze** klikněte na **Přidat** a přejděte k souboru ADSync.mdf.  Klikněte na **OK**.
![](media/how-to-connect-install-move-db/move2.png)

8. Po připojení databáze se vraťte na server Azure AD Connect a nainstalujte Azure AD Connect.
9. Po dokončení instalace MSI se spustí průvodce Azure AD Connect v režimu expresní instalace. Zavřete obrazovku kliknutím na ikonu Ukončit.
![Uvítání](./media/how-to-connect-install-move-db/db1.png)
10. Spusťte nový příkazový řádek nebo novou relaci PowerShellu. Přejděte do složky <drive>\Program Files\Microsoft Azure AD Connect. Spuštěním příkazu .\AzureADConnect.exe /useexistingdatabase spusťte průvodce Azure AD Connect v režimu instalace Použít stávající databázi.
![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Zobrazí se obrazovka Vítá vás Azure AD Connect. Jakmile odsouhlasíte licenční podmínky a oznámení o ochraně osobních údajů, klikněte na **Pokračovat**.
![Uvítání](./media/how-to-connect-install-move-db/db3.png)
12. Na obrazovce **Instalace požadovaných komponent** je povolená možnost **Použít existující SQL Server**. Zadejte název SQL Serveru, který je hostitelem databáze ADSync. Pokud instance stroje SQL použitá k hostování databáze ADSync není na SQL Serveru výchozí instancí, musíte zadat název instance stroje SQL. Dále, pokud není povolené procházení SQL, musíte zadat také číslo portu instance stroje SQL. Příklad:         
![Uvítání](./media/how-to-connect-install-move-db/db4.png)           

13. Na obrazovce **Připojení ke službě Azure AD** musíte zadat přihlašovací údaje globálního správce vašeho adresáře služby Azure AD. Je vhodné použít účet ve výchozí doméně onmicrosoft.com. Tento účet slouží jenom k vytvoření účtu služby v Azure AD, a po dokončení průvodce se už nepoužívá.
![Připojení](./media/how-to-connect-install-move-db/db5.png)
 
14. Na obrazovce **Připojení adresářů** je uvedená stávající doménová struktura AD nakonfigurovaná pro synchronizaci adresářů, vedle níž je ikona červeného křížku. K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Průvodce Azure AD Connect nemůže načíst přihlašovací údaje účtu služby AD DS uložené v databázi ADSync, protože jsou šifrované a může je dešifrovat pouze předchozí server Azure AD Connect. Klikněte na **Změnit přihlašovací údaje** a zadejte účet služby AD DS pro doménovou strukturu AD.
![Adresáře](./media/how-to-connect-install-move-db/db6.png)
 
 
15. V automaticky otevíraném dialogovém okně můžete buď (i) zadat přihlašovací údaje podnikového správce a nechat Azure AD Connect vytvořit účet služby AD DS za vás, nebo (ii) sami vytvořit účet služby AD DS a zadat jeho přihlašovací údaje do Azure AD Connect. Jakmile vyberete jednu z možností a zadáte potřebné přihlašovací údaje, kliknutím na **OK** zavřete automaticky otevírané dialogové okno.
![Uvítání](./media/how-to-connect-install-move-db/db7.png)
 
 
16. Po zadání přihlašovacích údajů se ikona červeného křížku změní na ikonu zeleného zaškrtnutí. Klikněte na **Další**.
![Uvítání](./media/how-to-connect-install-move-db/db8.png)
 
 
17. Na obrazovce **Připraveno ke konfiguraci** klikněte na **Nainstalovat**.
![Uvítání](./media/how-to-connect-install-move-db/db9.png)
 
 
18. Po dokončení instalace se na serveru Azure AD Connect automaticky zapne pracovní režim. Před vypnutím pracovního režimu se doporučuje zkontrolovat neočekávané změny v konfiguraci serveru a čekajících sestavách. 

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
- [Instalace nástroje Azure AD Connect s využitím existující databáze ADSync](how-to-connect-install-existing-database.md)
- [Instalace nástroje Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md)

