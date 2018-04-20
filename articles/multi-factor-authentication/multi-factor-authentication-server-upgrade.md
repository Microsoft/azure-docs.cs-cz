---
title: "Upgrade služby Azure MFA serveru | Microsoft Docs"
description: "Kroky a pokyny k upgradu serveru Azure Multi-Factor Authentication na novější verzi."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 456f2c64c91249f3ec2de4f2d79a2f77cc7598da
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgrade na nejnovější Azure Multi-Factor Authentication Server

Tento článek provede vás proces upgradu serveru Azure Multi-Factor Authentication (MFA) verze 6.0 nebo vyšší. Pokud je třeba upgradovat starší verzi agenta PhoneFactor, podívejte se na [Upgrade agenta PhoneFactor na Server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-upgrade.md).

Pokud provádíte upgrade z verze 6.x nebo starší na v7.x nebo novější, změňte všechny součásti z rozhraní .NET 2.0 na rozhraní .NET 4.5. Všechny součásti také vyžadují Microsoft Visual C++ 2015 Redistributable Update 1 nebo vyšší. MFA Server instalační program nainstaluje x86 a x64 verze těchto komponent, pokud již nejsou nainstalovány. Pokud portál User Portal a webová služba mobilní aplikace spustit na samostatných serverech, musíte nainstalovat tyto balíčky před provedením upgradu těchto součástí. Můžete vyhledat nejnovější aktualizace služby Microsoft Visual C++ 2015 Redistributable na [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Nainstalujte nejnovější verzi Azure MFA serveru

1. Postupujte podle pokynů v [stažení serveru Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md#download-the-mfa-server) získat nejnovější verzi Azure MFA serveru.
2. Vytvořte záložní kopii datového souboru MFA Server nachází v C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (za předpokladu, že výchozí umístění instalace) na hlavním serveru MFA.
3. Pokud spouštíte více serverů pro zajištění vysoké dostupnosti, změňte klientské systémy, které prováděl ověření vůči serveru MFA tak, aby se zastavit odesílání provozu na servery, které provádíte upgrade. Pokud použijete nástroj pro vyrovnávání zatížení, odeberte MFA serveru z nástroje pro vyrovnávání zatížení, provést upgrade a pak přidejte server zpátky do farmy.
4. Spusťte nové instalační program na každém serveru MFA. Nejprve upgradujte podřízené servery, protože starý datový soubor replikuje pomocí hlavní může číst. 

  Není nutné odinstalovat aktuální Server MFA před spuštěním Instalační služby. Instalační program provádí upgrade na místě. Cesta instalace je převzata z registru z předchozí instalace, takže se nainstaluje ve stejném umístění (například C:\Program Files\Multi-Factor Authentication Server). 
  
5. Pokud se zobrazí výzva k instalaci Microsoft Visual C++ 2015 Redistributable balíček aktualizace, přijměte řádku. X86 a x64 verze balíčku jsou nainstalovány.
5. Pokud používáte sady SDK webové služby, zobrazí se výzva k instalaci nové sady SDK webové služby. Při instalaci nové sady SDK webové služby se ujistěte, že název virtuálního adresáře odpovídá dříve nainstalované virtuálního adresáře (například MultiFactorAuthWebServiceSdk).
6. Opakujte kroky na všechny podřízené servery. Zvyšte úroveň jednoho podřízené jako nový hlavní server a pak upgradujte starého hlavního serveru. 

## <a name="upgrade-the-user-portal"></a>Upgrade portálu User Portal

1. Vytvořte záložní kopii souboru web.config, který je ve virtuálním adresáři umístění instalace portálu User Portal (např. C:\inetpub\wwwroot\MultiFactorAuth). Pokud výchozí motiv nebyly provedeny žádné změny, vytvořte záložní kopii složce App_Themes\Default. Je lepší vytvořit kopii do výchozí složky a vytvořit nový motiv než změňte výchozí motiv.
2. Pokud portálu User Portal běží na stejném serveru jako ostatní součásti serveru MFA, instalace MFA serveru vás vyzve k aktualizaci portálu User Portal. Přijměte řádku a nainstalujte aktualizaci portálu User Portal. Zkontrolujte, jestli název virtuálního adresáře odpovídá dříve nainstalované virtuálního adresáře (například MultiFactorAuth).
3. Pokud portál User Portal na vlastním serveru, zkopírujte soubor MultiFactorAuthenticationUserPortalSetup64.msi z umístění instalace jednoho ze serverů MFA a umístí jej do portálu User Portal webový server. Spusťte instalační program. 

  Pokud dojde k chybě stanovení, "Microsoft Visual C++ 2015 Redistributable Update 1 nebo vyšší je povinné," stáhněte a nainstalujte nejnovější balíček aktualizace z [Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte x86 a x64 verze.

4. Po instalaci aktualizovaný software portálu User Portal porovnejte web.config zálohování, které jste provedli v kroku 1 pomocí nového souboru web.config. Pokud neexistují žádné nové atributy do nového souboru Web.config, zkopírujte do virtuálního adresáře přepsat novým zálohování souboru web.config. Další možností je zkopírujte a vložte hodnoty appSettings a adresu URL sady SDK webové služby ze záložního souboru do nového souboru web.config.

Pokud máte portálu User Portal na více serverech, opakujte instalaci na všechny z nich. 


## <a name="upgrade-the-mobile-app-web-service"></a>Upgrade služby Mobile App Web

1. Vytvořte záložní kopii souboru web.config, který je ve virtuálním adresáři umístění instalace webové služby mobilní aplikace (například C:\inetpub\wwwroot\app nebo C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Zkopírujte soubor MultiFactorAuthenticationMobileAppWebServiceSetup64.msi z umístění instalace serveru MFA a umístí jej do mobilní aplikace registrace webový server.
3. Spusťte instalační program. 

  Pokud dojde k chybě s oznámením, že Microsoft Visual C++ 2015 Redistributable Update 1 nebo vyšší není vyžadováno, stáhněte a nainstalujte nejnovější balíček aktualizace z [Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte x86 a x64 verze.

4. Po instalaci aktualizovaný software webové služby mobilní aplikace porovnejte souboru web.config, který byl zálohován v kroku 1 pomocí nového souboru web.config. Pokud neexistují žádné nové atributy do nového souboru Web.config, můžete zkopírovat uloženého souboru web.config zpět do virtuálního adresáře a přepsat novým. Další možností je zkopírujte a vložte hodnoty appSettings a adresu URL sady SDK webové služby ze záložního souboru do nového souboru web.config.

Pokud máte webové služby mobilní aplikace na více serverech, opakujte instalaci na všechny z nich. 

## <a name="upgrade-the-ad-fs-adapters"></a>Upgrade adaptéry AD FS


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Pokud vícefaktorové ověřování běží na různých serverech než služba AD FS

Tyto pokyny platí pouze pro spuštění aplikace Multi-Factor Authentication Server samostatně z vašich serverů služby AD FS. Pokud obě služby spustit na stejném serveru, tuto část přeskočte a přejděte na postup instalace. 

1. Uložení kopie souboru MultiFactorAuthenticationAdfsAdapter.config, který byl zaregistrován ve službě AD FS nebo exportovat konfiguraci pomocí následujícího příkazu prostředí PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. V závislosti na dříve nainstalovaná verze je název adaptéru "WindowsAzureMultiFactorAuthentication" nebo "AzureMfaServerAuthentication".
2. Zkopírujte následující soubory z umístění instalace serveru MFA na servery služby AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Upravte skript Register-MultiFactorAuthenticationAdfsAdapter.ps1 přidáním `-ConfigurationFilePath [path]` na konec `Register-AdfsAuthenticationProvider` příkaz. Nahraďte *[cesta]* s úplnou cestou k MultiFactorAuthenticationAdfsAdapter.config soubor nebo konfiguračního souboru exportovali v předchozím kroku. 

  Zkontrolujte atributy v nové MultiFactorAuthenticationAdfsAdapter.config chcete zobrazit, pokud budou odpovídat původního konfiguračního souboru. Pokud žádné atributy přidané nebo odebrané v nové verzi, zkopírujte hodnoty atributů z původního konfiguračního souboru do nového nebo upravit původní soubor konfigurace tak, aby odpovídaly.

### <a name="install-new-ad-fs-adapters"></a>Instalace nové adaptéry služby AD FS

> [!IMPORTANT] 
> Uživatelé nebudou muset provést dvoustupňové ověřování během kroky 3-8 v této části. Pokud máte služby AD FS nakonfigurovaný v více clusterů, můžete odebrat, upgrade a obnovení každý cluster ve farmě nezávisle na jiných clusterů výpadky.

1. Odeberte některé servery služby AD FS z farmy. Aktualizujte tyto servery, zatímco jiné jsou pořád spuštěné.
2. Nainstalujte nový adaptér služby AD FS na každém serveru odebrány z farmy služby AD FS. Pokud je Server vícefaktorového ověřování nainstalovaný na každém serveru služby AD FS, můžete aktualizovat pomocí Správce serveru MFA UX V opačném aktualizujte spuštěním soubory MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Pokud dojde k chybě stanovení, "Microsoft Visual C++ 2015 Redistributable Update 1 nebo vyšší je povinné," stáhněte a nainstalujte nejnovější balíček aktualizace z [Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte x86 a x64 verze.

3. Přejděte na **služby AD FS** > **zásady ověřování** > **upravit globální vícefaktorového ověřování zásad**. Zrušte zaškrtnutí políčka **WindowsAzureMultiFactorAuthentication** nebo **AzureMFAServerAuthentication** (v závislosti na aktuální verzi). 

  Po dokončení tohoto kroku dvoustupňové ověřování přes MFA Server není k dispozici v tomto clusteru služby AD FS, dokud nedokončíte krok 8.

4. Spuštěním skriptu prostředí PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 zrušte registraci starší verzi adaptér AD FS. Ujistěte se, že *-název* parametr ("WindowsAzureMultiFactorAuthentication" nebo "AzureMFAServerAuthentication") odpovídá názvu, který se zobrazí v kroku 3. To platí pro všechny servery ve stejném clusteru služby AD FS, protože centrální konfigurace.
5. Spuštěním skriptu prostředí PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1 Zaregistrujte nový adaptér služby AD FS. To platí pro všechny servery ve stejném clusteru služby AD FS, protože centrální konfigurace.
6. Restartujte službu AD FS na každém serveru odebrány z farmy služby AD FS.
7. Přidejte aktualizovanou servery zpět do farmy služby AD FS a odeberte ostatní servery z farmy.
8. Přejděte na **služby AD FS** > **zásady ověřování** > **upravit globální vícefaktorového ověřování zásad**. Zkontrolujte **AzureMfaServerAuthentication**.
9. Opakujte krok 2: aktualizace serverů nyní odebrány z farmy služby AD FS a restartujte službu AD FS na těchto serverech.
10. Přidáte tyto servery zpátky do farmy služby AD FS.

## <a name="next-steps"></a>Další kroky

- Získat příklady [pokročilé scénáře s Azure Multi-Factor Authentication a sítě VPN třetí strany](multi-factor-authentication-advanced-vpn-configurations.md)

- [Synchronizace MFA serveru s Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)

- [Konfigurovat ověřování systému Windows](multi-factor-authentication-get-started-server-windows.md) pro vaše aplikace
