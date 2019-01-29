---
title: Jak nakonfigurovat samoobslužné přiřazení aplikací | Dokumentace Microsoftu
description: Povolení samoobslužného přístupu k aplikacím umožňuje uživatelům najít jejich vlastních aplikací
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: barbkess
ms.openlocfilehash: b2d08624a95d799962e331e3b4708dfa792d3ca8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169126"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Jak nakonfigurovat samoobslužné přiřazení aplikací

Předtím, než vaši uživatelé samoobslužných můžete objevovat aplikace z jejich přístupovém panelu, je potřeba povolit **samoobslužného přístupu k aplikacím** pro všechny aplikace, které chcete povolit uživatelům samoobslužné zjišťování a žádat o přístup k.

Tato funkce je skvělý způsob, jak šetřit čas i peníze jako skupina IT a důrazně doporučujeme jako součást nasazení moderních aplikací s Azure Active Directory.

Díky této funkci můžete:

-   Umožnit uživatelům samostatně objevovat aplikace z [přístupového panelu aplikací](https://myapps.microsoft.com/) bez varovat skupina IT.

-   Přidání těchto uživatelů do předem nakonfigurované skupiny, abyste mohli zobrazit, který žádá o přístup, odebrat přístup a správa přiřazené role.

-   Volitelně můžete povolit schvalovatel ke schválení žádosti o aplikace přístup, nemusí být skupina IT.

-   Volitelně můžete nakonfigurujte až 10 jednotlivce, kteří mohou schvalovat přístup k této aplikaci.

-   Volitelně můžete povolit obchodní schvalovatel tito uživatelé nastavit hesla můžete použít k přihlášení do aplikace, vpravo od schvalovatele obchodní [přístupového panelu aplikací](https://myapps.microsoft.com/).

-   Volitelně můžete automaticky přiřadíte přímo přiřadit aplikační role uživatelů samoobslužné služby.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolení samoobslužného přístupu k aplikacím umožňuje uživatelům najít jejich vlastních aplikací

Obchodní skupině schvalovat přístup k těmto aplikacím samoobslužného přístupu k aplikacím je skvělý způsob, jak povolit uživatelům samoobslužné zjišťování aplikací, volitelně povolit. Můžete povolit obchodní skupiny pro správu přiřazené těmto uživatelům pro heslo jednotné přihlašování v aplikacích vpravo od svých přístupových panelech přihlašovací údaje.

Povolení samoobslužného přístupu k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure Portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** z navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete povolit samoobslužné služby přístup ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **samoobslužné** z navigační nabídce vlevo aplikaci.

8.  Povolení samoobslužného přístupu k aplikacím pro tuto aplikaci, zapněte **povolit uživatelům žádat o přístup k této aplikaci?** přepnutím **Ano.**

9.  V dalším kroku vyberte skupiny, kterým uživatelé, kteří žádají o přístup k této aplikaci by měly být přidány, klepněte na volič vedle popisku **do které skupiny by měl přiřazení uživatelé měli přidat?** a vyberte skupinu.
  
    > [!NOTE]
    > Skupiny synchronizované z místního nejsou podporovány pro skupinu, do které by měli přidat uživatele, kteří žádají o přístup k této aplikaci.
  
10. **Volitelné:** Pokud chcete vyžadovat schválení firmy před uživatelé mají povolen přístup, nastavte **vyžadovat schválení před udělením přístupu k této aplikaci?** přepnutím **Ano**.

11. **Volitelné: Pro aplikace používající jednotné přihlašování heslem, pouze na** Pokud chcete povolit tyto firemními schvalovateli k zadání hesla, které se odesílají na tuto aplikaci pro schválené uživatele, nastavte **povolit schvalovatelům nastavovat hesla uživatelů pro tuto aplikace?**  přepnutím **Ano**.

12. **Volitelné:** Pokud chcete zadat firemními schvalovateli, kteří se může schvalovat přístup k této aplikaci, klepněte na volič vedle popisku **kdo může schvalovat přístup k této aplikaci?** vybrat až 10 jednotlivé firemními schvalovateli.

     > [!NOTE]
     > Skupiny se nepodporují.
     >
     >

13. **Volitelné:** **Pro aplikace, které zpřístupňují role**, pokud chcete přiřadit k roli schválených uživatelů samoobslužných služeb, klepněte na volič vedle **ke které roli by měla být přiřazena uživatelům v této aplikaci?** vyberte role, ke kterému Tito uživatelé měla být přiřazena.

14. Klikněte na tlačítko **Uložit** tlačítko v horní části okna na dokončení.

Po dokončení konfigurace samoobslužné služby aplikace, uživatelé mohou přejít na jejich [přístupového panelu aplikací](https://myapps.microsoft.com/) a klikněte na tlačítko **+ přidat** tlačítko a hned uvidíte aplikace, které jste povolili samoobslužné funkce přístup. Firemními schvalovateli oznámení se zobrazí také v jejich [přístupového panelu aplikací](https://myapps.microsoft.com/). Můžete povolit e-mailu oznamující, že uživatel má požádali o přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují jeden schvalovacích pracovních postupů, což znamená, že pokud zadáte několik schvalovatelů, jeden schvalovatel může schvalovatel přístup k aplikaci.

## <a name="next-steps"></a>Další postup
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../users-groups-roles/groups-self-service-management.md)
