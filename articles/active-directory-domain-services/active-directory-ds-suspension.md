---
title: 'Azure Active Directory Domain Services: Pozastaveno domény | Dokumentace Microsoftu'
description: Spravovaná doména pozastavení a zrušení
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: ergreenl
ms.openlocfilehash: 37524fbdf3cd521414a507f6fb67421708343ccd
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934245"
---
# <a name="suspended-domains"></a>Pozastavené domén
Pokud Azure AD Domain Services nemůže pro služby spravované domény pro dlouhou dobu, přejde do pozastaveného stavu spravované domény. Tento článek vysvětluje, proč jsou pozastavené spravovaných domén, délka pozastavení a řešení pozastavené domény.


## <a name="overview-of-suspended-domains"></a>Přehled pozastavené domén

![Časová osa pozastavené domény](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Na předchozím obrázku uvádí, jak je pozastavena doméně, jak dlouho se pozastaví a nakonec odstranění spravované domény. Následující části jsou podrobně popsané důvody, proč může pozastavit domény a jak zrušit pozastavení účtu uživatele spravované domény.


## <a name="why-are-managed-domains-suspended"></a>Proč jsou pozastavené spravované domény

Spravované domény jsou pozastavené, když se nachází ve stavu, kde je možné spravovat domény Azure AD Domain Services. To může způsobovat chybné konfiguraci, která zablokuje přístup k prostředkům vyžaduje Azure AD Domain Services, nebo předplatné Azure aktivní. Po 15 dnech toho, že není možné vyřídit spravované doméně Azure AD Domain Services pozastaví domény.

Přesné důvody, proč může pozastavit vaší domény jsou uvedeny níže:
* Máte nejméně jeden z následujících výstrah, které jsou k dispozici ve vaší doméně 15 po sobě jdoucích dní:
   * [AADDS104: Chyba sítě](active-directory-ds-troubleshoot-nsg.md).
* Vaše předplatné Azure je vypršela platnost, nebo neaktivní


## <a name="what-happens-when-a-domain-is-suspended"></a>Co se stane, když je pozastavený, domény?

Při pozastavení domény Azure AD Domain Services zastaví virtuální počítače, které služby spravované domény. To znamená, že zálohy jsou prováděny už nebudou moct uživatelé přihlásit se k vaší doméně a už provést synchronizaci s Azure AD.

Doména pouze zůstane ve stavu pozastavení maximálně 15 dnů. Aby bylo možné zajistit včasné obnovení, doporučujeme že nejdříve vyřešit pozastavení.

## <a name="how-do-i-know-if-my-domain-is-suspended"></a>Jak poznám, že pokud je pozastavený, Moje doména?
Spravovaná doména se zobrazí [výstraha](active-directory-ds-troubleshoot-alerts.md) na stránce stavu Azure AD Domain Services na portálu Azure portal, který deklaruje domény pozastaveno. Kromě toho stavu domény bude mít označení "Pozastaveno".


## <a name="unsuspending-and-restoring-domains"></a>Domény unsuspending a obnovení

Chcete-li zrušit pozastavení účtu uživatele domény, vyžaduje následující kroky:

1. Přejděte [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure portal
2. Klikněte na doménu, kterou chcete zrušit pozastavení účtu uživatele
3. Na levém navigačním panelu klikněte na tlačítko **stavu**
4. Klikněte na pozastavení výstrahu (ID upozornění bude AADDS503 nebo AADDS504, v závislosti na příčině pozastavení).
5. Klikněte na uvedený odkaz řešení ve výstraze a postupujte podle kroků pro vyřešení vašeho pozastavení.

Vaše doména se dají obnovit jen od posledního zálohování. Datum poslední zálohy je zobrazeny na stránce stav vaší spravované domény. Všechny změny od posledního zálohování nebude obnoven po unsuspension. Kromě toho Azure AD Domain Services ukládat lze pouze zálohy po dobu až 30 dnů. Pokud se poslední záloha je starší než 30 dní, zálohování se musí odstranit a Azure AD Domain Services, nebudou moci obnovit ze zálohy.

## <a name="deleting-domains"></a>Odstranění domény

Pokud domény je pozastaven po dobu více než 15 dnů, Azure AD Domain Services odstraní spravovanou doménu z důvodu nečinnosti a neschopnost služby domény. Už nebudou účtovat služby Azure AD Domain Services. Pokud chcete obnovit spravované domény, je potřeba ho znovu vytvořit.


## <a name="next-steps"></a>Další postup
- [Vyřešit upozornění ve vaší spravované doméně](active-directory-ds-troubleshoot-alerts.md)
- [Přečtěte si víc o službě Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontaktováním produktového týmu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontaktujte nás

Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
