---
title: 'Synchronizace Azure AD Connect: Rozšíření adresáře | Dokumentace Microsoftu'
description: Toto téma popisuje funkce rozšíření adresáře ve službě Azure AD Connect.
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
ms.openlocfilehash: 2b8892873541b4f792caa3538bfc51fff6c2d15b
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495613"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizace Azure AD Connect: Rozšíření adresáře
Rozšíření adresáře můžete použít k rozšíření schématu do služby Azure Active Directory (Azure AD) pomocí vlastních atributů z místní služby Active Directory. Tato funkce umožňuje vytváření obchodních aplikací prostřednictvím atributy, které budete nadále spravovat místní. Tyto atributy mohou být spotřebovány prostřednictvím [rozšíření adresáře Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) nebo [Microsoft Graphu](https://developer.microsoft.com/graph/). Dostupné atributy lze zobrazit pomocí [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) a [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)v uvedeném pořadí.

V současné době žádné úlohy Office 365 využívá tyto atributy.

Můžete nakonfigurovat další atributy, které se mají synchronizovat v cestě vlastní nastavení v Průvodci instalací.

>[!NOTE]
>Dostupné atributy pole rozlišuje velká a malá písmena.

![Průvodce rozšířením schématu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalace zobrazuje následující atributy, které jsou platné kandidáty:

* Typy uživatelů a skupin objektů
* Jednohodnotové atributy: Řetězec, logická hodnota, celé číslo, binární soubor
* Více jednohodnotových atributů: Řetězec, binární soubor


>[!NOTE]
> Přestože podporuje Azure AD Connect synchronizaci více Vážíme si toho služby Active Directory atributy do služby Azure AD jako rozšíření více Vážíme si toho adresáře, neexistuje momentálně žádný způsob, jak načíst a využívat data nahraná do atributů rozšíření adresáře více Vážíme si toho.

Seznam atributů, které načítají z mezipaměti schémat, která je vytvořena během instalace služby Azure AD Connect. Pokud jste rozšířili schéma služby Active Directory s další atributy, je nutné [aktualizovat schéma](how-to-connect-installation-wizard.md#refresh-directory-schema) předtím, než tyto nové atributy jsou viditelné.

Objekt ve službě Azure AD může mít až 100 atributů rozšíření adresáře. Maximální délka je 250 znaků. Pokud hodnota atributu je delší, synchronizační modul oříznut.

Během instalace služby Azure AD Connect je registrována aplikace, kde tyto atributy jsou k dispozici. Zobrazí se tato aplikace na webu Azure Portal.

![Schéma rozšíření aplikace](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Atributy mají předponu rozšíření \_{AppClientId}\_. AppClientId má stejnou hodnotu pro všechny atributy ve vašem tenantovi Azure AD.

Tyto atributy jsou teď k dispozici prostřednictvím Azure AD Graph API. Dotazování těchto pomocí [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Azure AD Graph Exploreru](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Nebo můžete zadat dotaz na atributy prostřednictvím rozhraní Microsoft Graph API s využitím [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Budete muset požádat o atributy, které mají být vráceny. Explicitně vybrat atributy takto: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Další informace najdete v tématu [Microsoft Graph: Použití parametrů dotazu](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
