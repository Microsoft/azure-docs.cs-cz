---
title: 'Azure AD Connect synchronizace: Rozšíření adresáře | Microsoft Docs'
description: Toto téma popisuje funkci rozšíření adresáře v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fdfce58bdd8e13637e77d01d4b6c0ab21f696a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607649"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synchronizace: Rozšíření adresáře
K rozšíření schématu v Azure Active Directory (Azure AD) s vlastními atributy z místní služby Active Directory můžete použít rozšíření adresáře. Tato funkce umožňuje sestavovat obchodní aplikace pomocí atributů, které budete nadále spravovat místně. Tyto atributy je možné spotřebovat prostřednictvím [rozšíření služby Graph API Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) nebo [Microsoft Graph](https://developer.microsoft.com/graph/)adresáře Azure AD. Dostupné atributy můžete zobrazit pomocí [Průzkumníka služby Azure AD Graph](https://graphexplorer.azurewebsites.net/) a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)v uvedeném pořadí.

V současné době žádné úlohy Office 365 nevyužívají tyto atributy.

Další atributy, které chcete synchronizovat, můžete nakonfigurovat v cestě k vlastnímu nastavení v Průvodci instalací nástroje.

>[!NOTE]
>V poli dostupné atributy se rozlišují malá a velká písmena.

![Průvodce rozšířením schématu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalace zobrazuje následující atributy, které jsou platné kandidáty:

* Typy objektů uživatelů a skupin
* Atributy s jednou hodnotou: Řetězec, logická hodnota, celé číslo, binární
* Vícehodnotových atributů: Řetězec, binární


>[!NOTE]
> I když Azure AD Connect podporuje synchronizaci vícehodnotových atributů služby Active Directory se službou Azure AD jako víceřádková rozšíření adresáře, neexistuje momentálně žádný způsob, jak načíst a využívat data nahraná v atributech rozšíření adresáře s více hodnotami.

Seznam atributů je načten z mezipaměti schématu, která je vytvořena během instalace Azure AD Connect. Pokud jste rozšíření schématu služby Active Directory rozšířili o další atributy, je nutné [schéma aktualizovat](how-to-connect-installation-wizard.md#refresh-directory-schema) předtím, než budou tyto nové atributy viditelné.

Objekt ve službě Azure AD může mít až 100 atributů pro rozšíření adresáře. Maximální délka je 250 znaků. Pokud je hodnota atributu delší, synchronizační modul ho zkrátí.

Během instalace Azure AD Connect je aplikace zaregistrovaná tam, kde jsou tyto atributy k dispozici. Tuto aplikaci můžete zobrazit v Azure Portal.

![Aplikace rozšíření schématu](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Atributy jsou předponou s rozšířením \_{AppClientId}.\_ AppClientId má stejnou hodnotu pro všechny atributy v tenantovi Azure AD.

Tyto atributy jsou nyní k dispozici prostřednictvím Graph API služby Azure AD. Můžete je dotazovat pomocí [Průzkumníka Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Průzkumník Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Můžete také zadat dotaz na atributy prostřednictvím rozhraní Microsoft Graph API pomocí [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Musíte požádat o vrácení atributů. Explicitně vyberte atributy takto: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $Select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Další informace najdete v tématu [Microsoft Graph: Použijte parametry](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)dotazu.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
