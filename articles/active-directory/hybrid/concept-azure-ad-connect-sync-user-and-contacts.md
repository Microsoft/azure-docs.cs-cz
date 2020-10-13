---
title: 'Azure AD Connect synchronizace: principy uživatelů, skupin a kontaktů | Microsoft Docs'
description: Vysvětluje uživatele, skupiny a kontakty v Azure AD Connect synchronizaci.
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
ms.openlocfilehash: d9470e9af38fdd814f5059538656e6a3dbb8e3a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279308"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect synchronizace: principy uživatelů, skupin a kontaktů
Existuje několik různých důvodů, proč byste měli mít několik doménových struktur služby Active Directory a existuje několik různých topologií nasazení. Mezi běžné modely patří nasazení prostředků účtů a doménové struktury sync'ed po fúzi & získání. Ale i v případě, že existují čistě modely, jsou běžné i hybridní modely. Výchozí konfigurace v Azure AD Connect synchronizace nepředpokládá žádný konkrétní model, ale v závislosti na tom, jak byla vybrána shoda uživatele v instalační příručce, je možné pozorovat různá chování.

V tomto tématu se seznámíte s tím, jak se výchozí konfigurace chová v určitých topologiích. Provedeme konfiguraci a pomocí editoru pravidel synchronizace se můžete podívat na konfiguraci.

Konfigurace předpokládá několik obecných pravidel:
* Bez ohledu na to, jaké pořadí importujeme ze zdrojových aktivních adresářů, by měl být konečný výsledek vždycky stejný.
* Aktivní účet bude vždy přispívat k přihlašovacím údajům, včetně **userPrincipalName** a **sourceAnchor**.
* Zakázaný účet bude přispívat jako userPrincipalName a sourceAnchor, pokud se nejedná o propojenou poštovní schránku, pokud není k dispozici žádný aktivní účet.
* Účet s propojenou poštovní schránkou nebude nikdy použit pro atribut userPrincipalName a sourceAnchor. Předpokládá se, že se později najde aktivní účet.
* Objekt kontaktů může být ve službě Azure AD zřízen jako kontakt nebo jako uživatel. Opravdu nevíte, dokud nebudou zpracovány všechny zdrojové doménové struktury služby Active Directory.

## <a name="groups"></a>Skupiny
Důležité body, které je potřeba znát při synchronizaci skupin ze služby Active Directory do Azure AD:

* Azure AD Connect vyloučí předdefinované skupiny zabezpečení z synchronizace adresářů.

* Azure AD Connect nepodporuje synchronizaci [členství primární skupiny](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) do služby Azure AD.

* Azure AD Connect nepodporuje synchronizaci [členství dynamických distribučních skupin](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups?view=exchserver-2019) do Azure AD.

* Postup synchronizace skupiny služby Active Directory se službou Azure AD jako skupina s povolenými e-maily:

    * Pokud je atribut *ProxyAddress* skupiny prázdný, musí mít jeho atribut *mail* hodnotu.

    * Pokud atribut *ProxyAddress* skupiny není prázdný, musí obsahovat alespoň jednu hodnotu adresy proxy serveru SMTP. Tady je několik příkladů:
    
      * Skupina služby Active Directory, jejíž atribut proxyAddress má hodnotu *{"X500:/0 = contoso. com/ou = Users/CN = test Group"}* nebude v Azure AD povolena pro poštu. Nemá adresu SMTP.
      
      * Skupina služby Active Directory, jejíž atribut proxyAddress má hodnoty *{"X500:/0 = contoso. com/ou = Users/CN = test Group", "SMTP: johndoe \@ contoso.com"}* bude v Azure AD povolená pro poštu.
      
      * Skupina služby Active Directory, jejíž atribut proxyAddress má hodnoty *{"X500:/0 = contoso. com/ou = Users/CN = test Group", "SMTP: johndoe \@ contoso.com"}* bude také ve službě Azure AD zapnutá pro poštu.

## <a name="contacts"></a>Kontakty
Kontakty, které představují uživatele v jiné doménové struktuře, jsou běžné po sloučení & získání, kde řešení GALSync přemostění dvou nebo více doménových struktur Exchange. Objekt Contact se vždy připojuje z prostoru konektoru k úložišti Metaverse pomocí atributu mail. Pokud již existuje objekt kontaktu nebo objekt uživatele se stejnou e-mailovou adresou, jsou objekty spojeny dohromady. Tato konfigurace je nakonfigurovaná v pravidle **z AD – kontaktujte připojení**. Existuje taky pravidlo s názvem **ve službě AD – kontakt je společný** s tokem atributů na atribut úložiště metaverse **sourceObjectType** s konstantním **kontaktem**. Toto pravidlo má velmi nízkou prioritu, takže pokud je libovolný objekt uživatele připojený ke stejnému objektu úložiště metaverse, pak pravidlo **v rámci služby AD – společný uživatel** přispěje uživateli hodnotu k tomuto atributu. S tímto pravidlem bude mít tento atribut hodnotu Contact, pokud není připojen žádný uživatel a hodnota uživatel, pokud byl nalezen alespoň jeden uživatel.

Při zřizování objektu pro Azure AD se odchozí pravidlo vychází z **AAD – kontaktní spojení** vytvoří objekt Contact, pokud je atribut úložiště metaverse **sourceObjectType** nastaven na hodnotu **kontakt**. Pokud je tento atribut nastavený na hodnotu **uživatel**, pak se pravidlo **odpojí do AAD – připojení uživatele** místo toho vytvoří objekt uživatele.
Je možné, že je objekt povýšen z kontaktu na uživatele, když se naimportuje a synchronizuje více zdrojových aktivních adresářů.

Například ve GALSync topologii najdete při importu první doménové struktury objekty kontaktů pro všechny ve druhé doménové struktuře. Tím dojde k přípravě nových objektů kontaktů v konektoru AAD. Když později naimportujeme a synchronizujete druhou doménovou strukturu, zjistíme reálné uživatele a spojíme je s existujícími objekty Metaverse. Odstraníme objekt Contact v AAD a místo toho vytvoříme nový objekt uživatele.

Pokud máte topologii, ve které jsou uživatelé zastoupeni jako kontakty, ujistěte se, že jste vybrali možnost odpovídat uživatelům v atributu mail v instalační příručce. Pokud vyberete jinou možnost, budete mít konfiguraci závislou na objednávkách. Objekty kontaktů se vždy spojí s atributem mail, ale objekty uživatele se budou spojovat pouze s atributem mail, pokud byla tato možnost vybrána v instalační příručce. Pak můžete ukončit dva různé objekty v úložišti Metaverse se stejným atributem pošty, pokud byl objekt kontaktu importován před objektem uživatele. Během exportu do služby Azure AD bude vyvolána chyba. Toto chování je záměrné a by znamenalo chybná data nebo to, že během instalace nebyla topologie správně zjištěna.

## <a name="disabled-accounts"></a>Zakázané účty
Zakázané účty se synchronizují i s Azure AD. Zakázané účty jsou běžné pro reprezentaci prostředků v systému Exchange, například konferenčních místnostech. Výjimkou jsou uživatelé s propojenou poštovní schránkou; Jak už jsme uvedli, tím se nikdy nezřídí účet pro Azure AD.

Předpokladem je, že pokud se najde zakázaný uživatelský účet, nebudeme později moct najít další aktivní účet a objekt se zřídí do Azure AD s nalezeným atributem userPrincipalName a sourceAnchor. V případě, že se ke stejnému objektu úložiště metaverse připojí jiný aktivní účet, použije se jeho userPrincipalName a sourceAnchor.

## <a name="changing-sourceanchor"></a>Změna sourceAnchor
Když se objekt vyexportuje do služby Azure AD, nebudete už moct sourceAnchor změnit. Když se objekt vyexportuje, atribut úložiště metaverse **cloudSourceAnchor** se nastaví s hodnotou **sourceAnchor** přijatou službou Azure AD. Pokud dojde ke změně **sourceAnchor** a neodpovídá **cloudSourceAnchor**, pravidlo **pro AAD – připojení uživatele** vyvolá **atribut Error sourceAnchor**, který se změnil. V takovém případě musí být konfigurace nebo data opraveny, aby se stejné sourceAnchor v úložišti Metaverse znovu nacházely ještě předtím, než bude možné objekt znovu synchronizovat.

## <a name="additional-resources"></a>Další materiály
* [Azure AD Connect synchronizace: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)