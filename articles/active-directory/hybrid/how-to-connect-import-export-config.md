---
title: Jak importovat a exportovat nastavení konfigurace Azure AD Connect
description: Tento článek popisuje Nejčastější dotazy k zřizování cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681951"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Import a export nastavení konfigurace Azure AD Connect 

Azure Active Directory (Azure AD) připojení nasazení se liší od instalace s jednou doménovou strukturou až po složitá nasazení, která se synchronizují napříč více doménovými strukturami pomocí vlastních pravidel synchronizace. Z důvodu velkého počtu možností konfigurace a mechanismů je důležité pochopit, jaká nastavení jsou platná, a umožnit tak rychlé nasazení serveru se stejnou konfigurací. Tato funkce zavádí možnost zařadit do katalogu konfiguraci daného synchronizačního serveru a importovat nastavení do nového nasazení. Různé snímky nastavení synchronizace je možné porovnat a snadno vizualizovat rozdíly mezi dvěma servery nebo stejným serverem v čase.

Pokaždé, když se konfigurace změní z Průvodce Azure AD Connect, nový soubor nastavení JSON s časovým razítkem se automaticky vyexportuje do **%ProgramData%\AADConnect**. Název souboru nastavení je ve formátu **použité-SynchronizationPolicy-*. JSON**, kde poslední část názvu souboru je časové razítko.

> [!IMPORTANT]
> Automaticky se exportují jenom změny provedené Azure AD Connect. Všechny změny provedené pomocí PowerShellu, Synchronization Service Manager, nebo editor pravidel synchronizace, se musí na vyžádání exportovat podle potřeby, aby bylo možné udržovat aktuální kopii. Export na vyžádání se dá použít také k umístění kopie nastavení v zabezpečeném umístění pro účely zotavení po havárii.

## <a name="export-azure-ad-connect-settings"></a>Exportovat nastavení Azure AD Connect 

Chcete-li zobrazit souhrn nastavení konfigurace, otevřete nástroj Azure AD Connect a vyberte další úkol s názvem **zobrazení nebo exportovat aktuální konfiguraci**. Zobrazí se stručný přehled vašich nastavení a možnost exportovat úplnou konfiguraci serveru.

Ve výchozím nastavení jsou nastavení exportována do **%ProgramData%\AADConnect**. Můžete také zvolit uložení nastavení do chráněného umístění, aby se zajistila dostupnost, pokud dojde k havárii. Nastavení jsou exportována pomocí formátu souboru JSON a není nutné je ručně vytvořit nebo upravit, aby bylo zajištěno logické konzistence. Import ručně vytvořeného nebo upravovaného souboru není podporován a může vést k neočekávaným výsledkům.

## <a name="import-azure-ad-connect-settings"></a>Importovat nastavení Azure AD Connect

Import dříve exportovaných nastavení:
 
1. Nainstalujte **Azure AD Connect** na nový server.
1. Jako **úvodní** stránku vyberte možnost **přizpůsobit** .
1. Vyberte **importovat nastavení synchronizace**. Vyhledejte dříve exportovaný soubor nastavení JSON.
1. Vyberte **Nainstalovat**.

   ![Snímek obrazovky zobrazující obrazovku instalovat požadované součásti](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Přepsat nastavení na této stránce, jako je například použití SQL Server namísto LocalDB nebo použití existujícího účtu služby namísto výchozího atributu VSA. Tato nastavení nejsou importovaná ze souboru nastavení konfigurace. Jsou zde dostupné informace a účely porovnání.

### <a name="import-installation-experience"></a>Importovat prostředí pro instalaci 

Prostředí pro import je záměrně zachované s minimálními vstupy od uživatele, aby snadno poskytovala reprodukovatelnost stávajícího serveru.

Tady jsou jediné změny, které je možné provést během instalace. Všechny ostatní změny lze provést po instalaci z Průvodce Azure AD Connect:
- **Azure Active Directory přihlašovací údaje**: ve výchozím nastavení se navrhne název účtu pro globálního správce Azure, který se používá ke konfiguraci původního serveru.    Pokud chcete synchronizovat informace s novým adresářem, je nutné ho změnit.
- **Přihlášení uživatele**: ve výchozím nastavení jsou vybrané přihlašovací možnosti nakonfigurované pro původní server a automaticky se zobrazí výzva k zadání přihlašovacích údajů nebo další informace, které jsou potřeba při konfiguraci. Ve výjimečných případech může být potřeba nastavit server s různými možnostmi, aby nedošlo ke změně chování aktivního serveru. V opačném případě vyberte možnost **Další** a použijte stejné nastavení.
- **Přihlašovací údaje pro místní adresář**: pro každý místní adresář zahrnutý do nastavení synchronizace musíte zadat přihlašovací údaje pro vytvoření synchronizačního účtu nebo zadání předem vytvořeného vlastního účtu synchronizace. Tento postup se shoduje s čistým prostředím instalace s výjimkou, že nemůžete přidat nebo odebrat adresáře.
- **Možnosti konfigurace**: stejně jako u čisté instalace můžete nakonfigurovat počáteční nastavení, jestli chcete spustit automatickou synchronizaci nebo povolit pracovní režim. Hlavním rozdílem je, že pracovní režim je ve výchozím nastavení záměrně povolený, aby bylo možné porovnat výsledky konfigurace a synchronizace před aktivně exportováním výsledků do Azure.

![Snímek obrazovky, na kterém se zobrazuje obrazovka připojit adresáře](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> V primární roli může být jenom jeden synchronizační Server a aktivně se exportují změny konfigurace do Azure. Všechny ostatní servery musí být umístěny v pracovním režimu.

## <a name="migrate-settings-from-an-existing-server"></a>Migrace nastavení z existujícího serveru 

Pokud existující server nepodporuje správu nastavení, můžete zvolit upgrade serveru na místě nebo migrovat nastavení pro použití na novém přípravném serveru.

Migrace vyžaduje spuštění skriptu PowerShellu, který extrahuje stávající nastavení pro použití v nové instalaci.Tuto metodu použijte, chcete-li zařadit do katalogu nastavení stávajícího serveru a pak je použít na nově nainstalovaný pracovní server.Porovnáním nastavení původního serveru s nově vytvořeným serverem se rychle vizualizují změny mezi servery.Pokud chcete zajistit, že se nevyžaduje žádná další konfigurace, postupujte stejně jako v procesu certifikace vaší organizace.

### <a name="migration-process"></a>Proces migrace 
Postup migrace nastavení:

1. Spusťte **AzureADConnect.msi** na novém přípravném serveru a zastavte **úvodní** stránku Azure AD Connect.

1. Zkopírujte **MigrateSettings.ps1** z adresáře Microsoft Azure AD Connect\Tools do umístění na existujícím serveru. Příkladem je C:\setup, kde instalační program je adresář, který byl vytvořen na stávajícím serveru.

   ![Snímek obrazovky zobrazující adresáře Azure AD Connect](media/how-to-connect-import-export-config/migrate1.png)

1. Spusťte skript, jak je znázorněno zde, a uložte celý konfigurační adresář serveru nižší úrovně. Zkopírujte tento adresář do nového přípravného serveru. Je nutné zkopírovat celou složku **exportovanou-ServerConfiguration-*** na nový server.

   ![Snímek obrazovky, který zobrazuje skript v prostředí Windows PowerShell. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Snímek obrazovky, který ukazuje kopírování exportované složky-ServerConfiguration-*.](media/how-to-connect-import-export-config/migrate3.png)

1. Spusťte **Azure AD Connect** dvojitým kliknutím na ikonu na ploše. Přijměte licenční podmínky pro software společnosti Microsoft a na další stránce vyberte možnost **přizpůsobit**.
1. Zaškrtněte políčko **importovat nastavení synchronizace** . Vyberte **Procházet** a přejděte do složky zkopírované do exportovaného-ServerConfiguration-*. Vyberte MigratedPolicy.jspro import migrovaných nastavení.

   ![Snímek obrazovky zobrazující možnost importovat nastavení synchronizace](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Ověření po instalaci 

Porovnání původního importovaného souboru nastavení se souborem exportovaných nastavení nově nasazeného serveru je důležitým krokem při porozumění případných rozdílech mezi zamýšleným a výsledným nasazením. Použití vaší oblíbené aplikace pro porovnání textu vedle sebe poskytuje okamžitou vizualizaci, která rychle zvýrazní všechny požadované nebo náhodné změny.

I když je mnoho dříve ručních kroků konfigurace eliminováno, měli byste i nadále postupovat podle procesu certifikace vaší organizace, abyste měli jistotu, že se nevyžaduje žádná další konfigurace. Tato konfigurace může nastat, pokud použijete upřesňující nastavení, která nejsou aktuálně zachycena v této verzi správy nastavení.

Tady jsou známá omezení:
- **Pravidla synchronizace**: priorita vlastního pravidla musí být v rezervovaném rozsahu 0 až 99, aby nedocházelo ke konfliktům se standardními pravidly Microsoftu. Umístění vlastního pravidla mimo vyhrazený rozsah může mít za následek, že se vaše vlastní pravidlo posune přibližně po přidání standardních pravidel do konfigurace. K podobnému problému dojde, pokud vaše konfigurace obsahuje upravená standardní pravidla. Změna standardního pravidla se nedoporučuje a umístění pravidla bude pravděpodobně nesprávné.
- **Zpětný zápis zařízení**: Tato nastavení se zaúčtují do katalogu. V tuto chvíli se v průběhu konfigurace nepoužívají. Pokud byl pro původní server povolen zpětný zápis zařízení, je nutné ručně nakonfigurovat funkci na nově nasazeném serveru.
- **Synchronizované typy objektů**: Přestože je možné omezit seznam synchronizovaných typů objektů (jako jsou uživatelé, kontakty a skupiny) pomocí Synchronization Service Manager, tato funkce se v současné době nepodporuje prostřednictvím nastavení synchronizace. Po dokončení instalace je nutné ručně znovu použít pokročilou konfiguraci.
- **Vlastní profily spuštění**: Přestože je možné změnit výchozí sadu profilů spuštění pomocí Synchronization Service Manager, tato funkce se v současné době nepodporuje prostřednictvím nastavení synchronizace. Po dokončení instalace je nutné ručně znovu použít pokročilou konfiguraci.
- **Konfigurace zřizovací hierarchie**: Tato pokročilá funkce Synchronization Service Manager není podporována prostřednictvím nastavení synchronizace. Po dokončení počátečního nasazení je nutné ručně změnit konfiguraci.
- **Active Directory Federation Services (AD FS) (AD FS) a ověřování PingFederate**: automaticky se vyberou metody přihlašování přidružené k těmto funkcím ověřování. Musíte interaktivně zadat všechny další požadované parametry konfigurace.
- **Zakázané vlastní synchronizační pravidlo se naimportuje jako povolené**: pravidlo zakázaného vlastního synchronizace se naimportuje jako povolené. Ujistěte se, že je na novém serveru moc zakázaný.

 ## <a name="next-steps"></a>Další kroky

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
