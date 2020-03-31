---
title: Staňte se místním partnerem kodéru – Azure Media Services
description: Tento článek popisuje, jak ověřit místní živé streamování kodérů.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298627"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Jak ověřit místní kodér živého streamování

Jako místní partner kodéru Mediálních služeb Azure propaguje Mediální služby váš produkt tím, že doporučuje váš kodér podnikovým zákazníkům. Chcete-li se stát partnerem místního kodéru, musíte ověřit kompatibilitu místního kodéru se službami Media Services. Chcete-li tak učinit, vyplňte následující ověření.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Předávací živé ověření události

1. V účtu Media Services zkontrolujte, zda je spuštěn **koncový bod streamování.** 
2. Vytvořte a spusťte **předávací** živou událost. <br/> Další informace naleznete v tématu [Stavy živých událostí a fakturace](live-event-states-billing.md).
3. Získejte ingestující adresy URL a nakonfigurujte místní kodér tak, aby používal adresu URL k odeslání živého datového proudu s více přenosovými rychlostmi do služby Media Services.
4. Získejte adresu URL náhledu a použijte ji k ověření, že vstup z kodéru je skutečně přijímán.
5. Vytvořte nový objekt **Asset.**
6. Vytvořte **živý výstup** a použijte název datového zdroje, který jste vytvořili.
7. Vytvořte **lokátor streamování** s předdefinovanými typy **zásad streamování.**
8. Seznam cest na **streamování Lokátor** získat zpět adresy URL použít.
9. Získejte název hostitele pro **koncový bod streamování,** ze kterého chcete streamovat.
10. Zkombinujte adresu URL z kroku 8 s názvem hostitele v kroku 9, abyste získali úplnou adresu URL.
11. Spusťte živý kodér po dobu přibližně 10 minut.
12. Zastavte živou událost. 
13. Pomocí přehrávače, jako je [Azure Media Player,](https://aka.ms/azuremediaplayer) můžete archivovaný datový zdroj sledovat, abyste zajistili, že přehrávání nebude mít žádné viditelné závady na všech úrovních kvality. Nebo sledujte a ověřujte prostřednictvím adresy URL náhledu během živé relace.
14. Zaznamenejte ID datového zdroje, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi použitou z vašeho živého kodéru.
15. Po vytvoření každé ukázky obnovte stav živé události.
16. Opakujte kroky 5 až 15 pro všechny konfigurace podporované kodérem (s signalizací reklamy a bez něj, titulky nebo různé rychlosti kódování).

## <a name="live-encoding-live-event-verification"></a>Živé kódování live event ověření

1. V účtu Media Services zkontrolujte, zda je spuštěn **koncový bod streamování.** 
2. Vytvořte a spusťte **živé kódování** živé události. <br/> Další informace naleznete v tématu [Stavy živých událostí a fakturace](live-event-states-billing.md).
3. Získejte ingestované adresy URL a nakonfigurujte kodér tak, aby přenášel živý datový proud s jedním datovým tokem do služby Media Services.
4. Získejte adresu URL náhledu a použijte ji k ověření, že vstup z kodéru je skutečně přijímán.
5. Vytvořte nový objekt **Asset.**
6. Vytvořte **živý výstup** a použijte název datového zdroje, který jste vytvořili.
7. Vytvořte **lokátor streamování** s předdefinovanými typy **zásad streamování.**
8. Seznam cest na **streamování Lokátor** získat zpět adresy URL použít.
9. Získejte název hostitele pro **koncový bod streamování,** ze kterého chcete streamovat.
10. Zkombinujte adresu URL z kroku 8 s názvem hostitele v kroku 9, abyste získali úplnou adresu URL.
11. Spusťte živý kodér po dobu přibližně 10 minut.
12. Zastavte živou událost.
13. Pomocí přehrávače, jako je [Azure Media Player,](https://aka.ms/azuremediaplayer) můžete archivovaný datový zdroj sledovat, abyste zajistili, že přehrávání nebude mít žádné viditelné závady na všech úrovních kvality. Nebo sledujte a ověřujte prostřednictvím adresy URL náhledu během živé relace.
14. Zaznamenejte ID datového zdroje, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi použitou z vašeho živého kodéru.
15. Po vytvoření každé ukázky obnovte stav živé události.
16. Opakujte kroky 5 až 15 pro všechny konfigurace podporované kodérem (s signalizací reklamy a bez něj, titulky nebo různé rychlosti kódování).

## <a name="longevity-verification"></a>Ověření dlouhověkosti

Postupujte podle stejných kroků jako při [ověřování předávaných živých událostí](#pass-through-live-event-verification) s výjimkou kroku 11. <br/>Místo 10 minut spusťte živý kodér po dobu jednoho týdne nebo déle. Pomocí přehrávače, jako je [Azure Media Player,](https://aka.ms/azuremediaplayer) můžete čas od času sledovat živé vysílání (nebo archivovaný datový zdroj), abyste zajistili, že přehrávání nebude mít žádné viditelné závady.

## <a name="email-your-recorded-settings"></a>Odeslání zaznamenaného nastavení e-mailem

Nakonec pošlete e-mailem zaznamenané nastavení a amshelp@microsoft.com parametry živého archivu službě Azure Media Services jako oznámení, že všechny kontroly vlastního ověření prošly. Uveďte také své kontaktní údaje pro případné následné sledování. S případnými dotazy týkajícími se tohoto procesu můžete kontaktovat tým Mediálních služeb Azure.

## <a name="see-also"></a>Viz také

[Testované místní kodéry](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Další kroky

[Živé vysílání pomocí mediálních služeb v3](live-streaming-overview.md)
