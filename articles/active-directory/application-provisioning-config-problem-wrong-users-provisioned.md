---
title: Nesprávný sadu uživatelů jsou se zřídí k aplikaci Galerie Azure AD | Microsoft Docs
description: Zjistěte, jak zjistit, proč jsou se zřídí jinou sadu uživatelů do aplikace než ty, které jste očekávali
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: f984db4c06a98f7d131860f8eb5d76a67a35a483
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332547"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nesprávný sadu uživatelů jsou se zřídí k aplikaci Galerie Azure AD

Uživatele, kteří jsou zřízené v aplikaci je primárně vycházejí z kteří uživatelé a skupiny byly **přiřazené** do aplikace.

Použijte v následujících zdrojích informací se dozvíte, jak zkontrolovat, kteří uživatelé a skupiny mají přiřazený k aplikaci v Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Přiřazení uživatele přímo jako správce

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** z levé navigační nabídky.

8.  Otevřete **přidat přiřazení** podokně klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele vás zajímá přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, zadejte v jiném **celý název** nebo **e-mailová adresa** do **vyhledávání podle názvu nebo e-mailová adresa** pole pro vyhledávání a klikněte na zaškrtávací políčko, chcete-li přidat tento uživatel **vybrané** seznamu.

13. Po dokončení výběru uživatelů klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** selektor v **přidat přiřazení** podokně vyberte roli přiřadit uživatele, který jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřadit aplikace pro vybraného uživatele.

Pokud je nakonfigurován zřizování a pro aplikace je již spuštěn, noví uživatelé musí být zřízená aplikace v přibližně 10 minut. Zkontrolujte **protokoly auditu** podrobnosti.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Přiřazení skupiny přímo na aplikace jako správce

Jeden nebo více skupin přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** z levé navigační nabídky.

8.  Otevřete **přidat přiřazení** podokně klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **název úplné skupiny** vás zajímá přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do skupiny **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název úplné skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole a Klikněte na zaškrtávací políčko k přidání do této skupiny **vybrané** seznamu.

13. Po dokončení výběru skupiny klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte **vybrat roli** selektor v **přidat přiřazení** podokně vyberte role pro přiřazení do skupin, které jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřazení aplikace k vybraným skupinám.

Pokud je nakonfigurován zřizování a pro aplikace je již spuštěn, noví uživatelé, obsažené v rámci skupiny by měl být zřízená aplikace v přibližně 10 minut. Zkontrolujte **protokoly auditu** podrobnosti.

>[!IMPORTANT]
>Zřizování název skupiny a údaje skupiny, kromě členy, pokud pro některé aplikace podporován. Můžete povolit nebo zakázat tuto funkci povolením nebo zakázáním **mapování** pro objekty skupiny ukazuje **zřizování** kartě. 
>
>

Pokud je povoleno zřizování skupiny, nezapomeňte si projít mapování atributů k zajištění, že na odpovídající pole je používána pro "Odpovídající ID". To může být zobrazovaný název nebo e-mailu alias, jako skupiny a její členy nelze zřídit Pokud odpovídající vlastnost není prázdný, nebo není vyplněný pro skupiny ve službě Azure AD.

## <a name="next-steps"></a>Další postup
[Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](active-directory-saas-app-provisioning.md)
