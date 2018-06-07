---
title: Azure AD Connect sync služby stínové atributy | Microsoft Docs
description: Popisuje, jak fungují stínové atributy ve službě Azure AD Connect sync service.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bd1ede2bf8ff642b7be0869e54a6f037b01dd262
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593391"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect sync služby stínové atributy
Většina atributy jsou reprezentována stejným způsobem jako ve službě Azure AD, jako jsou ve vaší místní službě Active Directory. Ale některé atributy mají některé zvláštní zpracování a hodnota atributu ve službě Azure AD může být jiná než co Azure AD Connect synchronizuje.

## <a name="introducing-shadow-attributes"></a>Představení stínové atributy
Některé atributy mají dva reprezentace ve službě Azure AD. Místní hodnota a vypočítaná hodnota, která jsou uloženy. Tyto další atributy, se nazývají stínové atributy. Jsou dva nejběžnější atributy, kde uvidíte toto chování **userPrincipalName** a **proxyAddress**. Změna hodnoty atributu se stane, když existují hodnoty v těchto atributech představující-ověření domény. Ale synchronizační modul v připojení přečte hodnotu v atributu stínové tak z jeho perspektivy, atribut bylo potvrzeno službou Azure AD.

Nejde zobrazit atributů stínové pomocí Azure portálu nebo pomocí prostředí PowerShell. Ale pochopení koncept pomáhá při řešení některých scénářích, kde atribut má jiné hodnoty na místě a v cloudu.

Abyste lépe pochopili chování, podívejte se na tomto příkladu ze společnosti Fabrikam:  
![Domény](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Mají více přípon UPN ve svojí místní službě Active Directory, ale jeden pouze ověřit.

### <a name="userprincipalname"></a>userPrincipalName
Uživatel má následující hodnoty atributu v doméně ověřit:

| Atribut | Hodnota |
| --- | --- |
| userPrincipalName na místě | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Atribut userPrincipalName je hodnota, která se zobrazí po pomocí prostředí PowerShell.

Vzhledem k tomu, že hodnota atributu skutečné místní je uložená ve službě Azure AD, když ověření domény fabrikam.com, aktualizuje Azure AD s hodnotou z shadowUserPrincipalName atribut userPrincipalName. Nemáte synchronizujte jakékoli změny z Azure AD Connect pro tyto hodnoty aktualizovat.

### <a name="proxyaddresses"></a>proxyAddresses
Stejný postup pro pouze včetně ověřených domén dochází k také pro proxyAddresses, ale s některé další logiku. Kontrolu ověřených domén se stane pouze pro poštovní schránky uživatele. Poštovní uživatele nebo kontaktujte představují uživateli v jiné organizaci Exchange a všechny hodnoty v poli proxyAddresses. můžete přidat do těchto objektů.

Uživatel poštovní schránky, buď místní nebo v systému Exchange Online se zobrazí pouze hodnoty ověřených domén. Ho může vypadat například takto:

| Atribut | Hodnota |
| --- | --- |
| místní proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

V takovém případě **smtp:abbie.spencer@fabrikam.com** byla odebrána, vzhledem k tomu, že doména nebyla ověřena. Ale Exchange také přidat **SIP:abbie.spencer@fabrikamonline.com**. Společnost Fabrikam nepoužíval Lync nebo Skype na místě, ale Azure AD a Exchange Online připravte se na to.

Tato logika pro proxyAddresses se označuje jako **ProxyCalc**. Vyvolání ProxyCalc každé změně na uživatele při:

- Uživatel byl přiřazen plánu služby, které zahrnuje Exchange Online i v případě, že uživatel nebyl licenci na Exchange. Například pokud uživatel je přiřazen Office E3 SKU, ale pouze byl přiřazen SharePoint Online. To platí i v případě, že poštovní schránky je stále místně.
- MsExchRecipientTypeDetails atributu obsahuje hodnotu.
- Můžete provést změnu proxyAddresses nebo userPrincipalName.

ProxyCalc může trvat nějakou dobu zpracování změn na uživatele a není synchronní s procesem exportu Azure AD Connect.

> [!NOTE]
> ProxyCalc logic obsahuje některé další chování pro pokročilé scénáře, které nejsou popsané v tomto tématu. Toto téma poskytuje pro pochopit chování a není dokumentu všechny interní logiku.

### <a name="quarantined-attribute-values"></a>Hodnoty atributů v karanténě
Stínové atributy se také používají po duplicitními hodnotami atributů. Další informace najdete v tématu [odolnosti duplicitní atribut](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Synchronizace služby Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).
