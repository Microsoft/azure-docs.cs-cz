---
title: Názorný postup integrace datového centra Azure Stack | Dokumentace Microsoftu
description: Zjistěte, co můžete očekávat úspěšné místního nasazení služby Azure Stack ve vašem datovém centru.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.openlocfilehash: 554fd9c32c9831d45a40c62a871e3a8d5f8d7cb9
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191747"
---
# <a name="azure-stack-datacenter-integration"></a>Integrace datových center Azure Stack

Tento článek popisuje uživatelské prostředí Azure Stack začátku do konce koupili prostřednictvím integrované řešení pro úspěšné nasazení na místě od poskytovatele řešení. Tyto informace slouží k usnadnění vaší cestě a chcete pomoct vytvořit očekávání toho, co, jako zákazník Azure Stack očekávat při integraci služby Azure Stack do vašeho datacentra.

Jako zákazník Azure Stack by měl očekáváte, že tyto fáze integrace datacenter:

|     |Fáze plánování|Objednávky procesů|Před nasazením|Objekt pro vytváření procesu|Doručování hardwaru|Místní nasazení|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Kontaktovat partnera poskytovat předprodejní podporu.|Příprava licencování softwaru a smlouvách podle potřeby.|Zadejte požadované nástroje ke shromažďování požadavky na integraci datacenter a dokumentace pro zákazníka.|Poskytovat nejnovější základní sestavení a nástroje řetězu aktualizace měsíční čím dál.|neuvedeno|Pracovníci podpory společnosti Microsoft vám pomůže s problémy nasazení.|
|**Partner**|Doporučujeme možnosti řešení na základě požadavků zákazníků.<br><br>Navrhnout testování konceptu (POC) v případě potřeby.<br><br>Vytvořte obchodní vztah.<br><br>Při rozhodování o úroveň podpory.|Příprava nezbytné smlouvy u zákazníka.<br><br>Vytvořte nákupní objednávky zákazníka.<br><br>Při rozhodování o doručení časové osy.<br><br>Připojte zákazník s Microsoftem v případě potřeby.|Poskytněte nezbytné školení pro zvýšení zajištění možnosti integrace přehled o všech požadavcích pro nasazení a datového centra zákazníka.<br><br>Zákazníkovi pomohli s ověření shromážděná data k zajištění úplnost a přesnosti.|Použijte poslední sestavení ověřené směrného plánu.<br><br>Použijte požadované sady Microsoft deployment toolkit.|Dodávejte hardwaru na webu zákazníka.|Zpracovat pracovník místní nasazení.<br><br>Rackem a skříní zásobníku.<br><br>Nasazení hostitele (HLH) životního cyklu hardwaru.<br><br>Nasazení Azure Stack.<br><br>Můžete předat do odběratele.|
|**Zákazníka**|Popsat zamýšlené použití případy a určete požadavky.|Určení model fakturace můžete použít, kontrolovat a schvalovat smluv.|Dokončení nasazení listu a ujistěte se, že všechny požadavky na nasazení jsou splněny a je připravený k nasazení.|neuvedeno|Příprava datacentra tím, že zajišťuje všechny požadované power a chlazením, ohraničení připojení a další požadavek integrace vyžaduje datacenter jsou na místě.|Být k dispozici během nasazení zadat přihlašovací údaje předplatného a podpoře nějaké otázky pro zadaná data.|
| | | | | | | |


## <a name="planning-phase"></a>Fáze plánování
Fáze plánování při Microsoftu nebo partnera řešení Azure Stack, bude spolupracovat s vámi na vyhodnocení a o vašich potřebách k určení, pokud je to pravé řešení pro vás Azure Stack:

Že vám pomůže rozhodnout o následujícím:

-   Azure Stack je to pravé řešení pro vaši organizaci?

-   Řešení jaké velikosti budete potřebovat?

-   Jaký typ modelu fakturace a licencování bude fungovat pro vaši organizaci?

-   Co jsou napájení a chladicí požadavky?

K zajištění, že jako hardwarové řešení. nejlépe odpovídají vašim potřebám, [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) lze pomoci při plánování k určení příslušné kapacity a konfigurace pro váš hardware Azure Stack předběžného nákupu řešení.

Je tabulka *není* určena pro použití jako náhradu pro zkoumání a analýzy hardwarových řešení, že nejlepší, aby odpovídala vašim potřebám. Při plánování nasazení služby Azure Stack, které byste si rovněž projít [důležité informace o integraci obecné datacenter](azure-stack-datacenter-integration.md) pro službu Azure Stack integrované systémy.

## <a name="order-process-phase"></a>Fáze procesu pořadí
V této fázi řadu vaše dotazy s ohledem na proveditelnosti by byly zodpovězeny. Teď, když jste připraveni potvrdit při nákupu Azure Stack a po podepsání všech požadovaných smluv a nákupních objednávek, budete požádáni o zadání dat požadavky na integraci na poskytovatele řešení.

## <a name="pre-deployment-phase"></a>Před nasazením fáze
V této fázi musíte rozhodnout, jak integrovat do vašeho datového centra Azure Stack. Pro usnadnění tohoto procesu Microsoft ve spolupráci s poskytovateli řešení spojit požadavky na šablonu, která vám pomohou při shromažďování nezbytné informace k plánování pro nasazení integrovaného systému ve vašem prostředí.

[Důležité informace o integraci obecné datacenter](azure-stack-datacenter-integration.md) článek obsahuje informace, které vám pomohou dokončit šablony označované jako list nasazení. 

> [!IMPORTANT]
> Během této fáze je důležité, aby všechny předběžné informace, je prozkoumat a jste se rozhodli před řazení řešení. Mějte na paměti, že tento krok je časově náročné a vyžaduje koordinaci a shromažďování dat z více oborů v rámci vaší organizace. 

Ve fázi předběžného nasazení je potřeba rozhodnout o následujícím:

- **Azure Stack připojení modelu a poskytovatelem identity**. Můžete také nasadit Azure Stack buď [připojení k Internetu (a do Azure) nebo odpojení](azure-stack-connection-models.md). Nejvíce výhod získat z Azure Stack, hybridní scénáře, včetně byste k nasazení připojení k Azure. Výběr Active Directory Federation Services (AD FS) nebo Azure Active Directory (Azure AD) je jednorázový rozhodnutí, které je nutné provést v době nasazení. **Nedá se změnit později bez opětovného nasazení celé systému**.

- **Licenční model**. Možnosti licencování modelu můžete zvolit ze, závisí na typu nasazení, které máte. Zvoleného poskytovatele identity nemá žádný vliv na klientské virtuální počítače nebo systému identit a účty, které používají.
    - Zákazníci, kteří jsou v [odpojení nasazení](azure-stack-disconnected-deployment.md) mají jenom jedna možnost: založená na kapacitě fakturace.

    - Zákazníci, kteří jsou v [připojené nasazení](azure-stack-connected-deployment.md) můžete si vybrat mezi založená na kapacitě fakturace a plateb jako využití. Účtování podle kapacity vyžaduje předplatné Azure se smlouvou Enterprise (EA) pro registraci. To je potřeba k registraci, které poskytuje dostupnost položky na webu Marketplace prostřednictvím předplatného Azure.

- **Integrace sítě**. [Integrace sítě](azure-stack-network.md) je zásadní pro nasazení, operace a správa systémy Azure Stack. Existují třeba mít na paměti, které zajišťují řešení Azure Stack je odolné a vysoce dostupné fyzické infrastruktury pro podporu jeho operace.

- **Integrace s branou firewall**. Je doporučeno, kterou jste [používejte bránu firewall](azure-stack-firewall.md) usnadňují zabezpečení Azure stacku. Brány firewall může zabránit útoků DDOS, zjišťování neoprávněných vniknutí a kontrolu obsahu. Nicméně je třeba poznamenat, že může být kritickým bodem propustnost pro služby Azure storage.


- **Požadavků na certifikát**. Je velmi důležité, který všechny [požadovaných certifikátů](azure-stack-pki-certs.md) jsou k dispozici *předchozí* na místě programátor přicházejících u vašeho datového centra pro nasazení.

Po předběžné informace jsou shromažďovány prostřednictvím list nasazení, poskytovatele řešení se spustí řízený proces objekt pro vytváření z dat shromážděných pro zajištění úspěšné integrace služby Azure Stack do vašeho datacentra.

## <a name="hardware-delivery-phase"></a>Fáze doručování hardwaru
Váš poskytovatel řešení bude spolupracovat s vámi na plánování při řešení budou doručeny do vašeho zařízení. Po přijetí a místě, musíte naplánovat čas s daným poskytovatelem řešení a mít pracovník pocházet místní nasazení Azure Stack.

Je **zásadní** , že všechna data požadovaného je zamknuté a k dispozici *před místě inženýr dorazí k nasazení řešení*.

-   Všechny certifikáty si musíte koupit a připravené.

-   Název domény musí být rozhodnuto o.

-   Všechny parametry integrace sítě jsou dokončeny a odpovídá co sdílet se svým poskytovatelem řešení.

> [!TIP]
> Pokud některé z těchto informací došlo ke změně, ujistěte se, že změna komunikovat poskytovatele řešení před naplánovat nasazování ve skutečnosti.

## <a name="onsite-deployment-phase"></a>Fáze nasazení na místě
Nasazení Azure Stack, inženýr získáte od svého poskytovatele hardwaru řešení bude potřeba k dispozici, aby aktivovala nasazení. Aby se zajistilo úspěšné nasazení, ujistěte se, že nedošlo ke změně všech informací získaných prostřednictvím list nasazení. 

Toto je, co lze očekávat od pracovníka místní během nasazení prostředí:

- Zkontrolujte všechny kabelů a ohraničení připojení k zajištění řešení správně dohromady a vyhovuje vašim požadavkům
- Konfigurovat řešení HLH (hardwaru životního cyklu hostitelů)
- Zkontrolujte, ujistěte se, že se používají správná nastavení BMC, systému BIOS a sítě.
- Ujistěte se, že je firmware všechny součásti ve schválené verzi řešení
- Spustit nasazení

> [!NOTE]
> Postup nasazení pracovníkem místní dokončení, může trvat přibližně týden firmy.

## <a name="post-integration-phase"></a>Po integraci fáze
Partnera musí provést několik kroků před řešení je předáno zákazníkovi ve fázi po integraci. V této fázi ověřování je důležité zajistit, že je systém nasazen a provést správně. 

Akce, které by měl být partnerem OEM jsou:

-   [Spustit test azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [Registrace ve službě Azure](azure-stack-registration.md)

-   [Syndikace Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   Přepínač zálohování konfiguračních souborů

-   Odebrat DVM

-   Při přípravě souhrnu zákazník nasazení

-   [Zkontrolovat dostupnost aktualizací, abyste měli jistotu, že software řešení se aktualizuje na nejnovější verzi](azure-stack-updates.md)

Existuje několik kroků, které jsou povinné nebo volitelné v závislosti na typu instalace.

-   Pokud nasazení byla dokončena pomocí [služby AD FS](azure-stack-integrate-identity.md), pak Azure Stack razítko muset možné integrovat se službou zákazníka je vlastní služba AD FS.

  > [!NOTE]
  > Tento krok je zodpovědností zákazníků, i když k nabízení služeb k tomu volitelně partnera.

-   Integrace s existujícím monitorování systému od příslušného partnera.

    -   [Integrace nástroje System Center Operations Manager](azure-stack-integrate-monitor.md) také podporuje správu vozového parku funkce.

    -   [Integrace Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Celkové časové osy

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Podpora
Azure Stack umožňuje konzistentních s Azure, integrovaném prostředí podpory, který se vztahuje celý životní cyklus systému. Pro úplnou podporu integrované systémy Azure Stack, zákazníci potřebují dva kontrakty podpora; jeden s Microsoftem (nebo jejich Cloud Solution Provider) pro podporu služeb Azure a jeden u poskytovatele hardwaru pro podporu systému. Integrovaná podpora prostředí nabízí koordinovanou eskalaci a řešení, výsledkem je konzistentní prostředí podpory bez ohledu na to, které volají nejprve. Zákazníci, kteří už mají plán Premier Azure – Standard / součástí je podpora ProDirect nebo partnerem s Microsoftem, podpory softwaru Azure Stack.

Integrovaná podpora prostředí využívá Exchange případ mechanismus pro obousměrný přenos případy podpory a aktualizace případu mezi společnostmi Microsoft a partnerské hardwaru. Microsoft Azure Stack je popsaný [moderní životní cyklus zásad](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Další postup
Další informace o [důležité informace o integraci obecné datacenter](azure-stack-datacenter-integration.md).
