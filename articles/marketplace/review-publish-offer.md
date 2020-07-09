---
title: Kontrola a publikování nabídky na komerčním webu Microsoft Marketplace
description: Použijte Partnerské centrum k odeslání nabídky do verze Preview, zobrazení náhledu vaší nabídky a její následné publikování na komerčním webu Microsoft Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 07/05/2020
ms.openlocfilehash: 34e56e5d92526cbf46408c670127e87781e342cd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119731"
---
# <a name="review-and-publish-an-offer-to-commercial-marketplace"></a>Kontrola a publikování nabídky na komerčním webu Marketplace

V tomto článku se dozvíte, jak pomocí partnerského centra odeslat nabídku do verze Preview, zobrazit náhled nabídky a potom ji publikovat na komerčním webu Microsoft Marketplace. Také pokryjeme, jak kontrolovat stav publikování během postupu publikování. Musíte již mít vytvořenou nabídku, kterou chcete publikovat.

## <a name="go-to-your-offer-in-commercial-marketplace"></a>Přejít na nabídku na komerčním webu Marketplace

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
1. V navigační nabídce vlevo vyberte **obchodní Marketplace**  >  **Přehled**.
1. Na kartě **Přehled** v části **nabídky**se ve sloupci **stav** u každé nabídky zobrazí jeden z následujících indikátorů stavu.
 
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

4. Ve sloupci **alias nabídky** vyberte nabídku, kterou chcete zobrazit a publikovat.

## <a name="submit-your-offer-to-preview"></a>Odeslání nabídky do verze Preview

1. Pokud chcete odeslat nabídku do verze Preview, vyberte **zkontrolovat a publikovat** v pravém horním rohu portálu. Zobrazí se stránka **Kontrola a publikování** .
1. Ujistěte se, že se sloupec **stav** pro každou stránku **dokončí**. Tři možné stavy jsou následující:
   - **Nezahájeno** – stránka se nedotkla a musí být dokončena.
   - **Nedokončeno** – stránka neobsahuje požadované informace nebo obsahuje chyby, které je třeba opravit. Budete se muset vrátit na stránku a aktualizovat ji.
   - **Dokončit** – stránka je dokončená. Byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby.
1. Pokud má kterákoli z stránek stav jiný než **úplný**, ve sloupci **Stránka** vyberte název stránky, opravte problém, uložte stránku a pak znovu vyberte **zkontrolovat a publikovat** , aby se vrátila na tuto stránku.
1. Až budou všechny stránky dokončené, poskytněte v poli **poznámky k certifikaci** pokyny k testování certifikačního týmu, aby se zajistilo správné testování vaší aplikace. Poskytněte jakékoli doplňkové poznámky užitečné pro porozumění vaší aplikaci.
1. Pokud chcete odeslat nabídku pro publikování, vyberte **publikovat**. Zobrazí se stránka s **přehledem nabídky** a zobrazí se stav publikování.

## <a name="validation-and-publishing-steps"></a>Postup ověření a publikování

Po výběru **publikovat**budou procesy ověřování a publikování pokračovat v uvedeném pořadí. Nejběžnější proces publikování je zobrazen v této tabulce:

| Fáze | Co se stane | 
| ------------ | ------------- | ------------- |
| Automatizované ověřování | Zpracováváme sadu automatizovaných ověření. | 
| Certifikace | Provádíme Ruční ověřování. | 
| Vytváření náhledu | Stránka výpisu pro vaši nabídku Preview je k dispozici všem uživatelům, kteří mají odkaz ve verzi Preview. Pokud bude vaše nabídka prodávána prostřednictvím Microsoft (s podporou transakcí), může koupit a získat přístup k této nabídce pro účely testování jenom ta cílová skupina, kterou jste zadali na stránce **verze Preview** vaší nabídky. | 
| Schvalování vydavatele | Pošleme vám e-mail s žádostí o náhled a schválení vaší nabídky. | 
| Publikování | Spuštěním série kroků ověříte, že nabídka Preview je publikovaná živě na komerčním webu Marketplace. | 
|||

## <a name="automated-validation-phase"></a>Fáze automatizovaného ověřování

Prvním krokem procesu publikování je sada automatizovaných ověření. Každý krok ověření odpovídá funkci, kterou jste zvolili při vytváření nabídky. Před přechodem nabídky k dalšímu kroku v procesu publikování je nutné provést každou kontrolu ověřením.

- **Nastavení toku nákupu nabídky (<10 min)**

   V tomto kroku zajistíme splnění vaší nabídky při nákupu zákazníky prostřednictvím Azure Portal. Tento krok platí jenom pro nabídky prodávané prostřednictvím Microsoftu.
- **Ověření dat testovacích jednotek (~ 5 min.)**

   V tomto kroku Ověřujeme data, která jste zadali na stránce Technická konfigurace nabídky. Funkce testovacích jednotek je testována a schválena. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

-   **Zřizování testovacích jednotek (~ 30 min.)**

    V tomto kroku nasadíme a provedeme po ověření dat a funkcí testovacích jednotek v předchozím kroku instance testovacího disku, aby byly připravené na použití pro zákazníky. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

-   **Ověření a registrace řízení vedoucího (<15 min)**

    V tomto kroku potvrzujeme, že váš systém pro správu potenciálních zákazníků může obdržet zájemce na základě podrobností, které jste zadali na stránce pro **nastavení nabídek** . Tento krok platí jenom pro nabídky se zapnutou správou potenciálních zákazníků.

## <a name="certification-phase"></a>Fáze certifikace

Před publikováním musí být nabídky odeslané na komerční Marketplace certifikované. Odeslané nabídky prošly přísným testováním, a to některými automatizovanými a jinými ručními. Další informace najdete v části [zásady certifikace komerčního tržiště](https://aka.ms/commercial-marketplace-certification-policies) .

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy ověřování, které probíhají během certifikace
Existují tři úrovně ověřování zahrnuté v procesu certifikace pro každou odeslanou nabídku.
-   Způsobilost pro firmy vydavatele
-   Ověření obsahu
-   Technické ověření

#### <a name="publisher-business-eligibility"></a>Způsobilost pro firmy vydavatele
Každý typ nabídky kontroluje sadu základních kritérií způsobilosti, které musí vydavatel splňovat. Kritéria způsobilosti můžou zahrnovat stav programu MPN vydavatele, uchovávané kompetence, úrovně kompetence atd.

#### <a name="content-validation"></a>Ověření obsahu

Informace, které jste zadali při vytváření vaší nabídky, jsou zkontrolovány z hlediska kvality a relevance. Tyto kontroly prověří vaše položky s podrobnostmi o seznamech na Marketplace, cenách, dostupnosti, přidružených plánech atd. Abychom splnili kritéria pro výpis Azure Marketplace a Microsoft AppSource, ověříme, že vaše nabídka zahrnuje:
-   Název, který přesně popisuje nabídku
-   Jasně psané popisy, které poskytují důkladný přehled a polohu hodnoty
-   Snímky obrazovky a videa kvality
-   Vysvětlení způsobu, jakým nabídka využívá platformy a nástroje společnosti Microsoft.

Další informace o kritériích ověřování obsahu najdete v tématu [Obecné zásady výpisu](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Technické ověření
Během technického ověřování dojde k tomu, že nabídka (balíček nebo binární) projde následující kontroly.
-   Prověřený malware
-   Monitorovaná síťová volání
-   Analyzovaný balíček
-   Důkladné prohledání funkce nabídky

Nabídka je testována na různých platformách a verzích, aby bylo zajištěno jejich robustní.

### <a name="certification-failure-report"></a>Sestava o chybách certifikace

Pokud vaše nabídka neselže u žádného ze seznamů, technických nebo zásad nebo pokud nemáte nárok na odeslání nabídky tohoto typu, pošleme vám zprávu o selhání certifikace.

Tato sestava obsahuje popisy všech zásad, které se nezdařily, spolu s revizními zprávami. Přečtěte si tuto e-mailovou sestavu, vyřešte všechny problémy, v případě potřeby proveďte aktualizace svojí nabídky a znovu odešlete nabídku pomocí [komerčního portálu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v partnerském centru. Nabídku můžete znovu odeslat tolikrát, kolikrát je potřeba, dokud neprojdete certifikaci.

## <a name="preview-creation-phase"></a>Fáze vytváření náhledu

Během fáze vytváření verze Preview vytvoříme verzi vaší nabídky, která bude dostupná jenom pro cílovou skupinu, kterou jste zadali na stránce **Preview pro cílovou skupinu** vaší nabídky, pokud nějaké máte. Verze Preview vaší nabídky nebude k dispozici nikomu mimo cílovou skupinu Preview, dokud nepublikujete nabídku živě.

> [!NOTE]
> Nepoužívejte cílovou skupinu Preview k tomu, aby uživatelé mimo vaši organizaci mohli zobrazit nabídku. Místo toho použijte možnost soukromé nabídky. V tuto chvíli nebyla vaše nabídka plně testována a ověřena a není připravena k externí distribuci. 

## <a name="publisher-signoff-phase"></a>Schvalování fáze vydavatele

Když je nabídka připravená na kontrolu a schvalování, pošleme vám e-mail s žádostí o kontrolu a schválení vaší nabídky Preview. Stránku s **přehledem nabídky** můžete také aktualizovat v prohlížeči, abyste viděli, jestli vaše nabídka dosáhla schvalování fáze vydavatele. Pokud má, budou k dispozici tlačítka **Přejít živě** a náhled.

Na následujícím snímku obrazovky vidíte stránku s **přehledem nabídky** pro SaaS nabídku. Postup ověření, který se zobrazí na této stránce, se liší v závislosti na typu nabídky a výběrech, které jste provedli při vytváření této nabídky.

![Popisuje stránku s přehledem nabídky pro nabídku v partnerském centru. Zobrazí se odkazy na tlačítko Přejít živě a náhled.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Náhled nabídky a schvalování**
1. Na stránce **Přehled nabídky** vyberte odkaz pod tlačítkem **Přejít do živého** zobrazení.
   > [!NOTE]
   > V závislosti na možnostech, které jste zvolili při vytváření vaší nabídky, se bude jednat o odkaz buď na AppSource Preview Azure Marketplace, ve verzi Preview, nebo v obou. Pokud jste se rozhodli prodat nabídku prostřednictvím Microsoftu, může kdokoli, kdo byl přidán do cílové skupiny Preview, otestovat pořízení a nasazení vaší nabídky, aby se zajistilo, že splní vaše požadavky v této fázi.

1. Chcete-li provést změny po zobrazení náhledu nabídky, můžete upravit a znovu odeslat publikování nové verze Preview. Další informace najdete v tématu [aktualizace stávající nabídky na komerčním webu Marketplace](./partner-center-portal/update-existing-offer.md).

1. Po schválení verze Preview budete moct svou nabídku publikovat na komerčním webu Marketplace, a to výběrem možnosti **Přejít na Live**.
   > [!TIP]
   > Pokud je vaše nabídka už živá a dostupná pro veřejnost na webu Marketplace, jakékoli aktualizace, které provedete, se neprojeví, dokud nevyberete možnost **Přejít do provozu**.

## <a name="publish-phase"></a>Fáze publikování

Teď, když jste se rozhodli, že budete mít k dispozici nabídku, která je dostupná na komerčním webu Marketplace, je k dispozici řada konečných ověřovacích kontrol, které provedeme, abychom zajistili, že je živá nabídka nakonfigurovaná stejně jako verze Preview nabídky.

-   **Nastavení toku nákupu nabídky (>10 min)**

    V tomto kroku zajistíme splnění vaší nabídky při nákupu zákazníky prostřednictvím Azure Portal. Tento krok platí jenom pro nabídky prodávané prostřednictvím Microsoftu.
-   **Ověření dat testovacích jednotek (~ 5 min.)**

    V tomto kroku Ověřujeme data, která jste zadali na stránce Technická konfigurace nabídky. Funkce testovacích jednotek je testována a schválena. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.

-   **Zřizování testovacích jednotek (~ 30 min.)**

      V tomto kroku nasadíme a replikujte instance testovacích jednotek, aby byly připravené k použití na zákazníky. Tento krok platí jenom pro nabídky s povolenou testovací jednotkou.
-   **Ověření a registrace řízení vedoucího (>15 min)**

    V tomto kroku potvrzujeme, že váš systém pro správu potenciálních zákazníků může obdržet zájemce na základě podrobností uvedených na stránce nabídky **nastavení nabídky** . Tento krok platí jenom pro nabídky se zapnutou správou potenciálních zákazníků.

-   **Finální publikování (>30 minut)**

    V tomto kroku zajistíme, že vaše nabídka bude veřejně dostupná na webu Marketplace.

Po dokončení těchto kontrol budou vaše nabídka na webu Marketplace živá.

## <a name="next-step"></a>Další krok

[Přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md)