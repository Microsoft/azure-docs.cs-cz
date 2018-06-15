---
title: Problém pomocí samoobslužné služby aplikace access | Microsoft Docs
description: Řešení potíží s problémy související s přístup k aplikaci Samoobslužné služby
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 6d4044414cfae9a79487d02709aab24998fdef0b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29382351"
---
# <a name="problem-using-self-service-application-access"></a>Problém pomocí samoobslužné služby aplikace access

Přístup k aplikaci Samoobslužné služby je skvělým způsobem, jak povolit uživatelům samoobslužné zjišťování aplikací, můžete povolit obchodní skupiny můžete schválit přístup pro tyto aplikace. Můžete povolit obchodní skupině pro správu přiřazené pro tyto uživatele pro heslo jednotné přihlašování v aplikacích vpravo z jejich přístup panelů přihlašovací údaje.

Než uživatelům můžete zjistit samoobslužné aplikací z jejich přístupového panelu, musíte povolit **přístup k aplikaci Samoobslužné služby** pro všechny aplikace, které chcete povolit uživatelům samoobslužné zjišťování a požádat o přístup k.

## <a name="general-issues-to-check-first"></a>Běžné problémy a proveďte nejprve kontrolu

-   Zajistěte, aby byl správně nakonfigurován přístup k aplikaci Samoobslužné služby. V kapitole "Jak konfigurovat přístup k aplikaci Samoobslužné služby".

-   Zkontrolujte, zda že uživatel nebo skupina povolen požádat o přístup k aplikaci Samoobslužné služby.

-   Zkontrolujte, zda že uživatel je návštěvu správné místa pro přístup k aplikaci Samoobslužné služby. Uživatelé mohou přejít na jejich [Panel přístupu aplikace](https://myapps.microsoft.com/) a klikněte na tlačítko **+ přidat** tlačítko k vyhledání aplikace, na které jste povolili samoobslužné služby přístup.

-   Pokud přístup k aplikaci Samoobslužné služby byl právě nakonfigurovali, zkuste pro přihlášení a odhlášení znovu do uživatele přístupového panelu po několika minutách chcete zobrazit, pokud se nezobrazují změny samoobslužné služby přístup.

## <a name="how-to-configure-self-service-application-access"></a>Postup konfigurace přístupu k aplikaci Samoobslužné služby

Pokud chcete povolit samoobslužné služby aplikaci přístup k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [ **portálu Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které chcete povolit samoobslužné přístup ze seznamu.

7.  Po načtení aplikace, klikněte na **samoobslužné služby** navigační nabídce vlevo aplikace.

8.  Pokud chcete povolit přístup k aplikaci Samoobslužné služby pro tuto aplikaci, zapněte **povolit uživatelům žádat o přístup k této aplikaci?** přepnutím **Ano.**

9.  V dalším kroku vyberte skupiny, které uživatelům, kteří požadují by se měl přístup k této aplikaci přidat, klikněte na tlačítko modulu pro výběr vedle popisek **skupinu, pro kterou má přiřazené byli přidáni uživatelé?** a vyberte skupinu.

10. **Volitelné:** nastaví, pokud chcete vyžadovat schválení obchodní před uživatelé mají povolen přístup **vyžadovat schválení před udělením přístupu k této aplikaci?** přepnutím **Ano**.

11. **Volitelné: pro aplikace pomocí hesla jednotné přihlašování na pouze** Pokud chcete povolit tyto firmy schvalovatelů k zadání hesla, které se odesílají na tuto žádost o schválení uživatelé, nastavte **povolit schvalovatelů nastavit uživatele hesla pro tuto aplikaci?**  přepnutím **Ano**.

12. **Volitelné:** pro zadání schvalovatelů firmy, kteří mají povoleno schválit přístup k této aplikaci, klikněte na výběr vedle popisek **kdo může schválit přístup k této aplikaci?** vybrat jednotlivé až 10 obchodní schvalovatelů.

 >[!NOTE]
 > Skupiny nejsou podporovány.
 >
 >

13. **Volitelné:** **pro aplikace, které zveřejňují role**, pokud chcete přiřadit roli schválené uživatelé samoobslužné služby, klikněte na modulu pro výběr vedle **do role, které by měl být přiřazena uživatelům v této aplikaci?** Vyberte roli, ke kterému by se měla přiřadit těmto uživatelům.

14. Klikněte **Uložit** tlačítka v horní části okna dokončit.

Po dokončení konfigurace samoobslužné služby aplikace, uživatelé mohou přejít na jejich [Panel přístupu aplikace](https://myapps.microsoft.com/) a klikněte na tlačítko **+ přidat** tlačítko k vyhledání aplikace, na které jste povolili samoobslužné služby přístup. Obchodní schvalovatelů také zobrazit oznámení v jejich [Panel přístupu aplikace](https://myapps.microsoft.com/). Můžete povolit e-mail s upozorněním, když uživatel požaduje přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují jeden schválení pracovní postupy, což znamená, že pokud zadáte několik schvalovatelů, jeden schvalovatel může schválit přístup k aplikaci.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší 

Otevřete lístek podpory s následujícími informacemi, pokud je k dispozici:

-   ID chyby korelace

-   Hlavní název uživatele (e-mailovou adresu uživatele)

-   TenantID

-   Typ prohlížeče

-   Dojde k časové pásmo a čas nebo období při chybě

-   Fiddler trasování

## <a name="next-steps"></a>Další postup
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](active-directory-accessmanagement-self-service-group-management.md)
