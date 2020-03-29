---
title: 'Synchronizace služby Azure AD Connect: Principy uživatelů, skupin a kontaktů | Dokumenty společnosti Microsoft'
description: Vysvětluje uživatele, skupiny a kontakty v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661747754369c17ca98ae69d477e04124b6a2942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245481"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synchronizace azure ad připojení: principy uživatelů, skupin a kontaktů
Existuje několik různých důvodů, proč byste měli více doménových struktur služby Active Directory a existuje několik různých topologie nasazení. Běžné modely zahrnují nasazení prostředků účtu a hlavní hoseznamu adres v doménových strukturách po sloučení & akvizice. Ale i když existují čistě modely, hybridní modely jsou také běžné. Výchozí konfigurace v synchronizaci Azure AD Connect nepředpokládá žádný konkrétní model, ale v závislosti na tom, jak byla v průvodci instalací vybrána shoda uživatelů, lze pozorovat různé chování.

V tomto tématu projdeme, jak se chová výchozí konfigurace v určitých topologii. Projdeme konfiguraci a Editor pravidel synchronizace lze použít k pohledu na konfiguraci.

Konfigurace předpokládá několik obecných pravidel:
* Bez ohledu na to, kterou objednávku importujeme ze zdrojových aktivních adresářů, konečný výsledek by měl být vždy stejný.
* Aktivní účet bude vždy přispívat přihlašovací informace, včetně **userPrincipalName** a **sourceAnchor**.
* Zakázaný účet bude přispívat userPrincipalName a sourceAnchor, pokud se nejedná o propojenou poštovní schránku, pokud není nalezen žádný aktivní účet.
* Účet s propojenou poštovní schránkou se nikdy nepoužije pro userPrincipalName a sourceAnchor. Předpokládá se, že aktivní účet bude nalezen později.
* Objekt kontaktu může být zřízena do Služby Azure AD jako kontakt nebo jako uživatel. Ve skutečnosti nevíte, dokud nebyly zpracovány všechny doménové struktury zdrojové služby Active Directory.

## <a name="groups"></a>Skupiny
Důležité body, které je třeba znát při synchronizaci skupin ze služby Active Directory do služby Azure AD:

* Azure AD Connect vylučuje předdefinované skupiny zabezpečení ze synchronizace adresářů.

* Azure AD Connect nepodporuje synchronizaci [členství primární skupiny](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) do Azure AD.

* Azure AD Connect nepodporuje synchronizaci [členství dynamické distribuční skupiny](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) do Azure AD.

* Synchronizace skupiny služby Active Directory do služby Azure AD jako skupiny s povolenou poštou:

    * Pokud je atribut *proxyAddress* skupiny prázdný, musí mít atribut *mail* hodnotu.

    * Pokud je atribut *proxyAddress* skupiny neprázdný, musí obsahovat alespoň jednu hodnotu proxy adresy SMTP. Zde je několik příkladů:
    
      * Skupina služby Active Directory, jejíž atribut proxyAddress má hodnotu *{"X500:/0=contoso.com/ou=users/cn=testgroup"},* nebude ve službě Azure AD povolena poštou. Nemá adresu SMTP.
      
      * Skupina služby Active Directory, jejíž atribut proxyAddress obsahuje hodnoty *\@{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe contoso.com"}* bude ve službě Azure AD povolena poštou.
      
      * Skupina služby Active Directory, jejíž atribut proxyAddress obsahuje hodnoty *{"X500:/0=contoso.com/ou=users/cn=testgroup",\@"smtp:johndoe contoso.com"},* bude také povolena poštou ve službě Azure AD.

## <a name="contacts"></a>Kontakty
Mít kontakty zastupující uživatele v jiné doménové struktuře je běžné po sloučení & akvizici, kde řešení GALSync přemosťuje dvě nebo více doménových struktur exchange. Objekt kontaktu se vždy připojuje z prostoru spojnice k metaverse pomocí atributu mail. Pokud již existuje objekt kontaktu nebo objekt uživatele se stejnou e-mailovou adresou, jsou objekty spojeny dohromady. To je nakonfigurováno v pravidle **V ze seznamu AD – kontaktní spojení**. K dispozici je také pravidlo s názvem **In z AD – Kontakt Společné** s tokem atributu na atribut metaverse **sourceObjectType** s konstantní **Kontakt**. Toto pravidlo má velmi nízkou prioritu, takže pokud je libovolný objekt uživatele připojen ke stejnému metaverse objektu, pak pravidlo **In z AD – User Common** přispěje hodnotou Uživatel k tomuto atributu. S tímto pravidlem bude mít tento atribut hodnotu Kontakt, pokud nebyl připojen žádný uživatel, a hodnotu Uživatel, pokud byl nalezen alespoň jeden uživatel.

Pro zřizování objektu do Azure AD, odchozí pravidlo **Out to AAD – Spojení kontaktu** vytvoří objekt kontaktu, pokud je atribut metaverse **sourceObjectType** nastaven na **Kontakt**. Pokud je tento atribut nastaven na **položku Uživatel**, vytvoří místo toho objekt uživatele pravidlo **Out to AAD – User Join.**
Je možné, že objekt je povýšen z kontakt na uživatele při importu a synchronizaci více zdrojových aktivních adresářů.

Například v topologii GALSync najdeme kontaktní objekty pro všechny v druhé doménové struktuře při importu první doménové struktury. Tím se vytvoří nové kontaktní objekty v konektoru AAD. Když později importujeme a synchronizujeme druhou doménovou strukturu, najdeme skutečné uživatele a připojíme je k existujícím metaverse objektům. Poté odstraníme objekt kontaktu v aad a místo toho vytvoříme nový objekt uživatele.

Pokud máte topologii, kde jsou uživatelé reprezentováni jako kontakty, ujistěte se, že vyberete tak, aby odpovídaly uživatelům na atribut pošty v instalační příručce. Pokud vyberete jinou možnost, budete mít konfiguraci závislou na objednávce. Objekty kontaktu se vždy připojí k atributu mail, ale objekty uživatele se připojí pouze k atributu mail, pokud byla tato možnost vybrána v instalační příručce. Potom můžete skončit se dvěma různými objekty v metaverse se stejným atributem mail, pokud byl objekt kontaktu importován před objektem uživatele. Během exportu do Azure AD bude vyvolána chyba. Toto chování je záměrné a označuje chybná data nebo že topologie nebyla během instalace správně identifikována.

## <a name="disabled-accounts"></a>Zakázané účty
Zakázané účty jsou také synchronizovány do služby Azure AD. Zakázané účty jsou společné pro reprezentaci prostředků ve Exchange, například konferenčních místností. Výjimkou jsou uživatelé s propojenou poštovní schránkou. jak již bylo zmíněno, tyto nikdy zřídit účet Azure AD.

Předpokladem je, že pokud je nalezen zakázaný uživatelský účet, pak nenajdeme jiný aktivní účet později a objekt je zřízena do Služby Azure AD s userPrincipalName a sourceAnchor nalezeno. V případě, že jiný aktivní účet se připojí ke stejnému metaverse objektu, pak jeho userPrincipalName a sourceAnchor bude použit.

## <a name="changing-sourceanchor"></a>Změna sourceAnchor
Když byl objekt exportován do Azure AD pak není povoleno změnit sourceAnchor už. Po exportu objektu metaverse atribut **cloudSourceAnchor** je nastavena s **sourceAnchor** hodnotu přijatou Azure AD. Pokud **sourceAnchor** je změněn a neodpovídá **cloudSourceAnchor**, pravidlo **Out to AAD – User Join** vyvolá chyba **sourceAnchor atribut změnil**. V tomto případě musí být opravena konfigurace nebo data, aby stejný sourceAnchor je k dispozici v metaverse znovu před objekt může být znovu synchronizován.

## <a name="additional-resources"></a>Další zdroje
* [Synchronizace připojení Azure AD: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

