---
title: Co je Agent Azure AD Connect správce – Azure AD Connect | Dokumentace Microsoftu
description: Popisuje nástroje pro synchronizaci a monitorovat místní prostředí s využitím Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687232"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co je agent pro správu Azure AD Connectu? 
Připojení správy agenta služby Azure AD je novou součástí služby Azure Active Directory Connect, který se dá nainstalovat na server služby Azure Active Directory Connect. Používá se k shromažďovat konkrétní údaje z prostředí služby Active Directory, která pomáhá při řešení problémů při otevření případu podpory pracovník odborné pomoci společnosti Microsoft. 

>[!NOTE]
>Agent pro správu není nainstalováno a povoleno ve výchozím nastavení.  Aby bylo možné shromažďovat data, která vám pomůže s případy podpory, musíte nainstalovat agenta.

Při instalaci, kde se zobrazí Microsoft podporu čeká agenta Azure AD Connect správu pro konkrétní požadavky pro data ze služby Azure Active Directory, získá požadovaná data z prostředí synchronizace a odesílá je do služby Azure Active Directory zpětnou analýzou. 

Informace, které načte připojení správy agenta služby Azure AD z vašeho prostředí není uložený žádným způsobem – se zobrazí jenom pro pracovníka podpory společnosti Microsoft jim pomohli při zkoumání a řešení potíží Azure Active Directory Connect související podpora případ, že jste otevřeli agenta služby Azure AD Connect správy není nainstalována na serveru připojení Azure AD ve výchozím nastavení. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalace agenta pro správu připojení Azure AD na serveru služby Azure AD Connect 
Agenta Azure AD Connect správu binární soubory jsou umístěny na serveru se službou AAD Connect. Pokud chcete nainstalovat agenta, postupujte takto: 



1. Otevřete prostředí powershell v režimu správce 
2. Přejděte do adresáře, kde je umístěn cd "C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles" aplikace 
3. Spusťte aplikaci AADConnectAdminAgentSetup.exe 
 
Po zobrazení výzvy zadejte přihlašovací údaje globálního správce Azure AD. 

>[!NOTE]
>Existuje známý problém ve kterém budete vyzváni k zadání pověření více než jednou. Tato chyba bude opravena v další vydané verzi.

Po instalaci agenta, zobrazí se vám následující dvě nové programů v seznamu "Přidat nebo odebrat programy" v ovládacím panelu serveru: 

![agent pro správu](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jaká data ve své službě synchronizace se zobrazí na služby Microsoftu? 
Při otevření případu podpory pracovník technické podpory Microsoftu můžete vidět, pro daného uživatele, relevantní data ve službě Active Directory, v prostoru konektoru Active Directory v Azure Active Directory Connect serveru, v prostoru konektoru služby Azure Active Directory v Azure Server služby Active Directory Connect a úložiště Metaverse na serveru Azure Active Directory Connect. 

Pracovníka podpory společnosti Microsoft nelze změnit všechna data ve vašem systému nebo naopak neuvidí všechna hesla. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co když nechci pracovníka podpory společnosti Microsoft pro přístup k Moje data? 
Po instalaci agenta, pokud nechcete, aby služba Microsoftu přístup k datům pro volání podpory, můžete zakázat funkci tak, že upravíte konfigurační soubor služby, jak je popsáno níže: 

1.  Otevřít **C:\Program Files\Microsoft Azure AD Connect správu Agent\AzureADConnectAdministrationAgentService.exe.config** v poznámkovém bloku.
2.  Zakázat **UserDataEnabled** nastavení, jak je znázorněno níže. Pokud **UserDataEnabled** nastavení existuje a je nastavena na hodnotu true, pak nastavte na hodnotu false. Pokud toto nastavení neexistuje, obnovte nastavení, jak je znázorněno níže.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Uložte konfigurační soubor.
4.  Restartujte službu agenta Azure AD Connect pro správu, jak je znázorněno níže

![agent pro správu](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
