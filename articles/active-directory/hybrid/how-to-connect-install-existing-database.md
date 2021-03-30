---
title: Instalace Azure AD Connect pomocí existující databáze ADSync | Microsoft Docs
description: Toto téma popisuje, jak používat existující databázi ADSync.
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
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8324b82a05d7e78772e0b0b6de3a9bfaa183411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91265387"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalace nástroje Azure AD Connect s využitím existující databáze ADSync
Azure AD Connect vyžaduje, aby databáze SQL Server ukládala data. Můžete buď použít výchozí SQL Server 2012 Express LocalDB nainstalované s Azure AD Connect nebo použít vlastní plnou verzi SQL. Dříve při instalaci Azure AD Connect byla vždy vytvořena nová databáze s názvem ADSync. U Azure AD Connect verze 1.1.613.0 (nebo po) máte možnost nainstalovat Azure AD Connect tak, že na ni přejdete na existující databázi ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Výhody použití existující databáze ADSync
Tak, že přejdete na existující databázi ADSync:

- Výjimkou jsou informace o přihlašovacích údajích, konfigurace synchronizace uložená v databázi ADSync (včetně vlastních pravidel synchronizace, konektorů, filtrování a volitelných funkcí) se při instalaci automaticky obnoví a použije. Přihlašovací údaje, které Azure AD Connect používají k synchronizaci změn s místními službami AD a službou Azure AD, jsou zašifrované a můžou k nim mít jenom předchozí Azure AD Connect Server.
- Také se obnoví všechna data identity (přidružená k prostorům konektoru a úložišti Metaverse) a synchronizačním souborům cookie uloženým v databázi ADSync. Nově nainstalovaný Azure AD Connect Server se může i nadále synchronizovat z místa, kde předchozí server Azure AD Connect vypnul, místo abyste museli provádět úplnou synchronizaci.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scénáře použití existující databáze ADSync jsou užitečné
Tyto výhody jsou užitečné v následujících scénářích:


- Máte existující nasazení Azure AD Connect. Stávající server Azure AD Connect nadále nepracuje, ale server SQL obsahující databázi ADSync stále funguje. Můžete nainstalovat nový server Azure AD Connect a nasměrovat ho na stávající databázi ADSync. 
- Máte existující nasazení Azure AD Connect. SQL Server obsahující databázi ADSync už nefunguje. Máte ale k dispozici poslední zálohu databáze. Databázi ADSync můžete nejdřív obnovit na nový SQL Server. Potom můžete nainstalovat nový server Azure AD Connect a nasměrovat ho na obnovenou databázi ADSync.
- Máte existující nasazení Azure AD Connect, které používá LocalDB. V důsledku limitu 10 GB, který je LocalDB, byste chtěli migrovat na úplný SQL. Databázi ADSync můžete zálohovat z LocalDB a obnovit ji na SQL Server. Potom můžete nový server Azure AD Connect přeinstalovat a nasměrovat ho na obnovenou databázi ADSync.
- Pokoušíte se nastavit pracovní server a chce se ujistit, že jeho konfigurace odpovídá aktuálnímu aktivnímu serveru. Databázi ADSync můžete zálohovat a obnovit ji na jiný SQL Server. Potom můžete nový server Azure AD Connect přeinstalovat a nasměrovat ho na obnovenou databázi ADSync.

## <a name="prerequisite-information"></a>Informace o požadavcích

Důležité poznámky, které byste si poznamenali, než budete pokračovat:

- Nezapomeňte si projít předpoklady pro instalaci Azure AD Connect na hardware a požadavky a účet a oprávnění potřebné k instalaci Azure AD Connect. Oprávnění potřebná k instalaci Azure AD Connect pomocí režimu "použít existující databázi" je stejné jako instalace "vlastní".
- Nasazení Azure AD Connect proti stávající databázi ADSync je podporováno pouze s úplným SQL. Pro SQL Express LocalDB se nepodporuje. Pokud máte existující databázi ADSync v LocalDB, kterou chcete použít, musíte nejdřív zálohovat databázi ADSync (LocalDB) a obnovit ji do úplného SQL. Potom můžete nasadit Azure AD Connect pro obnovenou databázi pomocí této metody.
- Verze Azure AD Connect používaná pro instalaci musí splňovat následující kritéria:
    - 1.1.613.0 nebo vyšší a
    - Stejná nebo vyšší než verze Azure AD Connect naposledy použitá s databází ADSync. Pokud je verze Azure AD Connect použitá pro instalaci vyšší než verze naposledy použité v databázi ADSync, může být vyžadována Úplná synchronizace.  Úplná synchronizace je vyžadována, pokud se mezi těmito dvěma verzemi mění pravidlo schématu nebo synchronizace. 
- Použitá databáze ADSync by měla obsahovat stav synchronizace, který je relativně nedávné. Poslední aktivita synchronizace s existující databází ADSync by měla být během posledních tří týdnů.
- Při instalaci Azure AD Connect pomocí metody použít existující databázi se nezachová Metoda přihlašování nakonfigurovaná na předchozím serveru Azure AD Connect. Dále nemůžete konfigurovat metodu přihlašování během instalace. Metodu přihlašování můžete nakonfigurovat pouze po dokončení instalace.
- Nemůžete mít více Azure AD Connectch serverů sdílejí stejnou databázi ADSync. Metoda "použít existující databázi" umožňuje znovu použít stávající databázi ADSync pomocí nového serveru Azure AD Connect. Sdílení nepodporuje.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Postup instalace Azure AD Connect s režimem "používání existující databáze"
1.  Stáhněte si instalační program Azure AD Connect (AzureADConnect.MSI) do Windows serveru. Dvojím kliknutím na instalační program Azure AD Connect spusťte instalaci Azure AD Connect.
2.  Po dokončení instalace MSI se spustí průvodce Azure AD Connect v režimu expresní instalace. Zavřete obrazovku kliknutím na ikonu Ukončit.
![Snímek obrazovky zobrazující stránku "Vítejte v Azure A D Connect" s "expresním nastavením" v levé nabídce, která je zvýrazněna.](./media/how-to-connect-install-existing-database/db1.png)
3.  Spusťte nový příkazový řádek nebo novou relaci PowerShellu. Přejděte do složky C:\Program Files\Microsoft Azure Active Directory Connect. Spuštěním příkazu .\AzureADConnect.exe /useexistingdatabase spusťte průvodce Azure AD Connect v režimu instalace Použít stávající databázi.

> [!NOTE]
> Přepínač **/UseExistingDatabase** použijte pouze v případě, že databáze již obsahuje data z dřívější instalace Azure AD Connect. Například při přechodu z místní databáze do úplné SQL Server databáze nebo při opětovném vytvoření Azure AD Connectho serveru a obnovení zálohy SQL databáze ADSync ze starší instalace Azure AD Connect. Pokud je databáze prázdná, to znamená, že neobsahuje žádná data z předchozí instalace Azure AD Connect, přeskočte tento krok.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Zobrazí se obrazovka Vítá vás Azure AD Connect. Jakmile odsouhlasíte licenční podmínky a oznámení o ochraně osobních údajů, klikněte na **Pokračovat**.
   ![Snímek obrazovky zobrazující stránku Vítá vás Azure A D Connect](./media/how-to-connect-install-existing-database/db3.png)
1. Na obrazovce **Instalace požadovaných komponent** je povolená možnost **Použít existující SQL Server**. Zadejte název SQL Serveru, který je hostitelem databáze ADSync. Pokud instance stroje SQL použitá k hostování databáze ADSync není na SQL Serveru výchozí instancí, musíte zadat název instance stroje SQL. Dále, pokud není povolené procházení SQL, musíte zadat také číslo portu instance stroje SQL. Například:         
   ![Snímek obrazovky zobrazující stránku nainstalovat požadované součásti](./media/how-to-connect-install-existing-database/db4.png)           

1. Na obrazovce **Připojení ke službě Azure AD** musíte zadat přihlašovací údaje globálního správce vašeho adresáře služby Azure AD. Je vhodné použít účet ve výchozí doméně onmicrosoft.com. Tento účet slouží jenom k vytvoření účtu služby v Azure AD, a po dokončení průvodce se už nepoužívá.
   ![Připojení](./media/how-to-connect-install-existing-database/db5.png)
 
1. Na obrazovce **Připojení adresářů** je uvedená stávající doménová struktura AD nakonfigurovaná pro synchronizaci adresářů, vedle níž je ikona červeného křížku. K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Průvodce Azure AD Connect nemůže načíst přihlašovací údaje účtu služby AD DS uložené v databázi ADSync, protože jsou šifrované a může je dešifrovat pouze předchozí server Azure AD Connect. Klikněte na **Změnit přihlašovací údaje** a zadejte účet služby AD DS pro doménovou strukturu AD.
   ![Adresáře](./media/how-to-connect-install-existing-database/db6.png)
 
1. V automaticky otevíraném dialogovém okně můžete buď (i) zadat přihlašovací údaje podnikového správce a nechat Azure AD Connect vytvořit účet služby AD DS za vás, nebo (ii) sami vytvořit účet služby AD DS a zadat jeho přihlašovací údaje do Azure AD Connect. Jakmile vyberete jednu z možností a zadáte potřebné přihlašovací údaje, kliknutím na **OK** zavřete automaticky otevírané dialogové okno.
   ![Snímek obrazovky, který zobrazuje místní dialog "účet doménové struktury D" s vybraným příkazem Vytvořit nový účet D.](./media/how-to-connect-install-existing-database/db7.png)
 
1. Po zadání přihlašovacích údajů se ikona červeného křížku změní na ikonu zeleného zaškrtnutí. Klikněte na **Next** (Další).
   ![Snímek obrazovky, na kterém se zobrazuje stránka "připojit adresáře"](./media/how-to-connect-install-existing-database/db8.png)
 
1. Na obrazovce **Připraveno ke konfiguraci** klikněte na **Nainstalovat**.
   ![Uvítání](./media/how-to-connect-install-existing-database/db9.png)
 
1. Po dokončení instalace se na serveru Azure AD Connect automaticky zapne pracovní režim. Před vypnutím pracovního režimu se doporučuje zkontrolovat neočekávané změny v konfiguraci serveru a čekajících sestavách. 

## <a name="post-installation-tasks"></a>Úlohy po skončení instalace
Při obnovení zálohy databáze vytvořené ve verzi Azure AD Connect před 1.2.65.0 bude pracovní server automaticky vybírat metodu přihlašování, kterou **nekonfigurujete**. I když budou obnoveny předvolby hodnoty hash hesla a zpětného zápisu hesel, musíte následně změnit metodu přihlašování tak, aby odpovídala ostatním zásadám platným pro aktivní synchronizační Server.  Při provádění těchto kroků se může stát, že se uživatelé nebudou přihlašovat, aby tento server byl aktivní.  

Následující tabulku použijte k ověření dalších potřebných kroků.

|Funkce|Postup|
|-----|-----|
|Synchronizace hodnot hash hesel| nastavení synchronizace hodnoty hash hesla a zpětného zápisu hesla jsou plně obnovena pro verze Azure AD Connect počínaje verzí 1.2.65.0.  Při obnovení pomocí starší verze Azure AD Connect zkontrolujte nastavení možností synchronizace pro tyto funkce, abyste se ujistili, že odpovídají aktivnímu synchronizačnímu serveru.  Není nutné provádět žádné další kroky konfigurace.|
|Federace se službou AD FS|Ověřování Azure budou dál používat zásady AD FS nakonfigurované pro aktivní synchronizační Server.  Pokud ke správě farmy AD FS používáte Azure AD Connect, můžete volitelně změnit metodu přihlašování na AD FS federaci při přípravě na pohotovostní server, který se stane aktivní instancí synchronizace.   Pokud jsou na aktivním synchronizačním serveru povolené možnosti zařízení, nakonfigurujte tyto možnosti na tomto serveru tak, že spustíte úlohu konfigurace možností zařízení.|
|Předávací ověřování a samostatná desktopová Sign-On|Aktualizujte metodu přihlašování tak, aby odpovídala konfiguraci aktivního synchronizačního serveru.  Pokud se to nedoporučuje před zvýšením úrovně serveru na primární, předávající ověřování společně s bezproblémovém jednotným přihlašováním se zakáže a váš tenant může být uzamčený, pokud nemáte synchronizaci hodnot hash hesel jako záložní možnost přihlášení. Všimněte si také, že pokud povolíte předávací ověřování v pracovním režimu, bude nainstalován nový agent ověřování, který bude zaregistrován a spuštěn jako agent s vysokou dostupností, který bude přijímat žádosti o přihlášení.|
|Federace s PingFederate|Ověřování Azure bude i nadále používat zásady PingFederate nakonfigurované pro aktivní synchronizační Server.  Volitelně můžete změnit způsob přihlašování, aby se PingFederate při přípravě na pohotovostní server, který se stane aktivní instancí synchronizace.  Tento krok může být odložený, dokud nebudete muset federovat další domény pomocí PingFederate.|

## <a name="next-steps"></a>Další kroky

- Nyní, když máte nainstalovanou službu Azure AD Connect, si můžete [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).
- Podrobněji se seznamte s těmito funkcemi, které byly povoleny v rámci instalace: [Prevence náhodného smazání](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).
- Zjistěte více o těchto běžných tématech: [plánovač a spouštění synchronizace](how-to-connect-sync-feature-scheduler.md).
- Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
