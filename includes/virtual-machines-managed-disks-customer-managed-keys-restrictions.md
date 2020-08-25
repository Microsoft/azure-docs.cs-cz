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
ms.openlocfilehash: 8833639b6efacc664596ecb2aa6f9da41ad23b81
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814127"
---
- Podporují se jenom [softwarové a hardwarové klíče RSA](../articles/key-vault/keys/about-keys.md) velikosti 2 048, 3 072 a 4 096-bit, žádné jiné klíče ani velikosti.
    - Klíče [HSM](../articles/key-vault/keys/hsm-protected-keys.md) vyžadují úroveň **Premium** trezorů klíčů Azure.
- Disky vytvořené z vlastních imagí šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem a musí být ve stejném předplatném.
- Snímky vytvořené z disků šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem.
- Všechny prostředky, které souvisejí s vašimi klíči spravovanými zákazníky (trezory klíčů Azure, sady šifrování disků, virtuální počítače, disky a snímky), musí být ve stejném předplatném a oblasti.
- Disky, snímky a image šifrované pomocí klíčů spravovaných zákazníkem se nedají přesunout do jiného předplatného.
- Spravované disky aktuálně nebo dříve zašifrované pomocí Azure Disk Encryption nelze šifrovat pomocí klíčů spravovaných zákazníkem.
- Dá se vytvořit jenom 50 sad pro šifrování disků na jednu oblast a předplatné.
- Informace o použití klíčů spravovaných zákazníkem s galeriem sdílených imagí najdete v tématu [verze Preview: použití klíčů spravovaných zákazníkem pro šifrování imagí](../articles/virtual-machines/image-version-encryption.md).