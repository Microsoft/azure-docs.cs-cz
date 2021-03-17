---
title: Řešení potíží s daty auditu ověřené změny v doméně | Microsoft Docs
description: Poskytuje informace, které se zobrazí v protokolech Azure Active Directory aktivit při změně domény ověřené uživateli.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87117427"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Řešení potíží: Auditovat data při ověřené změně domény 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Mám na uživatele hodně změn a nejste si jistí, jakou příčinu to způsobuje.

### <a name="symptoms"></a>Příznaky

Zkontrolujte protokoly auditu Azure AD a podívejte se na více aktualizací uživatelů, ke kterým dochází v tenantovi Azure AD. Tyto události **aktualizace uživatele** nezobrazují informace objektu **actor** , což způsobuje nejistotu na to, co aktivovalo velké změny pro uživatele. 

### <a name="cause"></a>Příčina

 Běžným důvodem pro změny hmotnosti objektu je nesynchronní operace back-endu s názvem **ProxyCalc**.  **ProxyCalc** je logika, která určuje odpovídající **userPrincipalName** a **proxy adresy**, které jsou aktualizovány v Azure AD Users, groups nebo Contacts. V rámci **ProxyCalc** je potřeba zajistit, aby všechny názvy **userPrincipalName** a **proxy** byly ve službě Azure AD v každém okamžiku konzistentní. **ProxyCalc** se musí aktivovat explicitní změnou, jako je ověřená změna domény a neprovádí se trvale na pozadí jako úloha. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Co znamená konzistence UserPrincipalName? 

V případě pouze cloudových uživatelů konzistence znamená, že hodnota **userPrincipalName** je nastavena na ověřenou příponu domény. Při zpracování nekonzistentního formátu **userPrincipalName** se **ProxyCalc** převede na výchozí příponu onmicrosoft.com, například: username@Contoso.onmicrosoft.com 

U synchronizovaných uživatelů konzistence znamená, že atribut **userPrincipalName** je nastaven na ověřenou příponu domény a odpovídá místní hodnotě **userPrincipalName** (ShadowUserPrincipalName). Když je zpracována nekonzistentní hodnota **userPrincipalName** , **ProxyCalc** se vrátí ke stejné hodnotě jako **ShadowUserPrincipalName** nebo v případě, že byla přípona domény z klienta odebrána, převede ji na výchozí příponu *. onmicrosoft.com. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Co znamená konzistence adres proxy? 

V případě pouze cloudových uživatelů konzistence znamená, že se adresy proxy shodují s příponou ověřené domény. Když se zpracuje nekonzistentní proxy adresa, **ProxyCalc** se převede na výchozí příponu *. onmicrosoft.com domény, třeba: SMTP:username@Contoso.onmicrosoft.com 

U synchronizovaných uživatelů konzistence znamená, že se adresy proxy shodují s hodnotami místních adres proxy (y) (tj. ShadowProxyAddresses). Očekává se, že **proxyAddresses** budou synchronizované s **ShadowProxyAddresses**. Pokud má synchronizovaný uživatel přiřazenou licenci k Exchangi, musí proxy adresy odpovídat hodnotám místních proxy adres a musí taky odpovídat ověřené příponě domény. V tomto scénáři **ProxyCalc** upraví nekonzistentní proxy adresu s neověřenou příponou domény a odebere se z objektu ve službě Azure AD. Pokud se tato neověřená doména ověří později, **ProxyCalc** se přepočítá a přidá adresu proxy serveru z **ShadowProxyAddresses** zpátky do objektu ve službě Azure AD.  

> [!NOTE]
> Aby se u synchronizovaných objektů zabránilo **ProxyCalc** logice při výpočtu neočekávaných výsledků, je nejlepší nastavit proxy adresy na ověřenou doménu Azure AD na místním objektu.  

  
Jedna z úloh správce, které by mohly aktivovat **ProxyCalc** , je vždy, když dojde ke změně ověřené domény. Tato úloha nastane pokaždé, když se do tenanta služby Azure AD přidá nebo odebere ověřená doména, která interně aktivuje **ProxyCalc**.  

Pokud například přidáte ověřenou doménu Fabrikam.com do svého tenanta Contoso.onmicrosoft.com, tato akce spustí operaci ProxyCalc pro všechny objekty v tenantovi. Tato událost bude zachycena v protokolech auditu Azure AD jako **aktualizace událostí uživatele** předchází událost **Přidání ověřené domény** . Na druhé straně, pokud se Fabrikam.com odebral z tenanta Contoso.onmicrosoft.com, pak před všemi událostmi **aktualizace uživatele** bude předcházet událost **Odebrání ověřené domény** .   

#### <a name="additional-notes"></a>Další poznámky:

ProxyCalc nezpůsobí změny určitých objektů, které: 

- nemáte aktivní licenci Exchange 
- mít **MSExchRemoteRecipientType** nastavenou na hodnotu null 
- nejsou považovány za sdílený prostředek. Sdílený prostředek je v případě, že **CloudMSExchRecipientDisplayType** obsahuje jednu z následujících hodnot: **MailboxUser (Shared)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **Group Mailbox**, **plánujeme poštovní schránku**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** . 
  
 Aby bylo možné sestavovat více korelace mezi dvěma různými událostmi, společnost Microsoft pracuje na aktualizaci informací objektu **actor** v protokolech auditu, aby identifikovala tyto změny aktivované ověřenou změnou domény. Tato akce vám pomůže zkontrolovat, kdy došlo k události změny potvrzené domény a začala aktualizovat objekty ve svém tenantovi. 

Ve většině případů neexistují žádné změny uživatelů, protože jejich **userPrincipalName** a **proxy adresy** jsou konzistentní, takže pracujeme na zobrazení v protokolu auditu pouze ty aktualizace, které způsobily skutečnou změnu objektu. Tato akce zabrání hluku v protokolech auditu a správcům pomůže sladit zbývající změny uživatelů s potvrzenou událostí změny domény, jak je vysvětleno výše. 

## <a name="next-steps"></a>Další kroky

[Azure AD Connect atributy stínové služby synchronizace](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
