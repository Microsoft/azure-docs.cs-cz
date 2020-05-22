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
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764335"
---
# <a name="using-certificates-with-batch"></a>Používání certifikátů se službou Batch

Hlavním důvodem pro použití certifikátů se službou Batch je, že máte aplikace spuštěné ve fondech, které se musí ověřit u koncového bodu. 

Pokud ještě certifikát nemáte, můžete pomocí nástroje příkazového řádku vytvořit certifikát podepsaný svým držitelem `makecert` .

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Ruční nahrávání certifikátů prostřednictvím Azure Portal

1. Z účtu Batch, do kterého chcete odeslat certifikát, vyberte **certifikáty** a pak vyberte **Přidat**. 

2. Nahrajte certifikát s příponou. pfx nebo. cer. 

Po nahrání se certifikát přidá do seznamu certifikátů a tento kryptografický otisk můžete ověřit.

Když teď vytvoříte fond služby Batch, můžete přejít na certifikáty v rámci fondu a přiřadit certifikát, který jste nahráli do tohoto fondu.

## <a name="next-steps"></a>Další kroky

Batch má rozhraní API pro certifikáty, [AZ Batch Certificate Create](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

Informace o používání Key Vault najdete v tématu [zabezpečený přístup Key Vault pomocí služby Batch](credential-access-key-vault.md).
