---
title: Azure Service Bus – migrace na autorizaci sdíleného přístupového podpisu
description: Přečtěte si o migraci ze služby Azure Active Directory Access Control na autorizaci sdíleného přístupového podpisu.
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 532bbaf0b983b2d4310780686777cbe895afebe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774623"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Service Bus – migrace ze služby Azure Active Directory Access Control Service na autorizaci podpisu sdíleného přístupu

Aplikace service bus dříve měly možnost používat dva různé autorizační modely: model [tokenu s sdíleným přístupovým podpisem (SAS)](service-bus-sas.md) poskytovaný přímo službou Service Bus a federovaný model, kde je správa pravidel autorizace spravována uvnitř služby [ACS (Azure Active Directory](/azure/active-directory/) Access Control Service) a tokeny získané ze služby ACS jsou předány službě Service Bus pro autorizaci přístupu k požadovaným funkcím.

Autorizační model ACS byl dlouho nahrazen [autorizací SAS](service-bus-authentication-and-authorization.md) jako upřednostňovaný model a veškerá dokumentace, pokyny a ukázky používají výhradně SAS dnes. Kromě toho již není možné vytvořit nové obory názvů service bus, které jsou spárovány se službou ACS.

SAS má výhodu v tom, že není okamžitě závislá na jiné službě, ale může být použita přímo od klienta bez zprostředkovatelů tím, že klientovi poskytuje přístup k názvu pravidla SAS a klíči pravidla. SAS lze také snadno integrovat s přístupem, ve kterém klient musí nejprve předat kontrolu autorizace s jinou službou a pak je vydán token. Druhý přístup je podobný vzor využití služby ACS, ale umožňuje vydávání přístupových tokenů na základě podmínek specifických pro aplikaci, které je obtížné vyjádřit v acs.

Pro všechny existující aplikace, které jsou závislé na službě ACS, vyzýváme zákazníky, aby migrovali své aplikace a místo toho spoléhali na SAS.

## <a name="migration-scenarios"></a> Scénáře migrace

ACS a Service Bus jsou integrovány prostřednictvím sdílené znalosti *podpisového klíče*. Podpisový klíč používá obor názvů služby ACS k podepisování autorizačních tokenů a služba Service Bus jej používá k ověření, že token byl vydán spárovaným oborem názvů služby ACS. Obor názvů služby ACS obsahuje identity služeb a autorizační pravidla. Autorizační pravidla definují, která identita služby nebo který token vydaný externím zprostředkovatelem identity získá jaký typ přístupu k části grafu oboru názvů Service Bus ve formě shody s nejdelší předponou.

Pravidlo služby ACS může například udělit deklaraci **Odeslat** na předponu `/` cesty k identitě služby, což znamená, že token vydaný službou ACS na základě tohoto pravidla uděluje klientům práva k odeslání všem entitám v oboru názvů. Pokud je `/abc`předpona cesty , identita je omezena `abc` na odesílání entitám s názvem nebo uspořádaným pod touto předponou. Předpokládá se, že čtenáři tohoto průvodce migrací jsou již obeznámeni s těmito koncepty.

Scénáře migrace spadají do tří širokých kategorií:

1.  **Nezměněné výchozí hodnoty**. Někteří zákazníci používají objekt [SharedSecretTokenProvider,](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) který předává automaticky generovanou identitu služby **vlastníka** a jeho tajný klíč pro obor názvů služby ACS, spárovaný s oborem názvů Service Bus a nepřidávejte nová pravidla.

2.  **Vlastní identity služby s jednoduchými pravidly**. Někteří zákazníci přidávají nové identity služby a udělují každé nové identitě služby **Oprávnění Pro odesílání**, **naslouchání**a **správa** pro jednu konkrétní entitu.

3.  **Vlastní identity služeb se složitými pravidly**. Velmi málo zákazníků má složité sady pravidel, ve kterých jsou externě vydané tokeny mapovány na práva v přenosu nebo kde je jedné identitě služby přiřazena diferencovaná práva na několika cestách oboru názvů prostřednictvím více pravidel.

Potřebujete-li pomoc s migrací složitých sad pravidel, můžete kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/). Další dva scénáře umožňují přímou migraci.

### <a name="unchanged-defaults"></a>Nezměněné výchozí hodnoty

Pokud vaše aplikace nezměnila výchozí hodnoty služby ACS, můžete nahradit veškeré použití [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objektem [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) a použít obor názvů předkonfigurovaný **RootManageSharedAccessKey** namísto účtu **vlastníka** služby ACS. Všimněte si, že i s účtem **vlastníka** služby ACS tato konfigurace nebyla (a stále je) obecně doporučena, protože tento účet nebo pravidlo poskytuje úplnou autoritu pro správu nad oborem názvů, včetně oprávnění k odstranění všech entit.

### <a name="simple-rules"></a>Jednoduchá pravidla

Pokud aplikace používá vlastní identity služby s jednoduchými pravidly, migrace je jednoduchá v případě, kdy byla vytvořena identita služby ACS, která poskytuje řízení přístupu v určité frontě. Tento scénář je často případ v řešení stylu SaaS, kde každá fronta se používá jako most do lokality klienta nebo pobočky a identita služby je vytvořen pro tento konkrétní web. V takovém případě lze příslušnou identitu služby migrovat do pravidla sdíleného přístupového podpisu přímo ve frontě. Název identity služby se může stát názvem pravidla SAS a klíč identity služby se může stát klíčem pravidla SAS. Práva pravidla SAS jsou pak konfigurována jako rovnocenná příslušnému pravidlu acs pro entitu.

Tuto novou a dodatečnou konfiguraci SAS můžete umístit na libovolném existujícím oboru názvů, který je federován službou ACS, a migrace mimo službu ACS se následně provede pomocí [služby SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) namísto [sharedsecrettokenprovider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Obor názvů nemusí být odpojen ze služeb ACS.

### <a name="complex-rules"></a>Komplexní pravidla

Pravidla SAS nejsou určeny k účtům, ale jsou pojmenovány podpisové klíče přidružené k právům. Jako takové scénáře, ve kterých aplikace vytvoří mnoho identit služby a uděluje jim přístupová práva pro několik entit nebo celý obor názvů stále vyžadují zprostředkovatele vystavující tokeny. Pokyny pro takového zprostředkovatele můžete získat [kontaktováním podpory](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

Další informace o ověřování služby Service Bus najdete v následujících tématech:

* [Ověřování a autorizace Service Bus](service-bus-authentication-and-authorization.md)
* [Ověřování služby Service Bus pomocí sdílených přístupových podpisů](service-bus-sas.md)

