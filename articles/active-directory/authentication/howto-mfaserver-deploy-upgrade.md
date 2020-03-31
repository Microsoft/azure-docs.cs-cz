---
title: Inovace serveru Azure MFA – Azure Active Directory
description: Kroky a pokyny k upgradu serveru Azure Multi-Factor Authentication Server na novější verzi.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756c45541907c52448805376e1b054180c31fdf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848098"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgrade na nejnovější server Azure Multi-Factor Authentication

Tento článek vás provede procesem upgradu serveru Azure Multi-Factor Authentication (MFA) v6.0 nebo vyšším. Pokud potřebujete upgradovat starou verzi agenta PhoneFactor, přečtěte [si informace o upgradu agenta PhoneFactor na server Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-upgrade-pf.md).

Pokud upgradujete z v6.x nebo staršího na v7.x nebo novější, všechny součásti se změní z .NET 2.0 na .NET 4.5. Všechny součásti také vyžadují redistribuovatelnou aktualizaci Microsoft Visual C++ 2015 1 nebo vyšší. Instalační program serveru MFA server nainstaluje verze x86 a x64 těchto součástí, pokud ještě nejsou nainstalovány. Pokud se uživatelský portál a webová služba mobilních aplikací spouštějí na samostatných serverech, je třeba tyto balíčky nainstalovat před upgradem těchto součástí. Nejnovější aktualizaci Microsoft Visual C++ 2015 Redistributable můžete vyhledat na [webu služby Stažení softwaru](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

Krok upgradu na první pohled:

* Upgrade serverů Azure MFA (podřízených a potom hlavní)
* Upgrade instancí portálu uživatelů
* Upgrade instancí adaptéru služby AD FS

## <a name="upgrade-azure-mfa-server"></a>Upgrade serveru Azure MFA

1. Pomocí pokynů v [části Stáhněte si server Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md#download-the-mfa-server) získáte nejnovější verzi instalačního programu serveru Azure MFA Server.
2. Vytvořte zálohu datového souboru serveru MFA Server umístěného na adrese C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (za předpokladu výchozího umístění instalace) na hlavním serveru MFA.
3. Pokud spustíte více serverů pro vysokou dostupnost, změňte klientské systémy, které se ověřují na server MFA tak, aby přestaly odesílat provoz na servery, které jsou inovované. Pokud používáte nástroje pro vyrovnávání zatížení, odeberte podřízený server MFA ze nástroje pro vyrovnávání zatížení, proveďte upgrade a přidejte server zpět do farmy.
4. Spusťte nový instalační program na každém serveru MFA. Nejprve inovujte podřízené servery, protože mohou číst starý datový soubor replikovaný hlavním serverem.

   > [!NOTE]
   > Při inovaci serveru by měl být odebrán z jakéhokoli loadbalancing nebo sdílení provozu s jinými servery MFA.
   >
   > Před spuštěním instalačního programu není nutné odinstalovat aktuální server MFA. Instalační program provede upgrade na místě. Instalační cesta je z registru vyzvednuta z předchozí instalace, takže se nainstaluje do stejného umístění (například C:\Program Files\Multi-Factor Authentication Server).
  
5. Pokud se zobrazí výzva k instalaci balíčku aktualizace Microsoft Visual C++ 2015 Redistributable update, přijměte výzvu. Jsou nainstalovány verze balíčku x86 i x64.
6. Pokud používáte sadu SDK webové služby, budete vyzváni k instalaci nové sady SDK webové služby. Při instalaci nové sady SDK webové služby se ujistěte, že název virtuálního adresáře odpovídá dříve nainstalovanému virtuálnímu adresáři (například MultiFactorAuthWebServiceSdk).
7. Opakujte kroky na všech podřízených serverech. Povýšit jednoho z podřízených být nový hlavní server, pak inovovat starý hlavní server.

## <a name="upgrade-the-user-portal"></a>Upgrade uživatelského portálu

Před přechodem do této části dokončete upgrade serverů MFA.

1. Vytvořte zálohu souboru web.config, který je ve virtuálním adresáři umístění instalace uživatelského portálu (například C:\inetpub\wwwroot\MultiFactorAuth). Pokud byly provedeny změny výchozího motivu, vytvořte také zálohu složky App_Themes\Default. Je lepší vytvořit kopii výchozí složky a vytvořit nový motiv, než změnit výchozí motiv.
2. Pokud je portál User Portal spuštěn na stejném serveru jako ostatní součásti serveru MFA, zobrazí se instalace serveru MFA výzvu k aktualizaci portálu pro uživatele. Přijměte výzvu a nainstalujte aktualizaci portálu uživatelů. Zkontrolujte, zda název virtuálního adresáře odpovídá dříve nainstalovanému virtuálnímu adresáři (například MultiFactorAuth).
3. Pokud je portál uživatele na vlastním serveru, zkopírujte soubor MultiFactorAuthenticationUserPortalSetup64.msi z umístění instalace jednoho ze serverů MFA a vložte jej na webový server portálu uživatele. Spusťte instalační program.

   Pokud dojde k chybě s poznámkou Microsoft Visual C++ 2015 Redistributable Update 1 nebo vyšší je vyžadována, stáhněte a nainstalujte nejnovější balíček aktualizace ze [služby Stažení softwaru .](https://www.microsoft.com/download/) Nainstalujte verze x86 i x64.

4. Po instalaci aktualizovaného softwaru portálu User Portal porovnejte zálohu web.config, kterou jste provedli v kroku 1, s novým souborem web.config. Pokud v novém souboru web.config neexistují žádné nové atributy, zkopírujte záložní soubor web.config do virtuálního adresáře a přepište nový. Další možností je zkopírovat/vložit hodnoty appSettings a adresu URL sady Web Service SDK ze záložního souboru do nového souboru web.config.

Pokud máte uživatelský portál na více serverech, opakujte instalaci na všech z nich.

## <a name="upgrade-the-mobile-app-web-service"></a>Upgrade webové služby mobilních aplikací

> [!NOTE]
> Při upgradu z verze serveru Azure MFA Server starší ch od 8.0 do 8.0+ lze webovou službu mobilní aplikace po upgradu odinstalovat

## <a name="upgrade-the-ad-fs-adapters"></a>Upgrade adaptérů služby AD FS

Před přechodem do této části dokončete upgrade serverů mfa a uživatelského portálu.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Pokud vícefaktorové služby běží na jiných serverech než službu AD FS

Tyto pokyny platí pouze v případě, že spouštějíte vícefaktorový ověřovací server odděleně od serverů služby AD FS. Pokud obě služby běží na stejných serverech, přeskočte tuto část a přejděte k krokům instalace. 

1. Uložte kopii souboru MultiFactorAuthenticationAdfsAdapter.config, který byl zaregistrován ve službě AD FS, nebo exportujte konfiguraci pomocí následujícího příkazu prostředí PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Název adaptéru je buď "WindowsAzureMultiFactorAuthentication" nebo "AzureMfaServerAuthentication" v závislosti na dříve nainstalované verzi.
2. Zkopírujte následující soubory z umístění instalace serveru MFA na servery služby AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Upravte skript Register-MultiFactorAuthenticationAdfsAdapter.ps1 `-ConfigurationFilePath [path]` přidáním na `Register-AdfsAuthenticationProvider` konec příkazu. Nahraďte *[cestu]* úplnou cestou k souboru MultiFactorAuthenticationAdfsAdapter.config nebo konfiguračnímu souboru exportovanému v předchozím kroku.

   Zkontrolujte atributy v novém multiFactorAuthenticationAdfsAdapter.config a zjistěte, zda odpovídají starému konfiguračnímu souboru. Pokud byly v nové verzi přidány nebo odebrány nějaké atributy, zkopírujte hodnoty atributů ze starého konfiguračního souboru do nového nebo upravte starý konfigurační soubor tak, aby odpovídal.

### <a name="install-new-ad-fs-adapters"></a>Instalace nových adaptérů služby AD FS

> [!IMPORTANT]
> Vaši uživatelé nebudou muset provádět dvoustupňové ověření během kroků 3-8 této části. Pokud máte službu AD FS nakonfigurovanou ve více clusterech, můžete odebrat, upgradovat a obnovit každý cluster ve farmě nezávisle na ostatních clusterech, abyste předešli prostojům.

1. Odeberte některé servery služby AD FS z farmy. Aktualizujte tyto servery, zatímco ostatní jsou stále spuštěny.
2. Nainstalujte nový adaptér služby AD FS na každý server odebraný ze farmy služby AD FS. Pokud je server MFA nainstalován na každém serveru služby AD FS, můžete jej aktualizovat prostřednictvím uživatelského rozhraní správce serveru MFA. V opačném případě aktualizujte spuštěním multiFactorAuthenticationAdfsAdapterSetup64.msi.

   Pokud dojde k chybě s poznámkou Microsoft Visual C++ 2015 Redistributable Update 1 nebo vyšší je vyžadována, stáhněte a nainstalujte nejnovější balíček aktualizace ze [služby Stažení softwaru .](https://www.microsoft.com/download/) Nainstalujte verze x86 i x64.

3. Přejděte na**zásady ověřování služby** >  **AD FS** > **Upravit globální zásady vícefaktorového ověřování**. Zrušením zaškrtnutí **políčka WindowsAzureMultiFactorAuthentication** nebo **AzureMFAServerAuthentication** (v závislosti na nainstalované aktuální verzi).

   Po dokončení tohoto kroku není dvoustupňové ověření prostřednictvím serveru MFA v tomto clusteru služby AD FS k dispozici, dokud nedokončíte krok 8.

4. Zrušení registrace starší verze adaptéru služby AD FS spuštěním skriptu Prostředí PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Ujistěte se, že parametr *-Name* (buď "WindowsAzureMultiFactorAuthentication" nebo "AzureMFAServerAuthentication") odpovídá názvu, který byl zobrazen v kroku 3. To platí pro všechny servery ve stejném clusteru služby AD FS, protože existuje centrální konfigurace.
5. Zaregistrujte nový adaptér služby AD FS spuštěním skriptu PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1. To platí pro všechny servery ve stejném clusteru služby AD FS, protože existuje centrální konfigurace.
6. Restartujte službu AD FS na každém serveru odebraném ze farmy služby AD FS.
7. Přidejte aktualizované servery zpět do farmy služby AD FS a odeberte ostatní servery ze farmy.
8. Přejděte na**zásady ověřování služby** >  **AD FS** > **Upravit globální zásady vícefaktorového ověřování**. Zkontrolujte **AzureMfaServerAuthentication**.
9. Opakováním kroku 2 aktualizujte servery, které jsou nyní odebrány z farmy služby AD FS, a restartujte službu AD FS na těchto serverech.
10. Přidejte tyto servery zpět do farmy služby AD FS.

## <a name="next-steps"></a>Další kroky

* Získejte příklady [pokročilých scénářů s vícefaktorovým ověřováním Azure a sítěmi VIRTUÁLNÍMI sítěmi třetích stran](howto-mfaserver-nps-vpn.md)

* [Synchronizace serveru MFA Server se službou Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Konfigurace ověřování systému Windows](howto-mfaserver-windows.md) pro vaše aplikace
