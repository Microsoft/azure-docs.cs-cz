---
title: Migrace účtu z portál partnerů cloudu do partnerského centra – komerční tržiště pro Azure
description: Postup migrace účtu z CPP do partnerského centra – Komerční tržiště pro Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147124"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrace účtu z portál partnerů cloudu do partnerského centra

Pokud máte existující účet CPP, nastavení vašeho účtu se musí migrovat do partnerského centra.

## <a name="account-migration-process"></a>Proces migrace účtu

Pokud jste uživatelem s rolí vlastníka v CPP pro daný účet, zobrazí se na stránce vašeho profilu vydavatele žlutý banner. Můžete patřit do jednoho z následujících dvou případů:

- Váš účet už je migrovaný a Vy jste připraveni spravovat nastavení svého účtu v partnerském centru.
- K dokončení migrace účtu do partnerského centra je potřeba provést několik dalších akcí.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Váš účet se migruje do partnerského centra.

Pro všechny účty, které dokončily migraci z CPP do partnerského centra, se Správa účtů stane v partnerském centru. Změny, jako je přidání nebo odstranění uživatele, budou synchronizovány zpět do CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Váš účet jste ještě nemigrovali do partnerského centra.

Kliknutím na hlavičku zahajte proces migrace účtu. Očekává se, že zadáte následující položky:

1. Pracovní e-mailová adresa: <br> <br> Ve většině případů se přihlaste pomocí e-mailové adresy, kterou používáte k přihlášení do CPP. V některých případech je třeba použít jinou e-mailovou adresu:

    * Účet Microsoft: Pokud je účet CPP účet Microsoft, musíte zadat platnou pracovní e-mailovou adresu přidruženou k tenantovi, pro kterou je ID MPN zaregistrované.

    * Neshoda tenanta: Pokud vaše pracovní e-mailová adresa nepatří klientovi, který je přidružený k ID Microsoft Partner Network k dispozici ve vašem účtu CPP, zobrazí se chyba. Pokud chcete přesunout předchozí chybu, zadejte e-mailovou adresu přidruženou k tenantovi. Zobrazí se chybová zpráva, která poskytne název tenanta.

2. Zaregistrovat se k programu Microsoft Partner Network

    V případě, že váš účet CPP nemá ID Microsoft Partner Network nebo má neplatný, bude nutné v rámci procesu aktivace zaregistrovat Microsoft Partner Network program.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Zaregistrovat se k programu Microsoft Partner Network

Společnosti, které se chtějí partnerům s Microsoftem spolupracovat, se musí připojit k Microsoft Partner Network (MPN) a získat ID MPN. Pokud už jste členem Microsoft Partner Network a máte ID MPN, nezapomeňte ho mít na ruce, protože ho budete muset zadat během procesu aktivace účtu.  

Pokud ještě nejste členem Microsoft Partner Network, můžete se [připojit hned](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) a získat ID MPN. Nezapomeňte si poznamenat své ID MPN, protože ho budete muset zadat během procesu aktivace účtu.

Další informace o Microsoft Partner Network najdete v tématu [připojení k Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) na webu partnera. Další informace o tom, jak se připojovat k Microsoft Partner Network výhodami nezávislých výrobců softwaru, najdete v [Centru zdrojů ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Přesun řešení založených na Dynamics 365 do partnerského centra

Pokud jste vytvořili Dynamics 365 pro zákaznickou zapojení nebo Dynamics 365 pro finance a provozní řešení na jednom obchodním portálu GTM, **měli byste tato řešení spravovat v partnerském centru**.

**Pokud jste svá řešení nepřesunuli do 31. srpna 2019**, proveďte níže uvedené kroky co nejdříve. Dokud to neuděláte:

- Nezávislí dodavatelé softwaru nebudou mít přístup k marketingovým výhodám
- Jejich stav přijde na společný prodej s upřednostněním.
- Ty, které vyžadují vložení do cloudu, se po 15. říjnu 2019 neshodují.

> [!NOTE]
> Pokud jste svůj účet MPN ve svém předplatném vytvořili v centru pro členství v partnerském centru (PMC), přihlaste se do [partnerského centra](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , abyste před dokončením následujících kroků ověřili, že jste účet migrovali. Pokud se vám zobrazí obrazovka s ID programu MPN, budete připraveni pokračovat. Pokud ne, musíte zahájit migraci účtu podle pokynů v [centru pro členství v partnerovi](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Pokud k tomu potřebujete pomoc, přejděte na [podporu](https://partner.microsoft.com/support?issueid=100-0077).

1. Přejít na [stránku Přehled komerčního tržiště v partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Pokud v levém navigačním podokně vidíte "komerční tržiště", jste zaregistrovaní a měli byste přejít k dalšímu kroku. Pokud ne, [Zaregistrujte si nyní komerční Marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .

2. Potvrzování nabídek najdete v AppSource [hledáním vašich nabídek](https://appsource.microsoft.com/). Pokud jsou vaše nabídky už v AppSource, přejděte k dalšímu kroku. U jakékoli nabídky, která není v AppSource, vytvořte [novou nabídku dynamics 365 for Customer Engagement](create-new-customer-engagement-offer.md) nebo [novou nabídku Dynamics 365 for Operations](create-new-operations-offer.md).

3. Ověřte registraci v programu Business Applications ISV Connect:
   * Na stránce [smlouvy](https://partner.microsoft.com/dashboard/account/agreements) v partnerském centru se ujistěte, že jste přijali **Business Applications dodatek ISV** k registraci v programu.
   * Na stránce [Nastavení účtu](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) zadejte informace o fakturaci.

4. Odešlete každou novou a stávající nabídku k certifikaci, a to i v případě, že byly vaše nabídky dříve certifikovány. Pokud máte nárok, můžete během tohoto procesu požádat o účast na úrovni Premium. Pokud byla vaše nabídka dřív certifikována, **musíte dokončit recertifikaci aplikace do 15. října 2019.** Certifikace nebo recertifikace bude vyžadovat, aby vaše aplikace podporovala nejnovější verzi naší Business Applications platformy.

5. Přejít na [jeden komerční partner GTM Portal](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) a přidejte adresu URL vašeho seznamu AppSource v části odkazy na Marketplace. Pokud potřebujete s tímto krokem pomáhat, pošlete nám e-mail na cosell@microsoft.comadresu.

## <a name="next-steps"></a>Další kroky

- [Správa účtu komerčního tržiště v partnerském centru](./manage-account.md) 
