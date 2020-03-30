---
title: Migrace účtu z portálu partnerů cloudu do Partnerského centra – Komerční tržiště pro Azure
description: Jak migrovat účet z CPP do Centra partnerů. - Komerční tržiště pro Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 4aa336183d25a524246a2036fb95e0a7657b84f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279806"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrace účtu z portálu partnerů cloudu do partnerského centra

Pokud máte existující účet cloudového partnerského portálu (CPP), je třeba migrovat nastavení vašeho účtu do Centra partnerů.

## <a name="account-migration-process"></a>Proces migrace účtu

Pokud jste uživatel s rolí Vlastník v CPP pro daný účet, zobrazí se na stránce Profilu aplikace Publisher žlutý nápis. Můžete patřit k jednomu z následujících dvou případů:

- Váš účet již byl migrován a jste připraveni spravovat nastavení účtu v Centru partnerů.
- Váš účet nebyl migrován do Centra partnerů a je třeba provést další kroky.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Váš účet byl migrován do Centra partnerů.

U všech účtů, které dokončily migraci z CPP do Partnerského centra, prostby účtů proběhne v Centru partnerů. Změny, jako je přidání/odstranění uživatele, budou synchronizovány zpět do cpp.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Účet jste ještě nemigrovali do Centra partnerů.

Kliknutím na banner zahájíte proces migrace účtu. Očekává se, že zadáte následující položky:

1. Pracovní e-mailová adresa: <br> <br> Ve většině případů se přihlaste pomocí e-mailové adresy, kterou používáte k přihlášení k cpp. V některých případech musí být použita jiná e-mailová adresa:

    * Účet Microsoft: Pokud je účet CPP účet Microsoft, zadejte platný pracovní e-mail přidružený k klientovi, pro kterého je registrováno ID sítě partnerů (Microsoft Partner Network). Další informace naleznete v [tématu Registrace do programu Microsoft Partner Network Program](#sign-up-for-microsoft-partner-network-program).

    * Neshoda klientů: Pokud vaše pracovní e-mailová adresa nepatří klientovi, který je přidružen k ID sítě partnerů Microsoft u vašeho účtu CPP, zobrazí se chyba. Chcete-li tuto chybu přenést, zadejte e-mailovou adresu přidruženou k tenantovi. Chybová zpráva bude poskytovat název klienta.

2. Registrace do programu Microsoft Partner Network

    Pokud váš účet CPP nemá ID sítě partnerů společnosti Microsoft nebo má neplatný účet, budete se muset v rámci procesu aktivace zaregistrovat do programu Microsoft Partner Network.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registrace do programu Microsoft Partner Network

Společnosti, které chtějí spolupracovat se společností Microsoft, se musí připojit k programu Microsoft Partner Network (MPN) a získat id mpn. Pokud jste již členem programu Microsoft Partner Network a máte ID MPN, mějte informace po ruce, jak je budete potřebovat během procesu aktivace účtu.  

Pokud nejste členem programu Microsoft Partner Network, můžete [se připojit zde](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) a získat id mpn. Poznamenejte si své ID MPN, protože ho budete muset zadat během procesu aktivace účtu.

Další informace o programu Microsoft Partner Network najdete [v tématu Připojení k síti partnerů společnosti Microsoft](https://partner.microsoft.com/en-US/membership) na partnerském webu. Další informace o výhodách neinstalačního programu společnosti Microsoft naleznete v centru [ISV Resource Hub](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Přesunutí nabídky Dynamics 365 a PowerApps do Centra partnerů

Aby bylo účelem zjednodušení správy účtů a nabídek pro služby Dynamics 365 Customer Engagement, PowerApps a Dynamics 365 Operations, byly nabídky přesunuty do [Centra partnerů](https://partner.microsoft.com/). Přesunutí zajišťuje, že stejný obsah je k dispozici pro veřejné i prodejce katalogy.

Konkrétní informace o tom, co je třeba udělat do **15.10.2019** pro vaše nabídky Dynamics 365 Customer Engagement, PowerApps a Dynamics 365 Operations, najdete v následujících pokynech.

> [!NOTE]
> To se nevztahuje na nabídky Dynamics 365 Business Central.  

1. Pokud byl váš účet členství v programu MPN původně vytvořen v Centru členství partnera (PMC), přihlaste se do [Centra partnerů](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) a ověřte, zda byl váš účet migrován. Pokud se zobrazí obrazovka s profilem s id MPN, můžete pokračovat. Pokud ne, musíte zahájit migraci účtu podle pokynů v [Centru členství partnera](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Pokud potřebujete pomoc, navštivte [podporu](https://partner.microsoft.com/support?issueid=100-0077).
2. Přejděte na [stránku přehledu komerčního tržiště v Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Pokud se v levém navigačním podokně zobrazí "Komerční tržiště", jste zaregistrovaní a měli byste pokračovat dalším krokem. Pokud ne, [zaregistrujte se na komerčním trhu](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) nyní.
3. Potvrďte, že vaše nabídky jsou v AppSource [vyhledáním nabídek](https://appsource.microsoft.com/). Pokud vaše nabídky jsou již v AppSource, pokračujte dalším krokem. Pro jakoukoli nabídku, která není v AppSource, vytvořte [novou nabídku Dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) nebo novou [nabídku Dynamics 365 Operations](create-new-operations-offer.md).
4. Na stránce Smlouvy s [Partnerským](https://partner.microsoft.com/dashboard/account/agreements)centrem zkontrolujte a přijměte **dodatek k isv podnikových aplikací**.
5. V [nastavení účtu](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)Partnerského centra zkontrolujte, zda jsou fakturační údaje úplné.
6. Odešlete každou novou a stávající nabídku k certifikaci a publikaci, a to i v případě, že vaše nabídky byly dříve certifikovány.
    * Vyplňte informační obrazovky, včetně poskytování aplikace pro certifikaci, stejně jako marketingové informace. **října 2019**vyberte **Odeslat** (pravý horní roh obrazovky). Tyto kroky musí být dokončeny, aby nedošlo k ovlivnění dostupnosti nabídky.
    * Pokud máte nárok, můžete požádat o účast v prémiové úrovni během tohoto procesu.
    * Certifikace nebo opětovná certifikace vyžaduje, aby vaše aplikace podporovala nejnovější verzi naší platformy podnikových aplikací.
    * Po schválení aplikace obdržíte e-mail, abyste se vrátili k nabídce a vybrali "přejít do provozu", abyste mohli nabídku přejít na zařízení Microsoft AppSource.

## <a name="additional-resources"></a>Další zdroje

Připojte se k [týdennímu komunitnímu volání dynamics ISV](https://aka.ms/DynamicsISV-CommunityCall) o podporu a aktualizace.

Pokud potřebujete pomoc s publikováním, certifikací nebo správou nabídek na marketplace, [odešlete lístek podpory](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Další kroky

- [Správa účtu Commercial Marketplace v Partnerském centru](./manage-account.md)
