---
title: Detekce anomálií pro účtované podle objemu dat | Azure Marketplace
description: Přečtěte si, jak automatické zjišťování anomálií pro účtované podle objemu dat pomáhá zajistit, aby se vaši zákazníci správně účtovali na měření využití komerční nabídky na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989362"
---
# <a name="anomaly-detection-for-metered-billing"></a>Detekce anomálií pro účtované měřené platby

Tento článek poskytuje podrobné informace o službě měření na webu Marketplace a přidružené funkci pro detekci automatizovaných anomálií, které vám pomůžou zajistit, aby zákazníci mohli správně fakturovat pro své měřené využití. Možnost účtovaného účtování je aktuálně dostupná pro nabídky [software jako služba](plan-saas-offer.md) (SaaS) a [aplikace Azure](plan-azure-application-offer.md#types-of-plans) s plánem spravované aplikace. Tato možnost umožňuje partnerům vytvářet nabídky v programu komerčního tržiště, které se účtují podle nestandardních jednotek.

Partneři, kteří mají vlastní měřiče nasazené pro SaaS a spravované aplikace, můžou zobrazit odchylku od očekávaného chování při používání jako anomálie _u určitých_ _vlastních měřičů_ v partnerském centru. Pro zmírnění rizika Partnerská služba používá službu detekce anomálií, která aplikuje algoritmy strojového učení, aby zjistila normální chování účtovaného měření, analyzuje využití účtované fakturace a objevují anomálie s minimálním zásahem uživatele. Partnerské centrum se zaměřuje na to, aby při použití _modelů detekce anomálií_ v datových sadách s měřením vyúčtováním na základě měřené ceny informovali vydavatele o tom, že hlášené využití překračuje očekávané využití.

## <a name="usability-experience"></a>Prostředí pro použitelnost

Microsoft spoléhá na to, že partner bude hlásit využívání služeb SaaS nebo Azure spravované aplikace před tím, než ho Microsoft fakturuje. Pokud je hlášeno nesprávné použití, zákazník by potenciálně mohl získat nesprávnou fakturu, může podvést i důvěryhodnost partnera.

Aby se to dalo zmírnit, je k dispozici funkce detekce automatizované anomálií pro aplikace SaaS i plány spravovaných aplikací Azure. Tato funkce je model strojového učení, který aktivně monitoruje využití na základě účtovaného měření a předpovídá očekávanou hodnotu využití v očekávaném rozsahu. Pokud je využití mimo očekávaný rozsah, je považováno za anomálii a na stránce s přehledem služby komerčního portálu se zobrazí oznámení o výstrahách na stránce Přehled nabídky v programu pro partnery.

Denní využití analýz modelů machine learningu Vydavatel uvidí všechny anomálie hlášené na využívání nadlimitního využití zákazníků pro každou vlastní dimenzi měřičů nabídek.

### <a name="view-and-manage-metered-usage-anomalies"></a>Zobrazení a Správa anomálií měřeného využití

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
1. V navigační nabídce vlevo vyberte **komerční web Marketplace**  >  **analyzovat**.
1. Vyberte kartu **anomálie měření využití** .

    [![Znázorňuje kartu anomálie měření využití na stránce používání.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Obrázek 1: karta anomálie podle měření využití***

1. Pro všechny anomálie využití zjištěné v rámci fakturace účtované podle objemu se zobrazí výzva k prověření a potvrzení, zda je anomálie pravdivá. Vyberte možnost **Označit jako anomálii** a potvrďte tak diagnostiku.

     [![Ukazuje dialogové okno označit jako anomálii.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Obrázek 2: označení jako anomálie – dialogové okno***

1. Pokud se domníváte, že objevená nadlimitní využití, která jsme zjistili, není pravá, můžete tuto zpětnou vazbu poskytnout tak, že v partnerském centru **vyberete** anomálii na konkrétní překročení nadlimitního využití.

    [![Ukazuje, proč se nejedná o dialogové okno anomálie.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Obrázek 3: Proč to není anomálie? Dialogové okno***

1. Seznam inventáře nepotvrzených anomálií můžete zobrazit tak, že přejdete na stránku dolů. Seznam obsahuje inventarizaci anomálií, které jste nepřijali. Můžete nastavit, aby se anomálie v partnerském centru označily jako pravé nebo nepravdivé.

   [![Znázorňuje seznam nepotvrzených anomálií partnerského centra na stránce používání.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Obrázek 4: seznam nepotvrzených anomálií partnerského centra***

1. Také se zobrazí protokol akce anomálií, který obsahuje akce, které jste použili při překročení využití. V protokolu akcí budete moci zjistit, které překročené události využívání byly označeny jako pravé nebo nepravdivé.

   [ ![ Ukazuje protokol akce anomálií na stránce využití.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Obrázek 5: protokol akcí anomálií***

1. Analýza partnerského centra nebude v sestavách exportu podporovat reportování nadlimitních událostí využití. Partnerské centrum umožňuje zadat korigované využití nadlimitního využití pro anomálii a podrobnosti jsou před Microsoft Teams předány k šetření. Na základě šetření společnost Microsoft vystavuje refundaci náhradě za zákazníka, pokud to bude potřeba. Když vyberete některou z anomálií označených příznakem, můžete vybrat **Označit jako anomálii** a označit tak nadlimitní využití jako originální.

   [ ![ Znázorňuje značku jako dialogové okno anomálie.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Obrázek: 6: označit jako anomálii – dialogové okno***

Když se nadlimitní využití v partnerském centru označí jako nepravidelně, zobrazí se v rámci této instance okno o 30 dní, ve kterém se tato anomálie označí jako pravá nebo nepravdivá. Po uplynutí 30 dnů se jako vydavatel nebudete moct chovat na anomáliích.

> [!IMPORTANT]
> Jednotky využívání nadlimitního využití nejsou způsobilé pro změnu stavu a finanční adjustaci.

Pro vybrané období výpočtu můžete zobrazit všechny anomálie (potvrzené nebo jinak). Různá výpočetní období jsou poslední 30 dní, posledních 60 dní a posledních 90 dnů.

> [!IMPORTANT]
> Požadujete, aby se v průběhu 30 dnů od okamžiku, kdy se anomálie nahlásila v partnerském centru, pracovaly na anomáliích označených příznakem.

Modální filtr v widgetu vám umožní vybrat jednotlivé nabídky a jednotlivé vlastní měřiče.

Když označíte překročené využití jako anomálii nebo potvrdili model, který označil výjimku jako pravou, nemůžete změnit výběr na neanomálii.

> [!IMPORTANT]
> V případě převzetí služeb při selhání můžete znovu odeslat nadlimitní využití.

## <a name="see-also"></a>Viz také
- [Měřená fakturace pro SaaS s využitím komerční služby měření na webu Marketplace](./partner-center-portal/saas-metered-billing.md)
- [Spravovaná fakturace podle objemu spravované aplikace](./partner-center-portal/azure-app-metered-billing.md)
