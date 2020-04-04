---
title: E-mailová oznámení pro služby Domény Azure AD | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat e-mailová oznámení, která vás upozorní na problémy ve spravované doméně služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8c6d59889e572893877f2178cade57e07aa91413
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654788"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Konfigurace e-mailových oznámení pro problémy ve službě Azure Active Directory Domain Services

Stav spravované domény Služby Azure Active Directory Domain Services (Azure AD DS) monitoruje platforma Azure. Stránka stavu na webu Azure Portal zobrazuje všechny výstrahy pro spravovanou doménu. Chcete-li zajistit, aby problémy byly zodpovězeny včas, e-mailová oznámení můžete nakonfigurovat tak, aby sestavy na výstrahy stavu, jakmile jsou zjištěny ve spravované doméně Azure AD DS.

Tento článek ukazuje, jak nakonfigurovat příjemce e-mailových oznámení pro spravovanou doménu Azure AD DS.

## <a name="email-notification-overview"></a>Přehled oznámení e-mailem

Pokud vás chcete upozornit na problémy se spravovanou doménou Azure AD DS, můžete nakonfigurovat e-mailová oznámení. Tato e-mailová oznámení určují spravovanou doménu Azure AD DS, ve které se výstraha nachází, a také poskytují čas detekce a odkaz na stránku stavu na webu Azure Portal. Problémy pak můžete vyřešit pomocí poskytnutých pokynů pro řešení potíží.

Následující příklad e-mailové oznámení označuje kritické upozornění nebo výstraha byla generována na spravované doméně Azure AD DS:

![Ukázkové e-mailové oznámení](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Před kliknutím na odkazy ve zprávě se vždy ujistěte, že e-mail pochází od ověřeného odesílatele společnosti Microsoft. E-mailová oznámení vždy `azure-noreply@microsoft.com` pocházejí z adresy.

### <a name="why-would-i-receive-email-notifications"></a>Proč bych měl dostávat e-mailová oznámení?

Azure AD DS odesílá e-mailová oznámení pro důležité aktualizace o spravované doméně. Tato oznámení jsou pouze pro naléhavé problémy, které mají vliv na službu a měly by být řešeny okamžitě. Každé e-mailové oznámení se aktivuje výstrahou ve spravované doméně Azure AD DS. Výstrahy se také zobrazí na webu Azure Portal a lze zobrazit na [stránce stavu Služby Azure AD DS][check-health].

Azure AD DS neodesílá e-maily pro reklamní, aktualizace nebo prodejní účely.

### <a name="when-will-i-receive-email-notifications"></a>Kdy budu dostávat e-mailová oznámení?

Oznámení se odešle okamžitě, když se na spravovanou doméně Azure AD DS najdou [nová výstraha.][troubleshoot-alerts] Pokud se výstraha nevyřeší, budou každé čtyři dny jako připomínka odeslána další e-mailová oznámení.

### <a name="who-should-receive-the-email-notifications"></a>Kdo by měl dostávat e-mailová oznámení?

Seznam příjemců e-mailu pro Azure AD DS by se měl skládat z lidí, kteří jsou schopni spravovat a provádět změny ve spravované doméně. Tento e-mailový seznam by měl být považován za vaše "první respondenty" na všechna upozornění a problémy.

Pro e-mailová oznámení můžete přidat až pět dalších příjemců e-mailů. Pokud chcete více než pět příjemců pro e-mailová oznámení, vytvořte distribuční seznam a místo toho jej přidejte do seznamu oznámení.

Můžete také zvolit, aby všichni *globální správci adresáře* Azure AD a všichni členové *skupiny Správci řadiče domény AAD* obdrželi e-mailová oznámení. Azure AD DS odesílá oznámení jenom až na 100 e-mailových adres, včetně seznamu globálních správců a správců řadiče domény AAD.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li zkontrolovat stávající příjemce e-mailových oznámení nebo přidat další příjemce, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **služby Domény Azure AD**.
1. Vyberte spravovanou doménu Azure AD DS, například *aaddscontoso.com*.
1. Na levé straně okna prostředku Azure AD DS vyberte **Nastavení oznámení**. Zobrazí se stávající příjemci e-mailových oznámení.
1. Chcete-li přidat příjemce e-mailu, zadejte e-mailovou adresu do tabulky dalších příjemců.
1. Až bude hotovo, vyberte **Uložit** na navigaci nahoře.

> [!WARNING]
> Když změníte nastavení oznámení, nastavení oznámení pro celou spravovanou doménu Azure AD DS se aktualizují, nejen sami.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Obdržel jsem e-mailové oznámení o upozornění, ale když jsem se přihlásil na portál Azure, nebyla žádná výstraha. Co se stalo?

Pokud je výstraha vyřešena, výstraha se vymaže z portálu Azure. Nejpravděpodobnějším důvodem je, že někdo jiný, kdo přijímá e-mailová oznámení, vyřešil výstrahu ve spravované doméně Azure AD DS nebo byl automaticky vyřešen platformou Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Proč nemohu upravit nastavení oznámení?

Pokud se vám na webu Azure Portal nedaří získat přístup k nastavení oznámení, nemáte oprávnění k úpravám spravované domény Azure AD DS. Musíte kontaktovat globálního správce buď získat oprávnění k úpravám prostředku Azure AD DS nebo být odebrány ze seznamu příjemců.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Nezdá se, že dostávám e-mailová oznámení, i když jsem za předpokladu, že moje e-mailová adresa. Proč?

Zkontrolujte, zda je v e-mailu odesláno upozornění ve `azure-noreply@microsoft.com`složce nevyžádané pošty nebo nevyžádané pošty, a ujistěte se, že jste povolili odesílateli aplikace .

## <a name="next-steps"></a>Další kroky

Další informace o řešení některých problémů, které mohou být hlášeny, naleznete [v tématu Řešení výstrah ve spravované doméně Azure AD DS][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
