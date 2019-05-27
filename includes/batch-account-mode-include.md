---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127570"
---
> [!NOTE]
> Při vytváření účtu Batch můžete zvolit mezi dvěma režimy *přidělování fondů*: **Předplatné uživatele** a **Služba Batch**. Pro většinu případů byste měli použít výchozí režim Služba Batch, při kterém se fondy přidělují na pozadí v předplatných spravovaných Azure. V alternativním režimu Předplatné uživatele se virtuální počítače a další prostředky služby Batch vytvářejí přímo ve vašem předplatném při vytvoření fondu. Režim Předplatné uživatele se vyžaduje, pokud chcete vytvořit fondy služby Batch pomocí [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Pokud chcete vytvořit účet Batch v režimu předplatného uživatele, musíte také zaregistrovat předplatné ve službě Azure Batch a k účtu přidružit službu Azure Key Vault.