---
title: 'Azure Active Directory Domain Services: Nastavení upozornění | Dokumentace Microsoftu'
description: Nastavení oznámení služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: ebac78ff3912d2aafba97b020b37df16020bd7fb
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853659"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Nastavení oznámení ve službě Azure AD Domain Services

Oznámení služby Azure AD Domain Services můžete aktualizovat poté, co upozornění stavu se detekuje ve vaší spravované doméně.  

Tato funkce dostupná pouze pro spravované domény, které nejsou v klasické virtuální sítě.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Návod k ověření nastavení e-mailové oznámení služby Azure AD Domain Services

1. Přejděte [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure portal
2. Zvolte vaši spravovanou doménu z tabulky
3. V levém navigačním panelu zvolte **nastavení oznámení**

Na stránce zobrazí všechny e-mailové příjemce pro e-mailová oznámení pro službu Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>Jak vypadá e-mailové oznámení?

Na následujícím obrázku je příklad e-mailové oznámení:

![Příklad e-mailové oznámení](./media/active-directory-domain-services-alerts/email-alert.png)

E-mailu určuje spravované domény, který je k dispozici na upozornění, aby čas detekce a odkaz na stránku stavu Azure AD Domain Services na webu Azure Portal.

> [!WARNING]
> Vždy ujistěte se, že e-mailu pochází z ověřených odesílatele Microsoft před kliknutím na odkazy v e-mailech. E-mailů vždy pocházejí z e-mailu azure-noreply@microsoft.com
>


## <a name="why-would-i-receive-email-notifications"></a>Proč by dostávat e-mailová oznámení?

Azure AD Domain Services odešle e-mailová oznámení pro důležité aktualizace informace o vaší doméně.  Tato oznámení jsou určeny pouze pro naléhavé věcech, které budou mít vliv na vaši službu a mělo by se řešit okamžitě. Každou e-mailové oznámení se aktivuje upozornění na vaši spravovanou doménu. Tyto výstrahy se také zobrazí na portálu Azure portal a můžete zobrazit na [stránku Stav Azure AD Domain Services](active-directory-ds-check-health.md).

Azure AD Domain Services neodesílá e-mailů do tohoto seznamu pro oznámení o inzerovaném programu, aktualizace nebo prodejní účely.

## <a name="when-will-i-receive-email-notifications"></a>Když obdrží e-mailová oznámení?

Oznámení se odešle okamžitě při [nová výstraha](active-directory-ds-troubleshoot-alerts.md) se nachází ve vaší spravované doméně. Pokud není vyřešit výstrahu, e-mailové oznámení se odešlou jako připomenutí každé čtyři dny.

## <a name="who-should-receive-the-email-notifications"></a>Příjemce e-mailová oznámení.


 Doporučujeme seznam příjemců e-mailu pro Azure AD Domain Services se skládá z lidí, kteří můžou spravovat a měnit ke spravované doméně. Tento seznam e-mailů by měl představit jako vaše "první respondéry" na všechny nalezené problémy. Pokud máte více než pět další e-maily, které chcete přidat, doporučujeme vytvořit distribuční seznam místo toho přidejte do seznamu oznámení.

Budete moci přidat až pět dalších e-mailů pro oznámení týkající se Azure AD Domain Services. Kromě toho můžete také zvolit, aby všichni globální správci adresáře a každý člen skupiny 'AAD DC Administrators"přijímat e-mailová oznámení služby Azure AD Domain Services. Azure AD Domain Services pouze pošle oznámení na až 100 e-mailové adresy, včetně seznamu globální správci a správci AAD DC.


## <a name="how-to-add-an-additional-email-recipient"></a>Postup přidání příjemcům další e-mailů

> [!WARNING]
> Při změně nastavení oznámení, změníte nastavení oznámení pro celou spravovanou doménu, ne jenom sami.

1. Přejděte [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure portal.
2. Klikněte na vaši spravovanou doménu.
3. Na levém navigačním panelu klikněte na tlačítko **nastavení oznámení**.
4. Chcete-li přidat e-mailu, zadejte e-mailovou adresu v tabulce Další příjemce.
5. Klikněte na tlačítko "Save" na skladě horní navigaci.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Zobrazila se mi oznámení o e-mailu pro výstrahy, ale při přihlášení k webu Azure portal se žádná výstraha. Co se stalo?

Pokud výstraha vyřeší, výstraha zmizí z portálu Azure portal. Nejpravděpodobnějším důvodem je, že někdo, kdo obdrží e-mailová oznámení vyřešit výstrahu ve vaší spravované doméně, nebo byla automaticky vyřešena službou Azure AD Domain Services.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Proč nemůžu upravovat nastavení oznámení?

Pokud nejde získat přístup ke stránce nastavení oznámení na webu Azure Portal, nemáte oprávnění k úpravám Azure AD Domain Services. Obraťte se na globálního správce buď získat oprávnění k úpravám prostředků Azure AD Domain Services a odebrat ze seznamu příjemců.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Nemám přijímat e-mailová oznámení, i když mám k dispozici Moje e-mailovou adresu. Proč?

Zkontrolujte složku s nevyžádanou poštou nebo nevyžádané e-mailu pro oznámení a ujistěte se, že na seznam povolených odesílatele (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Další postup
- [Vyřešit upozornění ve vaší spravované doméně](active-directory-ds-troubleshoot-alerts.md)
- [Přečtěte si víc o službě Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontaktováním produktového týmu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
