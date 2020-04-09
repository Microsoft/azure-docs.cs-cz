---
title: Aktualizace existující nabídky modulu Azure IoT Edge | Azure Marketplace
description: Jak aktualizovat existující nabídku modulu IoT Edge na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 019711f35d249e6684013feddb6b453d509f66d4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985350"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aktualizace existující nabídky modulu IoT Edge

>[!Important]
>dubna 2020 začneme přesouvat správu nabídek modulu IoT Edge do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky modulu IoT Edge](https://aka.ms/AzureCreateIoT) pro správu migrovaných nabídek.

Tento článek prochází různé aspekty aktualizace nabídky modulu IoT Edge na [portálu partnerů cloudu](https://cloudpartner.azure.com/) a pak znovu publikování nabídky.

Nabídku můžete aktualizovat několika důvody, například:

-  Přidání nové verze bitové kopie modulu IoT Edge do existujících skum.
-  Přidání nových skum.
-  Aktualizace metadat marketplace pro nabídku nebo jednotlivé skum.

Portál nabízí funkce **Porovnat** a **Historie,** které vám pomohou s těmito úpravami.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Nepovolené změny nabídky modulu IoT Edge nebo skladové položky

Existují atributy nabídky modulu IoT Edge nebo skladové položky, které nelze změnit po nabídce je aktivní na Azure Marketplace. Nelze změnit následující nastavení:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **ID skladové položky** existujících skladových částek
-  Značky verzí, například:`1.0.1`
-  Změny fakturačního/licenčního modelu stávajících skutých.

## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující operace aktualizace jsou běžné.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aktualizace verze bitové kopie modulu IoT Edge pro skladovou položku

Je běžné, že bitová kopie modulu IoT Edge se pravidelně aktualizuje o opravy zabezpečení, další funkce a tak dále. V tomto scénáři chcete aktualizovat bitovou kopii modulu IoT Edge, na kterou vaše skladová položka odkazuje, pomocí následujících kroků:

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.

3.  Na kartě **Skladové položky** vyberte skladovou položku přidruženou k bitové kopii modulu IoT Edge, kterou chcete aktualizovat.

4.  V části **Obrázek modulu Okraj**vyberte **+ Nová verze obrazu** a přidejte nový obrázek modulu IoT Edge.

5.  Zadejte nové **verze bitové kopie modulu**IoT Edge . Verze bitové kopie musí dodržovat stejná pravidla pro značky jako předchozí verze. Značky verze by měly být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla. Ověřte, zda je nová verze, kterou zadáte, větší než všechny předchozí verze.

6.  Vyberte **Publikovat** a spusťte pracovní postup a publikujte novou verzi modulu IoT Edge na Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidání nové skladové položky

Pomocí následujících kroků můžete zpřístupnit novou skladovou položku pro vaši nabídku: 

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.

3.  Na kartě **Skladové položky** vyberte **Přidat novou skladovou položku** a ve vyskakovacím okně zadejte **ID skladové** položky.

4.  Znovu publikujte modul IoT Edge pomocí kroků popsaných v [části Publikování modulu IoT Edge na Azure Marketplace](./cpp-publish-offer.md).

5.  Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali novou skladovou položku.


### <a name="update-offer-marketplace-metadata"></a>Aktualizovat metadata tržiště

Pomocí následujících kroků aktualizujte metadata webu marketplace přidružená k vaší nabídce. (Například: název společnosti, loga atd.)

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.

3.  Přejděte na kartu **Marketplace.** [Publish an IoT Edge module to Azure Marketplace](./cpp-publish-offer.md)

4.  Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali změny.

## <a name="compare-feature"></a>Funkce porovnání

Když u publikované nabídky provedete změny, můžete pomocí funkce **Porovnat** auditovat provedené změny. 

**Použití funkce Porovnat:**

1.  Kdykoli v procesu úprav vyberte **porovnat** pro vaši nabídku.

    ![Tlačítko Porovnat funkci](./media/iot-edge-module-compare.png)


2.  Podívejte se na souběžné verze marketingových prostředků a metadat.


## <a name="history-of-publishing-actions"></a>Historie publikačních akcí

Chcete-li zobrazit historickou publikační aktivitu, vyberte kartu **Historie** na levém panelu nabídek cloudového partnerského portálu. Můžete vidět akce s časovým razítkem provedené během životnosti nabídek Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
