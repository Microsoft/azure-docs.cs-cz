---
title: Migrace z Azure Active Directory Access Control Service k povolení sdíleného přístupového podpisu | Dokumentace Microsoftu
description: Migrace aplikací z Access Control Service k SAS
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: 39764ce2dce8e00d1b41179e7285a0cea1c808bf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698940"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrace z Azure Active Directory Access Control Service k povolení sdíleného přístupového podpisu

Aplikace Azure Relay v minulosti bylo výběr mezi používáním dva modely různých autorizace: [sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) token model poskytuje přímo se službou Relay vytvoříte a federované model kde správy autorizační pravidla spravuje uvnitř [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) a tokeny získat ze služby ACS jsou předány Relay k autorizaci přístupu pro požadované funkce.

Modelu autorizace služby ACS dlouho byla nahrazena [autorizace SAS](../service-bus-messaging/service-bus-authentication-and-authorization.md) jako upřednostňovaný model a všechny dokumentace, pokyny a ukázky výhradně pomocí SAS ještě dnes. Kromě toho se už nedají vytvořit nové obory názvů přenosu, které jsou párovány s ACS.

SAS má výhodu v tom není okamžitě závisí na jiné službě, ale je možné přímo z klienta bez jakékoli prostředníci tím, že klientský přístup ke klíči SAS pravidlo název a pravidla. SAS můžete také snadno integrovat s přístupem, ve kterém se klient musí nejdříve projít kontrolu autorizace pomocí jiné služby a pak vystaví token. Druhý přístup se podobá se vzorem využití služby ACS, ale umožňuje vydávající přístupové tokeny na základě podmínek specifické pro aplikace, které se těžko express ve službě ACS.

Pro všechny existující aplikace, které jsou závislé na služby ACS doporučujeme zákazníkům při migraci svých aplikací přináší setrvávání u SAS místo.

## <a name="migration-scenarios"></a>Scénáře migrace

Služby ACS a propojení jsou integrované prostřednictvím sdílenou znalost *podpisový klíč*. Podpisový klíč obor názvů služby ACS používá k podepisování tokenů autorizace a je používá Azure Relay k ověření, zda token vystaven spárovaného oboru názvů služby ACS. Obor názvů služby ACS obsahuje služba identity a autorizačních pravidel. Autorizační pravidla definovat, které identity služeb nebo které u tokenu vydaného službou externího zprostředkovatele identity získá typu přístup do části obor názvů služby Relay grafu ve formě nejdelší předponě shoda.

Například může udělit pravidlo služby ACS **odeslat** deklarace identity na této předponě cesta `/` identitu služby, což znamená, že uděluje u tokenu vydaného službou ACS podle tohoto pravidla klienta práva k odeslání ke všem entitám v oboru názvů. Pokud je předpona cesty `/abc`, je omezen na odesílání do entity s názvem identita `abc` nebo uspořádané pod tuto předponu. Předpokládá se čtenáři této pokyny k migraci jsou již znáte tyto koncepty.

Scénáře migrace se dělí do tří hlavních kategorií:

1.  **Beze změny výchozích hodnot**. Někteří zákazníci používat [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objekt předávání automaticky generované **vlastníka** identity a svůj tajný klíč pro obor názvů služby ACS, spárovaná s obor názvů služby Relay, služby a proveďte bez přidání nových pravidel.

2.  **Vlastní službu identit pomocí jednoduchých pravidel**. Někteří zákazníci přidat novou službu identit a udělit každou novou identitu služby **odeslat**, **naslouchání**, a **spravovat** oprávnění jedné konkrétní entity.

3.  **Vlastní službu identity s komplexní pravidla**. Komplexní pravidla sad v které externě vydané tokeny jsou mapovány na práva na předávání nebo kde je přiřazena jediné služby identity rozlišené práva na několik názvů cest prostřednictvím více pravidel mají velmi málo zákazníci.

Potřebujete pomoc s migrací komplexní pravidla sad, můžete kontaktovat [podpory Azure](https://azure.microsoft.com/support/options/). Těchto dvou scénářích povolit Jednoduchá migrace.

### <a name="unchanged-defaults"></a>Beze změny výchozích hodnot

Pokud vaše aplikace nedošlo ke změně výchozích hodnot služby ACS, můžete nahradit všechny [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) využití pomocí [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objektu a používání oboru názvů předkonfigurované  **RootManageSharedAccessKey** místo ACS **vlastníka** účtu. Všimněte si, že i přes ACS **vlastníka** účtu, tato konfigurace byla (a stále je) doporučuje obecně, protože tento účet/pravidlo poskytuje autority pro úplnou správu přes obor názvů, včetně oprávnění k odstranění některého entity.

### <a name="simple-rules"></a>Jednoduchá pravidla

Pokud aplikace používá vlastní službu identit pomocí jednoduchých pravidel, migrace je jednoduché v případě, kdy byla vytvořena identita služby ACS k poskytování řízení přístupu na konkrétní předávací službu. Tento scénář je často případ v řešení SaaS – vizuální styl kde každá relay slouží jako most web pro klienty nebo pobočku a identita služby se vytvoří pro daný web. Identita příslušné služby v tomto případě lze migrovat do pravidlo sdíleného přístupového podpisu, přímo na službě relay. Název identity služby může být název pravidla SAS a klíč identity služby se může stát klíč SAS pro pravidlo. Práva pravidlo SAS se pak pravidlo nakonfigurované ekvivalent v uvedeném pořadí příslušné služby ACS pro entitu.

Tato nová a další konfigurace SAS na místě můžete dělat na existujícího oboru názvů, která je Federovaná pomocí služby ACS a migrace z ACS následně probíhá s využitím [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) místo [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Obor názvů nemusí být odpojit od služby ACS.

### <a name="complex-rules"></a>Komplexní pravidla

Pravidla SAS se nepředpokládá, že se účty, ale jsou pojmenovány podpisové klíče související s právy. Jako takové scénáře, ve kterých aplikace vytvoří mnoho identit služeb a povolení udělí oprávnění pro několik entit nebo celý obor názvů stále vyžadují zprostředkovatele se vydání tokenu. Můžete získat pokyny pro takové prostředník podle [kontaktujete podporu](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další postup

Další informace o ověřování Azure Relay, najdete v následujících tématech:

* [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md)
* [Ověřování služby Service Bus se sdílenými přístupovými podpisy](../service-bus-messaging/service-bus-sas.md)


