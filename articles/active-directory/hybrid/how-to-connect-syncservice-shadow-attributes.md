---
title: Azure AD Connect atributů stínové služby synchronizace | Microsoft Docs
description: Popisuje, jak atributy stínů fungují ve službě Azure AD Connect Sync Service.
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
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088966"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect atributy stínové služby synchronizace
Většina atributů je ve službě Azure AD reprezentována stejným způsobem jako v místní službě Active Directory. Některé atributy ale mají nějaké speciální zpracování a hodnota atributu ve službě Azure AD se může lišit od toho, co se Azure AD Connect synchronizuje.

## <a name="introducing-shadow-attributes"></a>Představení atributů stínů
Některé atributy mají ve službě Azure AD dvě reprezentace. Místní hodnota i Počítaná hodnota jsou uloženy. Tyto dodatečné atributy se nazývají stínové atributy. Dva nejběžnější atributy, kde vidíte toto chování, jsou **userPrincipalName** a **ProxyAddress**. Změna hodnot atributů se projeví, pokud jsou v těchto atributech hodnoty, které představují neověřené domény. Synchronizační modul v Connect ale přečte hodnotu v atributu Shadow z pohledu své perspektivy a atribut byl potvrzen službou Azure AD.

Pomocí Azure Portal nebo prostředí PowerShell nemůžete zobrazit atributy stínů. Ale porozumění konceptu vám pomůže vyřešit některé scénáře, kde má atribut odlišné hodnoty v místním prostředí i v cloudu.

Chcete-li lépe pochopit chování, podívejte se na tento příklad ze společnosti Fabrikam:  
![Snímek obrazovky s příponou hlavního názvu uživatele (UPN) služby Active Directory zobrazuje několik příkladů s odpovídající hodnotou domény služby Azure AD, která není přidaná, není ověřena a ověřena.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Mají několik přípon UPN v místní službě Active Directory, ale ověřili jsme je jenom jednou.

### <a name="userprincipalname"></a>userPrincipalName (Hlavní název uživatele)
Uživatel má následující hodnoty atributu v neověřené doméně:

| Atribut | Hodnota |
| --- | --- |
| místní userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Atribut userPrincipalName je hodnota, která se zobrazí při použití prostředí PowerShell.

Vzhledem k tomu, že hodnota reálného místního atributu je uložená v Azure AD, při ověřování domény fabrikam.com aktualizuje Azure AD atribut userPrincipalName hodnotou z shadowUserPrincipalName. Pro aktualizace těchto hodnot není nutné synchronizovat žádné změny z Azure AD Connect.

### <a name="proxyaddresses"></a>proxyAddresses
Stejný postup, který zahrnuje jenom ověřené domény, se taky vyskytuje pro proxyAddresses, ale s nějakou další logikou. Kontroler ověřených domén se stane pouze pro uživatele poštovních schránek. Uživatel nebo kontakt s povoleným e-mailem představuje uživatele v jiné organizaci Exchange a v proxyAddresses můžete do těchto objektů přidat libovolné hodnoty.

Pro uživatele poštovní schránky, ať už místně nebo v Exchangi Online, se zobrazí jenom hodnoty pro ověřené domény. Může to vypadat takto:

| Atribut | Hodnota |
| --- | --- |
| místní proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

V tomto případě bylo odebráno **SMTP: Abbie. spencer \@ fabrikam.com** , protože tato doména nebyla ověřena. Ale Exchange Přidal také protokol **SIP: Abbie. spencer \@ fabrikamonline.com**. Společnost Fabrikam nepoužívala službu Lync/Skype v místním prostředí, ale připravuje ji pro Azure AD a Exchange Online.

Tato logika pro proxyAddresses je označována jako **ProxyCalc**. ProxyCalc se vyvolá při každé změně uživatele v těchto případech:

- Uživateli byl přiřazen plán služeb, který zahrnuje Exchange online i v případě, že uživatel nemá licenci pro Exchange. Například pokud je uživateli přiřazena SKU Office E3, ale byl mu přiřazen pouze SharePoint Online. To platí i v případě, že je vaše poštovní schránka pořád místní.
- Atribut msExchRecipientTypeDetails má hodnotu.
- Provedete změnu na proxyAddresses nebo userPrincipalName.

ProxyCalc může nějakou dobu trvat, než se změny uživatele zpracuje a není synchronní při Azure AD Connect procesu exportu.

> [!NOTE]
> Logika ProxyCalc má několik dalších chování pro pokročilé scénáře, které nejsou popsány v tomto tématu. Toto téma je k dispozici, abyste porozuměli chování a nezdokumentují všechny interní logiky.

### <a name="quarantined-attribute-values"></a>Hodnoty atributu v karanténě
Pokud existují duplicitní hodnoty atributů, používají se také atributy stínové kopie. Další informace najdete v tématu [odolnost duplicitních atributů](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Viz také
* [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md)
* [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
