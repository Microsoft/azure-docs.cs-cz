---
title: Čínské doručování obsahu s Azure CDN | Microsoft Docs
description: Přečtěte si o používání služby Azure Content Delivery Network (CDN) k doručování obsahu čínským uživatelům.
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
ms.openlocfilehash: 599ec041837460c30b4655531b822eab5f0eafa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778910"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Čínské doručování obsahu s Azure CDN

Globální Azure Content Delivery Network (CDN) může sloužit k obsahu pro čínské uživatele s umístěními bodů POP (Point-of-prezence) v Číně nebo jakémkoli bodě POP, který poskytuje nejlepší výkon pro požadavky pocházející z Číny. Pokud je ale pro zákazníky významným trhem a potřebují vysoký výkon, zvažte místo toho použití Azure CDN Číny.

Azure CDN Čína se liší od Azure CDN globální v tom, že doručuje obsah z bodů POP v Číně prostřednictvím partnerství s řadou místních zprostředkovatelů. V důsledku čínských předpisů a předpisů je nutné zaregistrovat samostatné předplatné pro použití Azure CDN Čína a vaše weby musí mít licenci ICP. Portál a prostředí API, které umožňuje povolit a spravovat doručování obsahu, jsou shodné mezi Azure CDN globálními a Azure CDN Číně.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Porovnání Azure CDN globální a Azure CDN Číně

Azure CDN globální a Azure CDN Čína mají následující funkce:

- Azure CDN globální:

     - Bran https://portal.azure.com  

     - Provádí doručování obsahu mimo Čínu.

     - Čtyři cenové úrovně: Microsoft Standard, Verizon Standard, Verizon Premium a Akamai Standard

     - [Dokumentace](./index.yml)

- Azure CDN Čína:

     - Bran https://portal.azure.cn

     - Provádí doručování obsahu v Číně.

     - Dvě cenové úrovně: Standard a Premium

     - [Dokumentace](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Další kroky

Další informace o Azure CDN Číně najdete v těchto tématech:

- [Content Delivery Network funkce](https://www.azure.cn/en-us/home/features/cdn/)

- [Přehled služby Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Použití Content Delivery Network Azure](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Dostupnost služby Azure v Číně](/azure/china/concepts-service-availability)