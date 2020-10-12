---
title: Kontrola a publikování nabídky na komerčním webu Microsoft Marketplace
description: Použijte Partnerské centrum k odeslání nabídky do verze Preview, zobrazení náhledu vaší nabídky a její následné publikování na komerčním webu Microsoft Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: eff1db3aca2cab77d18698634b84e8e1b1c99f7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375872"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Kontrola a publikování nabídky na komerčním webu Marketplace

V tomto článku se dozvíte, jak pomocí partnerského centra Odeslat vaši nabídku k publikování, zobrazit náhled vaší nabídky a potom ji publikovat na komerčním webu Marketplace. Také pokryjeme, jak kontrolovat stav publikování během postupu publikování. Musíte již mít vytvořenou nabídku, kterou chcete publikovat.

## <a name="offer-status"></a>Stav nabídky

Svůj stav nabídky si můžete prohlédnout na kartě **Přehled** na řídicím panelu komerčního tržiště v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Jeden z následujících indikátorů stavu se zobrazí ve sloupci **stav** u každé nabídky.

| Status | Popis |
| ------------ | ------------- |
| Koncept | Nabídka se vytvořila, ale není publikovaná. |
| Probíhá publikování. | Nabídka pracuje způsobem prostřednictvím procesu publikování. |
| Nutná pozornost | Zjistili jsme kritický problém během certifikace nebo v jiné fázi publikování. |
| Preview | Tuto nabídku jsme zaregistrovali, která teď čeká na konečné ověření vydavatelem. Pokud chcete publikovat nabídku živě, vyberte **Přejít na Live** . |
| Živě | Nabídka je na webu Marketplace živá a může ji zobrazit a získat zákazníci. |
| Čeká na zastavení prodeje | Vydavatel v nabídce nebo plánu vybral možnost ukončit prodej, ale akce ještě není dokončená. |
| Není k dispozici na webu Marketplace | Odebrali jsme dřív publikovanou nabídku na webu Marketplace. |
|||

## <a name="validation-and-publishing-steps"></a>Postup ověření a publikování

Až budete připraveni odeslat nabídku k publikování, vyberte **zkontrolovat a publikovat** v pravém horním rohu portálu. Stránka **Kontrola a publikování** zobrazuje stav každé stránky pro vaši nabídku, což může být jedna z následujících:

   - **Nespuštěno** – stránka je neúplná.
   - **Nedokončeno** – stránka neobsahuje požadované informace nebo obsahuje chyby, které je třeba opravit. Budete se muset vrátit na stránku a aktualizovat ji.
   - **Dokončit** – stránka je dokončená. Byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby.

Pokud má kterákoli z stránek stav jiný než **úplný**, je třeba opravit problém na této stránce a pak se vrátit na stránku **Kontrola a publikování** a potvrdit stav nyní zobrazuje jako **dokončený**. Některé typy nabídek vyžadují testování. V takovém případě se zobrazí **poznámky k poli certifikace** , kde potřebujete poskytnout pokyny pro testování certifikačního týmu a všechny doplňkové poznámky užitečné pro porozumění vaší aplikaci.

Po dokončení všech stránek a zadání použitelných zkušebních poznámek vyberte **publikovat** a zahajte tak procesy ověřování a publikování. Fáze a celková sekvence se můžou lišit v závislosti na typu nabídky, kterou publikujete. Následující tabulka uvádí jeden možný postup publikování. Jednotlivé fáze jsou podrobněji vysvětleny v následujících oddílech.

| Fáze | Změna |
| ------------ | ------------- | ------------- |
| [Automatizované ověřování](#automated-validation-phase) | Zpracováváme sadu automatizovaných ověření. |
| [Certifikace](#certification-phase) | Provádíme Ruční ověřování. |
| [Vytváření náhledu](#preview-creation-phase) | Stránka výpisu pro vaši nabídku Preview je k dispozici všem uživatelům, kteří mají odkaz ve verzi Preview. Pokud bude vaše nabídka prodávána prostřednictvím Microsoft (s podporou transakcí), může koupit a získat přístup k této nabídce pro účely testování jenom ta cílová skupina, kterou jste zadali na stránce **verze Preview** vaší nabídky. |
| [Odhlášení vydavatele](#publisher-sign-off-phase) | Pošleme vám e-mail s žádostí o náhled a schválení vaší nabídky. |
| [Publikovat](#publish-phase) | Spuštěním série kroků ověříte, že nabídka Preview je publikovaná živě na komerčním webu Marketplace. |
|||

## <a name="automated-validation-phase"></a>Fáze automatizovaného ověřování

Prvním krokem procesu publikování je sada automatizovaných ověření. Každý krok ověření odpovídá funkci, kterou jste zvolili při vytváření nabídky. Před přechodem nabídky k dalšímu kroku v procesu publikování musí být každá kontrola ověření dokončena.

- **Nastavení toku nákupu nabídky** (<10 min)

   Zajišťujeme, že vaše nabídka může být splněna, když je zákazníci koupili prostřednictvím Azure Portal. Tento krok platí jenom pro nabídky prodávané prostřednictvím Microsoftu.

- **Ověření dat testovacích jednotek** (~ 5 min.)

   Data, která jste zadali na stránce technické konfigurace nabídky, Ověřujeme. Otestujeme a schválíme funkce testovacích jednotek. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Zřizování testovacích jednotek** (~ 30 min.)

    Po ověření dat a funkcí testovacích jednotek v předchozím kroku nasadíme a replikujte instance testovacích jednotek, aby byly připravené k použití na zákazníky. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Ověření a registrace řízení vedoucího** (<15 min)

    Potvrzujeme, že váš systém pro správu potenciálních zákazníků může obdržet zájemce na základě podrobných údajů, které jste zadali na stránce pro **nastavení nabídek** . Tento krok platí jenom pro nabídky se zapnutou správou potenciálních zákazníků.

## <a name="certification-phase"></a>Fáze certifikace

Nabídky odeslané na komerční tržišti musí být před publikováním certifikovány. Nabídky jsou přísně testovány, některé automatizované a jiné ruční. Další informace najdete v tématu [zásady certifikace pro komerční tržiště](https://aka.ms/commercial-marketplace-certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy ověřování, které probíhají během certifikace

Existují tři úrovně ověřování zahrnuté v procesu certifikace pro každou odeslanou nabídku.

- Způsobilost pro firmy vydavatele
- Ověření obsahu
- Technické ověření

#### <a name="publisher-business-eligibility"></a>Způsobilost pro firmy vydavatele

Každý typ nabídky zkontroluje sadu požadovaných kritérií základní podmínky. Tato kritéria můžou zahrnovat stav vydavatele programu MPN, uchovávané kompetence, úrovně kompetence atd.

#### <a name="content-validation"></a>Ověření obsahu

Informace, které jste zadali při vytváření vaší nabídky, jsou zkontrolovány z hlediska kvality a relevance. Tyto kontroly prověří vaše položky s podrobnostmi o seznamech na Marketplace, cenách, dostupnosti, přidružených plánech atd. Abychom splnili kritéria pro výpis Microsoft AppSource a Azure Marketplace, ověříme, že vaše nabídka zahrnuje:

- Název, který přesně popisuje nabídku
- Jasně psané popisy, které poskytují důkladný přehled a polohu hodnoty
- Snímky obrazovky a videa kvality
- Vysvětlení způsobu, jakým nabídka využívá platformy a nástroje společnosti Microsoft.

Další informace o kritériích ověřování obsahu najdete v tématu [Obecné zásady výpisu](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Technické ověření

Během technického ověřování dojde k tomu, že nabídka (balíček nebo binární) projde následující kontroly.

- Prověřený malware
- Monitorovaná síťová volání
- Analyzovaný balíček
- Důkladné prohledání funkce nabídky

Nabídka je testována na různých platformách a verzích, aby bylo zajištěno jejich robustní.

### <a name="certification-failure-report"></a>Sestava o chybách certifikace

Pokud vaše nabídka neselže u žádného ze seznamů, technických nebo zásad nebo pokud nemáte nárok na odeslání nabídky tohoto typu, pošleme vám zprávu o selhání certifikace.

Tato sestava obsahuje popisy všech zásad, které se nezdařily, spolu s revizními zprávami. Přečtěte si tuto e-mailovou sestavu, vyřešte všechny problémy, v případě potřeby proveďte aktualizace svojí nabídky a znovu odešlete nabídku pomocí [komerčního portálu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v partnerském centru. Nabídku můžete znovu odeslat tolikrát, kolikrát je potřeba, dokud neprojdete certifikaci.

## <a name="preview-creation-phase"></a>Fáze vytváření verze Preview

Během fáze vytváření verze Preview vytvoříme verzi vaší nabídky, která bude dostupná jenom pro cílovou skupinu, kterou jste zadali na stránce **Preview pro cílovou skupinu** vaší nabídky, pokud nějaké máte. Verze Preview vaší nabídky nebude k dispozici nikomu mimo cílovou skupinu Preview, dokud nepublikujete nabídku živě.

> [!NOTE]
> Nepoužívejte cílovou skupinu Preview k tomu, aby uživatelé mimo vaši organizaci mohli zobrazit nabídku. Místo toho použijte možnost soukromé nabídky. V tuto chvíli nebyla vaše nabídka plně testována a ověřena a není připravena k externí distribuci.

## <a name="publisher-sign-off-phase"></a>Fáze pro odhlášení vydavatele

Když je nabídka připravená na kontrolu a její odhlášení, pošleme vám e-mail s žádostí o kontrolu a schválení vaší nabídky Preview. Stránku s **přehledem nabídky** můžete také aktualizovat v prohlížeči, abyste viděli, jestli vaše nabídka dosáhla fáze pro odhlášení vydavatele. Pokud má, budou k dispozici tlačítka **Přejít živě** a náhled.

Na následujícím snímku obrazovky vidíte stránku s **přehledem nabídky** pro SaaS nabídku. Postup ověření, který se zobrazí na této stránce, se liší v závislosti na typu nabídky a výběrech, které jste provedli při vytváření této nabídky.

![Popisuje stránku s přehledem nabídky pro nabídku v partnerském centru. Zobrazí se odkazy na tlačítko Přejít živě a náhled.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Zobrazení náhledu a schválení nabídky

> [!IMPORTANT]
> Pokud chcete ověřit kompletní tok nákupu a nastavování, kupte si nabídku, i když je ve verzi Preview. Nejdřív od Microsoftu upozorněte [lístek podpory](https://aka.ms/marketplacesupport) , abyste se ujistili, že Nezpracováváme poplatky.

Na stránce **Přehled nabídky** se zobrazí odkazy ve verzi Preview pod tlačítkem **Přejít na Live** . V závislosti na možnostech, které jste zvolili při vytváření vaší nabídky, se bude jednat o odkaz buď na AppSource Preview Azure Marketplace, ve verzi Preview, nebo v obou. Pokud jste se rozhodli prodat nabídku prostřednictvím Microsoftu, může kdokoli, kdo byl přidán do cílové skupiny Preview, otestovat pořízení a nasazení vaší nabídky, aby se zajistilo, že splní vaše požadavky v této fázi.

Po schválení verze Preview vyberte možnost **Přejít na Live** a publikujte nabídku živě na komerčním webu Marketplace. 

Chcete-li provést změny po zobrazení náhledu nabídky, můžete upravit a znovu odeslat žádost o publikování. Pokud je vaše nabídka už živá a dostupná pro veřejnost na webu Marketplace, jakékoli aktualizace, které provedete, se neprojeví, dokud nevyberete možnost **jít živě*. Další informace najdete v tématu [aktualizace stávající nabídky na komerčním webu Marketplace](./partner-center-portal/update-existing-offer.md) .

## <a name="publish-phase"></a>Fáze publikování

Teď, když jste se rozhodli začít pracovat s vaší nabídkou, která ji zpřístupňuje na komerčním webu Marketplace, provedeme řadu konečných kontrol ověření, abyste zajistili, že je živá nabídka nakonfigurovaná stejně jako verze Preview nabídky.

- **Nastavení toku nákupu nabídky** (>10 min)

    Zajišťujeme, že vaše nabídka může být splněna, když je zákazníci koupili prostřednictvím Azure Portal. Tento krok platí jenom pro nabídky prodávané prostřednictvím Microsoftu.

- **Ověření dat testovacích jednotek** (~ 5 min.)

    Data, která jste zadali na stránce technické konfigurace nabídky, Ověřujeme. Otestujeme a schválíme funkce testovacích jednotek. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Zřizování testovacích jednotek** (~ 30 min.)

    Nasadíme a replikují instance testovacích jednotek, aby byly připravené k použití na zákazníky. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

- **Ověření a registrace řízení vedoucího** (>15 min)

    Potvrzujeme, že váš systém pro správu potenciálních zákazníků může obdržet zájemce na základě podrobností uvedených na stránce s **nastavením vaší nabídky** . Tento krok platí jenom pro nabídky se zapnutou správou potenciálních zákazníků.

- **Finální publikování (>30 minut)**

    Zajistíme, že vaše nabídka bude veřejně dostupná na webu Marketplace.

Po dokončení těchto kontrol budou vaše nabídka na webu Marketplace živá.

## <a name="next-steps"></a>Další kroky

[Přístup k analytickým sestavám pro komerční tržiště v partnerském centru](partner-center-portal/analytics.md)
