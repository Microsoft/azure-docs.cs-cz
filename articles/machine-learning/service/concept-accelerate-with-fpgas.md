---
title: Azure Machine Learning a FPGAs
description: Zjistěte, jak zrychlit modely a hluboké neuronové sítě s FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Co je programovatelný brány pole pole (FPGA)?

Pole programovatelný brány pole (FPGA) jsou integrované okruhů, které můžete překonfigurovat, podle potřeby. FPGA můžete změnit podle potřeby implementovat nový logiku. Azure Machine Learning hardwaru Accelerated modely umožňují nasadit trénované modely FPGAs v cloudu Azure.

Tato funkce používá technologii Brainwave projektu, která zpracovává překladu hluboké neuronové sítě (DNN) do FPGA programu. 

## <a name="why-use-an-fpga"></a>Proč používat FPGA?

FPGAs poskytují velmi nízkou latencí a jsou zvláště efektivní pro vyhodnocování dat na velikost dávky, jeden (neexistuje požadavek pro velikost dávky velké).  Jsou hospodárným a k dispozici v Azure.  Projekt Brainwave můžete paralelní předem vyškolení DNNs mezi tyto FPGAs pro horizontální škálování služby.

## <a name="next-steps"></a>Další postup

[Nasadit model jako webovou službu na FPGA](how-to-deploy-fpga-web-service.md)

[Zjistěte, jak nainstalovat sadu SDK FPGA](reference-fpga-package-overview.md)