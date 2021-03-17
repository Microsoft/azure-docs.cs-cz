---
title: E-mailová oznámení pro Azure AD Domain Services | Microsoft Docs
description: Naučte se konfigurovat e-mailová oznámení, která vás upozorní na problémy ve Azure Active Directory Domain Services spravované doméně.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619160"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Konfigurace e-mailových oznámení pro problémy v Azure Active Directory Domain Services

Platforma Azure monitoruje stav spravované domény Azure Active Directory Domain Services (Azure služba AD DS). Stránka stav v Azure Portal zobrazuje všechny výstrahy pro spravovanou doménu. Aby se zajistilo, že budou problémy reagovat včas, je možné nakonfigurovat e-mailová oznámení, která budou hlásit výstrahy týkající se stavu, jakmile se zjistí ve spravované doméně Azure služba AD DS.

V tomto článku se dozvíte, jak nakonfigurovat příjemce e-mailových oznámení pro spravovanou doménu.

## <a name="email-notification-overview"></a>Přehled e-mailových oznámení

Pokud chcete upozornit na problémy se spravovanou doménou, můžete nakonfigurovat e-mailová oznámení. Tato e-mailová oznámení určují spravovanou doménu, na které je přítomná výstraha, a taky čas detekce a odkaz na stránku stavu v Azure Portal. Pak můžete tyto problémy vyřešit podle pokynů pro řešení problémů.

Následující příklad e-mailového oznámení indikuje, že ve spravované doméně bylo vygenerováno kritické upozornění, nebo Výstraha:

![Příklad e-mailového oznámení](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Před kliknutím na odkazy ve zprávě se ujistěte, že e-mail pochází od ověřeného odesilatele Microsoftu. E-mailová oznámení se vždycky podávají z `azure-noreply@microsoft.com` adresy.

### <a name="why-would-i-receive-email-notifications"></a>Proč dostávat e-mailová oznámení?

Azure služba AD DS odesílá e-mailová oznámení o důležitých aktualizacích spravované domény. Tato oznámení jsou určena pouze pro naléhavé problémy, které mají dopad na službu a měly by se řešit okamžitě. Každé e-mailové oznámení je aktivované výstrahou ve spravované doméně. Výstrahy se také zobrazí v Azure Portal a je možné je zobrazit na [stránce Azure služba AD DS Health][check-health].

Azure služba AD DS neodesílá e-maily pro účely inzerce, aktualizace nebo prodeje.

### <a name="when-will-i-receive-email-notifications"></a>Kdy získám e-mailová oznámení?

Když se ve spravované doméně najde [nová výstraha][troubleshoot-alerts] , pošle se oznámení okamžitě. Pokud se výstraha nevyřeší, budou se další e-mailová oznámení odesílat jako připomenutí každé čtyři dny.

### <a name="who-should-receive-the-email-notifications"></a>Kdo by měl dostávat e-mailová oznámení?

Seznam příjemců e-mailů pro Azure služba AD DS by měl obsahovat lidi, kteří můžou spravovat a provádět změny ve spravované doméně. Tento e-mailový seznam by se měl představit jako "první reakce" na všechny výstrahy a problémy.

Pro e-mailová oznámení můžete přidat až pět dalších příjemců e-mailů. Pokud chcete pro e-mailová oznámení použít více než pět příjemců, vytvořte distribuční seznam a přidejte ho do seznamu oznámení.

Můžete se také rozhodnout, že mají všichni *globální správci* adresáře Azure AD a všichni členové skupiny *Správci řadičů domény AAD* dostávat e-mailová oznámení. Azure služba AD DS odesílá oznámení až 100 e-mailových adres, včetně seznamu globálních správců a správců řadiče domény AAD.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li zkontrolovat existující příjemce e-mailových oznámení nebo přidat další příjemce, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**.
1. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Na levé straně okna prostředků Azure služba AD DS vyberte **Nastavení oznámení**. Zobrazí se stávající příjemci e-mailových oznámení.
1. Příjemce e-mailu přidáte tak, že do tabulky další příjemci zadáte e-mailovou adresu.
1. Po dokončení vyberte **Uložit** na horním navigačním panelu.

> [!WARNING]
> Když změníte nastavení oznámení, aktualizují se nastavení oznámení pro celou spravovanou doménu, ne jenom sami.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Obdržel (a) jsem mi e-mailem upozornění na upozornění, ale když jsem přihlášený k Azure Portal nedošlo k žádné výstraze. Co se stalo?

Pokud se výstraha vyřeší, výstraha se z Azure Portal nevymaže. Nejpravděpodobnějším důvodem je to, že někdo jiný, kdo obdrží e-mailová oznámení, vyřešil upozornění ve spravované doméně nebo ho automaticky vyřešila platforma Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Proč není možné upravovat nastavení oznámení?

Pokud nemůžete získat přístup k stránce nastavení oznámení v Azure Portal, nemáte oprávnění k úpravám spravované domény. Kontaktujte globálního správce, který získá oprávnění k úpravám prostředku Azure služba AD DS nebo ho odeberte ze seznamu příjemců.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Zdá se, že nepřijímám e-mailová oznámení, i když mi Mám e-mailovou adresu. Proč?

Zkontrolujte e-mailovou zprávu nebo složku Nevyžádaná pošta v e-mailu s oznámením a ujistěte se, že odesílateli povolíte `azure-noreply@microsoft.com` .

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s některými problémy, které mohou být hlášeny, najdete v tématu [řešení výstrah ve spravované doméně][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
