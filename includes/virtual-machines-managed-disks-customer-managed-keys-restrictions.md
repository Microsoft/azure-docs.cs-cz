---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c303527c7411ead585e70fc2e31db4dd2d35e996
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259825"
---
- Podporují se jenom [softwarové a hardwarové klíče RSA](../articles/key-vault/keys/about-keys.md) velikosti 2 048, 3 072 a 4 096-bit, žádné jiné klíče ani velikosti.
    - Klíče [HSM](../articles/key-vault/keys/hsm-protected-keys.md) vyžadují úroveň **Premium** trezorů klíčů Azure.
- Disky vytvořené z vlastních imagí šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem a musí být ve stejném předplatném.
- Snímky vytvořené z disků šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem.
- Všechny prostředky, které souvisejí s vašimi klíči spravovanými zákazníky (trezory klíčů Azure, sady šifrování disků, virtuální počítače, disky a snímky), musí být ve stejném předplatném a oblasti.
- Disky, snímky a image šifrované pomocí klíčů spravovaných zákazníkem se nedají přesunout do jiného předplatného.
- Spravované disky šifrované pomocí klíčů spravovaných zákazníkem se nedají taky šifrovat pomocí Azure Disk Encryption.
- Dá se vytvořit jenom 50 sad pro šifrování disků na jednu oblast a předplatné.
- Informace o použití klíčů spravovaných zákazníkem s galeriem sdílených imagí najdete v tématu [verze Preview: použití klíčů spravovaných zákazníkem pro šifrování imagí](../articles/virtual-machines/image-version-encryption.md).
