---
title: zahrnout soubor
description: zahrnout soubor
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389487"
---
| Názvy domén                                          | Odchozí porty | Description                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Veřejný cloud: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> Lidov `*.servicebus.chinacloudapi.cn`   | 443            | Vyžadováno místním prostředím Integration runtime pro interaktivní vytváření. |
| Veřejný cloud: `{datafactory}.{region}.datafactory.azure.net`<br> ani `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> Lidov `{datafactory}.{region}.datafactory.azure.cn` | 443            | Požadováno místním prostředím Integration runtime pro připojení ke službě Data Factory. <br>Pro nové vytvořené Data Factory ve veřejném cloudu Najděte plně kvalifikovaný název domény od svého místního hostovaného Integration Runtime klíče, který je ve formátu {DataFactory}. {region}. DataFactory. Azure. NET. Pokud v případě staré datové továrny nevidíte plně kvalifikovaný název domény v integrovaném integračním klíči, použijte místo toho *. frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Požadováno místním prostředím Integration runtime pro stahování aktualizací. Pokud jste zakázali automatickou aktualizaci, můžete tuto doménu přeskočit konfigurací. |
| Adresa URL služby Key Vault | 443           | Požaduje se Azure Key Vault, pokud přihlašovací údaje uložíte v Key Vault. |
