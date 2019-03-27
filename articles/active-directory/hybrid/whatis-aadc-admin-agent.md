---
title: Co je Agent Azure AD Connect správce – Azure AD Connect | Dokumentace Microsoftu
description: Popisuje nástroje pro synchronizaci a monitorovat místní prostředí s využitím Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b99b7b03898ad72b4d9c7cc2ba9154855475ef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488282"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co je připojení správce agenta služby Azure AD? 
Připojení správy agenta služby Azure AD je novou součástí služby Azure Active Directory Connect, který je nainstalován na serveru služby Azure Active Directory Connect. Používá se k shromažďovat konkrétní údaje z prostředí služby Active Directory, která pomáhá při řešení problémů při otevření případu podpory pracovník odborné pomoci společnosti Microsoft.

Při instalaci, kde se zobrazí Microsoft podporu čeká agenta Azure AD Connect správu pro konkrétní požadavky pro data ze služby Azure Active Directory, získá požadovaná data z prostředí synchronizace a odesílá je do služby Azure Active Directory zpětnou analýzou.

Informace, které načte připojení správy agenta služby Azure AD z vašeho prostředí není uložený žádným způsobem – se zobrazí jenom pro pracovníka podpory společnosti Microsoft jim pomohli při zkoumání a řešení potíží Azure Active Directory Connect případ související podpory, které jste otevřeli

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Instalace Agent Azure AD Connect správce na serveru služby Azure AD Connect 
Po instalaci agenta správce, zobrazí se vám následující dvě nové programů v seznamu "Přidat nebo odebrat programy" v ovládacím panelu serveru: 

![agent pro správu](media/whatis-aadc-admin-agent/adminagent1.png)

Odebrat nebo zrušit instalaci programů, jsou důležitou součástí instalace služby Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jaká data ve své službě synchronizace se zobrazí na služby Microsoftu?
Když otevřete případ podpory, pracovník technické podpory Microsoftu můžete vidět, pro daného uživatele, relevantní data ve službě Active Directory, v prostoru konektoru Active Directory v Azure Active Directory Connect serveru, v prostoru konektoru služby Azure Active Directory v Azure Server služby Active Directory Connect a úložiště Metaverse na serveru Azure Active Directory Connect.

Pracovníka podpory společnosti Microsoft nelze změnit všechna data ve vašem systému nebo naopak neuvidí všechna hesla.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co když nechci pracovníka podpory společnosti Microsoft pro přístup k Moje data? 
 
Pokud nechcete, aby volání služby Microsoftu přístup k datům pro volání podpory mohou znamenat při otevření podpory na portálu: 

  1.    Otevřít **C:\Program Files\Microsoft Azure AD Connect správu Agent\AzureADConnectAdministrationAgentService.exe.config** v poznámkovém bloku.
  2.    Zakázat **UserDataEnabled** nastavení, jak je znázorněno níže. Pokud **UserDataEnabled** nastavení existuje a je nastavena na hodnotu true, pak nastavte na hodnotu false. Pokud toto nastavení neexistuje, obnovte nastavení, jak je znázorněno níže.    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    Uložte konfigurační soubor.
  4.    Restartujte službu agenta Azure AD Connect pro správu, jak je znázorněno níže

![agent pro správu](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).