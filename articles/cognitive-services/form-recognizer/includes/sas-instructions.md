---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623188"
---
Pokud chcete načíst adresu URL SAS pro vlastní model školení, v Azure Portal klikněte na prostředek úložiště a vyberte kartu **Průzkumník služby Storage** . Přejděte do svého kontejneru, klikněte pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Je důležité získat SAS pro váš kontejner, ne pro samotný účet úložiště. Ujistěte se, že jsou zaškrtnuta oprávnění **číst**, **zapisovat**, **Odstranit** a **seznam** , a klikněte na tlačítko **vytvořit**. Pak zkopírujte hodnotu v oddílu **Adresa URL** do dočasného umístění. Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
