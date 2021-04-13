---
title: Program zařízení s certifikací Azure – kurz – testování zařízení
description: Podrobný průvodce testováním zařízení pomocí služby AICS na portálu zařízení s certifikací Azure
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310950"
---
# <a name="tutorial-test-and-submit-your-device"></a>Kurz: testování a odeslání zařízení

Další hlavní fáze certifikačního procesu (i když se dá dokončit před přidáním podrobností o zařízení) zahrnuje testování vašeho zařízení. Prostřednictvím portálu budete používat službu certifikace Azure IoT (AICS) k předvedení výkonu zařízení podle našich požadavků na certifikaci. Po úspěšném dokončení testovací fáze odešlete své zařízení k závěrečné kontrole a schválení certifikačním týmem Azure!

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení zařízení k IoT Hub pomocí služby Device Provisioning (DPS)
> * Testování zařízení podle vybraných certifikačních programů
> * Odešlete své zařízení ke kontrole certifikačního týmu Azure.

## <a name="prerequisites"></a>Požadavky

- Měli byste být přihlášeni a mít projekt pro vaše zařízení vytvořený na [portálu zařízení s certifikací Azure](https://certify.azure.com). Další informace najdete v tomto [kurzu](tutorial-01-creating-your-project.md).
- Volitelné Doporučujeme, abyste zařízení připravili a ručně ověřili jejich výkon podle požadavků na certifikaci. Důvodem je to, že pokud chcete znovu otestovat jiný kód zařízení nebo certifikační program, budete muset vytvořit nový projekt.

## <a name="connecting-your-device-using-dps"></a>Připojení zařízení pomocí DPS

Všechna certifikovaná zařízení jsou nutná k předvedení možnosti připojení k IoT Hub pomocí DPS. Následující kroky vás provedou procesem úspěšného připojení zařízení k testování na portálu.

1. Chcete-li zahájit fázi testování, vyberte `Connect & test` odkaz na stránce Souhrn projektu:  

    ![Odkaz pro připojení a testování](./media/images/connect-and-test-link.png)

1. V závislosti na vybraných certifikátech uvidíte požadované testy na stránce připojit & test. Přečtěte si tyto pokyny, abyste se ujistili, že budete používat správný certifikační program.  

    ![Stránka připojení a testování](./media/images/connect-and-test.png)

1. Připojte své zařízení k IoT Hub pomocí služby Device Provisioning (DPS). DPS podporuje možnosti připojení symetrických klíčů, certifikace X. 509 a čipu TPM (Trusted Platform Module). To je vyžadováno pro všechny certifikace.

    - *Další informace o připojení zařízení k Azure IoT Hub s DPS najdete v článku [zřizování zařízení přehled](../iot-dps/about-iot-dps.md "Služba Device Provisioning – přehled").*
    
1. Pokud používáte symetrické klíče, budete požádáni o konfiguraci DPS pomocí zadaného oboru ID DPS, ID zařízení, ověřovacího klíče a koncového bodu DPS. V opačném případě budete požádáni o zadání certifikátu X. 509 nebo ověřovacího klíče.

1. Po konfiguraci zařízení pomocí DPS potvrďte připojení kliknutím na `Connect` tlačítko v dolní části stránky. Po úspěšném připojení můžete kliknutím na tlačítko Přejít ke fázi testování `Next` .  

    ![Připojit a testovat připojení](./media/images/connected.png)

## <a name="testing-your-device"></a>Testování zařízení

Po úspěšném připojení zařízení k AICS jste teď připraveni spustit testy pro certifikace, které jsou specifické pro certifikační program, který používáte pro.

1. **Pro certifikaci zařízení s certifikací Azure**: na kartě vyberte možnosti zařízení zkontrolujete a vyberete, které testy chcete na svém zařízení spustit.
1. **Pro certifikaci IoT technologie Plug and Play**: pečlivě zkontrolujte parametry, které se zkontrolují během testu deklarovaného v modelu zařízení.
1. **Pro certifikaci spravovaná přes Edge**: nejsou potřeba žádné další kroky nad rámec demonstrace připojení.
1. Až dokončíte potřebné přípravy pro zadaný certifikační program, vyberte možnost `Next` pokračovat do fáze test.
1. Na stránce vyberte, pokud `Run tests` chcete začít používat AICS s vaším zařízením.
1. Jakmile obdržíte oznámení, že jste prošli testy, vyberte, jestli se chcete `Finish` vrátit na stránku souhrnu.

![Test byl úspěšný](./media/images/test-pass.png)

7. Pokud máte další dotazy nebo potřebujete pomoc při řešení potíží s AICS, navštivte naši příručku pro řešení potíží.

> [!NOTE]
> I když budete moct dokončit online proces certifikace pro IoT technologie Plug and Play a hranu, která bude spravovaná bez nutnosti odeslat zařízení na ruční kontrolu, můžete se obrátit na člena týmu s certifikací Azure Certified pro další ověřování zařízení, které překračuje testování prostřednictvím naší služby Automation.

## <a name="submitting-your-device-for-review"></a>Odesílá se zařízení ke kontrole.

Až dokončíte všechna povinná pole v části Podrobnosti o zařízení a úspěšně jste prošli automatizovaným testováním v procesu připojit & test, teď můžete informovat tým certifikovaný pro Azure, že jste připraveni na kontrolu certifikace.

1. Vyberte `Submit for review` na stránce Souhrn projektu:  

    ![Ověřit a certifikovat odkaz](./media/images/review-and-certify.png)

1. Potvrďte odeslání v místním okně. Po odeslání zařízení budou všechny podrobnosti o zařízení jen pro čtení, dokud se nepožadují úpravy. ( [Informace o tom, jak upravit informace o zařízení po publikování](./how-to-edit-published-device.md).)  

    ![Dialog spustit kontrolu certifikace](./media/images/start-certification-review.png)

1. Po odeslání projektu bude stránka souhrnu projektu označovat, že projekt je `Under Certification Review` Certifikační tým Azure:  

    ![V části Revize](./media/images/review-and-certify-under-review.png)

1. Do 5-7 pracovních dnů můžete očekávat e-mailovou odpověď od certifikačního týmu Azure na adresu poskytnutou v profilu vaší společnosti, která se týká stavu odeslání vašeho zařízení.

    - Schválené odeslání  
        Po kontrole a schválení projektu vám přijde e-mail. E-mail bude obsahovat sadu souborů, včetně označení zařízení s certifikací Azure Certified, používání BADGE a další informace o tom, jak rozšířit zprávu, že je zařízení certifikováno. Gratulujeme!

    - Čeká na odeslání  
        V případě, že váš projekt není schválený, budete moci provádět změny v podrobnostech projektu a pak znovu odeslat zařízení k certifikaci, jakmile bude připraven. Pošle se e-mail s informacemi o tom, proč se projekt neschválil, a provede kroky k opětovnému odeslání k certifikaci.

## <a name="next-steps"></a>Další kroky

Gratulujeme! Vaše zařízení nyní úspěšně prošlo všemi testy a bylo schváleno prostřednictvím programu zařízení s certifikací Azure Certified. Teď můžete své zařízení publikovat do katalogu zařízení s certifikací Azure Certified, kde si zákazníci můžou koupit své produkty s jistotou v jejich výkonu s Azure.
> [!div class="nextstepaction"]
> [Kurz: publikování zařízení](tutorial-04-publishing-your-device.md)

