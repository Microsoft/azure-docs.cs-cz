---
title: Rozšíření podsítě v Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o rozšíření podsítě v Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587508"
---
# <a name="subnet-extension"></a>Rozšíření podsítě
Migrace pracovního vytížení do veřejného cloudu vyžaduje pečlivé plánování a koordinaci. Jedním z klíčových aspektů může být možnost zachovat vaše IP adresy. Což může být důležité zejména v případě, že vaše aplikace mají závislost na adrese IP nebo máte požadavky na dodržování předpisů pro použití konkrétních IP adres. Virtuální síť Azure tento problém řeší tím, že vám umožní vytvářet virtuální sítě a podsítě pomocí rozsahu IP adres podle vašeho výběru.

Migrace může být trochu náročné, když je výše uvedený požadavek spojen s dalším požadavkem, aby některé aplikace zůstaly v místním prostředí. V takové situaci budete muset rozdělit aplikace mezi Azure a místní, bez přečíslování IP adres na obou stranách. Kromě toho budete muset povolit aplikacím komunikovat, jako by byly ve stejné síti.

Jedním z řešení výše uvedeného problému je rozšíření podsítě. Rozšíření sítě umožňuje aplikacím mluvit přes stejnou doménu vysílání, pokud existují v různých fyzických umístěních, a odstraní tak potřebu předělat topologii sítě. 

Rozšíření sítě není obecně osvědčeným postupem, ale níže uvedené případy použití mohou být nezbytné.

- **Fázovaná migrace**: Nejběžnější scénář je, že chcete fázovat migraci. Chcete přenést několik aplikací nejprve a v průběhu času migrovat zbytek aplikací do Azure.
- **Latence**: Požadavky na nízkou latenci může být dalším důvodem pro udržení některých aplikací v místním prostředí, abyste zajistili, že jsou co nejblíže vašemu datovému centru.
- **Dodržování předpisů**: Dalším případem použití je, že můžete mít požadavky na dodržování předpisů, aby některé vaše aplikace v místním prostředí.
 
> [!NOTE] 
> Neměli byste rozšiřovat podsítě, pokud to není nutné. V případech, kdy rozšířit podsítě, měli byste se pokusit, aby to mezikrok. Časem byste měli zkusit znovu očíslovat aplikace v místní síti a migrovat je do Azure.

V další části budeme diskutovat o tom, jak můžete rozšířit své podsítě do Azure.


## <a name="extend-your-subnet-to-azure"></a>Rozšíření podsítě do Azure
 Místní podsítě můžete rozšířit do Azure pomocí řešení založeného na síti vrstvy 3. Většina řešení používá technologii překrytí, jako je VXLAN, k rozšíření sítě vrstvy 2 pomocí překryvné sítě vrstvy 3. Níže uvedený diagram znázorňuje zobecněné řešení. V tomto řešení existuje stejná podsíť na obou stranách, tedy Azure a místní. 

![Příklad rozšíření podsítě](./media/subnet-extension/subnet-extension.png)

IP adresy z podsítě se přiřazují virtuálním počítačům v Azure a místně. Azure i místní mají vsíti vložené služby NVA. Když se virtuální počítač v Azure pokusí mluvit s virtuálním počítačem v místní síti, Azure NVA zachytí paket, zapouzdřuje ho a odešle přes VPN/Express Route do místní sítě. Místní síťové virtuální zařízení obdrží paket, dekaptuluje jej a předá zamýšlenému příjemci v jeho síti. Návratový provoz používá podobnou cestu a logiku.

Ve výše uvedeném příkladu Azure NVA a místní nVA komunikovat a získat informace o IP adresy za sebou. Složitější sítě mohou mít také službu mapování, která udržuje mapování mezi sítěmi pro propojení sítě sítě a IP adresami za nimi. Když služba NVA obdrží paket, dotazuje se služby mapování, aby zjistila adresu služby NVA, která má za sebou cílovou ADRESU IP.

V další části najdete podrobnosti o řešeních rozšíření podsítě, která jsme testovali v Azure.

## <a name="next-steps"></a>Další kroky 
[Rozšiřte svou podsíť do Azure pomocí řešení dodavatelů.](https://github.com/microsoft/Azure-LISP)