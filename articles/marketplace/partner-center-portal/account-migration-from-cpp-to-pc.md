---
title: Account migration from Cloud Partner Portal to Partner Center - Commercial Marketplace for Azure
description: How to migrate your account from CPP to Partner Center. - Commercial Marketplace for Azure
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 076250444989d4edb3ec86039ab25887ff1e206b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931317"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Account migration from Cloud Partner Portal to Partner Center

If you have an existing Cloud Partner Portal (CPP) account, your account settings need to be migrated to Partner Center.

## <a name="account-migration-process"></a>Account migration process

If you're a user with the Owner role in CPP for a given account, a yellow banner is shown on your Publisher Profile page. You may belong to one of the following two cases:

- Your account has already been migrated, and you're ready to manage your Account Settings in Partner Center.
- Your account has not been migrated to Partner Center and you need to take further action.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Your account has been migrated to Partner Center

For all accounts that have completed migration from the CPP to Partner Center, account management will happen in Partner Center. Changes such as user addition/deletion will be synced back to CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>You have not yet migrated your account to Partner Center

Click on the banner to start your account migration process. You're expected to enter the following items:

1. Work email address: <br> <br> In most cases, sign in with the email address that you use to sign into CPP. In certain cases, a different email address must be used:

    * Microsoft account: If the CPP account is a Microsoft account, enter a valid work email associated with the tenant for whom the Microsoft Partner Network (MPN) ID is registered. For more information, see [Sign up for Microsoft Partner Network Program](#sign-up-for-microsoft-partner-network-program).

    * Neshoda tenanta: Pokud vaše pracovní e-mailová adresa nepatří klientovi, který je přidružený k ID Microsoft Partner Network k dispozici ve vašem účtu CPP, zobrazí se chyba. Pokud chcete přesunout předchozí chybu, zadejte e-mailovou adresu přidruženou k tenantovi. Zobrazí se chybová zpráva, která poskytne název tenanta.

2. Zaregistrovat se k programu Microsoft Partner Network

    Pokud váš účet CPP nemá ID Microsoft Partner Network nebo má neplatnou hodnotu, budete muset v rámci procesu aktivace zaregistrovat Microsoft Partner Network program.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Zaregistrovat se k programu Microsoft Partner Network

Společnosti, které se chtějí partnerům s Microsoftem spolupracovat, se musí připojit k Microsoft Partner Network (MPN) a získat ID MPN. Pokud už jste členem Microsoft Partner Network a máte ID MPN, ponechte si informace užitečné, jak je budete potřebovat během procesu aktivace účtu.  

Pokud nejste členem Microsoft Partner Network, můžete se k [němu připojit](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) a získat ID MPN. Poznamenejte si ID MPN, protože ho budete muset zadat během procesu aktivace účtu.

Další informace o Microsoft Partner Network najdete v tématu [připojení k Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) na webu partnera. Další informace o výhodách ISV v Microsoft Partner Network najdete v [Centru zdrojů ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Přesunutí nabídek Dynamics 365 a PowerApps do partnerského centra

Aby bylo možné zjednodušit správu účtu a nabídky pro operace Dynamics 365 Customer Engagement, PowerApps a Dynamics 365, nabídky byly přesunuty do [partnerského centra](https://partner.microsoft.com/). Při přesunu se zajistí, že stejný obsah bude k dispozici pro veřejné i prodávající katalogy.

Konkrétní informace o tom, co je potřeba udělat **15. října 2019** pro nabídky služby Dynamics 365 Customer Engagement, PowerApps a Dynamics 365, najdete v následujících pokynech.

> [!NOTE]
> To se nevztahuje na nabídky Dynamics 365 Business Central.  

1. Pokud jste svůj účet MPN ve svém předplatném vytvořili v centru pro členství v partnerském centru (PMC), přihlaste se do [partnerského centra](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) a potvrďte, že byl váš účet migrován. Pokud se vám zobrazí obrazovka s ID programu MPN, budete připraveni pokračovat v práci. V takovém případě je potřeba spustit migraci účtu podle pokynů v centru pro členství v [partnerovi](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Pokud potřebujete pomoc, přejděte na [podporu](https://partner.microsoft.com/support?issueid=100-0077).
2. Přejít na [stránku Přehled komerčního tržiště v partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Pokud se v levém navigačním podokně zobrazí "komerční tržiště", jste zaregistrovaní a měli byste pokračovat k dalšímu kroku. Pokud ne, [Zaregistrujte si nyní komerční Marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
3. Potvrzování nabídek najdete v AppSource [hledáním vašich nabídek](https://appsource.microsoft.com/). Pokud jsou vaše nabídky už v AppSource, pokračujte k dalšímu kroku. V případě jakékoli nabídky, která není v AppSource, vytvořte novou nabídku Dynamics [365 Customer Engagement](create-new-customer-engagement-offer.md) nebo [novou nabídku operací Dynamics 365](create-new-operations-offer.md).
4. Na [stránce smlouvy](https://partner.microsoft.com/dashboard/account/agreements)partnerského centra se ujistěte, že jste zkontrolovali a přijali **Business Applications dodatek ISV**.
5. V [Nastavení účtu](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)partnerského centra se ujistěte, že jsou informace o fakturaci dokončené.
6. Odešlete všechny nové a stávající nabídky pro certifikaci a publikování, a to i v případě, že vaše nabídky byly dříve certifikovány.
    * Dokončete obrazovky s informacemi, včetně poskytování vaší aplikace k certifikaci, a také informace o marketingu. Vyberte **Odeslat** (v pravém horním rohu obrazovky) do **15. října 2019**. Tyto kroky je nutné provést, aby nedošlo k ovlivnění dostupnosti této nabídky.
    * Pokud máte nárok, můžete během tohoto procesu požádat o účast na úrovni Premium.
    * Certifikace nebo recertifikace vyžaduje, aby vaše aplikace podporovala nejnovější verzi naší Business Applications platformy.
    * Po schválení vaší aplikace obdržíte e-mail, který se má vrátit na nabídku, a vyberte "přejít do živého", aby se nabídka mohla na Microsoft AppSource živě začít.

## <a name="additional-resources"></a>Další materiály

Připojte se k týdennímu [volání komunity Dynamics ISV](https://aka.ms/DynamicsISV-CommunityCall) pro podporu a aktualizace.

Pokud potřebujete pomoc při publikování, certifikaci nebo správě nabídek na webu Marketplace, [odešlete lístek podpory](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Další kroky

- [Správa účtu komerčního tržiště v partnerském centru](./manage-account.md)
