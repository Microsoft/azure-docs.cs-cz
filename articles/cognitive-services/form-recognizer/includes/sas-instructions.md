---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505320"
---
Pokud chcete načíst adresu URL SAS pro vlastní model školení, v Azure Portal klikněte na prostředek úložiště a vyberte kartu **Průzkumník služby Storage** . Přejděte do svého kontejneru, klikněte pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Je důležité získat SAS pro váš kontejner, ne pro samotný účet úložiště. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v oddílu **Adresa URL** do dočasného umístění. Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![alternativní text](../media/quickstarts/get-sas-url.png)