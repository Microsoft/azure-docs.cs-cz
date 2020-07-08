---
title: Zobrazit podrobnosti o ověřování Azure Maps
description: Pomocí Azure Portal můžete zobrazit podrobnosti o ověřování Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988552"
---
Podrobnosti o ověřování účtu Azure Maps můžete zobrazit v Azure Portal. Ve svém účtu v nabídce **Nastavení** vyberte **ověřování**.

![Podrobnosti ověřování](../media/how-to-manage-authentication/how-to-view-auth.png)

Po vytvoření účtu Azure Maps se hodnota Azure Maps nachází na `x-ms-client-id` stránce s podrobnostmi ověřování Azure Portal. Tato hodnota představuje účet, který se bude používat pro REST API požadavky. Tato hodnota by měla být uložena v konfiguraci aplikace a načtena před provedením požadavků HTTP při použití ověřování Azure AD s Azure Maps.
