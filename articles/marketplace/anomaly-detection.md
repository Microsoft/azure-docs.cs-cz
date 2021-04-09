---
title: Správa anomálií s měřením fakturace v partnerském centru | Azure Marketplace
description: Přečtěte si, jak Automatická detekce anomálií pro účtované náklady pomáhá zajistit, aby se vaši zákazníci správně účtovali na měřené využití vašich komerčních nabídek na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092517"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>Správa anomálií s měřením fakturace v partnerském centru

Možnost vlastního měření podle objemu dat je teď k dispozici pro nabídky [software jako služba](plan-saas-offer.md) (SaaS) a [aplikace Azure](plan-azure-application-offer.md#types-of-plans) s plánem spravované aplikace.

Pokud používáte možnost účtovaného účtování k vytváření nabídek v programu komerčního tržiště, který vám umožní účtovat využití na základě nestandardních jednotek, musíte znát, kdy zákazník použil více služeb, než se očekávalo.

## <a name="use-the-anomaly-detection-feature"></a>Použití funkce detekce anomálií

Společnost Microsoft se spoléhá na vás, partner, aby vyhlásil nadlimitní využití svých SaaS nebo spravovaných aplikací Azure před tím, než společnost Microsoft fakturuje zákazníka. Pokud je hlášeno nesprávné použití, zákazník by potenciálně mohl získat nesprávnou fakturu, může podvést i důvěryhodnost partnera.

Aby bylo zajištěno, že se vaši zákazníci účtují správně, použijte funkci **detekce anomálií** pro aplikace SaaS i plány spravované aplikace Azure. Tato funkce monitoruje využití na základě účtovaného měření a předpovídá očekávanou hodnotu využití v očekávaném rozsahu. Pokud je využití mimo očekávaný rozsah, je považováno za neočekávané, (anomálii) a na stránce s přehledem vaší nabídky v programu komerčního portálu pro partnery partnera obdržíte oznámení výstrah. Využití vašich zákazníků můžete sledovat každý den v každé vlastní dimenzi měřičů, kterou jste nastavili.

## <a name="view-and-manage-metered-usage-anomalies"></a>Zobrazení a Správa anomálií měřeného využití

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
- [Služba detekce anomálií pro fakturaci na základě měření](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
