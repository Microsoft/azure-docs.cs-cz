---
title: Seznam povolených portů pro Avere vFXT pro Azure
description: Seznam portů používaných ve Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633846"
---
# <a name="required-ports"></a>Požadované porty

Porty uvedené v této části se používají pro vFXT příchozí a odchozí komunikaci.

Nikdy vystavit vFXT clusteru nebo clusteru instance kontroleru přímo do veřejného Internetu.

## <a name="api"></a>Rozhraní API

| Příchozí: | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| Odchozí: |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>Systém souborů NFS

| Příchozí a odchozí  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | Systém souborů NFS      |
| TCP/UDP | . 4045 | NLOCKMGR |
| TCP/UDP | . 4046 | MOUNTD   |
| TCP/UDP | 4047 | STAV   |

