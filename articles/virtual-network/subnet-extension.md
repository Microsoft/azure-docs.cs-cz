---
title: Rozšíření podsítě v Azure | Microsoft Docs
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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539294"
---
# <a name="subnet-extension"></a>Rozšíření podsítě
Migrace úloh do veřejného cloudu vyžaduje pečlivé plánování a koordinaci. Jedním z klíčových důležitých informací může být schopnost uchovávat vaše IP adresy. To může být důležité hlavně v případě, že vaše aplikace mají závislost IP adres nebo máte požadavky na dodržování předpisů pro použití konkrétních IP adres. Azure Virtual Network tento problém řeší tím, že vám umožní vytvořit virtuální síť a podsítě pomocí rozsahu IP adres podle vašeho výběru.

Migrace můžou být náročné, pokud je výše uvedený požadavek spojený s dalším požadavkem na místní udržování některých aplikací. V takovém případě budete muset rozdělit aplikace mezi Azure a místním prostředím bez přečíslování IP adres na obou stranách. Kromě toho budete muset umožňovat aplikacím, aby komunikovali, jako by byly ve stejné síti.

Jedním z řešení výše uvedeného problému je rozšíření podsítě. Rozšíření sítě umožňuje, aby aplikace komunikovaly se stejnou doménou všesměrového vysílání, když existují v různých fyzických umístěních, a odstraňují tak nutnost měnit architekt síťové topologie. 

I když rozšíření vaší sítě není v zásadě dobrým zvykem, může to být v případě potřeby nutné.

- Postupná **migrace**: Nejběžnější scénář je, že chcete fázi migrace. Před migrací zbývajících aplikací do Azure je třeba nejprve přenést několik aplikací.
- **Latence**: požadavky na nízkou latenci můžou být jiným důvodem, než budete některé aplikace uchovávat místně, abyste měli jistotu, že jsou co nejblíže vašemu datovému centru.
- **Dodržování předpisů**: jiný případ použití je, že můžete mít požadavky na dodržování předpisů, aby některé z vašich aplikací zůstaly v místním prostředí.
 
> [!NOTE] 
> Podsítě byste neměli rozšíříte, pokud to není nutné. V případech, kdy rozšíříte podsítě, byste se měli pokusit udělat krok za krokem. V čase byste měli zkusit znovu očíslovat aplikace v místní síti a migrovat je do Azure.

V další části se podíváme, jak můžete své podsítě rozšíříte do Azure.


## <a name="extend-your-subnet-to-azure"></a>Rozšiřování podsítě do Azure
 Místní podsítě můžete do Azure rozmístit pomocí překryvných síťových řešení vrstvy 3. Většina řešení používá překryvnou technologii, jako je VXLAN, k rozšiřování sítě vrstvy 2 pomocí překryté sítě vrstvy 3. Následující diagram znázorňuje zobecněné řešení. V tomto řešení existuje stejná podsíť na obou stranách, v Azure i v místním prostředí. 

![Příklad rozšíření podsítě](./media/subnet-extension/subnet-extension.png)

IP adresy z podsítě jsou přiřazené virtuálním počítačům v Azure a místním prostředí. V Azure i v místním prostředí jsou síťové virtuální zařízení vložené do jejich sítí. Když se virtuální počítač v Azure pokusí spojit s virtuálním počítačem v místní síti, Azure síťové virtuální zařízení zachytává paket, zapouzdřuje ho a pošle ho přes VPN/Express trasu do místní sítě. Místní síťové virtuální zařízení přijme paket, decapsulates ho a předá ho zamýšlenému příjemci v jeho síti. Návratový provoz používá podobnou cestu a logiku.

V předchozím příkladu síťové virtuální zařízení Azure a místní síťové virtuální zařízení komunikují a informace o IP adresách navzájem. Složitější sítě mohou mít také službu mapování, která udržuje mapování mezi síťová virtuální zařízení a IP adresami za nimi. Když síťové virtuální zařízení obdrží paket, dotazuje se na službu mapování, kde zjistí adresu síťové virtuální zařízení, která má cílovou IP adresu na pozadí.

V další části najdete podrobné informace o řešeních rozšíření podsítě, která jsme otestovali v Azure.

## <a name="next-steps"></a>Další kroky 
[Rozšíříte svou podsíť do Azure pomocí řešení dodavatelů.](https://github.com/microsoft/Azure-LISP)