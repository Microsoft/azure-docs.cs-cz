---
title: Úprava publikovaného zařízení s certifikací Azure
description: Průvodce úpravou informací o zařízení po certifikaci a publikování zařízení prostřednictvím programu zařízení s certifikací Azure
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: ddd6a377b477f5895db797f6f4e4917d180e40de
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969231"
---
# <a name="edit-your-published-device"></a>Úprava publikovaného zařízení

Až bude vaše zařízení certifikováno a Publikováno v katalogu zařízení s certifikací Azure, možná budete muset aktualizovat podrobnosti o zařízení. Důvodem může být aktualizace vašeho distribučního seznamu, změny v adresách URL stránky nákupu nebo aktualizace specifikací hardwaru (například verze operačního systému nebo přidání nového dílu). Pomocí portálu zařízení Certified v Azure usnadňujeme aktualizace informací o zařízení, aniž by bylo potřeba produkt z našeho katalogu odebrat.

## <a name="prerequisites"></a>Požadavky

- Měli byste být přihlášeni a mít **schválený** projekt pro vaše zařízení na [portálu zařízení s certifikací Azure](https://certify.azure.com). Pokud nemáte certifikované zařízení, můžete si v tomto [kurzu](tutorial-01-creating-your-project.md) začít.

## <a name="editing-your-published-project"></a>Úprava publikovaného projektu

V souhrnu projektu byste si měli všimnout, že váš projekt je v režimu jen pro čtení, protože již byl zkontrolován a přijat. Chcete-li provést změny, budete muset požádat o úpravu projektu a nechat aktualizaci schválit tým certifikace Azure.

1. Klikněte na `Request Metadata Edit` tlačítko v horní části stránky.  

    ![Žádost o aktualizaci metadat](./media/images/request-metadata-edit.png)

1. Potvrďte oznámení na stránce, kterou budete potřebovat k odeslání produktu ke kontrole po jeho úpravách.
    > [!NOTE]
    > Potvrzením této úpravy **neodeberete** zařízení z katalogu zařízení s certifikací Azure, pokud už je publikovaný. Vaše předchozí verze produktu zůstane v katalogu, dokud znovu nepublikujete své zařízení.

1. Po potvrzení tohoto upozornění můžete upravit podrobnosti o zařízení. Nezapomeňte si nechat poznámku v `Comments for Reviewer` části `Device Details` o tom, co bylo změněno.

    ![Poznámka k úpravě metadat](./media/images/edit-notes.png)

1. Na stránce Souhrn projektu klikněte na položku `Submit for review` a proveďte změny schválené týmem certifikace Azure.
1. Po kontrole a schválení změn můžete změny znovu publikovat v katalogu přes portál (viz náš [kurz](./tutorial-04-publishing-your-device.md)).

## <a name="next-steps"></a>Další kroky

Právě jste úspěšně upravili vaše zařízení v katalogu zařízení s certifikací Azure. Můžete si zaregistrovat změny v katalogu nebo certifikovat jiné zařízení.
- [Katalog zařízení s certifikací Azure](https://devicecatalog.azure.com/)
- [Začínáme s certifikací zařízení](./tutorial-01-creating-your-project.md)
