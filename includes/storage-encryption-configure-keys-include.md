---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665898"
---
Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete poskytovat klíče spravované zákazníkem na úrovni účtu úložiště.

Klíče spravované zákazníkem musí být uloženy v Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Storage šifrování a správě klíčů najdete v části [Azure Storage šifrování pro neaktivní data](../articles/storage/common/storage-service-encryption.md). Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
