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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67175410"
---
| Prostředek | Omezení |
| --- | --- |
| Maximální počet jednotek škálování | 10 na oblast<sup>1</sup> |
| Velikost mezipaměti | 5 GB na jednotku<sup>2</sup> |
| Souběžná připojení back-end<sup>3</sup> na AUTORITu http | 2 048 na jednotku<sup>4</sup> |
| Maximální velikost odpovědi v mezipaměti | 2 MB |
| Maximální velikost dokumentu zásad | 256 KB<sup>5</sup> | 
| Maximální počet domén vlastní brány na instanci služby<sup>6</sup> | 20 |
| Maximální počet certifikátů certifikační autority na instanci služby | 10 | 
| Maximální počet instancí služby na předplatné<sup>7</sup> | 20 | 
| Maximální počet předplatných na instanci služby<sup>7</sup> | 500 |
| Maximální počet klientských certifikátů na instanci služby<sup>7</sup> | 50 | 
| Maximální počet rozhraní API na instanci služby<sup>7</sup> | 50 | 
| Maximální počet operací rozhraní API na instanci služby<sup>7</sup> | 1 000 | 
| Maximální celková doba trvání žádosti<sup>7</sup> | 30 sekund | 
| Maximální velikost datové části vyrovnávací paměti<sup>7</sup> | 2 MB | 


<sup>1</sup> Omezení škálování závisí na cenové úrovni. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování, přečtěte si téma [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> . Velikost mezipaměti pro jednotky závisí na cenové úrovni. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování, přečtěte si téma [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> . Připojení se ve fondu a znovu používají, pokud není explicitně Uzavřeno pomocí back-endu.<br/>
<sup>4</sup> . Toto omezení je na jednotku úrovně Basic, Standard a Premium. Úroveň pro vývojáře je omezená na 1 024. Toto omezení se nevztahuje na úroveň spotřeby.<br/> 
<sup>5</sup>toto omezení se vztahuje na úrovně Basic, Standard a Premium. V úrovni spotřeby je velikost dokumentu zásad omezená na 4 KB.<br/>
<sup>6</sup> Tento prostředek je k dispozici pouze v úrovni Premium.<br/>
<sup>7</sup> Tento prostředek se vztahuje pouze na úroveň spotřeby.<br/>



