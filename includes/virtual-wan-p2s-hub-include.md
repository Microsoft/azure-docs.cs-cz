---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812667"
---
1. V části virtuální síť WAN vyberte rozbočovače a vyberte **+ nové centrum**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="nové centrum":::

1. Na stránce vytvořit virtuální rozbočovač vyplňte následující pole.

   * **Oblast** – vyberte oblast, do které chcete nasadit virtuální rozbočovač.
   * **Název** – zadejte název, který chcete zavolat do svého virtuálního rozbočovače.
   * **Privátní adresní prostor centra** – rozsah adres rozbočovače v zápisu CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="nové centrum":::

1. Na kartě Point-to-site vyplňte následující pole:

   * **Jednotky škálování brány** – což představuje agregovanou kapacitu brány VPN uživatele.
   * **Najeďte na položku Konfigurace lokality** , kterou jste vytvořili v předchozím kroku.
   * **Fond adres klienta** – pro vzdálené uživatele.
   * **IP adresa vlastního serveru DNS**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="nové centrum":::

1. Vyberte **Zkontrolovat a vytvořit**.
1. Na stránce **úspěšné ověření** vyberte **vytvořit**.