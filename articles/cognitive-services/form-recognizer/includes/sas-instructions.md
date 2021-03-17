---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467192"
---
Pokud chcete načíst adresu URL SAS pro vlastní model školení, v Azure Portal klikněte na prostředek úložiště a vyberte kartu **Průzkumník služby Storage** . Přejděte do svého kontejneru, klikněte pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Je důležité získat SAS pro váš kontejner, ne pro samotný účet úložiště. Ujistěte se, že jsou zaškrtnuta oprávnění **číst**, **zapisovat**, **Odstranit** a **seznam** , a klikněte na tlačítko **vytvořit**. Pak zkopírujte hodnotu v oddílu **Adresa URL** do dočasného umístění. Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
