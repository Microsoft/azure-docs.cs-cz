---
title: Používání certifikátů – Azure Batch
description: Použití certifikátů k povolení ověřování aplikací
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146381"
---
# <a name="using-certificates-with-batch"></a>Použití certifikátů se službou Batch

Hlavním důvodem pro použití certifikátů se službou Batch je, že máte aplikace spuštěné ve fondech, které se musí ověřit u koncového bodu. 

Pokud ještě certifikát nemáte, můžete pomocí nástroje příkazového řádku vytvořit certifikát podepsaný svým držitelem `makecert` .

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Ruční nahrávání certifikátů prostřednictvím Azure Portal

1. Z účtu Batch, do kterého chcete odeslat certifikát, vyberte **certifikáty** a pak vyberte **Přidat**. 

2. Nahrajte certifikát s příponou. pfx nebo. cer. 

Po nahrání se certifikát přidá do seznamu certifikátů a tento kryptografický otisk můžete ověřit.

Když teď vytvoříte fond služby Batch, můžete přejít na certifikáty v rámci fondu a přiřadit certifikát, který jste nahráli do tohoto fondu.

## <a name="next-steps"></a>Další kroky

Batch má rozhraní API pro certifikáty, [AZ Batch Certificate Create](/cli/azure/batch/certificate)

Informace o používání Key Vault najdete v tématu [zabezpečený přístup Key Vault pomocí služby Batch](credential-access-key-vault.md).
