---
title: Migrace z Access Control Service Azure AD na SAS
description: Přečtěte si o migraci z Azure Active Directory Access Control Service na autorizaci sdíleného přístupového podpisu.
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: ae0dd3827e17cc63b4b698eb8d88a08799c7278f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790349"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrace z Azure Active Directory Access Control Service na autorizaci sdíleného přístupového podpisu

Service Bus aplikace už dříve vybrali použití dvou různých autorizačních modelů: model tokenu [sdíleného přístupového podpisu (SAS)](service-bus-sas.md) poskytovaný přímo Service Bus a federovaný model, ve kterém je Správa autorizačních pravidel Spravováno uvnitř nástrojem [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) a tokeny získané ze služby ACS jsou předány Service Bus pro autorizaci přístupu k požadovaným funkcím.

Autorizační model služby ACS byl v současnosti nahrazen [autorizací SAS](service-bus-authentication-and-authorization.md) jako preferovaným modelem a všechny dokumentace, pokyny a ukázky používají výhradně SAS ještě dnes. Kromě toho již není možné vytvářet nové obory názvů Service Bus, které jsou spárovány se službou ACS.

SAS má výhodu v tom, že není přímo závislý na jiné službě, ale dá se použít přímo z klienta bez jakýchkoli dodavatelů tím, že klientovi poskytne přístup k názvu a klíči pravidla SAS. SAS je taky možné snadno integrovat s přístupem, ve kterém klient musí nejdřív projít kontrolu autorizace pomocí jiné služby a potom vystavit token. Druhý přístup je podobný vzoru využití služby ACS, ale umožňuje vystavovat tokeny přístupu na základě podmínek specifických pro aplikaci, které je obtížné vyjádřit ve službě ACS.

Pro všechny existující aplikace, které jsou závislé na službě ACS, doporučujeme zákazníkům migrovat své aplikace tak, aby se místo toho spoléhali na SAS.

## <a name="migration-scenarios"></a>Scénáře migrace

Služby ACS a Service Bus jsou integrovány prostřednictvím sdíleného vědomí *podpisového klíče*. Podpisový klíč se používá v oboru názvů ACS k podepisování tokenů autorizace a používá ho Service Bus k ověření toho, že token byl vydaný spárovaným oborem názvů ACS. Obor názvů ACS obsahuje identity služby a autorizační pravidla. Autorizační pravidla definují, která identita služby nebo který token vydaný externím zprostředkovatelem identity získá typ přístupu k části grafu oboru názvů Service Bus ve formě shody s nejdelší předponou.

Pravidlo ACS například může **udělit deklaraci identity na cestě** `/` k identitě služby, což znamená, že token vydaný službou ACS na základě tohoto pravidla uděluje oprávnění klienta k odesílání všem entitám v oboru názvů. Pokud je předpona cesty `/abc`, je identita omezena na odeslání do entit s názvem `abc` nebo uspořádané pod touto předponou. Předpokládá se, že čtenáři těchto pokynů k migraci už znají tyto koncepty.

Scénáře migrace spadají do tří hlavních kategorií:

1.  **Nezměněné výchozí hodnoty**. Někteří zákazníci používají objekt [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) , který předá automaticky generovanou identitu služby **vlastníka** a její tajný klíč pro obor názvů ACS, spárováný s oborem názvů Service Bus a nepřidává nová pravidla.

2.  **Vlastní identity služby s jednoduchými pravidly**. Někteří zákazníci přidávají nové identity služby a udělují každé nové identitě služby oprávnění **Odeslat**, **naslouchat**a **Spravovat** pro jednu konkrétní entitu.

3.  **Vlastní identity služby se složitými pravidly**. Příliš málo zákazníků má komplexní sady pravidel, ve kterých jsou externě vydávané tokeny namapovány na práva na Relay nebo kde je jedna identita služby přiřazena odlišná práva k několika cestám oboru názvů prostřednictvím více pravidel.

Pro pomoc s migrací komplexních sad pravidel se můžete obrátit na [podporu Azure](https://azure.microsoft.com/support/options/). Další dva scénáře umožňují přímá migrace.

### <a name="unchanged-defaults"></a>Nezměněné výchozí hodnoty

Pokud vaše aplikace nezměnila výchozí nastavení služby ACS, můžete nahradit všechna [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) využití objektem [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) a místo toho použít **RootManageSharedAccessKey** s oborem názvů. účtu **vlastníka** služby ACS. Pamatujte na to, že i s účtem **vlastníka** služby ACS byla tato konfigurace (a stále ještě) obecně doporučená, protože tento účet nebo pravidlo poskytují plnou autoritu pro správu nad oborem názvů, včetně oprávnění k odstraňování jakýchkoli entit.

### <a name="simple-rules"></a>Jednoduchá pravidla

Pokud aplikace používá vlastní identity služby s jednoduchými pravidly, migrace je jednoduchá v případě, že byla vytvořena identita služby ACS, která poskytuje řízení přístupu v konkrétní frontě. Tento scénář se často používá v řešeních ve stylu SaaS, kde se jednotlivé fronty používají jako most na webu klienta nebo pobočce a identita služby se pro danou lokalitu vytvoří. V takovém případě může být příslušná identita služby migrována do pravidla sdíleného přístupového podpisu přímo do fronty. Název identity služby se může stát názvem pravidla SAS a klíč identity služby se může stát klíčem pravidla SAS. Práva pravidla SAS se pak nakonfigurují jako odpovídající příslušné pravidlo služby ACS pro danou entitu.

Tuto novou a dodatečnou konfiguraci SAS můžete provést na jakémkoli stávajícím oboru názvů, který je federované pomocí služby ACS, a pak se migrace mimo službu ACS provede pomocí [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) místo [ SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Obor názvů není nutné odpojit od služby ACS.

### <a name="complex-rules"></a>Složitá pravidla

Pravidla SAS nejsou určena pro účty, ale mají pojmenované podpisové klíče přidružené k právům. V takových scénářích, ve kterých aplikace vytváří mnoho identit služeb a uděluje jim přístupová práva pro několik entit, nebo celý obor názvů stále vyžaduje zprostředkovatele vydávající token. Obraťte se na [podporu](https://azure.microsoft.com/support/options/), kde můžete získat pokyny pro takového zprostředkovatele.

## <a name="next-steps"></a>Další kroky

Další informace o ověřování Service Bus najdete v následujících tématech:

* [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md)
* [Service Bus ověřování pomocí sdílených přístupových podpisů](service-bus-sas.md)

