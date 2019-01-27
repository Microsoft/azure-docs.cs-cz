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
ms.openlocfilehash: 646ebc79e3615b289b60e47c0a38595e7892d8cc
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084859"
---
| Prostředek | Omezení |
| --- | --- |
| Maximální počet jednotek škálování | 10 za oblasti<sup>1</sup> |
| Velikost mezipaměti | 5 GB za každou jednotku<sup>2</sup> |
| Souběžné back-end připojení<sup>3</sup> za autorita protokolu HTTP | 2 048 na jednotku<sup>4</sup> |
| Velikost maximální odpověď uložená v mezipaměti | 2MB |
| Zásady maximální velikosti dokumentu | 256KB<sup>5</sup> | 
| Maximální vlastní bránu domén na instanci služby<sup>6</sup> | 20 | 
| Maximální počet instancí služby za předplatné<sup>7</sup> | 20 | 
| Maximální počet odběrů za instanci služby<sup>7</sup> | 500 |
| Maximální počet klientské certifikáty pro jednotlivé instance služby<sup>7</sup> | 50 | 
| Maximální počet rozhraní API na instanci služby<sup>7</sup> | 50 | 
| Maximální počet operací rozhraní API na instanci služby<sup>7</sup> | 1000 | 
| Maximální celková doba trvání žádosti<sup>7</sup> | 30 sekund | 
| Maximální velikost datové části do vyrovnávací paměti<sup>7</sup> | 2MB | 


<sup>1</sup> limity škálování závisí na cenové úrovni. Chcete-li zobrazit ceny vrstvy a jejich limity škálování přejděte na [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> za jednotky mezipaměti závisí na cenové úrovni. Chcete-li zobrazit ceny vrstvy a jejich limity škálování přejděte na [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> připojení jsou ve fondu a znovu použít, pokud explicitně ukončeno back-endu.<br/>
<sup>4</sup> na jednotku úrovně Basic, Standard a Premium. Úroveň Developer je omezená na 1024. Neplatí pro úroveň Consumption.<br/> 
<sup>5</sup> v Basic, Standard a Premium. Spotřeba úroveň zásad dokumentu velikost je omezena na 4KB.<br/>
<sup>6</sup> k dispozici pouze na úrovni Premium.<br/>
<sup>7</sup> týká se jenom úroveň Consumption.<br/>



