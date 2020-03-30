---
title: Co je agent správy azure ad připojit - Azure AD Connect | Dokumenty společnosti Microsoft
description: Popisuje nástroje používané k synchronizaci a monitorování místního prostředí pomocí Azure AD.
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
ms.openlocfilehash: 79f68635820125161ed4f5777e27a20de9e6fbe8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049382"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co je agent pro správu Azure AD Connectu? 
Agent pro správu služby Azure AD Connect je nová součást služby Azure Active Directory Connect, kterou lze nainstalovat na server Azure Active Directory Connect. Používá se ke shromažďování konkrétních dat z prostředí služby Active Directory, která pomáhá pracovníkovi podpory společnosti Microsoft řešit problémy při otevření případu podpory. 

>[!NOTE]
>Agent správce není ve výchozím nastavení nainstalován a povolen.  Je nutné nainstalovat agenta, aby bylo možné shromažďovat data pro pomoc s případy podpory.

Agent pro správu azure ad připojení čeká na konkrétní požadavky na data z Azure Active Directory, získá požadovaná data z prostředí synchronizace a odešle je do služby Azure Active Directory, kde se zobrazí podpoře Microsoftu. Inženýr. 

Informace, které agent správy Azure AD Connect načte z vašeho prostředí, nejsou žádným způsobem uloženy – zobrazí se pouze pracovníkovi podpory Microsoftu, který jim pomůže při zkoumání a řešení potíží s připojením Azure Active Directory Connect. související případ podpory, který jste otevřeli Agent správy azure ad připojení není nainstalován na serveru Azure AD Connect ve výchozím nastavení. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalace agenta správy Azure AD Connect na server Azure AD Connect 

Požadavky:
1.    Azure AD Connect je nainstalovaný na serveru
2.    Azure AD Connect Health je nainstalovaný na serveru

![agent správce](media/whatis-aadc-admin-agent/adminagent0.png)

Binární soubory agenta správy připojení Azure AD jsou umístěny na serveru AAD Connect. Chcete-li agenta nainstalovat, postupujte takto:

1.    Otevření powershellu v režimu správce
2.    Přejděte do adresáře, ve kterém je aplikace umístěna, na disku CD-ROM C:\Program Files\Microsoft Azure Active Directory Connect\Tools
3.    Spuštění souboru ConfigureAdminAgent.ps1

Po zobrazení výzvy zadejte přihlašovací údaje globálního správce Azure AD. To by mělo být stejné přihlašovací údaje zadané během instalace Azure AD Connect.

Po instalaci agenta se v ovládacím panelu serveru zobrazí následující dva nové programy v seznamu Přidat nebo odebrat programy: 

![agent správce](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jaká data ve službě Synchronizace se zobrazí servisnímu technikovi společnosti Microsoft? 
Když otevřete případ podpory, pracovník podpory společnosti Microsoft uvidí pro daného uživatele příslušná data ve službě Active Directory, místo konektoru služby Active Directory na serveru Azure Active Directory Connect, místo konektoru Azure Active Directory v Azure Server Služby Active Directory Connect a Metaverse na serveru Azure Active Directory Connect 

Pracovník podpory společnosti Microsoft nemůže změnit žádná data ve vašem systému a nemůže zobrazit žádná hesla. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co když nechci, aby k mým datům měl pracovník podpory společnosti Microsoft přístup? 
Po instalaci agenta, Pokud nechcete, aby servisní technik společnosti Microsoft přístup k datům pro volání podpory, můžete zakázat funkce úpravou konfigurační soubor služby, jak je popsáno níže: 

1.    Otevřete **c:\programové soubory\Agent správy připojení Microsoft Azure AD Connect\AzureADConnectAdministrationAgentService.exe.config** v poznámkovém bloku.
2.    Zakázat nastavení **UserDataEnabled,** jak je znázorněno níže. Pokud nastavení **UserDataEnabled** existuje a je nastaveno na hodnotu true, nastavte ho na hodnotu false. Pokud nastavení neexistuje, přidejte nastavení, jak je znázorněno níže.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.    Uložte konfigurační soubor.
4.    Restartujte službu Agent pro správu připojení Azure AD Connect, jak je znázorněno níže

![agent správce](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
