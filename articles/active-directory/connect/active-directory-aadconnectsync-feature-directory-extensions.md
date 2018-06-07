---
title: 'Synchronizace Azure AD Connect: rozšíření adresáře | Microsoft Docs'
description: Toto téma popisuje funkce rozšíření adresáře v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: dda35e63c209951547a667c46639dc0f37c87b43
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593628"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizace Azure AD Connect: rozšíření adresáře
Rozšíření adresáře můžete rozšířit schéma v Azure Active Directory (Azure AD) s vlastními atributy z místní služby Active Directory. Tato funkce umožňuje využívání atributy, které můžete dál spravovat místní stavět obchodní aplikace. Tyto atributy mohou být využívány prostřednictvím [rozšíření adresáře Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) nebo [Microsoft Graph](https://graph.microsoft.io/). Dostupné atributy můžete zobrazit pomocí [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) a [program Průzkumník systému Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer), v uvedeném pořadí.

Žádné úlohy Office 365 v současné době využívá tyto atributy.

Můžete nakonfigurovat další atributy, které chcete synchronizovat v cestě vlastní nastavení v Průvodci instalací.

![Průvodce rozšíření schématu](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

Instalace ukazuje následující atributy, které jsou kandidáty platné:

* Typy objektů uživatelů a skupin
* Jednohodnotové atributy: řetězec, logická hodnota, celé číslo, binární
* Více hodnot atributů: String, binární


>[!NOTE]
> Azure AD Connect podporuje synchronizaci s více hodnotami atributy služby Active Directory do Azure AD jako rozšíření adresáře s více hodnotami. Ale žádné funkce ve službě Azure AD aktuálně podporují použití rozšíření adresáře s více hodnotami.

V seznamu atributů je načten z mezipaměti schématu, který se vytvoří během instalace služby Azure AD Connect. Pokud jste rozšířili schéma Active Directory s další atributy, je nutné [aktualizovat schéma](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) před tyto nové atributy jsou viditelné.

Objekt ve službě Azure AD může mít až 100 atributů rozšíření adresáře. Maximální délka je 250 znaků. Pokud hodnota atributu je delší, synchronizační modul se zkrátí.

Během instalace služby Azure AD Connect je zaregistrován aplikace, které jsou k dispozici tyto atributy. Můžete zobrazit tuto aplikaci na portálu Azure.

![Aplikace rozšíření schématu](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Atributy mají předponu rozšíření \_{AppClientId}\_. AppClientId má stejnou hodnotu pro všechny atributy v klientovi služby Azure AD.

Tyto atributy jsou nyní k dispozici prostřednictvím Azure AD Graph API. Dotazujte je pomocí [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Průzkumník Azure AD Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Nebo můžete zadat dotaz na atributy prostřednictvím rozhraní Microsoft Graph API pomocí [program Průzkumník systému Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer#).

>[!NOTE]
> Budete muset požádat o atributy, které má být vrácen. Explicitně vyberte atributy takto: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Další informace najdete v tématu [Microsoft Graph: použijte parametry dotazu](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
