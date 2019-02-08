---
title: Instalace služby Azure AD Connect pomocí existující databáze ADSync | Dokumentace Microsoftu
description: Toto téma popisuje způsob použití existující databáze ADSync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 2f1b6cec49507c5106434bf42743aadd1b3230aa
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894968"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalace Azure AD Connect pomocí existující databáze ADSync
Azure AD Connect vyžaduje databázi SQL serveru ukládat data. Můžete buď použít výchozí nastavení, které SQL Server 2012 Express LocalDB nainstalované pomocí služby Azure AD Connect, nebo použijte vlastní plnou verzi SQL. Dříve při instalaci Azure AD Connect vytvoří nová databáze s názvem ADSync byla vždy. S Azure AD Connect verze 1.1.613.0 (nebo po) máte možnost nainstalovat Azure AD Connect kliknutím na existující databáze ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Výhody použití existující databáze ADSync
Najetím myší na existující databáze ADSync:

- S výjimkou přihlašovací údaje uložené v databázi ADSync (včetně vlastních synchronizační pravidla, konektory, filtrování a konfigurace volitelných funkcí) konfigurace synchronizace je automaticky obnovit a použít během instalace . Pověření používaná Azure AD Connect pro synchronizaci změn s místním AD a Azure AD jsou zašifrované a je přístupný pouze předchozí server Azure AD Connect.
- Všechna data identity (přidružený prostor konektoru a úložiště metaverse) a soubory cookie synchronizace uložené v databázi ADSync se také dají obnovit. Nově nainstalovaný Azure AD Connect, kterou server pokračovat v synchronizaci z tam, kde předchozí server Azure AD Connect přestali, namísto nutnosti provádět úplnou synchronizaci.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scénáře, kde je výhodné použití existující databáze ADSync
Mezi tyto výhody jsou užitečné v následujících scénářích:


- Máte existující nasazení služby Azure AD Connect. Existující server služby Azure AD Connect se už nefunguje, ale pořád funguje SQL serveru, který obsahuje databázi ADSync. Můžete nainstalovat nový server Azure AD Connect a nasměrovat ho na existující databáze ADSync. 
- Máte existující nasazení služby Azure AD Connect. SQL server obsahující databáze ADSync již není funkční. Máte ale Nedávná proveďte zálohu databáze. Nový SQL server můžete obnovit databázi ADSync nejprve. A můžete nainstalovat nový server Azure AD Connect a nasměrovat ho k obnovené databázi ADSync.
- Máte stávající nasazení služby Azure AD Connect, která používá LocalDB. Z důvodu 10 GB limitu pro LocalDB uložené chcete migrovat na SQL. Můžete zálohovat databázi ADSync z LocalDB a obnovit na SQL server. A můžete znovu nainstalovat nový server Azure AD Connect a nasměrovat ho k obnovené databázi ADSync.
- Pokoušíte se nastavit pracovní server a chce se ujistit, že její konfigurace se shoduje s aktuální aktivní server. Můžete zálohovat databázi ADSync a obnovit na jiný server SQL. A můžete znovu nainstalovat nový server Azure AD Connect a nasměrovat ho k obnovené databázi ADSync.

## <a name="prerequisite-information"></a>Informace o požadavcích

Důležité poznámky vzít si předtím, než budete pokračovat:

- Nezapomeňte zkontrolovat předpoklady pro instalaci Azure AD Connect na Hardware a požadavky a účet a oprávnění vyžadovaných pro instalaci Azure AD Connect. Oprávnění potřebná k instalaci Azure AD Connect s použitím "použít stávající databázi" režim je stejný jako "vlastní" instalace.
- Nasazení služby Azure AD Connect s existující ADSync databáze je podporováno pouze s plnou SQL. Není podporován v SQL Express LocalDB. Pokud máte existující databáze ADSync ve LocalDB, který chcete použít, musíte nejprve zálohovat databázi ADSync (LocalDB) a obnovit na SQL. A nasazením služby Azure AD Connect na obnovenou databázi pomocí této metody.
- Verze služby Azure AD Connect používá pro instalaci musí splňovat následující kritéria:
    - 1.1.613.0 nebo novější, a
    - Stejná nebo vyšší než verze služby Azure AD Connect naposledy použité v databázi ADSync. Azure AD Connect verze použitá pro instalaci je vyšší než verze naposledy použili s databáze ADSync, úplná synchronizace může být vyžadováno.  Úplná synchronizace je vyžadována, pokud dojde ke změnám schématu nebo synchronizační pravidlo mezi oběma verzemi. 
- Databáze ADSync používá by měl obsahovat synchronizace stavu, ve kterém je relativně nové. Poslední aktivita synchronizace s existující databáze ADSync by měla být v rámci poslední tři týdny.
- Při instalaci Azure AD Connect pomocí metody "použít stávající databázi", nezachová se metoda přihlašování nakonfigurované na předchozí server Azure AD Connect. Dále nelze konfigurovat způsob přihlášení během instalace. Metoda přihlašování můžete nakonfigurovat pouze po dokončení instalace.
- Nemůžete mít více servery Azure AD Connect, které sdílejí stejnou databázi ADSync. Metoda "použít stávající databázi" umožňuje znovu použít existující databáze ADSync nový server Azure AD Connect. Nepodporuje sdílení.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Postup instalace Azure AD Connect s režimem "použít stávající databázi"
1.  Stáhněte si instalační program služby Azure AD Connect (AzureADConnect.MSI) na Windows Server. Klikněte dvakrát na instalační program služby Azure AD Connect spustit instalaci Azure AD Connect.
2.  Po dokončení instalace MSI se spustí průvodce Azure AD Connect v režimu expresní instalace. Zavřete obrazovku kliknutím na ikonu Ukončit.
![Uvítání](./media/how-to-connect-install-existing-database/db1.png)
3.  Spusťte nový příkazový řádek nebo novou relaci PowerShellu. Přejděte do složky <drive>\Program Files\Microsoft Azure AD Connect. Spuštěním příkazu .\AzureADConnect.exe /useexistingdatabase spusťte průvodce Azure AD Connect v režimu instalace Použít stávající databázi.

> [!NOTE]
> Použijte přepínač **/useexistingdatabase** pouze pokud databáze již obsahuje data z předchozí instalace služby Azure AD Connect. Pokud přecházíte z místní databáze na úplné databáze serveru SQL Server nebo pokud byl znovu sestaven serveru služby Azure AD Connect a obnovit zálohu SQL databáze ADSync z předchozí instalace služby Azure AD Connect. Pokud jste vytvořili prázdnou databázi a použít pro instalaci, tento krok přeskočte.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
4.  Zobrazí se obrazovka Vítá vás Azure AD Connect. Jakmile odsouhlasíte licenční podmínky a oznámení o ochraně osobních údajů, klikněte na **Pokračovat**.
![Uvítání](./media/how-to-connect-install-existing-database/db3.png)
5.  Na obrazovce **Instalace požadovaných komponent** je povolená možnost **Použít existující SQL Server**. Zadejte název SQL Serveru, který je hostitelem databáze ADSync. Pokud instance stroje SQL použitá k hostování databáze ADSync není na SQL Serveru výchozí instancí, musíte zadat název instance stroje SQL. Dále, pokud není povolené procházení SQL, musíte zadat také číslo portu instance stroje SQL. Příklad:         
![Uvítání](./media/how-to-connect-install-existing-database/db4.png)           

6.  Na obrazovce **Připojení ke službě Azure AD** musíte zadat přihlašovací údaje globálního správce vašeho adresáře služby Azure AD. Je vhodné použít účet ve výchozí doméně onmicrosoft.com. Tento účet slouží jenom k vytvoření účtu služby v Azure AD, a po dokončení průvodce se už nepoužívá.
![Připojení](./media/how-to-connect-install-existing-database/db5.png)
 
7.  Na obrazovce **Připojení adresářů** je uvedená stávající doménová struktura AD nakonfigurovaná pro synchronizaci adresářů, vedle níž je ikona červeného křížku. K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Průvodce Azure AD Connect nemůže načíst přihlašovací údaje účtu služby AD DS uložené v databázi ADSync, protože jsou šifrované a může je dešifrovat pouze předchozí server Azure AD Connect. Klikněte na **Změnit přihlašovací údaje** a zadejte účet služby AD DS pro doménovou strukturu AD.
![Adresáře](./media/how-to-connect-install-existing-database/db6.png)
 
 
8.  V automaticky otevíraném dialogovém okně můžete buď (i) zadat přihlašovací údaje podnikového správce a nechat Azure AD Connect vytvořit účet služby AD DS za vás, nebo (ii) sami vytvořit účet služby AD DS a zadat jeho přihlašovací údaje do Azure AD Connect. Jakmile vyberete jednu z možností a zadáte potřebné přihlašovací údaje, kliknutím na **OK** zavřete automaticky otevírané dialogové okno.
![Uvítání](./media/how-to-connect-install-existing-database/db7.png)
 
 
9.  Po zadání přihlašovacích údajů se ikona červeného křížku změní na ikonu zeleného zaškrtnutí. Klikněte na **Další**.
![Uvítání](./media/how-to-connect-install-existing-database/db8.png)
 
 
10. Na obrazovce **Připraveno ke konfiguraci** klikněte na **Nainstalovat**.
![Uvítání](./media/how-to-connect-install-existing-database/db9.png)
 
 
11. Po dokončení instalace se na serveru Azure AD Connect automaticky zapne pracovní režim. Před vypnutím pracovního režimu se doporučuje zkontrolovat neočekávané změny v konfiguraci serveru a čekajících sestavách. 

## <a name="post-installation-tasks"></a>Úlohy po skončení instalace
Při obnovení zálohy databáze vytvořené ve verzi Azure AD Connect starší než 1.2.65.0 pracovní server automaticky vybere metoda přihlašování z **nekonfigurujte**. Během synchronizace hodnot hash hesel a předvolby zpětný zápis hesla se obnoví, musíte následně změnit metodu přihlašování tak, aby odpovídaly ostatní zásady platit pro váš server synchronizace služby active.  Nepodařilo se dokončit tyto kroky může zabránění uživatelům v přihlašování v by měl tento server bude aktivní.  

Následující tabulku použijte k ověření dalších kroků, které jsou požadovány.

|Funkce|Kroky|
|-----|-----|
|Synchronizace hodnot hash hesel| Synchronizace hodnot Hash hesel a zpětný zápis hesla budou plně obnoveny pro verze služby Azure AD Connect počínaje 1.2.65.0.  Pokud obnovujete, používá starší verzi služby Azure AD Connect, zkontrolujte nastavení možností synchronizace pro tyto funkce zajistit, že aby odpovídaly serveru synchronizace služby active.  Žádné další kroky konfigurace by měl být nezbytné.|
|Federace se službou AD FS|Ověřování Azure bude dál používat zásady služby AD FS nakonfigurovaný pro váš server synchronizace služby active.  Pokud používáte Azure AD Connect ke správě farmu služby AD FS, můžete volitelně změnit metodu přihlašování na federační služba AD FS v rámci přípravy pro pohotovostní server stávají instance synchronizace služby active.   Pokud na serveru synchronizace služby active jsou povolené možnosti zařízení, nakonfigurujte tyto možnosti na tomto serveru spuštěním úlohy "Konfigurovat možnosti zařízení".|
|Předávací ověřování a Desktop jednotného přihlašování|Aktualizujte přihlašovací metoda tak, aby odpovídaly konfiguraci na serveru synchronizace služby active.  Pokud se nedodrží předtím, než na zvyšování úrovně serveru pro primární a předávací ověřování spolu s bezproblémové jednotné přihlašování bude zakázáno a může být váš tenant uzamčený, pokud nemáte přihlášení synchronizace hodnot hash hesel jako záložní možnost. Všimněte si také, že když povolíte předávací ověřování v pracovním režimu, nové ověřovací agent se nainstaluje, registrované a poběží jako vysokou dostupnost agenta, který bude přijímat žádosti o přihlášení.|
|Federace s PingFederate|Ověřování Azure bude nadále používat PingFederate zásady nakonfigurované pro váš server synchronizace služby active.  V rámci přípravy pro pohotovostní server stávají instance synchronizace služby active může volitelně můžete změnit metodu přihlašování do služby PingFederate.  Tento krok může odloženo, dokud nebudete potřebovat pro vytvoření federace s PingFederate další domény.|
## <a name="next-steps"></a>Další postup

- Nyní, když máte nainstalovanou službu Azure AD Connect, můžete si [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).
- Další informace o těchto funkcích, které byly povoleny v rámci instalace: [Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).
- Zjistěte více o těchto běžných tématech: [plánovač a jak aktivovat synchronizaci](how-to-connect-sync-feature-scheduler.md).
- Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
