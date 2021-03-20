---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061597"
---
Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu.

1. Vyhledejte a zkopírujte soubor *.pfx* na klientský počítač. Na klientském počítači spusťte instalaci dvojím kliknutím na soubor *.pfx*. Ponechte **umístění úložiště** jako **aktuální uživatel** a pak vyberte **Další**.
1. Na stránce **Importovaný soubor** neprovádějte žádné změny. Vyberte **Další**.
1. Na stránce **ochrana privátního klíče** zadejte heslo pro certifikát, nebo ověřte, zda je objekt zabezpečení správný, a poté vyberte možnost **Další**.
1. Na stránce **úložiště certifikátů** ponechte výchozí umístění a pak vyberte **Další**.
1. Vyberte **Dokončit**. V části **Upozornění zabezpečení** pro instalaci certifikátu vyberte **Ano**. Pro toto upozornění zabezpečení můžete pohodlně vybrat Ano, protože jste certifikát vygenerovali.
1. Certifikát se teď úspěšně naimportoval.
