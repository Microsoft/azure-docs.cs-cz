---
title: Azure Relay – migrace na autorizaci sdíleného přístupového podpisu
description: Popisuje, jak migrovat aplikace Azure Relay z používání služby Azure Active Directory Access Control Service na autorizaci sdíleného přístupového podpisu.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 59b9e734526c56016e2ddf59c2afb5b8f7b4ad09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514897"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Relay – migrace ze služby Azure Active Directory Access Control Service na autorizaci sdíleného přístupového podpisu

Aplikace Azure Relay měly historicky na výběr pomocí dvou různých modelů autorizace: modelu [tokenu sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) poskytovaného přímo službou Relay a federovaného modelu, kde je správa pravidel autorizace spravována uvnitř služby [ACS (Azure Active Directory](/azure/active-directory/) Access Control Service) a tokeny získané ze služby ACS jsou předány rezidui pro autorizaci přístupu k požadovaným funkcím.

Autorizační model ACS byl dlouho nahrazen [autorizací SAS](../service-bus-messaging/service-bus-authentication-and-authorization.md) jako upřednostňovaný model a veškerá dokumentace, pokyny a ukázky používají výhradně SAS dnes. Kromě toho již není možné vytvářet nové obory názvů relay, které jsou spárovány se systémem ACS.

SAS má výhodu v tom, že není okamžitě závislá na jiné službě, ale může být použita přímo od klienta bez zprostředkovatelů tím, že klientovi poskytuje přístup k názvu pravidla SAS a klíči pravidla. SAS lze také snadno integrovat s přístupem, ve kterém klient musí nejprve předat kontrolu autorizace s jinou službou a pak je vydán token. Druhý přístup je podobný vzor využití služby ACS, ale umožňuje vydávání přístupových tokenů na základě podmínek specifických pro aplikaci, které je obtížné vyjádřit v acs.

Pro všechny existující aplikace, které jsou závislé na službě ACS, vyzýváme zákazníky, aby migrovali své aplikace a místo toho spoléhali na SAS.

## <a name="migration-scenarios"></a> Scénáře migrace

ACS a Relay jsou integrovány prostřednictvím sdílených znalostí *podpisového klíče*. Podpisový klíč používá obor názvů služby ACS k podepisování autorizačních tokenů a používá jej Azure Relay k ověření, že token byl vydán spárovaným oborem názvů služby ACS. Obor názvů služby ACS obsahuje identity služeb a autorizační pravidla. Autorizační pravidla definují, která identita služby nebo který token vydaný externím zprostředkovatelem identity získá jaký typ přístupu k části grafu oboru názvů Relay, ve formě shody s nejdelší předponou.

Pravidlo služby ACS může například udělit deklaraci **Odeslat** na předponu `/` cesty k identitě služby, což znamená, že token vydaný službou ACS na základě tohoto pravidla uděluje klientům práva k odeslání všem entitám v oboru názvů. Pokud je `/abc`předpona cesty , identita je omezena `abc` na odesílání entitám s názvem nebo uspořádaným pod touto předponou. Předpokládá se, že čtenáři tohoto průvodce migrací jsou již obeznámeni s těmito koncepty.

Scénáře migrace spadají do tří širokých kategorií:

1.  **Nezměněné výchozí hodnoty**. Někteří zákazníci používají objekt [SharedSecretTokenProvider,](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) který předává automaticky generovanou identitu služby **vlastníka** a jeho tajný klíč pro obor názvů služby ACS, spárovaný s oborem názvů Relay, a nepřidávají nová pravidla.

2.  **Vlastní identity služby s jednoduchými pravidly**. Někteří zákazníci přidávají nové identity služby a udělují každé nové identitě služby **Oprávnění Pro odesílání**, **naslouchání**a **správa** pro jednu konkrétní entitu.

3.  **Vlastní identity služeb se složitými pravidly**. Velmi málo zákazníků má složité sady pravidel, ve kterých jsou externě vydané tokeny mapovány na práva v přenosu nebo kde je jedné identitě služby přiřazena diferencovaná práva na několika cestách oboru názvů prostřednictvím více pravidel.

Potřebujete-li pomoc s migrací složitých sad pravidel, můžete kontaktovat [podporu Azure](https://azure.microsoft.com/support/options/). Další dva scénáře umožňují přímou migraci.

### <a name="unchanged-defaults"></a>Nezměněné výchozí hodnoty

Pokud vaše aplikace nezměnila výchozí hodnoty služby ACS, můžete nahradit veškeré použití [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objektem [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) a použít obor názvů předkonfigurovaný **RootManageSharedAccessKey** namísto účtu **vlastníka** služby ACS. Všimněte si, že i s účtem **vlastníka** služby ACS tato konfigurace nebyla (a stále je) obecně doporučena, protože tento účet nebo pravidlo poskytuje úplnou autoritu pro správu nad oborem názvů, včetně oprávnění k odstranění všech entit.

### <a name="simple-rules"></a>Jednoduchá pravidla

Pokud aplikace používá vlastní identity služby s jednoduchými pravidly, migrace je jednoduchá v případě, kdy byla vytvořena identita služby ACS, která poskytuje řízení přístupu v konkrétním přenosu. Tento scénář je často případ v řešení stylu SaaS, kde každé relé se používá jako most do lokality klienta nebo pobočky a identita služby je vytvořen pro tuto konkrétní lokalitu. V takovém případě lze příslušnou identitu služby migrovat do pravidla sdíleného přístupového podpisu přímo v přenosu. Název identity služby se může stát názvem pravidla SAS a klíč identity služby se může stát klíčem pravidla SAS. Práva pravidla SAS jsou pak konfigurována jako rovnocenná příslušnému pravidlu acs pro entitu.

Tuto novou a dodatečnou konfiguraci SAS můžete umístit na libovolném existujícím oboru názvů, který je federován službou ACS, a migrace mimo službu ACS se následně provede pomocí [služby SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) namísto [sharedsecrettokenprovider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Obor názvů nemusí být odpojen ze služeb ACS.

### <a name="complex-rules"></a>Komplexní pravidla

Pravidla SAS nejsou určeny k účtům, ale jsou pojmenovány podpisové klíče přidružené k právům. Jako takové scénáře, ve kterých aplikace vytvoří mnoho identit služby a uděluje jim přístupová práva pro několik entit nebo celý obor názvů stále vyžadují zprostředkovatele vystavující tokeny. Pokyny pro takového zprostředkovatele můžete získat [kontaktováním podpory](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

Další informace o ověřování Azure Relay najdete v následujících tématech:

* [Ověřování a autorizace Azure Relay](relay-authentication-and-authorization.md)
* [Ověřování služby Service Bus pomocí sdílených přístupových podpisů](../service-bus-messaging/service-bus-sas.md)


