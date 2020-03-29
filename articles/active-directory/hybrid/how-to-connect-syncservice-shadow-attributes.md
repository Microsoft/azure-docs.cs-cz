---
title: Atributy stínové služby synchronizace služby Azure AD Connect | Dokumenty společnosti Microsoft
description: Popisuje, jak stínové atributy fungují ve službě synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60384697"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributy stínů synchronizační služby Azure AD Connect
Většina atributů jsou reprezentovány stejným způsobem ve službě Azure AD jako ve vaší místní službě Active Directory. Ale některé atributy mají některé speciální zpracování a hodnota atributu ve službě Azure AD se může lišit od toho, co azure ad connect synchronizuje.

## <a name="introducing-shadow-attributes"></a>Zavedení stínových atributů
Některé atributy mají dvě reprezentace ve službě Azure AD. Místní hodnota a vypočtená hodnota jsou uloženy. Tyto další atributy se nazývají stínové atributy. Dva nejběžnější atributy, kde se zobrazí toto chování jsou **userPrincipalName** a **proxyAddress**. Ke změně hodnot atributů dojde, když jsou v těchto atributech hodnoty představující neověřené domény. Ale synchronizační modul v Connect přečte hodnotu v atributu stín, takže z jeho pohledu byl atribut potvrzen službou Azure AD.

Nelze zobrazit stínové atributy pomocí portálu Azure nebo s PowerShell. Ale pochopení konceptu vám pomůže řešit určité scénáře, kde atribut má různé hodnoty v místním prostředí a v cloudu.

Chcete-li lépe porozumět chování, podívejte se na tento příklad z Fabrikam:  
![Domény](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Mají více přípon UPN v místní službě Active Directory, ale ověřili pouze jednu.

### <a name="userprincipalname"></a>userPrincipalName (Hlavní název uživatele)
Uživatel má v neověřené doméně následující hodnoty atributů:

| Atribut | Hodnota |
| --- | --- |
| místní userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Atribut userPrincipalName je hodnota, která se zobrazí při použití prostředí PowerShell.

Vzhledem k tomu, že skutečná hodnota místního atributu je uložena ve službě Azure AD, při ověření fabrikam.com domény, Azure AD aktualizuje atribut userPrincipalName s hodnotou z shadowUserPrincipalName. Není třeba synchronizovat žádné změny z Azure AD Connect pro tyto hodnoty, které mají být aktualizovány.

### <a name="proxyaddresses"></a>proxyAddresses
Stejný proces pouze pro ověřené domény také dochází pro proxyAdresy, ale s některé další logiku. Kontrola ověřených domén se děje pouze pro uživatele poštovní schránky. Uživatel nebo kontakt s povolenou poštou představují uživatele v jiné organizaci serveru Exchange a k těmto objektům můžete přidat libovolné hodnoty v proxyAddresses.

Pro uživatele poštovní schránky, a to buď místně nebo v Exchange Online, se zobrazí pouze hodnoty pro ověřené domény. Mohlo by to vypadat takto:

| Atribut | Hodnota |
| --- | --- |
| místní proxyAdresy | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Proxy Adresy Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

V tomto případě **smtp:abbie.spencer\@fabrikam.com** byl odebrán, protože tato doména nebyla ověřena. Ale Exchange také přidal **SIP:abbie.spencer\@fabrikamonline.com**. Společnost Fabrikam nepoužívá lync/skype místně, ale Azure AD a Exchange Online se na to připravují.

Tato logika pro proxyAddresses se označuje jako **ProxyCalc**. ProxyCalc je vyvolána s každou změnou na uživatele, když:

- Uživateli byl přiřazen plán služeb, který zahrnuje Exchange Online i v případě, že uživatel nebyl licencován pro Exchange. Pokud je například uživateli přiřazena skladová položka Office E3, ale byla mu přiřazena jenom SharePoint Online. To platí i v případě, že vaše poštovní schránka je stále místní.
- Atribut msExchRecipientTypeDetails má hodnotu.
- Provedete změnu proxyAddresses nebo userPrincipalName.

ProxyCalc může nějakou dobu trvat, než zpracuje změnu u uživatele a není synchronní s procesem exportu Azure AD Connect.

> [!NOTE]
> Logika ProxyCalc má některé další chování pro pokročilé scénáře, které nejsou popsány v tomto tématu. Toto téma je k dispozici pro pochopení chování a není dokument ovat všechny vnitřní logiku.

### <a name="quarantined-attribute-values"></a>Hodnoty atributů v karanténě
Atributy stínů se používají také v případě, že existují duplicitní hodnoty atributů. Další informace naleznete [v tématu duplicitní odolnost atributu .](how-to-connect-syncservice-duplicate-attribute-resiliency.md)

## <a name="see-also"></a>Viz také
* [Synchronizace Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
