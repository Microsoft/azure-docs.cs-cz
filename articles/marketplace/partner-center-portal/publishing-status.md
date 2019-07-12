---
title: Zkontrolujte stav publikování vaší nabídky komerční Marketplace
description: Zkontrolujte stav ověření, certifikace a náhled kroků potřebných pro publikování nabídky prostřednictvím webu Marketplace a obchodní v Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ae4840877ae461244a76984ed8ac3edc82d57a38
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653986"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Zkontrolujte stav publikování vaší nabídky komerční Marketplace

Můžete zobrazit vaše aktuální **stav publikování** na **nabízí přehled** karty [portálu komerční Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v partnerském centru.

Pro každou nabídku musí zobrazí jedna z následujících indikátory stavu.

| **Stav**    | **Popis**  |
| :---------- | :-------------------|
| **Návrh** | Nabídka se vytvořila, ale není zveřejněná. |
| **Probíhá publikování** | Plán/nabídky pracuje cestě provede kroky procesu publikování. |
| **Nutná pozornost** | Kritický problém byl zjištěn během certifikace společnosti Microsoft nebo publikování kroků. |
| **Preview** | Nabídka byla certifikovat společností Microsoft a nyní čeká na finální ověření vydavatelem. Vyberte vysílat živě, aby nabídka live. |
| **Živé** | Nabídka je v provozu na webu Marketplace a vidí a můžou zákazníci získat. |
| **Čeká na ukončení prodej** | Vydavatel vybraných "zastavit zákazník" na nabídku nebo plán, ale akce nebyla dosud dokončena. |
| **Není k dispozici na webu Marketplace** | Byl odebrán dříve publikované nabídky nebo plán na webu Marketplace. |

## <a name="automated-validation"></a>Automatické ověření

Prvním krokem v procesu publikování je sada automatické ověření. Každého kroku ověření odpovídá funkci, kterou jste se rozhodli povolit při vytváření vaší nabídky. Pokud tato funkce není povolená, přeskočí ověření pokračovat dalším krokem publikování. Každé ověření je třeba provést předtím, než je schválený stav publikování.

- **Nabízejí nastavení toku nákupu (< 10 minut)**

V tomto kroku zajistíme, že vaše nabídka může být splněn jste si koupili zákazníků prostřednictvím webu Azure portal. Tento krok platí jenom pro nabídek prodávaných prostřednictvím společnosti Microsoft.

- **Test jednotky ověření dat (přibližně 5 minut)**

V tomto kroku jsme ověřit data, které jste zadali v testovací verze technické konfigurační oddíl nabídky. Funkce testu jednotky jsou testovány a schválena. Tento krok platí pouze pro nabídky s si testovací jízdu povolena.

- **Test jednotky zřizování (~ 30 min)**

V tomto kroku po ověření dat a funkce vaše testovací verze v předchozím kroku můžeme nasadit a replikovat instance vaše testovací verze tak, aby byly připraveny pro používání zákazníka.  Tento krok platí pouze pro nabídky s si testovací jízdu povolena.

- **Vedoucí řízení ověřování a registraci (< 15 min)**

V tomto kroku potvrdíme, že váš systém správy potenciálních zákazníků může přijímat potenciálních zákazníků na základě informací uvedených v nabídce nastavení. Tento krok platí pouze pro nabídky s povolenou správou potenciálních zákazníků.

## <a name="certification"></a>Certifikace

Před publikováním, musí mít certifikovanou nabídky odeslané do komerčních Marketplace v partnerském centru. Odeslání přísnými testy, některé automatizované a manuální, včetně kontroly před ostatními této oblasti podstupovali nabídky [zásady certifikaci Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Nabízejí odesílání musí být označen oprávnění k certifikaci předtím, než se pokračovat k dalšímu kroku v publikování toku.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy ověřování, které provedou během certifikace

Existují tři úrovně ověření součástí procesu certifikace pro každou nabídku odeslání.

- Vydavatel obchodní způsobilosti
- Ověření obsahu
- Technické ověření

#### <a name="publisher-business-eligibility"></a>Vydavatel obchodní způsobilosti

Každý typ nabídky zkontroluje sadu základní kritéria, které musí splnit vydavatele. Kritéria mohou zahrnovat stav programu MPN vydavatele, schopností nachází, úrovně kompetencí, atd.

#### <a name="content-validation"></a>Ověření obsahu

Během ověřování obsahu zkontrolují informace zadá, když jste vytvořili vaši nabídku kvalitu a spolehlivost. Tyto kontroly se zkontrolujte zadané pro marketplace podrobnosti, ceny, dostupnost, přidružené plány atd. Abyste splnili Azure Marketplace nebo AppSource výpis kritéria, ověříme, že vaše nabídka zahrnuje:

- název, který přesně popisuje nabídky;
- kvalitně napsané popisy, které poskytují důkladný přehled a návrh hodnoty;
- kvalita snímků obrazovky a doprovodných videa; a
- vysvětlení, jak tuto nabídku využívá platformy Microsoft a nástrojů.

Další informace týkající se obsahu ověřovací kritéria načtením [obecné výpis zásady](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general-policies).

#### <a name="technical-validation"></a>Technické ověření

Nabídka (balíček nebo binární) během technické ověření projde následující kontroly.
- Zkontrolovat malware
- Volání sítě monitorovat
- Balíček analyzován
- Důkladné prohledání skutečné funkce nabídky

Nabídky je testován napříč různými platformami a verze, aby se zajistilo, že je robustní.

Projděte si podrobnosti o konkrétní konfiguraci požadované pro vaší nabídky v části technických konfigurace tohoto dokumentu.

### <a name="certification-failure-report"></a>Sestava selhání certifikace

Po dokončení kontroly Pokud vaše nabídka uplynutí certifikační pak přesunu k dalšímu kroku v procesu publikování. V případě neúspěchu nabídkou na některý z kontroly výpis, technických nebo zásad nebo pokud nejste oprávněni předložit nabídku typu, je sestava selhání certifikační generována a pošle vám e-mailem.

Tato sestava obsahuje popis všech zásad, které se nepodařilo spolu s poznámky ke kontrole. Projděte si tuto sestavu e-mailu, řešit jakékoli problémy, provedení aktualizací pro vaši nabídku v případě potřeby a odešlete znovu nabídkou pomocí [portálu komerční Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v partnerském centru. (Můžete neúspěšné nabídka tolikrát, kolikrát podle potřeby až do certifikace předávání).

## <a name="preview-creation"></a>Vytvoření náhledu

Během **ve verzi Preview vytváření** krok, můžeme vytvořit verzi této nabídky pouze cílové skupině, která jste vytvořili v části Preview o vaší nabídce.

## <a name="publisher-approval"></a>Vydavatel schválení

V tomto kroku se dostat se žádostí o můžete zkontrolovat a schválit ve verzi preview nabídku před posledním krokem publikování.

Pokud jste vybrali na prodej vaší nabídky Microsoftu, bude možné otestovat pořízení a nasazení vaší nabídce, ujistěte se, že splňují vaše požadavky během této fáze schválení ve verzi preview. Vaše nabídka ještě nebudou k dispozici stydké webu Marketplace. Testování a schválit v této verzi preview, bude nutné vybrat **pro uvedení do provozu** na [ **nabízí přehled** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) řídicího panelu.

Pokud chcete provést změny na nabídku během této fáze ve verzi preview, můžete upravit a opětovného odesílání k publikování nové verze preview. Přečtěte si článek [aktualizaci existující marketplace nabízí](#update-existing-marketplace-offers) podrobnosti o další změny.

Pokud už vaši nabídku za provozu a dostupná na webu Marketplace, všech aktualizací provedete nebude publikování neprojeví, dokud nevyberete **pro uvedení do provozu** na [ **nabízí přehled** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) řídicí panel.

### <a name="publish-offer-to-the-public"></a>Publikování nabídky pro veřejnost

Přihlaste se do partnerského centra a přejít na nabídku. Budete přesměrováni na **nabízí přehled** stránky. V horní části této stránky, zobrazí se možnost pro **vysílat živě**. Vyberte **vysílat živě,** a po potvrzení, tuto nabídku start, získávání publikována veřejně. Obdržíte oznámení e-mailem při nabídky je v provozu.

## <a name="publish"></a>Publikování

Teď, když jste se rozhodli **vysílat živě** v rámci vaší nabídky, ji dáte k dispozici na webu Marketplace, jsou řadu kontrol finálním ověřením, které budou prošli zajistit konfiguraci se, že živé nabídka stejně jako verze preview verze nabídky.

- **Nabízejí nastavení toku nákupu (> 10 minut)**

V tomto kroku zajistíme, že vaše nabídka může být splněn jste si koupili zákazníků prostřednictvím webu Azure portal. Tento krok platí jenom pro nabídek prodávaných prostřednictvím společnosti Microsoft.

- **Test jednotky ověření dat (přibližně 5 minut)**

V tomto kroku jsme ověřit data, které jste zadali v testovací verze technické konfigurační oddíl nabídky. Funkce testu jednotky jsou testovány a schválena. Tento krok platí pouze pro nabídky s si testovací jízdu povolena.

- **Test jednotky zřizování (~ 30 min)**

V tomto kroku jsme nasazení a replikovat instance vaše testovací verze tak, aby byly připraveny pro používání zákazníka.  Tento krok platí pouze pro nabídky s si testovací jízdu povolena.

- **Vedoucí řízení ověřování a registraci (> 15 minut)**

V tomto kroku potvrdíme, že váš systém správy potenciálních zákazníků může přijímat potenciálních zákazníků na základě informací uvedených v nabídce nastavení. Tento krok platí pouze pro nabídky s povolenou správou potenciálních zákazníků.

- **Poslední publikování (> 30 minut)**

V tomto kroku zajistíme, že vaše nabídka se stanou veřejně dostupné na webu Marketplace.

## <a name="update-existing-marketplace-offers"></a>Aktualizace stávající nabídky na webu marketplace

Pokud chcete provést změny na nabídku, kterou jste už publikovali, musíte nejprve aktualizovat stávající nabídky a pak je znovu publikovat.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na webu Marketplace obchodní](./update-existing-offer.md)
