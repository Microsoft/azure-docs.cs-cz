---
title: Upgrade serveru Azure MFA – Azure Active Directory
description: Postup a pokyny k upgradu Azure Multi-Factor Authentication Server na novější verzi.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbddd2eb52414827561d8896dfc8bc9ff705f41b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584387"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgrade na nejnovější server Azure Multi-Factor Authentication

Tento článek vás provede procesem upgradu serveru Azure Multi-Factor Authentication (MFA) verze 6.0 nebo vyšší. Pokud potřebujete upgradovat starou verzi agenta PhoneFactor, přečtěte si téma [Upgrade agenta PhoneFactor na Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-upgrade-pf.md).

Pokud upgradujete z verze 6. x nebo starší na v7. x nebo novější, změní se všechny komponenty z .NET 2,0 na .NET 4,5. Všechny komponenty také vyžadují Microsoft Visual C++ 2015 Distribuovatelný Update 1 nebo novější. Instalační program serveru MFA nainstaluje verze x86 i x64 těchto komponent, pokud ještě nejsou nainstalované. Pokud se portál User Portal a webová služba mobilní aplikace spouští na samostatných serverech, musíte tyto balíčky nainstalovat ještě před upgradem těchto součástí. Nejnovější aktualizaci Microsoft Visual C++ 2015 Redistributable Update můžete vyhledat na [webu služby Stažení softwaru](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

Postup upgradu na první pohled:

* Upgrade serverů Azure MFA (podřízených a primárních)
* Upgrade instancí portálu User Portal
* Upgrade instancí adaptérů AD FS

## <a name="upgrade-azure-mfa-server"></a>Upgrade serveru Azure MFA

1. Pomocí pokynů v tématu [stažení Multi-Factor Authentication Server Azure](howto-mfaserver-deploy.md#download-the-mfa-server) získáte nejnovější verzi instalačního programu serveru Azure MFA.
2. Vytvořte zálohu datového souboru MFA serveru, který se nachází ve složce C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (za předpokladu, že výchozí umístění instalace) na primárním serveru MFA.
3. Pokud spouštíte více serverů pro zajištění vysoké dostupnosti, změňte klientské systémy, které se ověřují na MFA serveru, aby zastavily odesílání provozu na servery, které provádí upgrade. Pokud použijete nástroj pro vyrovnávání zatížení, odeberte podřízený server MFA z nástroje pro vyrovnávání zatížení, proveďte upgrade a pak přidejte server zpátky do farmy.
4. Spusťte nový instalační program na každém serveru MFA. Nejprve upgradujte podřízené servery, protože mohou číst starý datový soubor, který je replikován primárním serverem.

   > [!NOTE]
   > Při upgradu serveru by se měl odebrat z jakéhokoli vyrovnávání zatížení nebo sdílení přenosů s jinými MFA servery.
   >
   > Před spuštěním instalačního programu nemusíte odinstalovat aktuální MFA Server. Instalační program provede místní upgrade. Instalační cesta je převzata z registru z předchozí instalace, takže se nainstaluje do stejného umístění (například C:\Program Files\Multi-Factor Authentication Server).
  
5. Pokud se zobrazí výzva k instalaci balíčku aktualizace Microsoft Visual C++ 2015 Redistributable, Přijměte výzvu. Jsou nainstalovány verze x86 i x64 balíčku.
6. Pokud používáte sadu SDK webové služby, zobrazí se výzva k instalaci nové sady SDK webové služby. Při instalaci nové sady SDK webové služby se ujistěte, že název virtuálního adresáře odpovídá dříve nainstalovanému virtuálnímu adresáři (například MultiFactorAuthWebServiceSdk).
7. Opakujte kroky na všech podřízených serverech. Zvyšte úroveň jednoho z podřízených na novou primární a potom upgradujte starý primární server.

## <a name="upgrade-the-user-portal"></a>Upgrade portálu User Portal

Před přechodem do této části dokončete upgrade serverů MFA.

1. Vytvořte zálohu souboru web.config, který se nachází ve virtuálním adresáři umístění instalace portálu User Portal (například C:\inetpub\wwwroot\MultiFactorAuth). Pokud byly ve výchozím motivu provedeny nějaké změny, vytvořte také zálohu složky App_Themes \Default. Je lepší vytvořit kopii výchozí složky a vytvořit nový motiv, než aby bylo možné změnit výchozí motiv.
2. Pokud je uživatelský portál spuštěn na stejném serveru jako ostatní komponenty MFA serveru, zobrazí se při instalaci MFA serveru výzva k aktualizaci portálu User Portal. Potvrďte výzvu a nainstalujte aktualizaci portálu User Portal. Ověřte, že název virtuálního adresáře odpovídá dříve nainstalovanému virtuálnímu adresáři (například MultiFactorAuth).
3. Pokud je uživatelský portál na vlastním serveru, zkopírujte soubor MultiFactorAuthenticationUserPortalSetup64.msi z umístění instalace jednoho ze serverů MFA a umístěte jej na webový server portálu User Portal. Spusťte instalační program.

   Pokud dojde k chybě, je třeba "Microsoft Visual C++ 2015 Distribuovatelný Update 1 nebo vyšší", "Stáhněte si a nainstalujte nejnovější balíček aktualizace z [webu Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte verze x86 i x64.

4. Po instalaci aktualizovaného softwaru uživatelského portálu Porovnejte zálohu web.config, kterou jste provedli v kroku 1, s novým souborem web.config. Pokud v novém web.config neexistují žádné nové atributy, zkopírujte web.config zálohování do virtuálního adresáře a přepište ho. Další možností je zkopírovat/vložit hodnoty appSettings a adresu URL sady SDK webové služby ze záložního souboru do nového web.config.

Pokud máte portál User Portal na více serverech, opakujte instalaci na všech těchto serverech.

## <a name="upgrade-the-mobile-app-web-service"></a>Upgrade webové služby mobilní aplikace

> [!NOTE]
> Při upgradu z verze Azure MFA serveru starší než 8,0 na 8.0 + se může po upgradu odinstalovat webová služba mobilní aplikace.

## <a name="upgrade-the-ad-fs-adapters"></a>Upgrade AD FSch adaptérů

Než přejdete do této části, dokončete upgrade serverů MFA a portálu User Portal.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Pokud MFA běží na různých serverech než AD FS

Tyto pokyny platí jenom v případě, že spouštíte Multi-Factor Authentication Server nezávisle na vašich AD FSch serverech. Pokud obě služby běží na stejných serverech, přeskočte tuto část a přejděte k instalačním postupům. 

1. Uložte kopii souboru MultiFactorAuthenticationAdfsAdapter.config, který byl zaregistrován v AD FS, nebo exportujte konfiguraci pomocí následujícího příkazu prostředí PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` . Název adaptéru je buď "WindowsAzureMultiFactorAuthentication" nebo "AzureMfaServerAuthentication" v závislosti na dříve nainstalované verzi.
2. Zkopírujte následující soubory z umístění instalace MFA serveru na servery AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Upravte skript Register-MultiFactorAuthenticationAdfsAdapter.ps1 přidáním `-ConfigurationFilePath [path]` na konec `Register-AdfsAuthenticationProvider` příkazu. Nahraďte *[cesta]* úplnou cestou k souboru MultiFactorAuthenticationAdfsAdapter.config nebo konfiguračním souborem exportovaným v předchozím kroku.

   Zkontrolujte atributy v novém MultiFactorAuthenticationAdfsAdapter.config a podívejte se, zda odpovídají starému konfiguračnímu souboru. Pokud byly v nové verzi přidány nebo odebrány nějaké atributy, zkopírujte hodnoty atributů ze starého konfiguračního souboru do nového nebo upravte starý konfigurační soubor tak, aby odpovídal.

### <a name="install-new-ad-fs-adapters"></a>Nainstalovat nové adaptéry AD FS

> [!IMPORTANT]
> Během kroků 3-8 tohoto oddílu nebudou vaši uživatelé muset provádět dvoustupňové ověřování. Pokud jste AD FS nakonfigurovali v několika clusterech, můžete v jednotlivých clusterech odebrat, upgradovat a obnovovat jednotlivé clustery nezávisle na ostatních clusterech, abyste se vyhnuli výpadkům.

1. Odeberte z farmy některé AD FS servery. Aktualizujte tyto servery, zatímco ostatní stále běží.
2. Nainstalujte nový AD FS adaptér na každém serveru odebraném z farmy AD FS. Pokud je server MFA nainstalovaný na každém serveru AD FS, můžete ho aktualizovat prostřednictvím uživatelského rozhraní pro správu MFA serveru. V opačném případě aktualizujte spuštěním MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Pokud dojde k chybě, je třeba "Microsoft Visual C++ 2015 Distribuovatelný Update 1 nebo vyšší", "Stáhněte si a nainstalujte nejnovější balíček aktualizace z [webu Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte verze x86 i x64.

3. Přejít na **AD FS**  >  **zásady ověřování**  >  **Upravit globální zásady vícefaktorového ověřování**. Zrušte kontrolu **WindowsAzureMultiFactorAuthentication** nebo **AzureMFAServerAuthentication** (v závislosti na nainstalované aktuální verzi).

   Po dokončení tohoto kroku není v tomto AD FS clusteru k dispozici dvoustupňové ověřování prostřednictvím MFA serveru, dokud nedokončíte krok 8.

4. Zrušte registraci starší verze AD FS adaptéru spuštěním skriptu Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShellu. Zajistěte, aby se parametr *-Name* (buď WindowsAzureMultiFactorAuthentication nebo "AzureMFAServerAuthentication") shodoval s názvem, který byl zobrazen v kroku 3. To platí pro všechny servery ve stejném AD FS clusteru, protože existuje centrální konfigurace.
5. Zaregistrujte nový AD FS adaptér spuštěním skriptu Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShellu. To platí pro všechny servery ve stejném AD FS clusteru, protože existuje centrální konfigurace.
6. Restartujte službu AD FS na každém serveru odebraném z farmy AD FS.
7. Přidejte aktualizované servery zpět do farmy AD FS a odeberte ostatní servery z farmy.
8. Přejít na **AD FS**  >  **zásady ověřování**  >  **Upravit globální zásady vícefaktorového ověřování**. Podívejte se na **AzureMfaServerAuthentication**.
9. Opakujte krok 2 a aktualizujte servery, které jsou nyní odebrány z AD FS farmy a restartujte službu AD FS na těchto serverech.
10. Přidejte tyto servery zpátky do farmy AD FS.

## <a name="next-steps"></a>Další kroky

* Získejte příklady [pokročilých scénářů s Azure Multi-Factor Authentication a sítěmi VPN třetích stran](howto-mfaserver-nps-vpn.md) .

* [Synchronizace MFA serveru s Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Konfigurace ověřování systému Windows](howto-mfaserver-windows.md) pro vaše aplikace
