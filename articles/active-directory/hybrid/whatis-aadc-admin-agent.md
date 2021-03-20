---
title: Co je agent správce Azure AD Connect – Azure AD Connect | Microsoft Docs
description: Popisuje nástroje, které slouží k synchronizaci a monitorování místního prostředí s využitím Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91312953"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co je agent pro správu Azure AD Connectu? 
Azure AD Connect Agent pro správu je nová součást Azure Active Directory Connect, kterou je možné nainstalovat na Azure Active Directory Connect Server. Slouží ke shromažďování konkrétních dat z prostředí služby Active Directory, které pomáhá pracovníkovi podpory společnosti Microsoft řešit problémy při otevření případu podpory. 

>[!NOTE]
>Agent správce není nainstalován a povolen ve výchozím nastavení.  Je nutné nainstalovat agenta, aby bylo možné shromažďovat data pro pomoc s případy podpory.

Při instalaci čeká Agent správy Azure AD Connect na konkrétní požadavky na data od Azure Active Directory, získá požadovaná data z synchronizačního prostředí a pošle je do Azure Active Directory, kde se předloží pracovníkovi podpory Microsoftu. 

Informace o tom, že agent pro správu Azure AD Connect načítá z vašeho prostředí, není uložen jakýmkoli způsobem – zobrazí se pouze pracovníkovi podpory Microsoftu, který jim pomůže při zkoumání a řešení potíží s Azure Active Directory Connect souvisejícím případem podpory, který jste otevřeli Azure AD Connect Agent pro správu není ve výchozím nastavení nainstalován na Azure AD Connect serveru. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalace agenta pro správu Azure AD Connect na server Azure AD Connect 

Požadavky:
1.    Azure AD Connect je nainstalovaná na serveru.
2.    Azure AD Connect Health je nainstalovaná na serveru.

![Agent správce](media/whatis-aadc-admin-agent/adminagent0.png)

Binární soubory agenta správy Azure AD Connect jsou umístěné na serveru AAD Connect. Chcete-li nainstalovat agenta, postupujte následovně:

1.    Otevřít PowerShell v režimu správce
2.    Přejděte do adresáře, kde se nachází aplikace CD "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    Spustit ConfigureAdminAgent.ps1

Po zobrazení výzvy zadejte svoje přihlašovací údaje globálního správce služby Azure AD. Mělo by se jednat o stejné přihlašovací údaje, které jste zadali během Azure AD Connect instalace.

Po instalaci agenta se v seznamu přidat nebo odebrat programy v Ovládacích panelech serveru zobrazí následující dva nové programy: 

![Snímek obrazovky, který zobrazuje seznam přidat nebo odebrat programy obsahující nové programy, které jste přidali.](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jaká data má vaše služba synchronizace zobrazit pro Microsoft Service inženýr? 
Když otevřete případ podpory, může podpora Microsoftu pracovník pro daného uživatele zobrazit relevantní data ve službě Active Directory místo konektoru služby Active Directory na serveru Azure Active Directory Connect, Azure Active Directory konektoru v serveru Azure Active Directory Connect a v úložišti Metaverse na Azure Active Directory Connect serveru. 

Podpora Microsoftu inženýr nemůže změnit žádná data v systému a nevidí žádná hesla. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co když nechcete, aby měl pracovník podpory Microsoftu přístup k mým datům? 
Pokud nechcete, aby měl Microsoft Service inženýr přístup k vašim datům pro volání podpory, můžete po instalaci agenta zakázat jeho funkčnost úpravou konfiguračního souboru služby, jak je popsáno níže: 

1. Otevřete **C:\Program Files\Microsoft Azure AD Connect správa Agent\AzureADConnectAdministrationAgentService.exe.config** v programu Poznámkový blok.
2. Zakažte nastavení **UserDataEnabled** , jak je znázorněno níže. Pokud nastavení **UserDataEnabled** existuje a je nastavené na hodnotu true, pak ho nastavte na false. Pokud nastavení neexistuje, přidejte nastavení, jak je znázorněno níže.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Uložte konfigurační soubor.
4. Restartujte službu agenta správy Azure AD Connect, jak je znázorněno níže.

![Snímek obrazovky, který ukazuje, kde restartovat službu agenta správce Azure AD.](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
