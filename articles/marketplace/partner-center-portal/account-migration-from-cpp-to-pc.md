---
title: Migrace účtu z portál partnerů cloudu do partnerského centra – komerční tržiště pro Azure
description: Postup migrace účtu z CPP do partnerského centra – Komerční tržiště pro Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.openlocfilehash: 9ab9a57641e6b34942ae6d4293311714177aa012
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533184"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrace účtu z portál partnerů cloudu do partnerského centra

Když se vaše nabídky migrují přes portál partnerů cloudu (CPP) do partnerského centra (PC), jsou zamčené pro úpravy v CPP. V tomto okamžiku je potřeba migrovat nastavení účtu do partnerského centra.  Nastavení vašeho účtu i vaše nabídky se dají spravovat v partnerském centru.

## <a name="account-migration-process"></a>Proces migrace účtu

Při migraci nabídek z úrovně CPP je váš účet nakonfigurovaný pro migraci. 
 
Pokud jste uživatelem s rolí vlastníka v CPP pro daný účet, zobrazí se na stránce vašeho profilu vydavatele žlutý banner.  Zobrazí se výzva k přesunutí nastavení účtu do partnerského centra. 

Kliknutím na hlavičku zahajte proces migrace účtu. Očekává se, že zadáte následující položky:

### <a name="work-e-mail-address"></a>**Pracovní e-mailová adresa**

Ve většině případů se přihlaste pomocí e-mailové adresy, které používáte k přihlášení do CPP. V některých případech je třeba použít jinou e-mailovou adresu:

* Microsoft Account: Pokud je účet CPP účtem Microsoft, musíte zadat platnou pracovní e-mailovou adresu přidruženou k tenantovi, pro kterou je ID MPN zaregistrované. 

* Neshoda tenanta: Pokud vaše pracovní e-mailová adresa nepatří klientovi, který je přidružený k ID Microsoft Partner Network k dispozici ve vašem účtu CPP, zobrazí se chyba. Pokud chcete přesunout předchozí chybu, zadejte e-mailovou adresu přidruženou k tenantovi. Zobrazí se chybová zpráva, která poskytne název tenanta. 

### <a name="sign-up-for-microsoft-partner-network-program"></a>Zaregistrovat se k programu Microsoft Partner Network

V případě, že váš účet CPP nemá ID Microsoft Partner Network nebo má neplatný, bude nutné v rámci procesu aktivace zaregistrovat Microsoft Partner Network program.

## <a name="account-activation-is-complete"></a>Aktivace účtu je dokončená.

Migrace účtu se musí pro daný účet provést jenom jednou. Jakmile daný partner dokončí migraci tohoto účtu, zobrazí se tomuto chování na stránce profilu vydavatele všichni vlastníci:

1. Zobrazí se stránka nastavení partnera v Microsoft Partner Network, kde můžete spravovat nastavení účtu partnera Microsoftu. 
2. Po dokončení migrace účtu se žlutý banner na stránce vašeho profilu vydavatele zobrazí uživatelům patřícím do role vlastníka v CPP pro daný účet a požádá ho o správu nastavení svého účtu v partnerském centru. 
3. Stránka nastavení účtu v CPP je pak převedena do režimu jen pro čtení. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Přesun řešení založených na Dynamics 365 do partnerského centra

Pokud máte Dynamics 365 pro zákaznickou zapojení nebo Dynamics 365 pro finance a provozní řešení na jednom obchodním portálu GTM, postupujte podle **těchto pokynů do 31. srpna 2019,** aby se tato řešení přesunula do partnerského centra.

> [!NOTE]
> Pokud byl váš účet původně vytvořený v centru pro členství v partnerském centru (PMC), přihlaste se do [partnerského centra](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) a potvrďte, že byl váš účet migrován před dokončením následujících kroků. Pokud se vám zobrazí obrazovka s ID programu MPN, budete připraveni pokračovat. Pokud ne, musíte zahájit migraci účtu podle pokynů v [centru pro členství](https://partners.microsoft.com/partnerprogram/Welcome.aspx)v partnerovi. Pokud k tomu potřebujete pomoc, přejděte na [podporu](https://partner.microsoft.com/support?issueid=100-0077).

1. Přejít na [stránku Přehled komerčního tržiště v partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Pokud v levém navigačním podokně vidíte "komerční tržiště", jste zaregistrovaní a měli byste přejít k dalšímu kroku. Pokud ne, [Zaregistrujte si nyní komerční Marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
2. Potvrzování nabídek najdete v AppSource [hledáním vašich nabídek](https://appsource.microsoft.com/). Pokud jsou vaše nabídky už v AppSource, přejděte k dalšímu kroku. U jakékoli nabídky, která není v AppSource, vytvořte [novou nabídku dynamics 365 for Customer Engagement](create-new-customer-engagement-offer.md) nebo [novou nabídku Dynamics 365 for Operations](create-new-operations-offer.md).
3. Ověřte registraci v programu Business Applications ISV Connect:
  
   * Na stránce [smlouvy](https://partner.microsoft.com/dashboard/account/agreements) v partnerském centru se ujistěte, že jste přijali **Business Applications dodatek ISV** k registraci v programu.
   * Na stránce [Nastavení účtu](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) zadejte informace o fakturaci.

4. Odešlete každou novou a stávající nabídku k certifikaci, a to i v případě, že byly vaše nabídky dříve certifikovány. **Doporučujeme co nejdříve odeslat co nejrychleji, aby bylo možné provést schválení do 31. srpna 2019.**
5. Přejít na [jeden komerční partner GTM Portal](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) a přidejte adresu URL vašeho seznamu AppSource v části odkazy na Marketplace. Pokud potřebujete s tímto krokem pomáhat, pošlete nám e-mail na cosell@microsoft.comadresu.

## <a name="next-steps"></a>Další postup

- [Správa účtu komerčního tržiště v partnerském centru](./manage-account.md) 
