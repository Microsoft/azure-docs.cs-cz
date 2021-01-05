---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807803"
---
Pokud chcete načíst adresu URL SAS pro vlastní model školení, v Azure Portal klikněte na prostředek úložiště a vyberte kartu **Průzkumník služby Storage** . Přejděte do svého kontejneru, klikněte pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Je důležité získat SAS pro váš kontejner, ne pro samotný účet úložiště. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v oddílu **Adresa URL** do dočasného umístění. Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.