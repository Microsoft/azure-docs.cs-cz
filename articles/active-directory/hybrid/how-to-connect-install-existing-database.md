---
title: Instalace služby Azure AD Connect pomocí existující databáze ADSync | Dokumenty společnosti Microsoft
description: Toto téma popisuje použití existující databáze ADSync.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245159"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalace nástroje Azure AD Connect s využitím existující databáze ADSync
Azure AD Connect vyžaduje databázi SQL Serveru k ukládání dat. Můžete buď použít výchozí SQL Server 2012 Express LocalDB nainstalovaný s Azure AD Connect nebo použít vlastní plnou verzi SQL. Dříve při instalaci Služby Azure AD Connect byla vždy vytvořena nová databáze s názvem ADSync. S Azure AD Connect verze 1.1.613.0 (nebo po), máte možnost nainstalovat Azure AD Connect tím, že ukazuje na existující databázi ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Výhody použití existující databáze ADSync
Odkazem na existující databázi ADSync:

- S výjimkou informací o pověřeních se konfigurace synchronizace uložená v databázi ADSync (včetně vlastních pravidel synchronizace, konektorů, filtrování a konfigurace volitelných funkcí) automaticky obnoví a použije během instalace. . Přihlašovací údaje používané službou Azure AD Connect k synchronizaci změn s místní službou AD a službou Azure AD jsou šifrované a lze k nim přistupovat jenom na předchozím serveru Azure AD Connect.
- Všechna data identity (spojená s mezerami konektoru a metaverse) a synchronizační soubory cookie uložené v databázi ADSync jsou také obnoveny. Nově nainstalovaný server Azure AD Connect můžete pokračovat v synchronizaci z místa, kde předchozí server Azure AD Connect skončila, namísto nutnosti provést úplnou synchronizaci.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scénáře, kde je výhodné používat existující databázi ADSync
Tyto výhody jsou užitečné v následujících scénářích:


- Máte existující nasazení Azure AD Connect. Váš stávající server Azure AD Connect už nefunguje, ale server SQL obsahující databázi ADSync stále funguje. Můžete nainstalovat nový server Azure AD Connect a nasměrovat ho na existující databázi ADSync. 
- Máte existující nasazení Azure AD Connect. Server SQL obsahující databázi ADSync již nefunguje. Máte však nedávné zálohování databáze. Databázi ADSync můžete nejprve obnovit na nový server SQL. Poté můžete nainstalovat nový server Azure AD Connect a nasměrovat ho do obnovené databáze ADSync.
- Máte existující nasazení Azure AD Connect, které používá LocalDB. Vzhledem k limitu 10 GB uloženému localdb, chcete migrovat do úplné SQL. Databázi ADSync můžete zálohovat z LocalDB a obnovit ji na server SQL. Poté můžete přeinstalovat nový server Azure AD Connect a nasměrovat ho do obnovené databáze ADSync.
- Pokoušíte se nastavit pracovní server a chcete se ujistit, že jeho konfigurace odpovídá konfiguraci aktuálního aktivního serveru. Databázi ADSync můžete zálohovat a obnovit ji na jiný server SQL. Poté můžete přeinstalovat nový server Azure AD Connect a nasměrovat ho do obnovené databáze ADSync.

## <a name="prerequisite-information"></a>Informace o požadavcích

Důležité poznámky, které je třeba vzít na vědomí, než budete pokračovat:

- Ujistěte se, že zkontrolujte předpoklady pro instalaci Azure AD Connect na hardware a požadavky a účet a oprávnění potřebné pro instalaci Azure AD Connect. Oprávnění požadovaná pro instalaci Služby Azure AD Connect pomocí režimu "použít existující databázi" jsou stejná jako vlastní instalace.
- Nasazení služby Azure AD Connect proti existující databázi ADSync je podporováno jenom s úplným SQL. Není podporován a SQL Express LocalDB. Pokud máte existující databázi ADSync v LocalDB, kterou chcete použít, musíte nejprve zálohovat databázi ADSync (LocalDB) a obnovit ji do úplného SQL. Po kterém můžete nasadit Azure AD Connect proti obnovené databázi pomocí této metody.
- Verze služby Azure AD Connect používaná k instalaci musí splňovat následující kritéria:
    - 1.1.613.0 nebo vyšší, And
    - Stejné nebo vyšší než verze Azure AD Connect naposledy použitá s databází ADSync. Pokud je verze Azure AD Connect použitá pro instalaci vyšší než verze naposledy použitá s databází ADSync, může být vyžadována úplná synchronizace.  Úplná synchronizace je vyžadována, pokud mezi těmito dvěma verzemi dochází ke změnám schématu nebo pravidel synchronizace. 
- Použitá databáze ADSync by měla obsahovat stav synchronizace, který je relativně aktuální. Poslední synchronizační aktivita s existující databází ADSync by měla být v posledních třech týdnech.
- Při instalaci Azure AD Connect pomocí metody "použít existující databázi" není zachována metoda přihlášení nakonfigurovaná na předchozím serveru Azure AD Connect. Dále nelze konfigurovat metodu přihlášení během instalace. Metodu přihlášení lze konfigurovat pouze po dokončení instalace.
- Nemůžete mít více serverů Azure AD Connect sdílet stejnou databázi ADSync. Metoda "použít existující databázi" umožňuje znovu použít existující databázi ADSync s novým serverem Azure AD Connect. Nepodporuje sdílení.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Postup instalace azure ad připojení s režimem "použít existující databázi"
1.  Stáhněte si instalační službu Azure AD Connect (AzureADConnect.MSI) na server Windows. Poklikáním na instalační službu Azure AD Connect spusťte instalaci služby Azure AD Connect.
2.  Po dokončení instalace MSI se spustí průvodce Azure AD Connect v režimu expresní instalace. Zavřete obrazovku kliknutím na ikonu Ukončit.
![Uvítání](./media/how-to-connect-install-existing-database/db1.png)
3.  Spusťte nový příkazový řádek nebo novou relaci PowerShellu. Přejděte do složky C:\Program Files\Microsoft Azure Active Directory Connect. Spuštěním příkazu .\AzureADConnect.exe /useexistingdatabase spusťte průvodce Azure AD Connect v režimu instalace Použít stávající databázi.

> [!NOTE]
> Přepínač **/UseExistingDatabase** použijte pouze v případě, že databáze již obsahuje data z dřívější instalace služby Azure AD Connect. Například při přechodu z místní databáze do úplné databáze SQL Serveru nebo při opětovném vytvoření serveru Azure AD Connect a obnovení zálohy SQL databáze ADSync z dřívější instalace služby Azure AD Connect. Pokud je databáze prázdná, to znamená, že neobsahuje žádná data z předchozí instalace Služby Azure AD Connect, tento krok přeskočte.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Zobrazí se obrazovka Vítá vás Azure AD Connect. Jakmile odsouhlasíte licenční podmínky a oznámení o ochraně osobních údajů, klikněte na **Pokračovat**.
   ![Uvítání](./media/how-to-connect-install-existing-database/db3.png)
1. Na obrazovce **Instalace požadovaných komponent** je povolená možnost **Použít existující SQL Server**. Zadejte název SQL Serveru, který je hostitelem databáze ADSync. Pokud instance stroje SQL použitá k hostování databáze ADSync není na SQL Serveru výchozí instancí, musíte zadat název instance stroje SQL. Dále, pokud není povolené procházení SQL, musíte zadat také číslo portu instance stroje SQL. Například:         
   ![Uvítání](./media/how-to-connect-install-existing-database/db4.png)           

1. Na obrazovce **Připojení ke službě Azure AD** musíte zadat přihlašovací údaje globálního správce vašeho adresáře služby Azure AD. Je vhodné použít účet ve výchozí doméně onmicrosoft.com. Tento účet slouží jenom k vytvoření účtu služby v Azure AD, a po dokončení průvodce se už nepoužívá.
   ![Připojení](./media/how-to-connect-install-existing-database/db5.png)
 
1. Na obrazovce **Připojení adresářů** je uvedená stávající doménová struktura AD nakonfigurovaná pro synchronizaci adresářů, vedle níž je ikona červeného křížku. K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Průvodce Azure AD Connect nemůže načíst přihlašovací údaje účtu služby AD DS uložené v databázi ADSync, protože jsou šifrované a může je dešifrovat pouze předchozí server Azure AD Connect. Klikněte na **Změnit přihlašovací údaje** a zadejte účet služby AD DS pro doménovou strukturu AD.
   ![Adresáře](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. V automaticky otevíraném dialogovém okně můžete buď (i) zadat přihlašovací údaje podnikového správce a nechat Azure AD Connect vytvořit účet služby AD DS za vás, nebo (ii) sami vytvořit účet služby AD DS a zadat jeho přihlašovací údaje do Azure AD Connect. Jakmile vyberete jednu z možností a zadáte potřebné přihlašovací údaje, kliknutím na **OK** zavřete automaticky otevírané dialogové okno.
   ![Uvítání](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. Po zadání přihlašovacích údajů se ikona červeného křížku změní na ikonu zeleného zaškrtnutí. Klikněte na **Další**.
   ![Uvítání](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. Na obrazovce **Připraveno ke konfiguraci** klikněte na **Nainstalovat**.
   ![Uvítání](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Po dokončení instalace se na serveru Azure AD Connect automaticky zapne pracovní režim. Před vypnutím pracovního režimu se doporučuje zkontrolovat neočekávané změny v konfiguraci serveru a čekajících sestavách. 

## <a name="post-installation-tasks"></a>Úlohy po skončení instalace
Při obnovení zálohy databáze vytvořené verzí služby Azure AD Connect před verzí 1.2.65.0 pracovní server automaticky vybere metodu přihlášení **Do Not Configure**. Zatímco budou obnoveny předvolby synchronizace hodnot hash hesel a zpětný zápis hesla, je nutné následně změnit metodu přihlášení tak, aby odpovídala ostatním zásadám, které jsou platné pro aktivní synchronizační server.  Pokud tyto kroky nedokončíte, může dojít k tomu, že se uživatelé nebudou přihlašovat, pokud bude tento server aktivní.  

V následující tabulce ověřte všechny další požadované kroky.

|Funkce|Kroky|
|-----|-----|
|Synchronizace hodnot hash hesel| Nastavení synchronizace hash hesel a zpětný zápis hesla jsou plně obnovena pro verze Azure AD Connect počínaje 1.2.65.0.  Pokud obnovení pomocí starší verze Azure AD Connect, zkontrolujte nastavení možnosti synchronizace pro tyto funkce, aby zajistily, že odpovídají aktivní synchronizační server.  Žádné další kroky konfigurace by neměly být nutné.|
|Federace se službou AD FS|Ověřování Azure bude nadále používat zásady služby AD FS nakonfigurované pro aktivní synchronizační server.  Pokud ke správě farmy služby AD FS používáte službu Azure AD Connect, můžete volitelně změnit metodu přihlášení na federaci služby AD FS v rámci přípravy na to, že se váš pohotovostní server stane aktivní instancí synchronizace.   Pokud jsou na aktivním synchronizačním serveru povoleny možnosti zařízení, nakonfigurujte tyto možnosti na tomto serveru spuštěním úlohy Konfigurovat možnosti zařízení.|
|Předávací ověřování a jednotné přihlašování na ploše|Aktualizujte metodu přihlášení tak, aby odpovídala konfiguraci na aktivním synchronizačním serveru.  Pokud to není dodrženo před povýšením serveru na primární, předávací ověřování spolu s bezproblémovým jednotném přihlášením bude zakázáno a váš tenant může být uzamčen, pokud nemáte synchronizaci hodnoty hash hesla jako možnost záložního přihlášení. Všimněte si také, že když povolíte předávací ověřování v pracovním režimu, bude nainstalován nový agent ověřování, zaregistruje se a bude spuštěn jako agent s vysokou dostupností, který bude přijímat požadavky na přihlášení.|
|Federace s PingFederate|Ověřování Azure bude nadále používat zásady PingFederate nakonfigurované pro aktivní synchronizační server.  Volitelně můžete změnit metodu přihlášení na PingFederate v rámci přípravy na pohotovostní server stává aktivní instanci synchronizace.  Tento krok může být odložen, dokud nebudete muset federate další domény s PingFederate.|

## <a name="next-steps"></a>Další kroky

- Nyní, když máte nainstalovanou službu Azure AD Connect, si můžete [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).
- Podrobněji se seznamte s těmito funkcemi, které byly povoleny v rámci instalace: [Prevence náhodného smazání](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).
- Zjistěte více o těchto běžných tématech: [plánovač a spouštění synchronizace](how-to-connect-sync-feature-scheduler.md).
- Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
