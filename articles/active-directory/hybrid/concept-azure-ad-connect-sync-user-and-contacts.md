---
title: 'Synchronizace Azure AD Connect: Principy uživatelů, skupin a kontaktů | Dokumentace Microsoftu'
description: Vysvětluje, uživatele, skupiny nebo kontakty ve službě Azure AD Connect sync.
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
ms.openlocfilehash: 7605a8cee265822f133b3f72ce5de90add5fc0d0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210539"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synchronizace Azure AD Connect: Principy uživatelů, skupin a kontaktů
Existuje několik různých důvodů, proč byste měli několik doménových struktur služby Active Directory a existuje několik topologií jiného nasazení. Běžné modely zahrnují při nasazování prostředků účtu a doménových struktur sync'ed GAL po fúze a akvizice. Ale i v případě, že existují čistě modely, hybridní modely jsou běžné také. Výchozí konfigurace ve službě Azure AD Connect sync nepřebírá žádné konkrétní modelu, ale v závislosti na tom, jak uživatel odpovídající byla vybrána v instalační příručce, může být dodržen jiné chování.

V tomto tématu doporučujeme projít chování výchozí konfigurace v určitých topologiích. Provedeme konfiguraci a Editor pravidel synchronizace je možné se podívat na konfiguraci.

Existuje několik obecná pravidla, předpokládá, konfigurace:
* Bez ohledu na pořadí naimportujeme ze zdroje Active Directory konečný výsledek musí být vždy stejné.
* Aktivní účet bude vždy měli přispívat přihlašovací údaje, včetně **userPrincipalName** a **sourceAnchor**.
* Zakázaný účet přispějí userPrincipalName a sourceAnchor, pokud není propojená poštovní schránka, pokud není žádný aktivní účet, která se má najít.
* Účet s poštovní schránku propojenou se nikdy používat pro userPrincipalName a sourceAnchor. Předpokládá se, že aktivní účet bude nalezen později.
* Objekt kontaktu může zřídit do služby Azure AD jako kontakt nebo jako uživatel. Ve skutečnosti neznáte dokud byly zpracovány všechny zdrojové doménové struktury služby Active Directory.

## <a name="groups"></a>Skupiny
Důležité body je potřeba vědět při synchronizaci skupin služby Active Directory do služby Azure AD:

* Azure AD Connect nezahrnuje integrované zabezpečení skupiny z synchronizace adresářů.

* Azure AD Connect nepodporuje synchronizaci [primární členství ve skupinách](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) do služby Azure AD.

* Azure AD Connect nepodporuje synchronizaci [členství v dynamické skupině distribučních](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) do služby Azure AD.

* Synchronizovat skupiny služby Active Directory k Azure AD jako poštovní skupina:

    * Pokud skupiny *proxyAddress* atributu je prázdný, jeho *e-mailu* atribut musí mít hodnotu.

    * Pokud skupina *proxyAddress* atribut je prázdný, musí obsahovat alespoň jednu hodnotu adresy proxy serveru SMTP. Zde je několik příkladů:
    
      * Skupinu služby Active Directory, jehož atribut proxyAddress má hodnotu *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* nebudou poštovní ve službě Azure AD. Nemá adresu SMTP.
      
      * Skupinu služby Active Directory, jehož atribut proxyAddress má hodnoty *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe@contoso.com"}* bude Poštovní ve službě Azure AD.
      
      * Skupinu služby Active Directory, jehož atribut proxyAddress má hodnoty *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe@contoso.com"}* bude také povolenou poštu ve službě Azure AD.

## <a name="contacts"></a>Kontakty
Kontakty představující uživatele v jiné doménové struktuře, je běžné po fúze a akvizice kde GALSync řešení, je přemostění dva nebo více doménovými strukturami systému Exchange. Objekt kontaktu se vždy připojuje z prostoru konektoru do úložiště metaverse pomocí atributu e-mailu. Pokud již existuje objekt kontaktní nebo uživatel se stejnou adresou e-mailu, objekty jsou spojeny dohromady. To je nakonfigurovaný v pravidle **v ze služby AD – připojte se k kontakt**. K dispozici je také pravidlo s názvem **v ze služby AD – kontaktujte běžné** s tok atributů pro atribut úložiště metaverze **sourceObjectType** s konstanty **kontakt**. Toto pravidlo má velmi nízkou prioritu, pokud jakýkoli objekt uživatele se propojuje se na stejný objekt úložiště metaverse, bude toto pravidlo **v ze služby AD – běžné uživatele** přispějí hodnotu uživatele na tento atribut. S tímto pravidlem bude mít tento atribut hodnoty kontaktu, pokud byl připojen žádný uživatel a uživatele Pokud byl nalezen minimálně jeden uživatel.

Pro objekt do služby Azure AD, odchozí pravidlo zřizování **na AAD – obraťte se na připojení** vytvoří objekt kontaktu, pokud atribut úložiště metaverze **sourceObjectType** je nastavena na **kontaktovat**. Pokud tento atribut je nastaven na **uživatele**, potom pravidla **na AAD – uživatel připojit** vytvoří objekt uživatele.
Je možné, že objekt je povýšen z kontaktu na uživatele při další zdrojové aktivního adresáře byly naimportovány a synchronizovány.

Například v topologii GALSync jsme najdete kontaktní objekty pro všechny uživatele v druhé doménové struktury při naimportujeme první doménovou strukturu. Tím se fáze nových kontaktů objektů v konektoru AAD. Když později importovat a synchronizovat druhé doménové struktury, budeme vyhledání skutečných uživatelů a připojte je k existující objekty úložiště metaverse. Potom jsme se odstranit objekt kontaktu v AAD a místo toho vytvořte nový objekt uživatele.

Pokud máte topologii, kde uživatelé jsou reprezentováni jako kontakty, ujistěte se, zda že jste vybrali pro vyhledání uživatelů na atribut mail v instalační příručce. Pokud vyberete jinou možnost, bude mít pořadí závislé konfigurace. Kontaktní objekty se vždy připojí u atributu mail, ale uživatelské objekty se připojí pouze u atributu mail, pokud tuto možnost jste vybrali v Průvodci instalací. Může potom skončíte se dva objekty v úložišti metaverse na stejný atribut e-mailu Pokud objekt kontaktu byl importován před objektu user. Během exportu do služby Azure AD bude vyvolána k chybě. Toto chování je záměrné a naznačují chybný data nebo, že topologie nebyla správně identifikovat během instalace.

## <a name="disabled-accounts"></a>Zakázané účty
Zakázané účty se synchronizují také do služby Azure AD. Zakázané účty jsou společné pro reprezentaci prostředků v systému Exchange, například konferenčních místnostech dojde. Výjimkou je uživatelé s poštovní schránku propojenou; Jak už jsme zmínili tyto nikdy zřídí účet do služby Azure AD.

Předpokladem je, že pokud účet zakázaný uživatel se nenašel, pak jsme nenajde jiné aktivní účet později a je zajištěno objektu do služby Azure AD userPrincipalName a sourceAnchor nalezen. V případě, že jiné aktivní účet se připojí ke stejnému objektu úložiště metaverse, bude použita jeho hodnota userPrincipalName a sourceAnchor.

## <a name="changing-sourceanchor"></a>Změna sourceAnchor
Pokud objekt byly exportovány do služby Azure AD a změna sourceAnchor již není povolena. Pokud byl objekt exportovat atribut úložiště metaverze **cloudSourceAnchor** nastavená **sourceAnchor** hodnotu přijat službou Azure AD. Pokud **sourceAnchor** se změní a neodpovídá **cloudSourceAnchor**, pravidlo **na AAD – uživatel připojit** vyvolá chybu **má atribut sourceAnchor Změnit**. V takovém případě konfigurace nebo dat musí být opraveny stejné sourceAnchor je k dispozici v úložišti metaverse znovu předtím, než objekt lze znovu synchronizovat.

## <a name="additional-resources"></a>Další prostředky
* [Synchronizace služby Azure AD Connect: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

