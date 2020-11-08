---
title: Postup testování a publikování nabídky aplikací Azure
description: Pomocí partnerského centra můžete odeslat nabídku aplikace Azure do verze Preview, zobrazit náhled nabídky, otestovat ji a potom ji publikovat na komerčním webu Microsoft Marketplace.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: b54e965188be51ec54110bb85d8cda8f01256836
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370077"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Postup testování a publikování nabídky aplikací Azure

Tento článek vysvětluje, jak pomocí partnerského centra odeslat nabídku aplikace Azure pro publikování, zobrazit náhled vaší nabídky, otestovat ji a potom ji publikovat na komerčním webu Marketplace. Musíte již mít vytvořenou nabídku, kterou chcete publikovat.

## <a name="submit-your-offer-for-publishing"></a>Odeslání nabídky k publikování

1. Přihlaste se na řídicí panel komerčního tržiště v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Na stránce **Přehled** vyberte nabídku, kterou chcete publikovat.
1. V pravém horním rohu portálu vyberte **zkontrolovat a publikovat**.
1. Ujistěte se, že se sloupec **stav** pro každou stránku **dokončí**. Tři možné stavy jsou následující:
    - **Nespuštěno** – stránka je neúplná.
    - **Nedokončeno** – stránka neobsahuje požadované informace nebo obsahuje chyby, které je třeba opravit. Budete se muset vrátit na stránku a aktualizovat ji.
    - **Dokončit** – stránka je dokončená. Byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby.
1. Pokud má kterákoli z těchto stránek stav jiný než **úplný** , vyberte název stránky, opravte problém, uložte stránku a pak znovu vyberte **zkontrolovat a publikovat** , abyste se vrátili na tuto stránku.
1. Až budou všechny stránky dokončené, poskytněte v poli **poznámky k certifikaci** pokyny k testování certifikačního týmu, aby se zajistilo správné testování vaší aplikace. Poskytněte jakékoli doplňkové poznámky užitečné pro porozumění vaší aplikaci.
1. Chcete-li spustit proces publikování pro vaši nabídku, vyberte možnost **publikovat**. Zobrazí se stránka s **přehledem nabídky** a zobrazí se **stav publikování** nabídky.

Stav publikování vaší nabídky se změní při přesunu prostřednictvím procesu publikování. Podrobné informace o tomto procesu najdete v tématu [Postup ověření a publikování](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Náhled a otestování vaší nabídky

Po přípravě nabídky na vaše přihlášení vám pošleme e-mail s žádostí o kontrolu a schválení vaší nabídky Preview. Stránku s **přehledem nabídky** můžete také aktualizovat v prohlížeči, abyste viděli, jestli vaše nabídka dosáhla fáze pro odhlášení vydavatele. Pokud má, bude k dispozici tlačítko **Přejít živě** a odkaz na verzi Preview. Pokud jste se rozhodli prodat nabídku prostřednictvím Microsoftu, může kdokoli, kdo byl přidán do cílové skupiny Preview, otestovat pořízení a nasazení vaší nabídky, aby se zajistilo, že splní vaše požadavky v této fázi.

Na následujícím snímku obrazovky se zobrazí stránka s **přehledem nabídky** pro nabídku se dvěma odkazy ve verzi Preview pod tlačítkem **Přejít živě** . Postup ověření, který se zobrazí na této stránce, se liší v závislosti na výběrech, které jste provedli při vytváření této nabídky.

[![Popisuje stránku s přehledem nabídky pro nabídku v partnerském centru. Zobrazí se odkazy na tlačítko Přejít živě a náhled.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

K zobrazení náhledu vaší nabídky použijte následující postup.

1. Na stránce **Přehled nabídky** vyberte odkaz ve verzi Preview pod tlačítkem **Přejít na Live** . 

1. Pokud chcete ověřit kompletní tok nákupu a nastavení, kupte si nabídku, zatímco je ve verzi Preview. Nejdřív upozorněte Microsoft [lístek podpory](https://aka.ms/marketplacesupport) , aby se zajistilo, že nebudeme zpracovávat poplatky.

1. Pokud vaše aplikace Azure podporuje [účtované měření pomocí komerční služby měření na webu Marketplace](./partner-center-portal/azure-app-metered-billing.md), Projděte si osvědčené postupy testování, které jsou popsané v části [rozhraní API pro fakturaci s měřením na webu Marketplace](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Pokud potřebujete provést změny po zobrazení náhledu a testování nabídky, můžete je upravit a znovu odeslat pro publikování nové verze Preview. Další informace najdete v tématu [aktualizace stávající nabídky na komerčním webu Marketplace](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publikování vaší nabídky živě

Po dokončení všech testů ve verzi Preview vyberte možnost **Přejít do živého** publikování vaší nabídky na komerčním webu Marketplace.

   > [!TIP]
   > Pokud je vaše nabídka na komerčním webu Marketplace už živá, žádné aktualizace, které provedete, nebudou aktivní, dokud nevyberete možnost **Spustit** jako.

Teď, když jste si zvolili, aby vaše nabídka byla dostupná na komerčním webu Marketplace, provedeme celou řadu konečných kontrol ověření, abyste zajistili, že je živá nabídka nakonfigurovaná stejně jako verze Preview nabídky. Podrobnosti o těchto kontrolách ověřování najdete v tématu [fáze publikování](review-publish-offer.md#publish-phase).

Po dokončení těchto kontrol budou vaše nabídka na webu Marketplace živá.

### <a name="errors-and-review-feedback"></a>Chyby a kontrola zpětné vazby

Krok **ručního ověření** v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a její přidružené technické prostředky (zejména šablonu Azure Resource Manager) jsou obvykle prezentovány jako odkazy žádosti o přijetí změn (PR). Vysvětlení, jak zobrazit tyto PR a reagovat na ně, najdete v tématu [zpracování zpětné vazby na revizi](partner-center-portal/azure-apps-review-feedback.md).

Pokud máte v jednom nebo několika krocích publikování chyby, opravte je ještě před opětovným publikováním vaší nabídky.

## <a name="next-step"></a>Další krok

- [Přístup k analytickým sestavám pro komerční tržiště v partnerském centru](partner-center-portal/analytics.md)
- Naučte se, [jak nabízet nabídku aplikací Azure](create-new-azure-apps-offer-marketing.md) prostřednictvím společného prodeje s Microsoftem a prodávat prostřednictvím programů CSP.
