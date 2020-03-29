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
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895263"
---
Azure Storage šifruje všechna data v účtu úložiště v klidovém stavu. Ve výchozím nastavení jsou data šifrována pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí pro šifrování dat objektů blob a souborů.

Klíče spravované zákazníkem musí být uloženy v trezoru klíčů Azure. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale mohou být v různých předplatných. Další informace o šifrování Azure Storage a správě klíčů najdete v tématu [šifrování Azure Storage pro data v klidovém stavu](../articles/storage/common/storage-service-encryption.md). Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
