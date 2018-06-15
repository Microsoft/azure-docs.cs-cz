---
title: Migrace ze služby Řízení přístupu Azure ve službě Active Directory do sdíleného přístupového podpisu autorizace | Microsoft Docs
description: Migrace aplikací z služby Řízení přístupu na SAS
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 7a2a55a6ad6a721a39c9f064aad817f841dd3235
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2017
ms.locfileid: "27160250"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrace ze služby Řízení přístupu Azure ve službě Active Directory do sdíleného přístupového podpisu autorizace

Service Bus aplikace měly možnost použití dvou různých autorizace modely: [sdíleného přístupového podpisu (SAS)](service-bus-sas.md) tokenu modelu poskytované služby Service Bus a federované model kde správy autorizační pravidla spravuje uvnitř [Azure Active Directory](/azure/active-directory/) služby Řízení přístupu (ACS) a tokeny získat ze serveru ACS se předávají do sběrnice pro autorizaci přístupu k požadované funkce.

Modelu autorizace služby ACS dlouho byla nahrazena [autorizace SAS](service-bus-authentication-and-authorization.md) jako preferovaným modelem a veškerá dokumentace, pokyny a ukázky výhradně pomocí SAS ještě dnes. Kromě toho je již nebude možné vytvořit nové obory názvů Service Bus, které jsou spárovaný s služby ACS.

SAS má výhodu v tom není okamžitě závisí na jiné službě, ale dá se použít přímo z klienta bez jakékoli prostředníci tím, že klientský přístup ke klíči název a pravidla SAS pravidlo. SAS můžete také snadno integrovat s přístupem, ve kterém klienta má nejdříve předat kontrolu autorizace pomocí jiné služby a pak je vystaví token. Pozdější přístup je podobná vzor používání služby ACS, ale umožňuje vydávání tokenů přístupu na základě podmínek specifické pro aplikace, které je obtížné express v rámci služby ACS.

Pro všechny existující aplikace, které jsou závislé na služby ACS doporučujeme zákazníkům migrace aplikací a místo toho spoléhají na SAS.

## <a name="migration-scenarios"></a>Scénáře migrace

Jsou integrované služby ACS a služby Service Bus pomocí sdíleného znalosti *podpisového klíče*. Podpisový klíč slouží oboru názvů služby ACS k podepisování tokenů autorizace a Service Bus je používána k ověření, zda byl vydán token podle spárované oboru názvů služby ACS. Obor názvů služby ACS obsahuje služba identity a pravidla autorizace. Autorizační pravidla definovat, které identita služby nebo které token vystavený externí zprostředkovatele identity získá, jaký typ přístupu, kterou část oboru názvů Service Bus graf ve formě nejdelší předponě shody.

Například může udělit pravidlo služby ACS **odeslat** deklarace identity na předponě, cesta `/` identitu služby, což znamená, že tokenem vydaným službou ACS založené na tomto pravidle uděluje klienta práva k odeslání do všech entit v oboru názvů. Pokud se cesta předponu `/abc`, identita je omezen na odesílání entit s názvem `abc` nebo uspořádané pod tuto předponu. Předpokládá se, čtečky tyto pokyny pro migraci jsou již obeznámeni s tyto koncepty.

Scénáře migrace rozdělit do tří základních skupin:

1.  **Beze změny výchozích nastavení**. Někteří zákazníci použít [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objekt, předávání automaticky generované **vlastníka** služba identity a jeho tajný klíč pro obor názvů služby ACS, spárovaný s oboru názvů Service Bus a není přidávat nová pravidla.

2.  **Vlastní služba identity s jednoduchých pravidel**. Někteří zákazníci přidejte nové identity služby a udělte každou novou identitu služby **odeslat**, **naslouchání**, a **spravovat** oprávnění pro jednu konkrétní entitu.

3.  **Vlastní služba identity s složitějších pravidel**. Velmi několik zákazníků mít komplexní pravidlo sady v které externě vystavené tokeny jsou namapované na práva na předávání nebo kde je přiřazená identity jedné služby rozlišené práva na několik názvů cest prostřednictvím více pravidel.

Pomoc s migrací sad pravidel komplexní, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). Příslušné dva scénáře povolit přehledné migrace.

### <a name="unchanged-defaults"></a>Beze změny výchozích nastavení

Pokud vaše aplikace se nezměnila výchozí nastavení služby ACS, můžete nahradit všechny [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) využití s [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objektu a používání oboru názvů předkonfigurované  **RootManageSharedAccessKey** místo služby ACS **vlastníka** účtu. Všimněte si, že i když služby ACS **vlastníka** účtu, tato konfigurace byla (a stále je) nedoporučuje nejsou obvykle, protože tento účet pravidlo poskytuje autoritu pro úplnou správu přes oboru názvů, včetně oprávnění k odstranění některého entity.

### <a name="simple-rules"></a>Jednoduchých pravidel

Pokud aplikace používá vlastní služba identity s jednoduchých pravidel, je migrace přehledné v případě, kdy byla vytvořena identita služby ACS k poskytování řízení přístupu na konkrétní fronty. Tento scénář se často stává v řešení SaaS stylu kde každá fronta slouží jako most lokalitu klienta nebo pobočky a identita služby se vytvoří pro daný web. Identita příslušné služby v takovém případě lze migrovat do sdíleného přístupového podpisu pravidlo, přímo do fronty. Název identity služby se může stát název pravidla SAS a klíč identity služby se může stát pravidlo klíče SAS. Práva pravidlo SAS se pak nakonfigurované ekvivalent v uvedeném pořadí příslušné služby ACS pravidla pro entitu.

Tento nový a další konfigurace SAS na místě můžete provést v jakékoli existující obor názvů, který je sdružených se službou ACS a migrace od služby ACS je následně provést pomocí [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) místo [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Obor názvů nemusí být odpojit od služby ACS.

### <a name="complex-rules"></a>Komplexní pravidla

Pravidla SAS nejsou určeny jako účty, ale jsou pojmenované podpisové klíče související s právy. Jako takový scénáře, ve kterých aplikace vytvoří mnoho identit služby a jim udělí oprávnění pro několik entit nebo celý obor názvů stále vyžadují vydání tokenu prostředník. Můžete získat pokyny pro takové prostředník podle [obrátíte na podporu](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další postup

Další informace o ověřování služby Service Bus, najdete v následujících tématech:

* [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md)
* [Ověřování služby Service Bus s podpisy sdíleného přístupu](service-bus-sas.md)
* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)

