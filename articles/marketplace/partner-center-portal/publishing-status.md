---
title: Kontrola stavu publikování nabídky Commercial Marketplace
description: Zkontrolujte stav kroků ověření, certifikace a náhledu potřebných k publikování nabídky prostřednictvím komerčního tržiště v Centru partnerů společnosti Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275675"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Kontrola stavu publikování nabídky Commercial Marketplace

Aktuální **stav publikování** můžete zobrazit na kartě **Přehled nabídky** na [portálu Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v Centru partnerů.

Pro každou nabídku by měl být zobrazen jeden z následujících indikátorů stavu.

| **Stav**    | **Popis**  |
| :---------- | :-------------------|
| **Koncept** | Nabídka byla vytvořena, ale není publikována. |
| **Probíhá publikování** | Nabídka/plán se propracovává kroky procesu publikování. |
| **Nutná pozornost** | Při certifikaci společností Microsoft nebo v některém z kroků publikování byl zjištěn kritický problém. |
| **Náhled** | Nabídka byla certifikována společností Microsoft a nyní čeká na konečné ověření vydavatelem. Chcete-li nabídku oživit, vyberte možnost Přejít živě. |
| **Živé** | Nabídka je živě na trhu a mohou být viděna a získána zákazníky. |
| **Čeká na zastavení prodeje** | Aplikace Publisher vybrala možnost "stop sell" v nabídce nebo plánu, ale akce ještě nebyla dokončena. |
| **Není k dispozici na trhu** | Dříve publikovaná nabídka/plán na trhu byla odebrána. |

## <a name="automated-validation"></a>Automatické ověření

Prvním krokem v procesu publikování je sada automatizovaných ověření. Každý krok ověření odpovídá funkci, kterou jste zvolili při vytváření nabídky. Pokud tato funkce nebyla povolena, ověření přeskočí dopředu na další krok publikování. Každá kontrola ověření musí být dokončena před schválením stavu publikování.

- **Nastavení nákupního toku (<10 min)**

V tomto kroku zajistíme, že vaši nabídku můžete splnit při nákupu zákazníky prostřednictvím portálu Azure. Tento krok platí pouze pro nabídky prodávané prostřednictvím společnosti Microsoft.

- **Ověření dat testovací jednotky (~5 min)**

V tomto kroku ověříme data, která jste zadali v části Technické konfigurace v nabídce. Funkce testovací jednotky je testována a schválena. Tento krok je použitelný pouze pro nabídky s povolenou zkušební jízdou.

- **Zřizování zkušební jízdy (~30 min)**

V tomto kroku po ověření dat a funkcí testovací jednotky v předchozím kroku nasadíme a replikujeme instance testovací jednotky tak, aby byly připravené pro použití zákazníkem.  Tento krok je použitelný pouze pro nabídky s povolenou zkušební jízdou.

- **Ověření a registrace vedení vedení (<15 min)**

V tomto kroku potvrzujeme, že váš systém správy potenciálních zákazníků může přijímat zákazníky na základě podrobností uvedených v nastavení nabídky. Tento krok platí pouze pro nabídky s povolenou správou potenciálních zákazníků.

## <a name="certification"></a>Certifikace

Před zveřejněním musí být nabídky odeslané na komerční tržiště v partnerském centru certifikovány. Odeslané nabídky procházejí přísným testováním, některé automatizované a jiné ruční, včetně kontroly proti [zásadám certifikace Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Odeslání nabídky musí být označena jako způsobilá k certifikaci, než přejde k dalšímu kroku v toku publikování.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy validace, ke kterým dochází během certifikace

V procesu certifikace každé předložené nabídky jsou zahrnuty tři úrovně validace.

- Nárok na podnikání vydavatele
- Ověření obsahu
- Technická validace

#### <a name="publisher-business-eligibility"></a>Nárok na podnikání vydavatele

Každý typ nabídky zkontroluje sadu základních kritérií způsobilosti, která musí vydavatel splňovat. Kritéria způsobilosti mohou zahrnovat status programu MPN vydavatele, držené kompetence, úrovně kompetencí atd.

#### <a name="content-validation"></a>Ověření obsahu

Během ověřování obsahu jsou informace zadané při vytvoření nabídky kontrolovány z hlediska kvality a relevance. Tyto kontroly zkontrolují vaše položky pro podrobnosti o výpisu na trhu, ceny, dostupnost, přidružené plány atd. Abychom splnili kritéria pro zápis na Azure Marketplace nebo AppSource, ověříme, že vaše nabídka zahrnuje:

- název, který přesně popisuje nabídku;
- dobře psané popisy, které poskytují důkladný přehled a hodnotu;
- kvalitní screenshoty a doprovodná videa; A
- vysvětlení, jak nabídka využívá platformy a nástroje společnosti Microsoft.

Další informace o kritériích pro ověření obsahu naleznete v [obecných zásadách výpisu](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Technická validace

Během technické ověření nabídka (balíček nebo binární) prochází následující mise.
- Vyhledání malwaru
- Monitorovaná síťová volání
- Balíček analyzován
- Důkladné prohledání aktuální funkčnosti nabídky

Nabídka je testována na různých platformách a verzích, aby byla zajištěna její robustnost.

V části Technická konfigurace tohoto dokumentu si přečtěte konkrétní podrobnosti o konfiguraci, které jsou požadovány pro vaši nabídku.

### <a name="certification-failure-report"></a>Zpráva o selhání certifikace

Po dokončení kontroly, pokud vaše nabídka prošla certifikací, pak se přesune k dalšímu kroku v procesu publikování. Pokud vaše nabídka neprošla některou z kontrol zápisu, technických nebo zásad nebo pokud nemáte nárok na odeslání nabídky tohoto typu, vygeneruje se zpráva o selhání certifikace a pošle vám e-mail.

Tato sestava obsahuje popisy všech zásad, které se nezdařily, spolu s poznámkami k revizi. Projděte si tento e-mailový přehled, vyřaďte případné problémy, v případě potřeby aktualizujte nabídku a nabídku znovu odešlete pomocí [portálu Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) v Centru partnerů. (Nabídku můžete odeslat tolikrát, kolikrát je potřeba, až do schválení certifikace).

## <a name="preview-creation"></a>Vytvoření náhledu

Během kroku **vytvoření náhledu** vytvoříme verzi vaší nabídky přístupnou pouze publiku, které jste zadali v části Náhled ve své nabídce.

>[!Note]
> Nepoužívejte tento krok, abyste lidem mimo vaši organizaci poskytli přehled o nabídce. Místo toho použijte možnost **Soukromá nabídka.** V tomto okamžiku vaše nabídka nebyla plně testována a ověřena a není připravena pro vnější distribuci.

## <a name="publisher-approval"></a>Schválení vydavatele

V tomto kroku budete e-mailem s žádostí o kontrolu a schválení náhledu nabídky před konečným krokem publikování.

Pokud jste se rozhodli prodávat svou nabídku prostřednictvím společnosti Microsoft, budete moci otestovat pořízení a nasazení nabídky, abyste zajistili, že během této fáze schvalování náhledu splňuje vaše požadavky. Vaše nabídka ještě nebude dostupná na veřejném trhu. Jakmile tento náhled otestujete a schválíte, budete muset na řídicím panelu [**Přehled nabídek**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) vybrat **možnost Přejít** na živé vysílání.

Pokud chcete v této fázi náhledu provést změny v nabídce, můžete upravit a znovu odeslat a publikovat nový náhled. Podrobnosti o dalších změnách najdete v článku [Aktualizace existujících nabídek marketplace.](#update-existing-marketplace-offers)

Pokud je vaše nabídka již živá a dostupná veřejnosti na webu Marketplace, žádné aktualizace, které provedete, nebudou aktivní, dokud na řídicím panelu [**Přehled nabídek**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nevyberete **možnost Přejít** na živé vysílání.

### <a name="publish-offer-to-the-public"></a>Zveřejnit nabídku veřejnosti

Přihlaste se do Partnerského centra a získejte přístup k nabídce. Budete přesměrováni na stránku **Přehled nabídky.** V horní části této stránky se zobrazí možnost **Přejít živě**. Vyberte **Přejít živě** a po potvrzení se nabídka začne zveřejňovat. Jakmile bude nabídka v provozu, obdržíte e-mailové oznámení.

## <a name="publish"></a>Publikování

Nyní, když jste zvolili **go live** s vaší nabídkou, takže je k dispozici na trhu, existuje řada konečných ověření kontrol, které budou prosakovány, aby bylo zajištěno, že živá nabídka je nakonfigurována stejně jako předběžná verze nabídky.

- **Nastavení nákupního toku (>10 min)**

V tomto kroku zajistíme, že vaši nabídku můžete splnit při nákupu zákazníky prostřednictvím portálu Azure. Tento krok platí pouze pro nabídky prodávané prostřednictvím společnosti Microsoft.

- **Ověření dat testovací jednotky (~5 min)**

V tomto kroku ověříme data, která jste zadali v části Technické konfigurace v nabídce. Funkce testovací jednotky je testována a schválena. Tento krok je použitelný pouze pro nabídky s povolenou zkušební jízdou.

- **Zřizování zkušební jízdy (~30 min)**

V tomto kroku nasadíme a replikujeme instance testovací jednotky tak, aby byly připravené pro použití zákazníkem.  Tento krok je použitelný pouze pro nabídky s povolenou zkušební jízdou.

- **Ověření a registrace vedení vedení (>15 min)**

V tomto kroku potvrzujeme, že váš systém správy potenciálních zákazníků může přijímat zákazníky na základě podrobností uvedených v nastavení nabídky. Tento krok platí pouze pro nabídky s povolenou správou potenciálních zákazníků.

- **Konečné publikování (>30 minut)**

V tomto kroku zajistíme, že vaše nabídka bude veřejně dostupná na trhu.

## <a name="update-existing-marketplace-offers"></a>Aktualizace existujících nabídek tržiště

Pokud chcete provést změny v nabídce, kterou jste už publikovali, budete muset nejdřív aktualizovat existující nabídku a pak ji znovu publikovat.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
