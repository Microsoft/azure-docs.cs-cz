---
title: Postup testování a publikování nabídky SaaS na komerčním webu Microsoft Marketplace
description: Pomocí partnerského centra můžete odeslat nabídku SaaS do verze Preview, zobrazit náhled nabídky, otestovat ji a pak ji publikovat na komerčním webu Microsoft Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: bef59ea9e7de77e7f9a80cc3950762ea70238b87
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380735"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Postup testování a publikování nabídky SaaS na komerčním webu Marketplace

Tento článek vysvětluje, jak pomocí partnerského centra odeslat nabídku SaaS k publikování, zobrazit náhled vaší nabídky, otestovat ji a potom ji publikovat na komerčním webu Marketplace. Musíte již mít vytvořenou nabídku, kterou chcete publikovat.

## <a name="submit-your-offer-for-publishing"></a>Odeslání nabídky k publikování

1. Přihlaste se na řídicí panel komerčního tržiště v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Na stránce **Přehled** vyberte nabídku, kterou chcete publikovat.
1. V pravém horním rohu portálu vyberte **zkontrolovat a publikovat**.
2. Ujistěte se, že se sloupec **stav** pro každou stránku **dokončí**. Tři možné stavy jsou následující:

   - **Nespuštěno** – stránka je neúplná.
   - **Nedokončeno** – stránka neobsahuje požadované informace nebo obsahuje chyby, které je třeba opravit. Budete se muset vrátit na stránku a aktualizovat ji.
   - **Dokončit** – stránka je dokončená. Byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby.

1. Pokud má kterákoli z těchto stránek stav jiný než **úplný**, vyberte název stránky, opravte problém, uložte stránku a pak znovu vyberte **zkontrolovat a publikovat** , abyste se vrátili na tuto stránku.
1. Až budou všechny stránky dokončené, poskytněte v poli **poznámky k certifikaci** pokyny k testování certifikačního týmu, aby se zajistilo správné testování vaší aplikace. Poskytněte jakékoli doplňkové poznámky užitečné pro porozumění vaší aplikaci.
1. Chcete-li spustit proces publikování pro vaši nabídku, vyberte možnost **publikovat**. Zobrazí se stránka s **přehledem nabídky** a zobrazí se **stav publikování**nabídky.

Stav publikování vaší nabídky se změní při přesunu prostřednictvím procesu publikování. Podrobné informace o tomto procesu najdete v tématu [Postup ověření a publikování](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Náhled a otestování vaší nabídky

Po přípravě nabídky na vaše přihlášení vám pošleme e-mail s žádostí o kontrolu a schválení vaší nabídky Preview. Stránku s **přehledem nabídky** můžete také aktualizovat v prohlížeči, abyste viděli, jestli vaše nabídka dosáhla fáze pro odhlášení vydavatele. Pokud má, budou k dispozici tlačítka **Přejít živě** a náhled. V závislosti na možnostech, které jste zvolili při vytváření vaší nabídky, se zobrazí odkaz pro Microsoft AppSource Preview, Azure Marketplace Preview nebo obojí. Pokud jste se rozhodli prodat nabídku prostřednictvím Microsoftu, může kdokoli, kdo byl přidán do cílové skupiny Preview, otestovat pořízení a nasazení vaší nabídky, aby se zajistilo, že splní vaše požadavky v této fázi.

Na následujícím snímku obrazovky vidíte stránku s **přehledem nabídky** pro nabídku SaaS, která obsahuje dvě odkazy ve verzi Preview pod tlačítkem **Přejít živě** . Postup ověření, který se zobrazí na této stránce, se liší v závislosti na výběrech, které jste provedli při vytváření této nabídky.

![Popisuje stránku s přehledem nabídky pro nabídku v partnerském centru. Zobrazí se odkazy na tlačítko Přejít živě a náhled.](media/publish-status-publisher-signoff.png)

K zobrazení náhledu vaší nabídky použijte následující postup.

1. Na stránce **Přehled nabídky** vyberte odkaz ve verzi Preview pod tlačítkem **Přejít na Live** . 

1. Pokud chcete ověřit kompletní tok nákupu a nastavení, kupte si nabídku, zatímco je ve verzi Preview. Nejdřív upozorněte Microsoft [lístek podpory](https://aka.ms/marketplacesupport) , aby se zajistilo, že nebudeme zpracovávat poplatky.

1. Pokud vaše nabídka SaaS podporuje [účtované měření pomocí komerční služby měření na webu Marketplace](./partner-center-portal/saas-metered-billing.md), Projděte si osvědčené postupy testování, které jsou popsané v části [rozhraní API pro fakturaci s měřením na webu Marketplace](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Přečtěte si pokyny k testování v tématu [rozhraní API pro splnění SaaS verze 2 na webu Microsoft Commercial Marketplace](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) a ujistěte se, že se vaše nabídka úspěšně integruje s rozhraními API předtím, než publikujete nabídku živě.

1. Pokud potřebujete provést změny po zobrazení náhledu a testování nabídky, můžete je upravit a znovu odeslat pro publikování nové verze Preview. Další informace najdete v tématu [aktualizace stávající nabídky na komerčním webu Marketplace](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publikování vaší nabídky živě

Po dokončení všech testů ve verzi Preview vyberte možnost **Přejít do živého** publikování vaší nabídky na komerčním webu Marketplace.

   > [!TIP]
   > Pokud je vaše nabídka na komerčním webu Marketplace už živá, žádné aktualizace, které provedete, nebudou aktivní, dokud nevyberete možnost **Spustit**jako.

Teď, když jste si zvolili, aby vaše nabídka byla dostupná na komerčním webu Marketplace, provedeme celou řadu konečných kontrol ověření, abyste zajistili, že je živá nabídka nakonfigurovaná stejně jako verze Preview nabídky. Podrobnosti o těchto kontrolách ověřování najdete v tématu [fáze publikování](review-publish-offer.md#publish-phase).

Po dokončení těchto kontrol budou vaše nabídka na webu Marketplace živá.

## <a name="next-step"></a>Další krok

[Přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md)