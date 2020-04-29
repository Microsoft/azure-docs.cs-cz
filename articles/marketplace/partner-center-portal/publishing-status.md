---
title: Podívejte se na stav publikování vaší nabídky na komerčním webu Marketplace.
description: Kontrola stavu kroků ověření, certifikace a verze Preview požadovaných k publikování nabídky prostřednictvím komerčního tržiště v partnerském centru Microsoftu.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275675"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Podívejte se na stav publikování vaší nabídky na komerčním webu Marketplace.

Aktuální **stav publikování** můžete zobrazit na kartě **Přehled nabídky** na [portálu komerčního webu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v partnerském centru.

U každé nabídky by se mělo zobrazit jeden z následujících indikátorů stavu.

| **Stav**    | **Popis**  |
| :---------- | :-------------------|
| **Koncept** | Nabídka se vytvořila, ale není publikovaná. |
| **Probíhá publikování.** | Nabídka/plán pracuje způsobem, který prochází postupem procesu publikování. |
| **Nutná pozornost** | Došlo ke zjištění kritického problému během certifikace od společnosti Microsoft nebo některého z kroků publikování. |
| **Tisk** | Nabídka byla certifikována společností Microsoft a nyní očekává konečné ověření vydavatelem. Výběrem možnosti přejít na Live zajistěte, aby byla nabídka živá. |
| **Živé** | Nabídka je na webu Marketplace živá a může ji zobrazit a získat zákazníci. |
| **Čeká na zastavení prodeje** | Vydavatel v nabídce nebo plánu vybral možnost ukončit prodej, ale akce ještě není dokončená. |
| **Není k dispozici na webu Marketplace** | Odebrali jsme dříve publikovanou nabídku nebo plán na webu Marketplace. |

## <a name="automated-validation"></a>Automatizované ověřování

Prvním krokem procesu publikování je sada automatizovaných ověření. Každý krok ověření odpovídá funkci, kterou jste zvolili k povolení při vytváření vaší nabídky. Pokud tato funkce není povolená, ověřování se přeskočí k dalšímu kroku publikování. Před schválením stavu publikování je nutné provést každou kontrolu ověření.

- **Nastavení toku nákupu nabídky (<10 min)**

V tomto kroku zajistíme splnění vaší nabídky při nákupu zákazníky prostřednictvím Azure Portal. Tento krok platí jenom pro nabídky prodávané prostřednictvím Microsoftu.

- **Ověření dat testovacích jednotek (~ 5 min.)**

V tomto kroku Ověřujeme data, která jste zadali v části technická konfigurace testovacích jednotek v této nabídce. Funkce testovacích jednotek je testována a schválena. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Zřizování testovacích jednotek (~ 30 min.)**

V tomto kroku nasadíme a provedeme po ověření dat a funkcí testovacích jednotek v předchozím kroku instance testovacího disku, aby byly připravené na použití pro zákazníky.  Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Ověření a registrace řízení vedoucího (<15 min)**

V tomto kroku potvrzujeme, že váš systém pro správu potenciálních zákazníků může obdržet zájemce na základě podrobností uvedených v nastavení nabídky. Tento krok platí jenom pro nabídky se zapnutou správou potenciálních zákazníků.

## <a name="certification"></a>Certifikace

Před publikováním musí být nabídky odeslané na komerční tržišti v partnerském centru certifikovány. Odeslané nabídky prošly přísným testováním, některými automatizovanými a jinými ručními, včetně kontroly nad [Azure Marketplacemi zásadami certifikace](https://docs.microsoft.com/legal/marketplace/general-policies). Podání nabídky musí být označeno jako opravňující k certifikaci, aby bylo možné pokračovat k dalšímu kroku v publikačním toku.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy ověřování, které probíhají během certifikace

Existují tři úrovně ověřování zahrnuté v procesu certifikace pro každou podanou nabídku.

- Způsobilost pro firmy vydavatele
- Ověření obsahu
- Technické ověření

#### <a name="publisher-business-eligibility"></a>Způsobilost pro firmy vydavatele

Každý typ nabídky kontroluje sadu základních kritérií způsobilosti, které musí vydavatel splňovat. Kritéria způsobilosti můžou zahrnovat stav programu MPN vydavatele, uchovávané kompetence, úrovně kompetence atd.

#### <a name="content-validation"></a>Ověření obsahu

Při ověřování obsahu jsou informace, které jste zadali při vytváření vaší nabídky, kontrolovány na kvalitu a relevanci. Tyto kontroly prověří vaše položky s podrobnostmi o seznamech na Marketplace, cenách, dostupnosti, přidružených plánech atd. Pro splnění kritérií pro výpis Azure Marketplace a/nebo AppSource ověříme, že vaše nabídka zahrnuje:

- název, který přesně popisuje nabídku;
- jasně psané popisy, které poskytují důkladný přehled a změnu hodnoty;
- snímky obrazovky kvality a doprovodná videa; ani
- Vysvětlení způsobu, jakým nabídka využívá platformy a nástroje společnosti Microsoft.

Další informace o kritériích ověřování obsahu najdete v tématu [Obecné zásady výpisu](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Technické ověření

Během technického ověřování dojde k tomu, že nabídka (balíček nebo binární) projde následující kontroly.
- Prověřený malware
- Monitorovaná síťová volání
- Analyzovaný balíček
- Důkladné prohledání aktuálních funkcí nabídky

Nabídka je testována na různých platformách a verzích, aby bylo zajištěno jejich robustní.

Zkontrolujte konkrétní konfigurační údaje požadované pro vaši nabídku v části technická konfigurace tohoto dokumentu.

### <a name="certification-failure-report"></a>Sestava o chybách certifikace

Po dokončení kontroly se v případě, že vaše nabídka prošla certifikací, přesune na další krok v procesu publikování. Pokud se vaší nabídce nezdařila žádná z těchto kontrol seznamů, technických nebo zásad nebo pokud nemáte nárok na odeslání nabídky tohoto typu, je generována zpráva o selhání certifikace a e-mailem.

Tato sestava obsahuje popisy všech zásad, které se nezdařily, spolu s revizními zprávami. Přečtěte si tuto e-mailovou sestavu, vyřešte případné problémy, proveďte v případě potřeby aktualizace nabídky a znovu odešlete nabídku pomocí [komerčního portálu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v partnerském centru. (Nabídku můžete znovu odeslat tolikrát, kolikrát je potřeba, dokud neprojdete certifikátem).

## <a name="preview-creation"></a>Vytváření náhledu

Během kroku **vytvoření verze Preview** vytvoříme verzi nabídky, která je dostupná jenom pro cílovou skupinu, kterou jste zadali v části verze Preview vaší nabídky.

>[!Note]
> Nepoužívejte tento krok k tomu, aby uživatelé mimo vaši organizaci měli přehled o nabídce. Místo toho použijte možnost **soukromé nabídky** . V tuto chvíli nebyla vaše nabídka plně testována a ověřena a není připravena k externí distribuci.

## <a name="publisher-approval"></a>Schválení vydavatele

V tomto kroku se vám pošle e-mail s žádostí o kontrolu a schválení vaší nabídky Preview před konečným krokem publikování.

Pokud jste si zvolili prodej vaší nabídky prostřednictvím Microsoftu, budete moct otestovat navýšení a nasazení vaší nabídky, abyste měli jistotu, že vyhovují vašim požadavkům v této fázi schvalování verze Preview. Vaše nabídka nebude zatím dostupná na veřejném webu Marketplace. Jakmile otestujete a schválíte tuto verzi Preview, budete muset na řídicím panelu [**Přehled nabídek**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) vybrat možnost **Přejít na aktivní** .

Pokud chcete v této fázi Preview provádět změny nabídky, můžete je upravit a znovu odeslat a publikovat novou verzi Preview. Podrobnosti o dalších změnách najdete v článku [aktualizace stávajících nabídek Marketplace](#update-existing-marketplace-offers) .

Pokud je vaše nabídka už živá a dostupná pro veřejnost na webu Marketplace, jakékoli aktualizace, které provedete, se neprojeví, dokud na řídicím panelu [**Přehled nabídky**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nevyberete možnost **Přejít na aktivní** .

### <a name="publish-offer-to-the-public"></a>Publikovat nabídku veřejné

Přihlaste se k partnerskému centru a získejte přístup k této nabídce. Budete přesměrováni na stránku s **přehledem nabídky** . V horní části této stránky se zobrazí možnost **začít živě**. Vyberte **Přejít do provozu** a po potvrzení se nabídka začne publikovat na veřejném. Až bude nabídka živá, dostanete e-mailové oznámení.

## <a name="publish"></a>Publikování

Teď, když jste vybrali možnost **začít živě** s vaší nabídkou a zpřístupnit ji na webu Marketplace, máte k dispozici řadu konečných kontrol ověření, které se budou zvýšit, aby se zajistilo, že je živá nabídka nakonfigurovaná stejně jako verze Preview nabídky.

- **Nastavení toku nákupu nabídky (>10 min)**

V tomto kroku zajistíme splnění vaší nabídky při nákupu zákazníky prostřednictvím Azure Portal. Tento krok platí jenom pro nabídky prodávané prostřednictvím Microsoftu.

- **Ověření dat testovacích jednotek (~ 5 min.)**

V tomto kroku Ověřujeme data, která jste zadali v části technická konfigurace testovacích jednotek v této nabídce. Funkce testovacích jednotek je testována a schválena. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Zřizování testovacích jednotek (~ 30 min.)**

V tomto kroku nasadíme a replikujte instance testovacích jednotek, aby byly připravené k použití na zákazníky.  Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Ověření a registrace řízení vedoucího (>15 min)**

V tomto kroku potvrzujeme, že váš systém pro správu potenciálních zákazníků může obdržet zájemce na základě podrobností uvedených v nastavení nabídky. Tento krok platí jenom pro nabídky se zapnutou správou potenciálních zákazníků.

- **Finální publikování (>30 minut)**

V tomto kroku zajistíme, že vaše nabídka bude veřejně dostupná na webu Marketplace.

## <a name="update-existing-marketplace-offers"></a>Aktualizace stávajících nabídek Marketplace

Pokud chcete provést změny v nabídce, kterou jste už publikovali, musíte nejdřív aktualizovat existující nabídku a pak ji znovu publikovat.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
