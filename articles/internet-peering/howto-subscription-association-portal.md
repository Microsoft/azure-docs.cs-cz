---
title: Přidružení partnerského čísla ASN k předplatnému Azure s využitím portálu
titleSuffix: Azure
description: Přidružení partnerského čísla ASN k předplatnému Azure s využitím portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912148"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Přidružení partnerského čísla ASN k předplatnému Azure s využitím portálu

Před odesláním požadavku partnerského vztahu byste měli nejprve přidružit asn s předplatným Azure pomocí následujících kroků.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Vytvoření peerasn u družit vaše ASN s předplatným Azure

### <a name="sign-in-to-the-portal"></a>Přihlášení k portálu
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Zaregistrovat se pro zprostředkovatele prostředků partnerského vztahu
Zaregistrujte se pro zprostředkovatele partnerského vztahu v předplatném podle následujících kroků. Pokud to neprovedete, prostředky Azure potřebné k nastavení partnerského vztahu nejsou přístupné.

1. Klikněte na **Odběry** v levém horním rohu portálu. Pokud ji nevidíte, klikněte na **Další služby** a vyhledejte ji.

    > [!div class="mx-imgBorder"]
    > ![Otevřená předplatná](./media/rp-subscriptions-open.png)

1. Klikněte na předplatné, které chcete použít pro partnerský vztah.

    > [!div class="mx-imgBorder"]
    > ![Spuštění předplatného](./media/rp-subscriptions-launch.png)

1. Po otevření předplatného klikněte vlevo na **Poskytovatele prostředků**. Potom v pravém podokně vyhledejte *partnerský vztah* v okně hledání nebo pomocí posuvníku vyhledejte **Microsoft.Peering** a podívejte se na **stav**. Pokud je stav ***Registrováno***, přeskočte níže uvedené kroky a přejděte k části **Vytvořit PeerAsn**. Pokud je stav ***NotRegistered***, vyberte **Microsoft.Peering** a klikněte na **Registrovat**.

    > [!div class="mx-imgBorder"]
    > ![Začátek registrace](./media/rp-register-start.png)

1. Všimněte si, že stav se změní na ***Registrace***.

    > [!div class="mx-imgBorder"]
    > ![Probíhá registrace](./media/rp-register-progress.png)

1. Počkejte na min nebo tak pro to k dokončení registrace. Poté klikněte na **tlačítko Aktualizovat** a ověřte, zda je stav ***registrován***.

    > [!div class="mx-imgBorder"]
    > ![Registrace dokončena](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Vytvořit PeerAsn
Můžete vytvořit nový prostředek PeerAsn pro přidružení číslo autonomního systému (ASN) s předplatným Azure. K předplatnému můžete přidružit více seznamů ASN vytvořením **programu PeerAsn** pro každý asn, který potřebujete přidružit.

1. Klikněte **na Vytvořit zdroj** > **Zobrazit vše**.

    > [!div class="mx-imgBorder"]
    > ![Hledat PeerAsn](./media/peerasn-seeall.png)

1. Ve vyhledávacím poli *vyhledejte funkci PeerAsn* a na klávesnici stiskněte *enter.* Z výsledků klikněte na **zdroj PeerAsn.**

    > [!div class="mx-imgBorder"]
    > ![Spuštění PeerAsn](./media/peerasn-launch.png)

1. Po spuštění **peerasn** klepněte na **tlačítko Vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Vytvořit PeerAsn](./media/peerasn-create.png)

1. Na stránce **Přidružit obrázek ASN partnera** v části **Základy** vyplňte pole, jak je znázorněno níže.

    > [!div class="mx-imgBorder"]
    > ![Karta Základy PeerAsn](./media/peerasn-basics-tab.png)

    * **Název** odpovídá názvu prostředku a může být cokoli, co si vyberete.  
    * Zvolte **předplatné,** které potřebujete přidružit ASN.
    * **Název partnera** odpovídá názvu vaší společnosti a musí být co nejblíže profilu PeeringDB. Všimněte si, že hodnota podporuje pouze znaky a-z, A-Z a mezery
    * Do pole **Asn partnera** zadejte svůj asn.
    * Klikněte na **Vytvořit novou** a zadejte **e-mailovou adresu** a telefonní **číslo** pro vaše network operations center (NOC)
1. Poté klikněte na **tlačítko Revize + vytvořit** a sledovat, že portál spouští základní ověření zadaných informací. Tohle je nahoře zobrazeno na pásu karet, jako *Spuštěné konečné ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta PeerAsn Review](./media/peerasn-review-tab-validation.png)

1. Jakmile se zpráva na pásu karet změní na *Ověření předáno*, ověřte své informace a odešlete žádost kliknutím na **Vytvořit**. Pokud ověření neprojde, klikněte na **Předchozí** a opakujte výše uvedené kroky, abyste změnili požadavek a zajistili, že zadávané hodnoty nebudou mít žádné chyby.

    > [!div class="mx-imgBorder"]
    > ![Karta PeerAsn Review](./media/peerasn-review-tab.png)

1. Po odeslání žádosti počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí níže.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn Úspěch](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Zobrazit stav peerasnu
Po úspěšném nasazení prostředku PeerAsn budete muset počkat, až společnost Microsoft schválí žádost o přidružení. Schválení může trvat až 12 hodin. Po schválení obdržíte oznámení na e-mailovou adresu uvedenou ve výše uvedené části.

> [!IMPORTANT]
> Před odesláním požadavku partnerského vztahu počkejte na stav ValidationState, který se změní na "Schváleno". Toto schválení může trvat až 12 hodin.

## <a name="modify-peerasn"></a>Upravit PeerAsn
Úprava PeerAsn není aktuálně podporována. Pokud potřebujete provést úpravu, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Odstranit PeerAsn
Odstranění peerasn není aktuálně podporováno. Pokud potřebujete odstranit PeerAsn, obraťte se na [partnerský vztah microsoftu](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava peeringu typu Direct s využitím portálu](howto-direct-portal.md)
* [Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu](howto-legacy-direct-portal.md)
* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](howto-exchange-portal.md)
* [Převod starší verze peeringu typu Exchange na prostředek Azure s využitím portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)