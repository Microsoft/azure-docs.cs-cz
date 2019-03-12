---
title: zahrnout soubor
description: zahrnout soubor
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553374"
---
| Prostředek | Omezení |
| --- | --- |
| Maximální počet jednotek škálování | 10 za oblasti<sup>1</sup> |
| Velikost mezipaměti | 5 GB za každou jednotku<sup>2</sup> |
| Souběžná připojení back-end<sup>3</sup> za autorita protokolu HTTP | 2 048 na jednotku<sup>4</sup> |
| Velikost maximální odpověď uložená v mezipaměti | 2 MB |
| Zásady maximální velikosti dokumentu | 256 KB<sup>5</sup> | 
| Maximální vlastní bránu domén na instanci služby<sup>6</sup> | 20 |
| Maximální počet certifikáty certifikační Autority pro jednotlivé instance služby | 10 | 
| Maximální počet instancí služby za předplatné<sup>7</sup> | 20 | 
| Maximální počet odběrů za instanci služby<sup>7</sup> | 500 |
| Maximální počet klientské certifikáty pro jednotlivé instance služby<sup>7</sup> | 50 | 
| Maximální počet rozhraní API na instanci služby<sup>7</sup> | 50 | 
| Maximální počet operací rozhraní API na instanci služby<sup>7</sup> | 1 000 | 
| Maximální celková doba trvání žádosti<sup>7</sup> | 30 sekund | 
| Maximální velikost datové části do vyrovnávací paměti<sup>7</sup> | 2 MB | 


<sup>1</sup>limity škálování závisí na cenové úrovni. Cenové úrovně a jejich limity škálování najdete v tématu [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>za jednotky mezipaměti závisí na cenové úrovni. Cenové úrovně a jejich limity škálování najdete v tématu [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>připojení jsou ve fondu a znovu použít, pokud explicitně ukončeno back-endu.<br/>
<sup>4</sup>toto omezení je na jednotku úrovně Basic, Standard a Premium. Úroveň Developer je omezena na 1 024. Toto omezení neplatí pro úroveň Consumption.<br/> 
<sup>5</sup>toto omezení se vztahuje na úrovních Basic, Standard a Premium. Na úrovni Consumption velikost dokument zásad je omezena na 4 KB.<br/>
<sup>6</sup>tento prostředek k dispozici pouze na úrovni Premium.<br/>
<sup>7</sup>tento prostředek se vztahuje na pouze úroveň Consumption.<br/>



