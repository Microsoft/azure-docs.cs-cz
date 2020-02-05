---
title: Vytvoření fondů zdrojů s mapováním předplatného Azure
description: Popisuje postup vytvoření fondů zdrojů pro privátní cloud služby AVS prostřednictvím mapování předplatného Azure.
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014959"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Vytvoření fondů zdrojů pro privátní cloud služby AVS pomocí mapování předplatných Azure
Mapování předplatného Azure umožňuje vytvářet fondy zdrojů pro privátní cloud služby AVS z dostupných fondů prostředků vSphere. Na portálu služby AVS můžete zobrazit a spravovat předplatné Azure pro privátní cloudy služby AVS.

> [!NOTE]
> Mapování fondu zdrojů také mapuje všechny podřízené fondy zdrojů. Nadřazený fond zdrojů nejde namapovat, pokud už jsou namapované nějaké podřízené fondy zdrojů.

1. [Přístup k portálu AVS](access-cloudsimple-portal.md).
2. Otevřete stránku **prostředky** a vyberte **mapování předplatných Azure**.  
3. Klikněte na **Upravit mapování předplatného Azure**.  
4. Chcete-li namapovat dostupné fondy zdrojů, vyberte je na levé straně a klikněte na šipku vpravo. 
5. Mapování odstraníte tak, že je vyberete vpravo a kliknete na šipku vlevo. 

    ![Předplatná Azure](media/resources-azure-mapping.png)

6. Klikněte na **OK**.
