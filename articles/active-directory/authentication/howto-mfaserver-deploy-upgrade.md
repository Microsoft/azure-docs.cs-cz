---
title: Aktualizace Azure MFA Server | Dokumentace Microsoftu
description: Postupy a pokyny, jak upgradovat na novější verzi serveru Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: dbb95c8aadc66e7ece93fa800055a0f6be81f015
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161408"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgrade na Azure Multi-Factor Authentication Server nejnovější verzi

Tento článek vás provedete upgrade Azure Multi-Factor Authentication (MFA) Server verze 6.0 nebo vyšší. Pokud je třeba upgradovat stará verze agenta PhoneFactor, podívejte se na [Upgrade agenta PhoneFactor na Server Azure Multi-Factor Authentication](howto-mfaserver-deploy-upgrade-pf.md).

Při upgradu z verze 6.x nebo starší na v7.x nebo novější, změňte všechny součásti z rozhraní .NET 2.0 na .NET 4.5. Všechny součásti také vyžaduje Microsoft Visual C++ 2015 Redistributable Update 1 nebo novější. Instalační program MFA Server nainstaluje x86 a x64 verze těchto komponent, pokud ještě nejsou nainstalovány. Pokud portál User Portal a webová služba mobilní aplikace běží na samostatných serverech, musíte nainstalovat tyto balíčky před provedením upgradu těchto součástí. Můžete vyhledat nejnovější aktualizace Microsoft Visual C++ 2015 Redistributable na [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Nainstalujte nejnovější verzi Azure MFA serveru

1. Postupujte podle pokynů v [stažení serveru Azure Multi-Factor Authentication](howto-mfaserver-deploy.md#download-the-mfa-server) získat nejnovější verzi Azure MFA serveru.
2. Vytvořte záložní kopii souboru dat MFA serveru v umístění C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (za předpokladu, že výchozí umístění instalace) na hlavním serveru MFA.
3. Pokud spustíte více serverů pro zajištění vysoké dostupnosti, změňte klientské systémy, které se ověřují pro MFA Server, takže vyhodnocování se zastaví odesílání provozu do serverů, které provádíte upgrade. Pokud používáte nástroj pro vyrovnávání zatížení, z nástroje pro vyrovnávání zatížení odebrat MFA Server, proveďte upgrade a pak server přidáte do farmy.
4. Spusťte v novém instalačním programu na každém serveru MFA. Nejdříve upgradujte podřízené servery, protože může číst starý soubor dat se replikuje hlavní. 

  Není nutné odinstalovat aktuální MFA Server před spuštěním instalačního programu. Instalační program provádí upgrade v místě. Cesta instalace je převzata z registru z předchozí instalace, takže se nainstaluje ve stejném umístění (například C:\Program Files\Multi-Factor Authentication Server). 
  
5. Pokud budete vyzváni k instalaci balíčku Microsoft Visual C++ 2015 Redistributable aktualizaci, přijmout výzvu. Instalují se x86 a x64 verze balíčku.
5. Pokud používáte sadu SDK webové služby, zobrazí se výzva k instalaci nové sady SDK webové služby. Při instalaci nové sady SDK webové služby, ujistěte se, že název virtuálního adresáře odpovídá dříve nainstalované virtuální adresář (třeba MultiFactorAuthWebServiceSdk).
6. Opakujte kroky na všechny podřízené servery. Zvyšte úroveň jednoho z podřízených uzlů bude nový hlavní server a pak upgradujte starého hlavního serveru. 

## <a name="upgrade-the-user-portal"></a>Upgrade portál User Portal

1. Vytvořte záložní kopii souboru web.config, který je ve virtuálním adresáři umístění instalace portálu User Portal (například C:\inetpub\wwwroot\MultiFactorAuth). Pokud výchozí motiv nebyly provedeny žádné změny, vytvořte zálohu na App_Themes\Default složku. Je lepší vytvořit kopii výchozí složce a vytvořit nový motiv než změňte výchozí motiv.
2. Pokud portál User Portal běží na stejném serveru jako ostatní součásti serveru MFA, instalace MFA Server vás vyzve k aktualizovat rozhraní User Portal. Přijmout výzvu aktualizaci a nainstalovat portál User Portal. Zkontrolujte, že název virtuálního adresáře odpovídá dříve nainstalované virtuální adresář (třeba MultiFactorAuth).
3. Pokud je portál User Portal na vlastním serveru, zkopírujte soubor MultiFactorAuthenticationUserPortalSetup64.msi z umístění instalace služby některý server MFA a umístěny na webovém serveru portálu User Portal. Spusťte instalační program. 

  Pokud dojde k chybě s, "Microsoft Visual C++ 2015 Redistributable Update 1 nebo novější je nutné," stáhněte a nainstalujte nejnovější balíček aktualizace z [Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte x86 a x64 verze.

4. Po instalaci aktualizace softwaru portálu User Portal, porovnejte web.config zálohu, kterou jste zadali v kroku 1 pomocí nového souboru web.config. Pokud neexistují žádné nové atributy do nového souboru Web.config, zkopírujte do virtuálního adresáře přepsat novým zálohování web.config. Další možností je kopírování/vkládání hodnoty appSettings a adresu URL sady SDK webové služby ze záložního souboru do nového souboru web.config.

Pokud máte na portálu User Portal na více serverech, opakujte instalaci na všechny z nich. 

## <a name="upgrade-the-mobile-app-web-service"></a>Upgradovat službu Mobile App Web Service

> [!NOTE]
> Pokud upgradujete ze starší verze Azure MFA Serveru než 8.0 na verzi 8.0 nebo novější, po upgradu je možné odinstalovat webovou službu mobilní aplikace.

## <a name="upgrade-the-ad-fs-adapters"></a>Upgrade adaptéry AD FS

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Pokud vícefaktorové ověřování běží na různých serverech než služba AD FS

Tyto pokyny platí pouze pokud Multi-Factor Authentication Server samostatně z vašich serverů služby AD FS. Pokud obě služby běží stejných serverů, přeskočte tuto část a přejděte na instalační postup, který. 

1. Uložit kopii souboru MultiFactorAuthenticationAdfsAdapter.config, který byl zaregistrován ve službě AD FS nebo exportovat konfiguraci pomocí následujícího příkazu Powershellu: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. V závislosti na dříve nainstalovanou verzi je název adaptéru "WindowsAzureMultiFactorAuthentication" nebo "AzureMfaServerAuthentication".
2. Zkopírujte následující soubory z umístění instalace serveru MFA na servery služby AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Upravte skript Register-MultiFactorAuthenticationAdfsAdapter.ps1 tak, že přidáte `-ConfigurationFilePath [path]` na konec objektu `Register-AdfsAuthenticationProvider` příkazu. Nahraďte *[cesta]* s úplnou cestou k MultiFactorAuthenticationAdfsAdapter.config souboru nebo konfigurace exportovali v předchozím kroku. 

  Zkontrolujte atributy v nové MultiFactorAuthenticationAdfsAdapter.config zobrazíte, pokud se shodují starý konfigurační soubor. Pokud všechny atributy se přidaly nebo odebraly v nové verzi, zkopírujte hodnoty atributů z starý konfigurační soubor do nové nebo úprava starý konfigurační soubor tak, aby odpovídaly.

### <a name="install-new-ad-fs-adapters"></a>Instalace nové adaptéry služby AD FS

> [!IMPORTANT] 
> Vaši uživatelé nebudou muset dvoustupňové ověřování během kroků 3-8 v této části. Pokud máte službu AD FS nakonfigurovaný v několika clusterech, můžete odebrat, upgrade a obnovení každý cluster ve farmě nezávisle na jiných clusterů, aby se zabránilo výpadkům.

1. Odeberte některé servery služby AD FS z farmy. Aktualizace těchto serverů, zatímco ostatní jsou pořád spuštěné.
2. Nainstalujte nový adaptér služby AD FS na každém serveru odstraněny z farmy služby AD FS. Pokud MFA Server nainstalovaný na každém serveru služby AD FS, můžete aktualizovat pomocí Správce serveru MFA uživatelské prostředí V opačném případě aktualizujte spuštěním MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Pokud dojde k chybě s, "Microsoft Visual C++ 2015 Redistributable Update 1 nebo novější je nutné," stáhněte a nainstalujte nejnovější balíček aktualizace z [Microsoft Download Center](https://www.microsoft.com/download/). Nainstalujte x86 a x64 verze.

3. Přejděte na **služby AD FS** > **zásady ověřování** > **upravte zásady globální Vícefaktorové ověřování**. Zrušte zaškrtnutí políčka **WindowsAzureMultiFactorAuthentication** nebo **AzureMFAServerAuthentication** (v závislosti na aktuální verzi nainstalovanou). 

  Po dokončení tohoto kroku se dvoustupňové ověřování přes MFA Server není k dispozici v tomto clusteru služby AD FS, dokud nedokončíte krok 8.

4. Zrušit registraci starší verzi adaptéru služby AD FS pomocí skriptu prostředí PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Ujistěte se, že *– název* parametr ("WindowsAzureMultiFactorAuthentication" nebo "AzureMFAServerAuthentication") odpovídá názvu, který se zobrazí v kroku 3. To platí pro všechny servery ve stejném clusteru služby AD FS, protože centrální konfigurace.
5. Zaregistrujte nový adaptér služby AD FS pomocí skriptu prostředí PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1. To platí pro všechny servery ve stejném clusteru služby AD FS, protože centrální konfigurace.
6. Restartujte službu AD FS na každém serveru odstraněny z farmy služby AD FS.
7. Přidejte aktualizované serverů zpět do farmy služby AD FS a odebrat ostatní servery z farmy.
8. Přejděte na **služby AD FS** > **zásady ověřování** > **upravte zásady globální Vícefaktorové ověřování**. Zkontrolujte **AzureMfaServerAuthentication**.
9. Opakujte krok 2: aktualizace serverů nyní odstraněny z farmy služby AD FS a restartujte službu AD FS na těchto serverech.
10. Tyto servery přidáte zpátky do farmy služby AD FS.

## <a name="next-steps"></a>Další postup

- Získání příkladů z [pokročilé scénáře se službou Azure Multi-Factor Authentication a sítěmi VPN třetích stran](howto-mfaserver-nps-vpn.md)

- [Synchronizace MFA serveru s Windows Server Active Directory](howto-mfaserver-dir-ad.md)

- [Konfigurace ověřování Windows](howto-mfaserver-windows.md) pro vaše aplikace
