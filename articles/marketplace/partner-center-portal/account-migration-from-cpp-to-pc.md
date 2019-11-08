---
title: Migrace účtu z portál partnerů cloudu do partnerského centra – komerční tržiště pro Azure
description: Postup migrace účtu z CPP do partnerského centra – Komerční tržiště pro Azure
author: ChJenk
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: f8644a2aa989b7013cbbd64e8a8194bc08f40251
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813104"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrace účtu z portál partnerů cloudu do partnerského centra

Pokud máte existující účet portál partnerů cloudu (CPP), musí být vaše nastavení účtu migrována do partnerského centra.

## <a name="account-migration-process"></a>Proces migrace účtu

Pokud jste uživatel s rolí vlastníka v CPP pro daný účet, zobrazí se na stránce vašeho profilu vydavatele žlutý banner. Můžete patřit do jednoho z následujících dvou případů:

- Váš účet už je migrovaný a Vy jste připraveni spravovat nastavení svého účtu v partnerském centru.
- Váš účet nebyl migrován do partnerského centra a je třeba provést další akci.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Váš účet se migruje do partnerského centra.

Pro všechny účty, které dokončily migraci z CPP do partnerského centra, se Správa účtů stane v partnerském centru. Změny, jako je přidání nebo odstranění uživatele, budou synchronizovány zpět do CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Váš účet jste ještě nemigrovali do partnerského centra.

Kliknutím na hlavičku spusťte proces migrace účtu. Očekává se, že zadáte následující položky:

1. Pracovní e-mailová adresa: <br> <br> Ve většině případů se přihlaste pomocí e-mailové adresy, kterou používáte k přihlášení do CPP. V některých případech je třeba použít jinou e-mailovou adresu:

    * Účet Microsoft: Pokud je účet CPP účet Microsoft, zadejte platný pracovní e-mail přidružený ke klientovi, pro kterého je ID Microsoft Partner Network (MPN) zaregistrované. Další informace najdete v tématu [Registrace k Microsoft Partner Network programu](#sign-up-for-microsoft-partner-network-program).

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

## <a name="additional-resources"></a>Další zdroje

Připojte se k týdennímu [volání komunity Dynamics ISV](https://aka.ms/DynamicsISV-CommunityCall) pro podporu a aktualizace.

Pokud potřebujete pomoc při publikování, certifikaci nebo správě nabídek na webu Marketplace, [odešlete lístek podpory](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Další kroky

- [Správa účtu komerčního tržiště v partnerském centru](./manage-account.md)
