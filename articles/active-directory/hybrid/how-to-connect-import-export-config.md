---
title: Jak importovat a exportovat nastavení konfigurace Azure AD Connect
description: V tomto dokumentu jsou popsány nejčastější dotazy k zřizování cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a13236294f74bbe4bdaf8c1a30408afad09d9796
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225128"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Import a export nastavení konfigurace Azure AD Connect (Public Preview) 

Nasazení Azure AD Connect se liší od instalace s jednou doménovou strukturou, do komplexních nasazení, která se synchronizují napříč více doménovými strukturami pomocí vlastních pravidel synchronizace. Vzhledem k velkému počtu možností konfigurace a mechanismů je důležité pochopit, jaká nastavení jsou platná, a umožnit tak rychlé nasazení serveru se stejnou konfigurací. Tato funkce zavádí možnost zařadit do katalogu konfiguraci daného synchronizačního serveru a importovat nastavení do nového nasazení. Různé snímky nastavení synchronizace je možné porovnat a snadno vizualizovat rozdíly mezi dvěma servery nebo stejným serverem v čase. 

Pokaždé, když se konfigurace změní z Průvodce Azure AD Connect, nový soubor nastavení JSON s časovým razítkem se automaticky vyexportuje do **%ProgramData%\AADConnect**. Nastavení název souboru má podobu **použitou-SynchronizationPolicy-*. JSON** , kde poslední část názvu souboru je časové razítko. 

>[!IMPORTANT]
> Automaticky se exportují jenom změny provedené Azure AD Connect. Všechny změny provedené pomocí PowerShellu, Synchronization Service Manager nebo editoru pravidel synchronizace, se musí na vyžádání exportovat podle potřeby, aby se udržovala aktuální kopie. Export na vyžádání se dá použít také k umístění kopie nastavení v zabezpečeném umístění pro účely zotavení po havárii. 

## <a name="exporting-azure-ad-connect-settings"></a>Export nastavení Azure AD Connect 

Chcete-li zobrazit souhrn nastavení konfigurace, otevřete nástroj Azure AD Connect a vyberte další úkol s názvem: zobrazení nebo export aktuální konfigurace. Zobrazí se stručný přehled vašich nastavení a možnost exportovat úplnou konfiguraci serveru. 

Ve výchozím nastavení se nastavení exportují do **%ProgramData%\AADConnect**, ale můžete se rozhodnout uložit nastavení do chráněného umístění, aby se zajistila dostupnost v případě havárie. Nastavení se exportují pomocí formátu souboru JSON a neměli byste je vytvářet ani upravovat, aby se zajistila logická konzistence. Import ručně vytvořeného nebo upravovaného souboru není podporován a může vést k neočekávaným výsledkům. 

## <a name="importing-azure-ad-connect-settings"></a>Import nastavení Azure AD Connect 

Pokud chcete importovat dříve exportovaná nastavení, udělejte toto:
 
1. Nainstalujte **Azure AD Connect** na nový server. 
2. Jako **úvodní** stránku vyberte **přizpůsobit** možnost. 
3. Klikněte na **importovat nastavení synchronizace**. Vyhledejte dříve exportovaný soubor nastavení JSON.  
4. Klikněte na **Install** (Nainstalovat).

![Nainstalovat komponenty](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Přepište prosím na této stránce nastavení, jako je třeba použití SQL Server namísto LocalDB nebo použití existujícího účtu služby místo výchozích instančních služeb atd. Tato nastavení se neimportují ze souboru nastavení konfigurace, ale existují pro informace a účely porovnání.

### <a name="import-installation-experience"></a>Importovat prostředí pro instalaci 

Naimportování prostředí pro instalaci je záměrně jednoduché s minimálními vstupy od uživatele až po snadné poskytování reprodukovatelnosti stávajícího serveru.  

Tady jsou jediné změny, které je možné provést během instalace. Všechny ostatní změny lze provést po instalaci z Průvodce Azure AD Connect.: 
- **Azure Active Directory přihlašovací údaje**   – ve výchozím nastavení je navržený název účtu pro globálního správce Azure, který se používá ke konfiguraci původního serveru **MUST**, a   Pokud chcete informace synchronizovat s novým adresářem, musíte ho změnit.
- **Přihlášení uživatele**   – ve výchozím nastavení jsou vybrané možnosti přihlášení nakonfigurované pro původní server a automaticky se zobrazí výzva k zadání přihlašovacích údajů a dalších informací potřebných během konfigurace. Ve výjimečných případech může být potřeba nastavit server s různými možnostmi, aby nedošlo ke změně chování aktivního serveru. Jinak stačí stisknout tlačítko Další, aby se používalo stejné nastavení. 
- **Přihlašovací údaje**   místního adresáře – pro každý místní adresář zahrnutý do nastavení synchronizace musíte zadat přihlašovací údaje pro vytvoření synchronizačního účtu nebo zadání předem vytvořeného vlastního účtu synchronizace. Tento postup se shoduje s čistým prostředím instalace s výjimkou, že nemůžete přidat ani odebrat adresáře. 
- **Možnosti konfigurace**   – stejně jako u čisté instalace se můžete rozhodnout, jestli chcete nakonfigurovat počáteční nastavení pro spuštění automatické synchronizace nebo povolení pracovního režimu. Hlavním rozdílem je, že pracovní režim je ve výchozím nastavení záměrně povolený, aby bylo možné porovnat výsledky konfigurace a synchronizace před aktivně exportováním výsledků do Azure. 

![Připojení adresářů](media/how-to-connect-import-export-config/import2.png)

>[!NOTE]
>V primární roli může být jenom jeden synchronizační Server a aktivně se exportují změny konfigurace do Azure. Všechny ostatní servery musí být umístěny v pracovním režimu. 

## <a name="migrating-settings-from-an-existing-server"></a>Migrují se nastavení z existujícího serveru. 

Pokud existující server nepodporuje správu nastavení, můžete zvolit upgrade serveru na místě nebo migrovat nastavení pro použití na novém přípravném serveru.  

Migrace vyžaduje spuštění skriptu PowerShellu, který extrahuje stávající nastavení pro použití v nové instalaci.Tato metoda se doporučuje pro zařazení nastavení stávajícího serveru do katalogu a pak je použít na nově instalovaný přípravný Server.Porovnáním nastavení původního serveru s nově vytvořeným serverem se rychle vizualizují změny mezi servery.Pokud chcete zajistit, že se nevyžaduje žádná další konfigurace, postupujte stejně jako v procesu certifikace vaší organizace.  

### <a name="migration-process"></a>Proces migrace 
K migraci nastavení postupujte takto:

1. Otevřete na novém přípravném serveru program cmd jako správce.
2. Extrahujte **AzureADConnect.msi** spuštěním následujícího: `msiexec /a msifile/qb TARGETDIR=targetpath` kde **msifile** je adresa MSI a **targetPath** je adresář, do kterého chcete soubory extrahovat.
   
   Případě`msiexec /a "C:\Holding\AzureADConnect.msi" TARGETDIR="C:\extractedfiles"`
3. Zkopírujte **MigrateSettings.ps1** z adresáře Microsoft Azure AD Connect\Tools do umístění na existujícím serveru.  Například C:\setup.  Kde Setup je adresář, který byl vytvořen na stávajícím serveru. 
![Připojení adresářů](media/how-to-connect-import-export-config/migrate1.png)

4. Spusťte skript, jak je znázorněno níže, a uložte celý konfigurační adresář serveru nižší úrovně. Zkopírujte tento adresář do nového přípravného serveru. Všimněte si, že je potřeba zkopírovat celou složku **exportovanou-ServerConfiguration-*** na nový server. 
 ![Připojení adresářů](media/how-to-connect-import-export-config/migrate2.png)

 ![Připojení adresářů](media/how-to-connect-import-export-config/migrate3.png)

5. Spusťte **Azure AD Connect** dvojitým kliknutím na ikonu na ploše. Přijmout smlouvu EULA, na další stránce klikněte na tlačítko **přizpůsobit** . 
6. Zaškrtněte políčko **importovat nastavení synchronizace** a klikněte na tlačítko **Procházet** a přejděte do složky zkopírované do exportovaného-ServerConfiguration-* a vyberte MigratedPolicy.jspro import migrovaných nastavení.
 ![Připojení adresářů](media/how-to-connect-import-export-config/migrate4.png)

7. Chcete-li porovnat migrované nastavení s nastavením použít, vyhledejte nejnovější **migrované SynchronizationPolicy-*. JSON** a **aplikované – SynchronizationPolicy-*. JSON** (* je časové razítko) v rámci **%ProgramData%\AADConnect**. K porovnání parity použijte svůj oblíbený nástroj pro porovnání souborů. 

## <a name="post-installation-verification"></a>Ověření po instalaci 

Porovnání původního importovaného souboru nastavení s exportovaným souborem nastavení nově nasazeného serveru je důležitým krokem při porozumění případných rozdílech mezi zamýšleným a výsledným nasazením. Použití vaší oblíbené aplikace pro porovnání textu vedle sebe poskytuje okamžitou vizualizaci, která rychle zvýrazní všechny požadované nebo náhodné změny. I když je mnoho dříve ručních kroků konfigurace eliminováno, měli byste i nadále postupovat podle procesu certifikace vaší organizace, abyste měli jistotu, že se nevyžaduje žádná další konfigurace. Tato konfigurace může nastat, pokud použijete Pokročilá nastavení, která nejsou aktuálně zachycena ve verzi Public Preview správy nastavení. 

Mezi známá omezení patří následující: 
- **Pravidla synchronizace**   – Priorita vlastního pravidla musí být v rezervovaném rozsahu 0-99, aby nedocházelo ke konfliktům se standardními pravidly Microsoftu. Umístění vlastního pravidla mimo vyhrazený rozsah může mít za následek, že se vaše vlastní pravidlo posune přibližně po přidání standardních pravidel do konfigurace. K podobnému problému dojde, pokud vaše konfigurace obsahuje upravená standardní pravidla. Změna standardního pravidla se důrazně nedoporučuje a umístění pravidla je pravděpodobně nesprávné. Zpětný zápis zařízení – tato nastavení jsou v katalogu, ale v současné době se při konfiguraci nepoužívají. Pokud byl pro původní server povolen zpětný zápis zařízení, je nutné ručně nakonfigurovat funkci na nově nasazeném serveru. 
- **Synchronizované typy objektů**   – i když je možné omezit seznam synchronizovaných typů objektů (uživatelů, kontaktů, skupin atd.) pomocí Synchronization Service Manager, není tato funkce v současnosti podporovaná prostřednictvím nastavení synchronizace. Po dokončení instalace je nutné ručně znovu použít pokročilou konfiguraci. 
- **Vlastní profily spuštění**   – Přestože je možné změnit výchozí sadu profilů spuštění pomocí Synchronization Service Manager, tato funkce se v současné době nepodporuje prostřednictvím nastavení synchronizace. Po dokončení instalace je nutné ručně znovu použít pokročilou konfiguraci. 
- **Konfigurace zřizovací hierarchie**   – Tato pokročilá funkce Synchronization Service Manager není podporována prostřednictvím nastavení synchronizace a je nutné ji po dokončení počátečního nasazení ručně změnit. 
- Ověřování AD FS a **PingFederate**   – metody přihlašování přidružené k těmto funkcím ověřování budou automaticky vybrány, ale musíte interaktivně zadat všechny další požadované parametry konfigurace. 

 ## <a name="next-steps"></a>Další kroky

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
