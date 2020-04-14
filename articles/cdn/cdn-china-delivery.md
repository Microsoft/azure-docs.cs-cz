---
title: Doručování obsahu z Číny pomocí Azure CDN | Dokumenty společnosti Microsoft
description: Přečtěte si o používání sítě pro doručování obsahu Azure (CDN) k doručování obsahu uživatelům v Číně.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: fa144c7ebd68e6f5dd192fca83dc6f306d7b8d63
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254100"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Doručování obsahu z Číny pomocí Azure CDN

Azure Content Delivery Network (CDN) global může poskytovat obsah uživatelům v Číně s umístěním bodu přítomnosti (POP) v blízkosti Číny nebo jakékoli pop, který poskytuje nejlepší výkon pro požadavky pocházející z Číny. Pokud je však Čína významným trhem pro vaše zákazníky a potřebují rychlý výkon, zvažte místo toho použití Azure CDN China.

Azure CDN Čína se liší od Azure CDN globální v tom, že dodává obsah z POP uvnitř Číny partnerství s řadou místních poskytovatelů. Vzhledem k čínskédodržování předpisů a regulace, musíte zaregistrovat samostatné předplatné používat Azure CDN Čínu a vaše webové stránky musí mít licenci ICP. Prostředí portálu a rozhraní API umožňující ho povolit a spravovat doručování obsahu je identické mezi Azure CDN global a Azure CDN China.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Porovnání azure cdn globální a Azure CDN Čína

Azure CDN global a Azure CDN China mají následující funkce:

- Azure CDN globální:

     - Portál:https://portal.azure.com  

     - Provádí doručování obsahu mimo Čínu

     - Čtyři cenové úrovně: Microsoft standard, Verizon standard, Verizon premium a Akamai standard

     - [Dokumentace](https://docs.microsoft.com/azure/cdn/)

- Azure CDN Čína:

     - Portál:https://portal.azure.cn

     - Provádí doručování obsahu uvnitř Číny

     - Dvě cenové úrovně: Standardní a prémiová

     - [Dokumentace](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Další kroky

Další informace o Azure CDN China najdete v tématu:

- [Funkce sítě pro doručování obsahu](https://www.azure.cn/en-us/home/features/cdn/)

- [Přehled sítě pro doručování obsahu Azure](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Použití sítě pro doručování obsahu Azure](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Dostupnost služeb Azure v Číně](https://docs.microsoft.com/azure/china/concepts-service-availability)



