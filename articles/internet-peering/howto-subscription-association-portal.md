---
title: Přidružení partnerského čísla ASN k předplatnému Azure pomocí portálu
titleSuffix: Azure
description: Přidružení partnerského čísla ASN k předplatnému Azure pomocí portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912148"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Přidružení partnerského čísla ASN k předplatnému Azure pomocí portálu

Před odesláním žádosti o vytvoření partnerského vztahu byste nejdřív měli přidružit své ASN k předplatnému Azure pomocí následujících kroků.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Vytvoření PeerAsn k přidružení vašeho ASN k předplatnému Azure

### <a name="sign-in-to-the-portal"></a>Přihlásit se k portálu
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registrace pro poskytovatele prostředků partnerského vztahu
Pomocí následujícího postupu zaregistrujte pro poskytovatele prostředků partnerského vztahu v předplatném. Pokud to neprovedete, prostředky Azure potřebné k nastavení partnerského vztahu nejsou přístupné.

1. V levém horním rohu portálu klikněte na **odběry** . Pokud ho nevidíte, klikněte na **Další služby** a vyhledejte ho.

    > [!div class="mx-imgBorder"]
    > ![otevření předplatných](./media/rp-subscriptions-open.png)

1. Klikněte na předplatné, které chcete použít pro partnerský vztah.

    > [!div class="mx-imgBorder"]
    > ![spustit odběr](./media/rp-subscriptions-launch.png)

1. Po otevření předplatného klikněte na levé straně na **poskytovatelé prostředků**. Pak v pravém podokně vyhledejte *partnerské vztahy* v okně hledání nebo pomocí posuvníku Najděte **Microsoft. peering** a podívejte se na **stav**. Pokud je stav ***zaregistrován***, přeskočte následující postup a přejděte k části **Vytvoření PeerAsn**. Pokud je stav ***NotRegistered***, vyberte **Microsoft. peering** a klikněte na **zaregistrovat**.

    > [!div class="mx-imgBorder"]
    > ![zahájení registrace](./media/rp-register-start.png)

1. Všimněte si, že se stav změní na ***registrování***.

    > [!div class="mx-imgBorder"]
    > Probíhá registrace ![](./media/rp-register-progress.png)

1. Počkejte na minimální nebo tak, aby se registrace dokončila. Pak klikněte na **aktualizovat** a ověřte, jestli je stav ***zaregistrovaný***.

    > [!div class="mx-imgBorder"]
    > Registrace ![dokončena](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Vytvořit PeerAsn
Můžete vytvořit nový prostředek PeerAsn pro přiřazení čísla autonomního systému (ASN) k předplatnému Azure. K předplatnému můžete přidružit více čísla ASN vytvořením **PeerAsn** pro každé číslo ASN, které potřebujete přidružit.

1. Klikněte na **vytvořit prostředek** > **Zobrazit vše**.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn hledání](./media/peerasn-seeall.png)

1. Do vyhledávacího pole vyhledejte *PeerAsn* a stiskněte *ENTER* na klávesnici. Z výsledků klikněte na prostředek **PeerAsn** .

    > [!div class="mx-imgBorder"]
    > ![spustit PeerAsn](./media/peerasn-launch.png)

1. Po spuštění **PeerAsn** klikněte na **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![vytvoření](./media/peerasn-create.png) PeerAsn

1. Na stránce **přidružit partnerské číslo ASN** na kartě **základy** vyplňte pole, jak vidíte níže.

    > [!div class="mx-imgBorder"]
    > Karta základy PeerAsn ![](./media/peerasn-basics-tab.png)

    * **Název** odpovídá názvu prostředku a může to být cokoli, co si zvolíte.  
    * Vyberte **předplatné** , ke kterému potřebujete přidružit číslo ASN.
    * Název **partnerského** zařízení odpovídá názvu vaší společnosti a musí být co nejblíže vašemu PeeringDB profilu. Všimněte si, že hodnota podporuje jenom znaky a – z, a – Z a mezera.
    * Do pole **ASN partnerského** zařízení zadejte své ASN.
    * Klikněte na **vytvořit nový** a zadejte **e-mailovou adresu** a **telefonní číslo** pro vaše síťové provozní centrum (noc).
1. Pak klikněte na **zkontrolovat + vytvořit** a sledujte, že na portálu běží základní ověřování informací, které jste zadali. Zobrazuje se na pásu karet nahoře a *spouští se konečné ověření...* .

    > [!div class="mx-imgBorder"]
    > Karta Kontrola ![PeerAsn](./media/peerasn-review-tab-validation.png)

1. Jakmile se zpráva na pásu karet změní na *prošlé*, ověřte informace a odešlete žádost kliknutím na **vytvořit**. Pokud ověření neprojde, klikněte na **Předchozí** a Zopakováním výše uvedených kroků upravte svůj požadavek a ujistěte se, že hodnoty, které zadáte, nemají žádné chyby.

    > [!div class="mx-imgBorder"]
    > Karta Kontrola ![PeerAsn](./media/peerasn-review-tab.png)

1. Po odeslání žádosti počkejte, než se dokončí nasazení. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí jako níže.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn byla úspěšná](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Zobrazit stav PeerAsn
Po úspěšném nasazení PeerAsn prostředku budete muset počkat, až společnost Microsoft schválí žádost o přidružení. Schválení může trvat až 12 hodin. Po schválení obdržíte e-mailovou adresu zadanou v předchozím oddílu oznámení.

> [!IMPORTANT]
> Před odesláním žádosti o partnerský vztah počkejte, než ValidationState zapnete "Schváleno". Toto schválení může trvat až 12 hodin.

## <a name="modify-peerasn"></a>Upravit PeerAsn
Úpravy PeerAsn se v tuto chvíli nepodporují. Pokud potřebujete změnit, obraťte se na [partnerský vztah Microsoftu](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Odstranit PeerAsn
Odstranění PeerAsn se v tuto chvíli nepodporuje. Pokud potřebujete odstranit PeerAsn, obraťte se na [partnerský vztah Microsoftu](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
* [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md)
* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
* [Převod starší verze serveru Exchange peering na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje informací:

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .