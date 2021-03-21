---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98859732"
---
- Podporují se jenom [softwarové a hardwarové klíče RSA](../articles/key-vault/keys/about-keys.md) velikosti 2 048, 3 072 a 4 096-bit, žádné jiné klíče ani velikosti.
    - Klíče [HSM](../articles/key-vault/keys/hsm-protected-keys.md) vyžadují úroveň **Premium** trezorů klíčů Azure.
- Disky vytvořené z vlastních imagí šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem a musí být ve stejném předplatném.
- Snímky vytvořené z disků šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem.
- Všechny prostředky, které souvisejí s vašimi klíči spravovanými zákazníky (trezory klíčů Azure, sady šifrování disků, virtuální počítače, disky a snímky), musí být ve stejném předplatném a oblasti.
- Disky, snímky a image šifrované pomocí klíčů spravovaných zákazníkem se nedají přesunout do jiné skupiny prostředků a předplatného.
- Spravované disky aktuálně nebo dříve zašifrované pomocí Azure Disk Encryption nelze šifrovat pomocí klíčů spravovaných zákazníkem.
- Dá se vytvořit jenom 1000 sad pro šifrování disků na jednu oblast a předplatné.
- Informace o použití klíčů spravovaných zákazníkem s galeriem sdílených imagí najdete v tématu [verze Preview: použití klíčů spravovaných zákazníkem pro šifrování imagí](../articles/virtual-machines/image-version-encryption.md).
