---
title: 'Synchronizace služby Azure AD Connect: Rozšíření adresáře | Dokumenty společnosti Microsoft'
description: Toto téma popisuje funkci rozšíření adresáře ve službě Azure AD Connect.
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
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917909"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronizace služby Azure AD Connect: Rozšíření adresáře
Pomocí rozšíření adresáře můžete rozšířit schéma ve službě Azure Active Directory (Azure AD) o vlastní atributy z místní služby Active Directory. Tato funkce umožňuje vytvářet obchodní aplikace tím, že spotřebovává atributy, které budete i nadále spravovat místně. Tyto atributy lze spotřebovat prostřednictvím [rozšíření](https://docs.microsoft.com/graph/extensibility-overview
). Dostupné atributy můžete zobrazit pomocí [aplikace Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Tuto funkci můžete také použít k vytvoření dynamických skupin ve službě Azure AD.

V současné době žádné úlohy Office 365 spotřebovává tyto atributy.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Přizpůsobení atributů, které chcete synchronizovat s Azure AD

Nakonfigurujete další atributy, které chcete synchronizovat v cestě k vlastnímu nastavení v průvodci instalací.

>[!NOTE]
>Pole Dostupné atributy rozlišuje malá a velká písmena.

![Průvodce rozšířením schématu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalace zobrazuje následující atributy, které jsou platnými kandidáty:

* Typy objektů uživatele a skupiny
* Atributy s jednou hodnotou: Řetězec, Logická hodnota, Celé číslo, Binární
* Atributy s více hodnotami: Řetězec, Binární


>[!NOTE]
> Přestože Azure AD Connect podporuje synchronizaci atributů služby Active Directory s více hodnotami do služby Azure AD jako rozšíření adresáře s více hodnotami, v současné době neexistuje žádný způsob, jak načíst nebo spotřebovat data odeslaná v atributech rozšíření adresáře s více hodnotami.

Seznam atributů se čte z mezipaměti schématu, která se vytvoří během instalace Služby Azure AD Connect. Pokud jste rozšířili schéma služby Active Directory o další atributy, je nutné [aktualizovat schéma](how-to-connect-installation-wizard.md#refresh-directory-schema) dříve, než budou tyto nové atributy viditelné.

Objekt ve službě Azure AD může mít až 100 atributů pro rozšíření adresářů. Maximální délka je 250 znaků. Pokud je hodnota atributu delší, synchronizační modul ji zkrátí.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Změny konfigurace ve službě Azure AD provedené průvodcem

Během instalace Azure AD Connect je registrovaná aplikace, kde jsou k dispozici tyto atributy. Tuto aplikaci najdete na webu Azure Portal. Jeho název je vždy **tenanta schema rozšíření aplikace**.

![Aplikace pro rozšíření schématu](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Ujistěte se, že vyberete **všechny aplikace** pro zobrazení této aplikace.

Atributy jsou předponou s **příponou \_{ApplicationId}\_**. ApplicationId má stejnou hodnotu pro všechny atributy ve vašem tenantovi Azure AD. Tuto hodnotu budete potřebovat pro všechny ostatní scénáře v tomto tématu.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Zobrazení atributů pomocí rozhraní Microsoft Graph API

Tyto atributy jsou nyní k dispozici prostřednictvím rozhraní Microsoft Graph API pomocí [aplikace Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> V rozhraní MICROSOFT Graph API, musíte požádat o atributy, které mají být vráceny. Explicitně vyberte atributy, jako je tento: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`.
>
> Další informace naleznete v [tématu Microsoft Graph: Use query parameters](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Použití atributů v dynamických skupinách

Jedním z užitečnějších scénářů je použití těchto atributů v dynamických bezpečnostních funkcích nebo skupinách Office 365.

1. Vytvořte novou skupinu ve službě Azure AD. Pojmenujte jej a ujistěte se, že **typ členství** je **dynamický uživatel**.

   ![Snímek obrazovky s novou skupinou](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Tuto možnost **vyberte, chcete-li přidat dynamický dotaz**. Pokud se podíváte na vlastnosti, pak se nezobrazí tyto rozšířené atributy. Musíte je nejprve přidat. Klepněte na **tlačítko Získat vlastní vlastnosti rozšíření**, zadejte ID aplikace a klepněte na tlačítko Aktualizovat **vlastnosti**.

   ![Snímek obrazovky, na který byla přidána rozšíření adresáře](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Otevřete rozbalovací soubor vlastností a všimněte si, že přidané atributy jsou nyní viditelné.

   ![Snímek obrazovky s novými atributy, které se zobrazují v ui](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Vyplňte výraz tak, aby vyhovoval vašim požadavkům. V našem příkladu je pravidlo nastaveno na **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Prodej a marketing")**.

4. Po vytvoření skupiny, dejte Azure AD nějaký čas nanaplnění členů a zkontrolujte členy.

   ![Snímek obrazovky se členy v dynamické skupině](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
