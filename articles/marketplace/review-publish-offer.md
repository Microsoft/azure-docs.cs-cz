---
title: Kontrola a publikování nabídky na komerčním webu Microsoft Marketplace
description: Použijte Partnerské centrum k odeslání nabídky do verze Preview, zobrazení náhledu vaší nabídky a její následné publikování na komerčním webu Microsoft Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: f8ddcbef6d2ce4a9cba625374db9908335954424
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630890"
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

- **Ověření nabídky** (<30 min)

    Pro SaaS nabízíme rychlé ověření obsahu a technických konfigurací. Vytváření náhledu nebude zablokováno při jakýchkoli potížích s ověřením zjištěných v tomto kroku. Zjistíte-li všechny problémy, které mohou selhat s certifikací, zobrazí se na stránce **Přehled nabídky** odkaz na **sestavu ověření zobrazení** . Sestava bude obsahovat popisy všech porušení zásad.

    > [!IMPORTANT]
    > Před výběrem tlačítka **Přejít na Live** nezapomeňte zkontrolovat a vyřešit všechna upozornění z kroku ověření nabídky. V opačném případě dojde k selhání certifikace, která by zpozdila vaši nabídku před živým zprovozněním.

## <a name="certification-phase"></a>Fáze certifikace

Nabídky odeslané na komerční tržišti musí být před publikováním certifikovány. Nabídky jsou přísně testovány, některé automatizované a jiné ruční. Další informace najdete v tématu [zásady certifikace pro komerční tržiště](/legal/marketplace/certification-policies).

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

Další informace o kritériích ověřování obsahu najdete v tématu [Obecné zásady výpisu](/legal/marketplace/certification-policies#100-general).

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

## <a name="cancel-publishing"></a>Zrušit publikování

Zrušení nabídky se stavem **publikování** probíhá:

1. Vyberte název nabídky a otevřete stránku **Přehled nabídky** .
1. Vyberte **Zrušit publikování** v pravém horním rohu stránky.
1. Potvrďte, že chcete zastavit nabídku publikováním.

Pokud chcete nabídku publikovat později, budete muset spustit proces publikování.

> [!NOTE]
> Publikování nabídky můžete zastavit pouze v případě, že nabídka ještě neprobíhala na základě odhlášení vydavatele. Po výběru možnosti **Přejít na Live** nebudete mít možnost zrušit publikování.

## <a name="publisher-sign-off-phase"></a>Fáze pro odhlášení vydavatele

Když je nabídka připravená na kontrolu a její odhlášení, pošleme vám e-mail s žádostí o kontrolu a schválení vaší nabídky Preview. Stránku s **přehledem nabídky** můžete také aktualizovat v prohlížeči, abyste viděli, jestli vaše nabídka dosáhla fáze pro odhlášení vydavatele. Pokud má, budou k dispozici tlačítka **Přejít živě** a náhled.

Následující snímek obrazovky ukazuje stránku s **přehledem nabídky** v programu pro komerční tržišti v partnerském centru. Postup ověření, který se zobrazí na této stránce, se liší v závislosti na typu nabídky a výběrech, které jste provedli při vytváření této nabídky. Všimněte si, že certifikační fáze se může nacházet během odesílání do verze Preview nebo v reálném čase v závislosti na typu nabídky.

![Popisuje stránku s přehledem nabídky pro nabídku v partnerském centru. Zobrazí se odkazy na tlačítko Přejít živě a náhled.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Zobrazení náhledu a schválení nabídky

> [!IMPORTANT]
> Pokud chcete ověřit kompletní tok nákupu a nastavování, kupte si nabídku, i když je ve verzi Preview. Nejdřív od Microsoftu upozorněte [lístek podpory](https://aka.ms/marketplacesupport) , abyste se ujistili, že Nezpracováváme poplatky. Pokud vaše nabídka obsahuje seznam _kontaktů_ , otestujte, jestli je potenciální zájemce vytvořen podle očekávání, zadáním podrobností o kontaktech ve verzi Preview.

Na stránce **Přehled nabídky** se zobrazí odkazy ve verzi Preview pod tlačítkem **Přejít na Live** . V závislosti na možnostech, které jste zvolili při vytváření vaší nabídky, se bude jednat o odkaz buď na AppSource Preview Azure Marketplace, ve verzi Preview, nebo v obou. Pokud jste se rozhodli prodat nabídku prostřednictvím Microsoftu, může kdokoli, kdo byl přidán do cílové skupiny Preview, otestovat pořízení a nasazení vaší nabídky, aby se zajistilo, že splní vaše požadavky v této fázi.

> [!IMPORTANT]
> Před výběrem tlačítka **Přejít na Live** nezapomeňte zkontrolovat a vyřešit všechna upozornění z kroku _ověření nabídky_ . V opačném případě dojde k selhání certifikace, která by zpozdila vaši nabídku před živým zprovozněním.

Po schválení verze Preview vyberte možnost **Přejít na Live** a publikujte nabídku živě na komerčním webu Marketplace. 

Chcete-li provést změny po zobrazení náhledu nabídky, můžete upravit a znovu odeslat žádost o publikování. Pokud je vaše nabídka už živá a dostupná pro veřejnost na webu Marketplace, jakékoli aktualizace, které provedete, se neprojeví, dokud nevyberete možnost **jít živě*. Další informace najdete v tématu [aktualizace stávající nabídky na komerčním webu Marketplace](partner-center-portal/update-existing-offer.md) .

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

## <a name="publishing-history"></a>Historie publikování

Stránka **Historie** v partnerském centru zobrazuje události publikování pro vaše nabídky komerčních tržišť. U každé události na stránce se zobrazí uživatel, který inicioval akci, typ události a datum a čas události. [Kroky ověřování a publikování](#validation-and-publishing-steps) jsou uvedené s datem a časem dokončení.

Zobrazení historie vaší nabídky:

1.    Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2.    V navigační nabídce na levé straně vyberte **obchodní Marketplace**  >  **Přehled**.
3.    Vyberte svoji nabídku.
4.    V navigační nabídce vlevo vyberte kartu **Historie** . Stránka poskytuje podrobné informace o následujících událostech, které se vztahují na vaši nabídku:

|Událost    |Description    |
|---------|---------------|
|Nabídka vytvořena    |Nabídka se vytvořila v partnerském centru. Uživatel vybral v přehledu **komerčního tržiště** typ nabídky, ID nabídky a alias nabídky  >  .    |
|Vytvořený plán: *název plánu*    |Uživatel vytvořil nový plán zadáním ID plánu a názvu plánu na kartě **Přehled plánu** .</br>*Tato událost se vztahuje pouze na typy nabídek, které podporují plány*.    |
|Plán se odstranil    |Uživatel odstranil pracovní plán, který nebyl publikován, výběrem možnosti **Odstranit koncept** ze stránky **Přehled plánu** .</br>*Tato událost se vztahuje pouze na typy nabídek, které podporují plány*.    |
|Vyvolaný plán zastavil prodej: *název plánu*    |Uživatel zahájil plán, který se zastavil – prodej výběrem možnosti **ukončit prodej** na stránce **Přehled plánu** .</br>*Tato událost se vztahuje pouze na typy nabídek, které podporují plány*.    |
|Zrušit prodej – plán zastavit: *název plánu*    |Uživatel zrušil plán – prodej výběrem možnosti **zpět ukončit prodej** na stránce **Přehled plánu** .</br>*Tato událost se vztahuje pouze na typy nabídek, které podporují plány*.    |
|Odeslaná nabídka do verze Preview    |Uživatel odeslal nabídku do náhledu výběrem možnosti **publikovat** na stránce **Kontrola a publikování** .    |
|Zrušení vyvolaného odeslání do verze Preview    |Uživatel požádal o zrušení publikace nabídky do verze Preview výběrem možnosti **Zrušit publikování** na stránce **přehledu nabídky** po odeslání do náhledu.</br>*Tato událost se zobrazí při zpracování žádosti o zrušení*.    |
|Zrušené odeslání do verze Preview    |Uživatel zrušil publikaci nabídky do verze Preview výběrem možnosti **Zrušit publikování** na stránce **Přehled nabídky** po odeslání do náhledu.</br>*Tato událost se zobrazí po úspěšném zpracování žádosti o zrušení*.    |
|Odhlásit se a přejít na Live    |Uživatel publikoval nabídku na komerční tržišti výběrem možnosti **Přejít živě** na stránce **Přehled nabídky** .    |
|Zrušení zahájení publikování na webu Marketplace    |Uživatel požádal o zrušení publikace nabídky kliknutím na **tlačítko Zrušit publikování** na stránce **Přehled nabídky** po přihlášení k webu.</br>*Tato událost se zobrazí při zpracování žádosti o zrušení*.    |
|Zrušené publikování na komerčním webu Marketplace    |Uživatel zrušil publikaci nabídky kliknutím na **tlačítko Zrušit publikování** na stránce s **přehledem nabídky** po přihlášení k webu.</br>*Tato událost se zobrazí po úspěšném zpracování žádosti o zrušení*.    |
|Synchronizovat soukromou cílovou skupinu    |Uživatel aktualizoval a synchronizoval soukromou cílovou skupinu tak, že vybere možnost **synchronizovat soukromou cílovou skupinu** na stránce **Přehled plánu** nebo na stránce s **cenami plánu & dostupnosti** .</br>*Tato událost se vztahuje pouze na typy nabídek, které podporují soukromé plány*.    |
|Zastavit prodej nabídky    |Uživatel zastavil prodej nabídky výběrem možnosti **ukončit prodej** na stránce **Přehled nabídky** .    |

> [!NOTE]
> Stránka historie neříká, že se uložil koncept nabídky.

### <a name="filter-options"></a>Možnosti filtru

Pomocí filtrů můžete zúžit celou historii nabídky na konkrétní události publikování:

1.    V pravém horním rohu stránky vyberte tlačítko filtr.
2.    Zvolte Filtr a pak vyberte **použít** a podívejte se, které události historie odpovídají vybraným kritériím.
3.    Vyberte možnost **Vymazat filtry** a vraťte se k úplné historii vaší nabídky.

Existují čtyři filtry:
* Události
* Uživatelé
* Date (Datum)
* Stránky

Když vyberete filtr **stránky** , můžete vybrat libovolné stránky partnerského centra, které se vztahují k vašemu typu nabídky. Když se použije, filtr **stránky** zobrazí všechny **odeslané nabídky pro náhled událostí ve verzi Preview** se změnami vybrané stránky.

* Pro všechny nabídky je stránka pro **nastavení nabídky** ve výchozím nastavení součástí každé události odeslání.
* U nabídek, které podporují plány, je pro každou událost odeslání zahrnutá Stránka s **přehledem plánu** .
* Pro nabídky, které podporují testovací verzi, je pro každou událost odeslání zahrnutá stránka **testovacího disku** .

### <a name="users"></a>Uživatelé

Pokud uživatel inicioval událost, stránka Historie zobrazí uživatele podle následujících scénářů:

#### <a name="the-event-was-initiated-by-the-publisher"></a>Vydavatel zahájil událost.

Uživatelům s oprávněním pro publikování v nabídce bude zobrazen jejich název pro události publikování, které iniciují.

[![Stránka historie zobrazuje jména uživatelů s oprávněními k publikování.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>Událost byla iniciována společností Microsoft.

Správcům Microsoftu můžete udělit oprávnění zahájit akce vaším jménem nebo provést nápravné akce po neočekávané systémové chybě. Název a logo společnosti Microsoft se zobrazí pro publikování událostí inicializovaných společností Microsoft jménem svého účtu.

[![Příklad, jak stránka Historie zobrazuje události iniciované Microsoftem](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>Událost byla iniciována neidentifikovaným uživatelem.

Uživatelé, kteří už nejsou k účtu přidružení, budou mít po stažení oprávnění k publikování z něj odebráni jejich jméno ze sloupce **iniciované** .

[![Příklad, jak stránka Historie zobrazuje události iniciované neidentifikovanými uživateli](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Postup ověření a publikování

Pro systémové procesy, které odpovídají [postupům ověření a publikování](#validation-and-publishing-steps), se nezobrazuje žádný uživatel. Tyto události jsou barevně kódované podle stavu dokončení události.

[![Příklad, jak stránka Historie zobrazuje kroky ověření a publikování.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Další kroky

[Přístup k analytickým sestavám pro komerční tržiště v partnerském centru](partner-center-portal/analytics.md)
