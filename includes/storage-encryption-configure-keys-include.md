---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115433"
---
Azure Storage podporuje šifrování v klidovém stavu pomocí klíčů spravovaných microsoftem nebo klíče spravované zákazníkem. Klíče spravované zákazníkem umožňují vytvořit, otáčení, zakázat a odvolat přístup k ovládacím prvkům.

Spravovat klíče a auditovat využití klíčů pomocí Azure Key Vault. Můžete vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo rozhraní API služby Azure Key Vault můžete použít ke generování klíčů. Účet úložiště a trezoru klíčů musí být ve stejné oblasti, ale mohou být v různých předplatných. Další informace o službě Azure Key Vault najdete v tématu [co je Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
