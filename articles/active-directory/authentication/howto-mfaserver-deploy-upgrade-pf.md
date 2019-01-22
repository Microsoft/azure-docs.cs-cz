---
title: Upgrade PhoneFactoru na Azure MFA Server | Dokumentace Microsoftu
description: Zahájení práce s Azure MFA Serverem, když upgradujete ze staršího agenta PhoneFactor
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: e2c3024b16a7ef2fc02abb8bbdb75c166a6962cd
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432366"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Upgrade agenta PhoneFactor na Azure Multi-Factor Authentication Server

Pokud chcete upgradovat agenta PhoneFactor verze 5.x nebo starší na Azure Multi-Factor Authentication Server, odinstalujte nejprve agenta PhoneFactor a připojené součásti. Pak lze instalovat Multi-Factor Authentication Server a jeho připojené součásti.

## <a name="uninstall-the-phonefactor-agent"></a>Odinstalace agenta PhoneFactor

1. Nejprve zálohujte datový soubor PhoneFactor. Výchozí umístění instalace je C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.

2. Pokud je nainstalován portál uživatele:
  1. Přejděte do složky instalace a zálohujte soubor web.config. Výchozí umístění instalace je C:\inetpub\wwwroot\PhoneFactor.

  2. Pokud jste přidali vlastní motivy na portál, zálohujte vlastní složky pod adresářem C:\inetpub\wwwroot\PhoneFactor\App_Themes.

  3. Odinstalujte rozhraní User Portal prostřednictvím agenta PhoneFactor (dostupné, pouze pokud je nainstalován na stejném serveru jako agent PhoneFactor) nebo pomocí programů a funkcí systému Windows.

3. Pokud je nainstalována webová služba mobilní aplikace:

  1. Přejděte do složky instalace a zálohujte soubor web.config. Výchozí umístění instalace je C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

  2. Odinstalujte službu mobilní webové aplikace prostřednictvím programů a funkcí systému Windows.

4. Pokud je nainstalována sada Web Service SDK, odinstalujte ji prostřednictvím agenta PhoneFactor nebo programů a funkcí systému Windows.

5. Odinstalujte službu agent PhoneFactor prostřednictvím programů a funkcí systému Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instalace Multi-Factor Authentication Serveru

Cesta instalace se převzala z registru z předchozí instalace agenta PhoneFactor, takže by instalace měla proběhnout do stejného umístění (například C:\Program Files\PhoneFactor). Nové instalace mají různé výchozí instalační cesty (například C:\Program Files\Multi-Factor Authentication Server). Datový soubor zanechaný předchozí agentem PhoneFactor musí být upgradován během instalace, takže nastavení uživatelů by mělo být po instalaci nového serveru Multi-Factor Authentication stále přítomno.

1. Pokud budete vyzváni, aktivovujte server Multi-Factor Authentication a ujistěte se, že je přiřazen do správné replikační skupiny.

2. Pokud byla sada Web Service SDK dříve nainstalována, nainstalujte novou sadu Web Service SDK prostřednictvím uživatelského rozhraní serveru Multi-Factor Authentication.

  Výchozí název virtuálního adresáře je nyní **MultiFactorAuthWebServiceSdk**, a ne **PhoneFactorWebServiceSdk**. Pokud chcete použít předchozí název, musíte změnit název virtuálního adresáře během instalace. Jinak pokud instalaci povolíte použít nový výchozí název, musíte změnit adresu URL ve všech aplikacích, které odkazují na sadu Web Service SDK (například portál User Portal a Webová služba mobilní aplikace), aby odkazovaly na správné místo.

3. Pokud bylo rozhraní User Portal dříve nainstalováno na serveru agenta PhoneFactor, nainstalujte nový uživatelský portál Multi-Factor Authentication prostřednictvím uživatelského rozhraní serveru Multi-Factor Authentication.

  Výchozí název virtuálního adresáře je nyní **MultiFactorAuth**, a ne **PhoneFactor**. Pokud chcete použít předchozí název, musíte změnit název virtuálního adresáře během instalace. Jinak pokud povolíte instalaci pro použití nového výchozího názvu, doporučujeme kliknout na ikonu portálu pro uživatele na serveru Multi-Factor Authentication a aktualizovat adresu URL portálu pro uživatele na kartě Nastavení.

4. Pokud byly portál pro uživatele nebo webové služby mobilní aplikace dříve nainstalovány na jiném serveru než agent PhoneFactor:

  1. Přejděte do umístění instalace (například C:\Program Files\PhoneFactor) a zkopírujte jeden nebo několik instalačních programů na jiný server. Existují 32bitové a 64bitové verze instalačních programů pro portál pro uživatele a webové služby mobilní aplikace. Nazývají se MultiFactorAuthenticationUserPortalSetupXX.msi a MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Chcete-li nainstalovat portál pro uživatele na webovém serveru, otevřete příkazový řádek jako správce a spusťte soubor MultiFactorAuthenticationUserPortalSetupXX.msi.

    Výchozí název virtuálního adresáře je nyní **MultiFactorAuth**, a ne **PhoneFactor**. Pokud chcete použít předchozí název, musíte změnit název virtuálního adresáře během instalace. Jinak pokud povolíte instalaci pro použití nového výchozího názvu, doporučujeme kliknout na ikonu portálu pro uživatele na serveru Multi-Factor Authentication a aktualizovat adresu URL portálu pro uživatele na kartě Nastavení. Je potřeba informovat stávající uživatele o nové adrese URL.

  3. Přejděte do umístění instalace portálu User Portal (například C:\inetpub\wwwroot\MultiFactorAuth) a upravte soubor web.config. Zkopírujte hodnoty v oddílech appSettings a applicationSettings z původního souboru web.config, který se zálohoval před upgradem, do nového souboru web.config. Pokud byl při instalaci sady Web Service SDK zachován výchozí název virtuálního adresáře, změňte adresu URL v části applicationSettings tak, aby odkazovala na správné místo. Pokud byly v předchozím soubor web.config změněny ostatní výchozí hodnoty, tyto změny budou stejně použity na nový soubor web.config.

> [!NOTE]
> Pokud upgradujete ze starší verze Azure MFA Serveru než 8.0 na verzi 8.0 nebo novější, po upgradu je možné odinstalovat webovou službu mobilní aplikace.

## <a name="next-steps"></a>Další postup

- [Instalace uživatelského portálu](howto-mfaserver-deploy-userportal.md) pro Azure Multi-Factor Authentication Server

- [Konfigurace ověřování systému Windows](howto-mfaserver-windows.md) pro vaše aplikace 
